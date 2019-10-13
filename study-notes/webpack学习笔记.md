# webpack学习笔记

## 摘要

- 通过webpack启动指定不同的构建配置文件，对于需要拆分成多个文件的复杂配置是非常有用

> 通过向 npm run build 命令和你的参数之间添加两个中横线，可以将自定义参数传递给 webpack，例如：npm run build -- --colors

## 管理资源

### 加载CSS

- import 一个 CSS 文件，使用 `style-loader` 和 `css-loader` 插件加载

### 加载图片

- `file-loader` 把image路径转换成打包后的url，在`css-loader`也有类似的处理。`html-loader` 以相同的方式处理 `<img src="./my-image.png" />`

> 压缩和优化图像。查看 image-webpack-loader 和 url-loader，以了解更多关于如果增强加载处理图片功能。

### 加载字体

- `file-loader` 和 `url-loader` 可以接收并加载任何文件，然后将其输出到构建目录

### 加载数据

- node.js内置支持 `import` json文件
- 要导入 CSV、TSV 和 XML，可以使用 `csv-loader` 和 `xml-loader`

## 管理输出

### html-webpack-plugin

- `html-webpack-plugin` 会自动构建html主页文件，并注入 `<script>` 动态引入构建时生成的js文件

### clean-webpack-plugin

- `clean-webpack-plugin` 自动清理 `dist` 目录，可配置 `dry` 选项为 `true`，让它自动根据 `output.path` 目录清理构建目录

### Manifest

存储了构建过程中的数据，在对构建过程进行缓存优化和其他自定义处理时非常有用

## 开发

### 使用 source map

几个js文件打包到一个文件后，js错误会指向打包后的文件，难以追踪和定位错误的位置。使用 source map 可以解决定位问题。

使用前：

    Uncaught ReferenceError: conosle is not defined
    at HTMLButtonElement.e (app.bundle.js:1)

使用后：

    Uncaught ReferenceError: conosle is not defined
      at HTMLButtonElement.e (print.js:2)

### 选择一个开发工具

#### 使用观察模式（webpack's Watch Mode）

    "watch": "webpack --watch"

唯一的缺点是要手动刷新浏览器才能看到效果

#### 使用 webpack-dev-server

`webpack-dev-server` 提供了一个简单的 web 服务器，能够实时重新加载

    // webpack.config.js
    devServer: {
      contentBase: './dist'
    },

    // package.json
    "start": "webpack-dev-server --open"

#### 使用 webpack-dev-middleware

`webpack-dev-middleware` 是一个容器(wrapper)，它可以把 webpack 处理后的文件传递给一个服务器(server)。 `webpack-dev-server` 在内部使用了它。`webpack-dev-middleware` 配合 `express` `server` 的示例：

    // webpack.config.js
    output: {
      ...
      publicPath: '/'
    }
    
    // package.json
    "server": "node server.js",
根目录：`server.js`

    const express = require('express');
    const webpack = require('webpack');
    const webpackDevMiddleware = require('webpack-dev-middleware');
    
    const app = express();
    const config = require('./webpack.config.js');
    const compiler = webpack(config);
    
    // Tell express to use the webpack-dev-middleware and use the webpack.config.js
    // configuration file as a base.
    app.use(webpackDevMiddleware(compiler, {
      publicPath: config.output.publicPath
    }));
    
    // Serve the files on port 3000.
    app.listen(3000, function () {
      console.log('Example app listening on port 3000!\n');
    });

## 模块热替换

### 启用HMR

    // webpack.config.js
    ...
    const webpack = require('webpack');
    ...
    entry: {
      app: './src/index.js'
    },
    devServer: {
      ...
      hot: true
    },
    plugins: [
      ...
      new webpack.NamedModulesPlugin(),
      new webpack.HotModuleReplacementPlugin()
    ]

    // index.js
    if (module.hot) {
      module.hot.accept('./print.js', function() {
        console.log('Accepting the updated printMe module!');
        printMe();
      })
    }

    // print.js
    ...
    console.log('Updating print.js...')

### 通过 Node.js API

当使用 webpack dev server 和 Node.js API 时，不要将 dev server 选项放在 webpack 配置对象(webpack config object)中。

> 参考官方文档

## tree shaking

### 添加一个通用模块

自动打包引用的模块

### 将文件标记为无副作用(sideEffects)

配置剔除未调用的js

### 压缩输入

webpack4中增加如下配置可以启用 uglifyjs 压缩插件

    // webpack.config.js
    mode: "production"

## 生产环境构建

### 配置

生产和开发环境使用不同的构建配置，公共配置抽出来，使用 `webpack-merge` 插件进行合并处理

### Minification

