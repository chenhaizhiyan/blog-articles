# eslint学习笔记

## 部分常用eslint的npm包

### eslint

eslint是一个用于识别和报告ecmascript/javascript代码中的模式的工具。在许多方面，它类似于jslint和jshint，但也有一些例外。

- eslint使用estree进行javascript解析
- eslint使用ast来计算代码中的模式
- eslint是完全可插入的，每个规则都是一个插件，您可以在运行时添加更多规则。

### eslint-config-prettier

关闭所有不必要或可能与更漂亮的规则冲突的规则
这允许您使用您最喜欢的可共享配置，而不让它的风格选择妨碍使用更漂亮的配置。
请注意，此配置只转换的规则，因此与其他配置一起使用它是有意义的。

### eslint-config-standard

此模块适用于高级用户。你可能想用标准代替。

### eslint-plugin-html 

这个eslint插件允许linting和修复HTML文件中包含的内联脚本。

### eslint-plugin-import

此插件旨在支持ES2015+（ES6+）导入/导出语法的linting，并防止出现文件路径和导入名称拼写错误的问题。ES2015+Stat模块语法提供的所有好处都在编辑器中标记出来了。

### eslint-plugin-node

为node.js增加eslint的规则

### eslint-plugin-prettier

作为eslint规则运行更漂亮，并将差异报告为单个eslint问题

### eslint-plugin-promise

javascript的promises最佳实践

### eslint-plugin-standard

eslint 标准规则

### eslint-plugin-vue

vue官方eslint插件