### 之前遗漏点
##### [创建一个 Vue 实例](https://cn.vuejs.org/v2/guide/instance.html#%E5%88%9B%E5%BB%BA%E4%B8%80%E4%B8%AA-Vue-%E5%AE%9E%E4%BE%8B "创建一个 Vue 实例")
- 所有的 Vue 组件都是 Vue 实例，并且接受相同的选项对象 (一些根实例特有的选项除外)。拥有相同的property和方法

##### [实例生命周期钩子](https://cn.vuejs.org/v2/guide/instance.html#%E5%AE%9E%E4%BE%8B%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E9%92%A9%E5%AD%90 "实例生命周期钩子")
- 不要在选项 property 或回调上使用[箭头函数](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Arrow_functions)，比如 `created: () => console.log(this.a)` 或 `vm.$watch('a', newValue => this.myMethod())`。因为箭头函数并没有 `this`，`this` 会作为变量一直向上级词法作用域查找，直至找到为止，经常导致 `Uncaught TypeError: Cannot read property of undefined` 或 `Uncaught TypeError: this.myMethod is not a function` 之类的错误。

##### [生命周期图示](https://cn.vuejs.org/v2/guide/instance.html#%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E5%9B%BE%E7%A4%BA "生命周期图示")
- ![[vue-lifecycle.png]]
- 最好能背诵

### 问题
##### [创建一个 Vue 实例](https://cn.vuejs.org/v2/guide/instance.html#%E5%88%9B%E5%BB%BA%E4%B8%80%E4%B8%AA-Vue-%E5%AE%9E%E4%BE%8B "创建一个 Vue 实例")
- 原文：虽然没有完全遵循MVVM 模型，但是 Vue 的设计也受到了它的启发
	问：什么是MVVM模型？
	
##### [数据与方法](https://cn.vuejs.org/v2/guide/instance.html#%E6%95%B0%E6%8D%AE%E4%B8%8E%E6%96%B9%E6%B3%95 "数据与方法")
- 原文：值得注意的是只有当实例被创建时就已经存在于 `data` 中的 property 才是**响应式**的。
	问：为什么？

- 原文：这里唯一的例外是使用 `Object.freeze()`，这会阻止修改现有的 property，也意味着响应系统无法再_追踪_变化。
	问：这个有什么用处吗？

- Vue 实例还暴露了一些有用的实例 property 与方法。它们都有前缀 `$`，以便与用户定义的 property 区分开来
	问：用户定义的 property是在data中定义的property吗？（data中定义的property可以在vm实例中直接访问，所以有此猜测）

##### [实例生命周期钩子](https://cn.vuejs.org/v2/guide/instance.html#%E5%AE%9E%E4%BE%8B%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E9%92%A9%E5%AD%90 "实例生命周期钩子")
- 原文：生命周期钩子的 `this` 上下文指向调用它的 Vue 实例。
	问：为什么？（需要到源码中找到答案）
	
##### [生命周期图示](https://cn.vuejs.org/v2/guide/instance.html#%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E5%9B%BE%E7%A4%BA "生命周期图示")
- 原文：![[vue-lifecycle.png]]
	问：
	1. vm.$mount(el)有什么用，el又是什么呢，el option？
	2. compile el's outerHTML as template中的outerHTML是什么意思？
	3. virtual DOM re-render and patch中的patch是什么意思