除 `UglifyJSPlugin` 外，还有 `BabelMinifyWebpackPlugin` 和 `ClosureCompilerPlugin` 在压缩代码方面也很受欢迎

### source map

在生产环境中启用 `source map` 选项，与开发环境不同，可以减少bundle的大小

    // webpack.prod.js
    ...
    devtool: 'source-map',
    plugins: [
      new UglifyJSPlugin({
        sourceMap: true
      })
    ]

### 指定环境

使用 `webpack` 内置的 `DefinePlugin` 为所有的依赖定义 `NODE_ENV` 变量

    // webpack.prod.js
    const webpack = require('webpack');
    ...
      plugins: [
        new webpack.DefinePlugin({
          'process.env.NODE_ENV': JSON.stringify('production')
        })
      ]

    // src/index.js
    if (process.env.NODE_ENV !== 'production') {
      console.log('Looks like we are in development mode!');
    }

> 技术上讲，NODE_ENV 是一个由 Node.js 暴露给执行脚本的系统环境变量。通常用于决定在开发环境与生产环境(dev-vs-prod)下，服务器工具、构建脚本和客户端 library 的行为。然而，与预期不同的是，无法在构建脚本 webpack.config.js 中，将 process.env.NODE_ENV 设置为 "production"，请查看 #2537。因此，例如 process.env.NODE_ENV === 'production' ? '[name].[hash].bundle.js' : '[name].bundle.js' 这样的条件语句，在 webpack 配置文件中，无法按照预期运行。

### Split CSS

通常最好的做法是使用 `ExtractTextPlugin` 将 `CSS` 分离成单独的文件

### CLI 替代选项

`UglifyJSPlugin` 和 `DefinePlugin` 都可以在启动命令时设置，但建议还是使用配置方式，配置方式能够更好地帮助你了解自己正在做的事情。配置方式还可以让你更方便地控制这两个插件中的其他选项

## 代码分离

### 入口起点(entry points)

存在一些问题

- 如果入口 chunks 之间包含重复的模块，那些重复模块都会被引入到各个 bundle 中
- 这种方法不够灵活，并且不能将核心应用程序逻辑进行动态拆分代码

### 防止重复(prevent duplication)

`CommonsChunkPlugin` 插件可以将公共的依赖模块提取到已有的入口 chunk 中，或者提取到一个新生成的 chunk。 `CommonsChunkPlugin` 在webpack4中已经移除，由 `SplitChunksPlugin` 替换相关功能

    // webpack.config.js
    ...
    optimization: {
      splitChunks: {
        chunks: 'all',
        name: 'common'
      }
    }

对于代码分离很有帮助的插件和 loaders:

- `ExtractTextPlugin`: 用于将 CSS 从主应用程序中分离
- `bundle-loader`: 用于分离代码和延迟加载生成的 bundle
- `promise-loader`: 类似于 `bundle-loader` ，但是使用的是 promises

### 动态导入(dynamic imports)

当涉及到动态代码拆分时，webpack 提供了两个类似的技术。对于动态导入，第一种，也是优先选择的方式是，使用符合 ECMAScript 提案 的 import() 语法。第二种，则是使用 webpack 特定的 `require.ensure`

### bundle 分析(bundle analysis)

- [官方分析工具](https://github.com/webpack/analyse) 
- `webpack-chart`: webpack 数据交互饼图
- `webpack-visualizer`: 可视化并分析你的 bundle，检查哪些模块占用空间，哪些可能是重复使用的
- `webpack-bundle-analyzer`: 一款分析 bundle 内容的插件及 CLI 工具，以便捷的、交互式、可缩放的树状图形式展现给用户


## 缓存

### 输出文件的文件名(Output Filenames)

    // webpack.config.js
    output: {
      filename: '[name].[chunkhash].js'
      ...
    }

### 提取模板(Extracting Boilerplate)

    // webpack.config.js
    ...
    entry: {
      app: './src/index.js',
      vendor: [
        'lodash'
      ]
    }
    optimization: {
      splitChunks: {
        chunks: 'all',
        name: 'vendor'
      },
      splitChunks: {
        chunks: 'all',
        name: 'manifest'
      }
    }

### 模块标识符(Module Identifiers)

- `main` bundle 会随着自身的新增内容的修改，而发生变化
- `vendor` bundle 会随着自身的 module.id 的修改，而发生变化
- `manifest` bundle 会因为当前包含一个新模块的引用，而发生变化

 `vendor` 的 hash 发生变化是我们要修复的。可以使用 ` NamedModulesPlugin` 或 `HashedModuleIdsPlugin` 保证无论怎么修改本地文件， `vendor` 的 hash 都不会发生变化


## 创建 library

