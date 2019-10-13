# vue单页面网站重构成SSR笔记

## 从零构建项目
- 新增 ```package.json``` 文件：

		{
		  "name": "website-ui-ssr",
		  "scripts": {
		    "dev": "nuxt"
		  }
		}

- 安装 ```nuxt```

        npm install --save nuxt

- 新增 ```nuxt.config.js``` 文件

        module.exports = {
          mode: 'spa',
          title: '',
          head: {
            meta: [
              {
                charset: 'utf-8'
              }
            ]
          }
        }

- 新增 ```pages/index.vue``` 文件

		<template>
		  <section>
		    网站主页
		  </section>
		</template>
		
		<script>
		export default {}
		</script>

- 在根目录下新增 ```plugins``` ```layouts``` ```pages``` 目录

## 安装已使用的插件

		npm install --save axios echarts element-ui vue vue-baidu-map vuex normalize.css
	
## 迁移相关旧文件

- 复制 ```api``` ```assets``` ```plugins``` ```utils``` 目录到新项目
- 复制 ```vue.config.js``` 文件到根目录
- 根据原项目 ```main.js``` 文件进行新的配置

### element-ui

在 ```plugins``` 目录下增加 ```element-ui.js``` 文件：

	import Vue from 'vue'
	import Element from 'element-ui'
	
	export default () => {
	  Vue.use(Element)
	}

在 ```nuxt.config.js``` 文件中增加配置：

	module.exports = {
	  css: [
	    'element-ui/lib/theme-chalk/index.css'
	  ],
	  plugins: [
	    '@/plugins/element-ui'
	  ]
	}

修改 ```pages/index.vue``` 文件（**号包括部分），验证 ```element-ui``` 配置是否正确
	
	<template>
	  <section>
	    网站主页
	    **
		<el-button type="primary">按钮</el-button>
		**
	  </section>
	</template>

### normalize.css

    npm install --save-dev node-sass sass-loader
 
模板 --> layout --> pages

### 路由修改
<router-view></router-view>替换为\<nuxt>\</nuxt>



