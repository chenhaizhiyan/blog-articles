# vue3.0


## 回顾3.0设计目标
- 更小
- 更快
- 加强TypeScript支持
- 加强API设计一致性
- 提高自身可维护性
- 开放更多底层功能

### 如何更快？
- Object.defineProperty -> Proxy
- Virtual DOM 重构
- 更多编译时优化
	- Slot 默认编译为函数
	- MonoMorphic vnode factory
	- Compiler-generated flags for vnode/children types

### 传统vdom的性能瓶颈
- 虽然Vue的性能跟模板大小正相关，跟动态节点的数量无关。在一些组件整个模板内只有少量动态节点的情况下，这些遍历都是性能的浪费
- 根本原因：JSX和手写的render function 是完全动态的，过度的灵活性导致运行时可以用于优化的信息不足

