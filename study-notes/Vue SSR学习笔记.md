# Vue SSR 学习笔记

## 脚手架项目初始化

	npx create-nuxt-app hello-nuxt
初始化项目配置选项：

- server framework
- ui framework
- rendering mode

完成配置后。使用
	```npm run dev```
命令启动开发环境

	npm install vue vue-server-renderer --save
	
	npm install --save-dev node-sass sass-loader

## nuxt路由
路由，以目录和文件作路由

### 路由传参
以下划线作为传参

### 嵌套路由
单文件和同名目录实现

### 过渡动效

### middleware

## nuxt视图

### 布局

- 自定义布局

页面复用的时候用起来非常直观和顺手

- 错误页面

可定义404、500等错误页面

### 页面
nuxt.js对vue组件进行了扩展，增加了如下特殊配置项：

| 属性名 | 描述 |
| ------ | ------ |
| asyncData | 异步数据处理 |
| fetch | 与 ```asyncData``` 类似，但用于填充状态树 |
| head | 页面头部配置API |
| layout | 布局文件 |
| loading |  |
| transition | 页面过渡效果 |
| scrollToTop | 页面渲染前是否需要滚动到顶部 |
| validate | 校验动态路由的参数 |
| middleware | 路由中间件 |

## nuxt异步数据

## nuxt插件

### 配置引入
plugin目录下增加对应插件的配置文件
nuxt.config.js中配置引入plugin下的配置文件

### 注入实例

this.$*可访问

### 注入context

### 联合注入

## nuxt模块

nuxt 可以以编程形式使用

## nuxt-vuex状态树

### 状态树

### fetch

### nuxtServerInit 方法

## nuxt命令和部署



## 七、添加一个全局的css

