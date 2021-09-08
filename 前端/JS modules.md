将js程序拆分为可按需导入的单独模块的机制
提供该能力的一些库和框架：nodejs，commonjs，基于AMD的RequestJS、webpack、babel

### 好处
- 避免命名冲突
- 更好的分离，按需加载
- 更高复用性
- 更高维护性

### 历史
1. 全局模式
```javascript
function foo() {}
```
2. namespace 命名空间模式
```javascript
var obj = {
	foo: function() {
	}
}
```
3. IIFE模式：匿名函数自调用（闭包）
```javascript
(function () {
	var msg = "hello"
	function foo () {
		console.log('foo', msg)
	}
	window.module = { foo } // 向外暴露
})()

// 引入依赖
(function (window) {
	var msg = "hello"
	function foo () {
		console.log('foo', msg)
	}
	window.module = { foo } // 向外暴露
})(window) // 传参进去

```

### 模块化规范
下列都是规范，具体的实现可能有多种
- CommonJS
- AMD
- CMD(用的少)
- ES6

### commonjs
1. 每个文件可作为一个模块
2. 服务器端：模块加载是运行时同步加载的
3. 浏览器端：模块需要提前编译打包处理

```javascript
// 暴露模块
module.exports = value
exports.xxx = value
// 引入模块
require(xxx)
```
实现：
1. 服务器端：[[nodejs#模块化]]、[[nodejs#模块种类]]、[[nodejs#模块其实只是函数]]
2. 浏览器端：browserify，使用参见官网


### AMD
专门用于浏览器端，模块加载是异步的
基本语法：
```javascript
//定义没有依赖的模块
// module1.js
define(function() {
	let name = 'module1'
	function getName() {
	}
	return {
		getName
	}
})

//定义有依赖的模块
// module2.js
define(['module1'], function(module1) {
	function showMsg () {
		console.log(module1.getName())
	}
	return {
		showMsg
	}
})

//main.js
(function() {
	requirejs.config({
		// baseUrl: 'js', //基本路径、出发点在根目录下
		// 配置模块名称与对应文件的路径之间的映射关系
		paths: {
			module1: './module1.js'
			module2: './module2.js'
		}
	})
	requirejs(['module2'], function(module2) {
		module2.showMsg
	})
})
```
```html
	<!-- 引入require.js 并制定js主文件的入口 -->
	<script data-main="js/main.js" src="js/lib/require.js"></script>
```
### CMD
- 专门用于浏览器
- 加载是异步的
- 按需加载
```javascript
// html中引入sea.js

// 
define(function(require, exports, module)) {
	// 同步引入
	var module1 = require('./module1')
	// 异步引入
	require.async('./module2', function(module2) {
		console.log(module2)
	})
	function foo () {
	}
	module.exports = {
		name: '111'
		foo
	}
	exports.foo = foo
}
```
### ES6
- 依赖模块需要编译打包处理

### 浏览器原生支持
[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Modules)
#### 基本的示例文件的结构
	index.html
	main.mjs
	modules/
    	canvas.mjs
    	square.mjs
### `.mjs` 与 `.js`
使用扩展名`.mjs`的好处：
-   比较清晰，这可以指出哪些文件是模块，哪些是常规的 JavaScript。
-   这能保证你的模块可以被运行时环境和构建工具识别，比如 Node.js和Babel
继续使用 `.js` 扩展名的理由
- 确保服务器能够正常地处理 `Content-Type` 头，其应该包含 Javascript 的MIME 类型 `text/javascript`，否则可能会得到 一个严格 MIME 类型检查错误：“The server responded with a non-JavaScript MIME type （服务器返回了非 JavaScript MIME 类型）”，并且浏览器会拒绝执行相应的 JavaScript 代码
- 需要服务器支持。多数服务器可以正确处理`.js`文件类型，但`.mjs`还不行
- 为了学习和保证代码的可移植的目的，建议使用 `.js`

已经可以正常响应 `.mjs` 的服务器有 GitHub 页面 和 Node.js 的 `http-server`。

解决两者的问题，开发中使用`.mjs`，构建中转换为`.js`

### 导出模块的变量
```javascript
	export const name = '111'
	export function foo() {}
	export { name, foo }
```

### 导入某个模块的变量到js中
```javascript 
	import { name, draw, reportArea, reportPerimeter } from '/js-examples/modules/basic-modules/modules/square.mjs';
```
- 模块文件的路径是相对于站点根目录的相对路径
- 或者用点语法意味 “当前路径”
```javascript
./modules/square.mjs
```

### 应用模块到HTML中
```html
<script type="module" src="main.mjs"></script>
```
- 该标签应该作为顶级：导入模块功能的脚本基本是作为顶级模块，显然：否则有可能找不到某些导出的变量
- 只能在模块内部使用 `import` 和`export` 语句 ；不能是普通脚本文件

### 其他 模块与标准脚本的不同
-   你需要注意本地测试 —  如果你通过本地加载Html 文件 (比如一个 `file://` 路径的文件), 你将会遇到 CORS 错误，因为Javascript 模块安全性需要。你需要通过一个服务器来测试。
-   另请注意，您可能会从模块内部定义的脚本部分获得不同的行为，而不是标准脚本。 这是因为模块自动使用严格模式。
-   加载一个模块脚本时不需要使用 `defer` 属性， 模块会自动延迟加载。
-   最后一个但不是不重要，你需要明白模块功能导入到单独的脚本文件的范围 — 他们无法在全局获得。因此，你只能在导入这些功能的脚本文件中使用他们，你也无法通过Javascript console 中获取到他们，比如，在DevTools 中你仍然能够获取到语法错误，但是你可能无法像你想的那样使用一些debug 技术
### 默认导出导入
```javascript
export default randomSquare
export default function(ctx) {
  ...
}
import randomSquare from './modules/square.mjs'
import {default as randomSquare} from './modules/square.mjs'
```

### 使用`as`重命名导入变量

### 模块整体导入作为一个对象
```javascript
import * as Module from '/modules/module.mjs';

//使用
Module.function1()
Module.function2()
```

### 导入导出类
和普通变量一样

### 导入并导出
```javascript
export { Square } from '/js-examples/modules/module-aggregation/modules/shapes/square.mjs';
export { Triangle } from '/js-examples/modules/module-aggregation/modules/shapes/triangle.mjs';
export { Circle } from '/js-examples/modules/module-aggregation/modules/shapes/circle.mjs';
```

### 动态加载模块
仅在需要时动态加载模块，而不必预先加载所有模块
```javascript
import('/modules/myModule.mjs')
  .then((module) => {
    // Do something with the module.
  });
```

### 注意
-   在前面已经提到了，在这里再重申一次： `.mjs` 后缀的文件需要以 MIME-type 为 `javascript/esm` 来加载(或者其他的JavaScript 兼容的 MIME-type ，比如 `application/javascript`), 否则，你会一个严格的 MIME 类型检查错误，像是这样的 "The server responded with a non-JavaScript MIME type".
-   如果你尝试用本地文件加载HTML 文件 (i.e. with a `file://` URL), 由于JavaScript 模块的安全性要求，你会遇到CORS 错误。你需要通过服务器来做你的测试。GitHub pages is ideal as it also serves `.mjs` files with the correct MIME type.
-   因为`.mjs` 是一个相当新的文件后缀, 一些操作系统可能无法识别，或者尝试把它替换成别的。比如，我们发现macOS悄悄地该我们的 `.mjs` 后缀的文件后面添加上 `.js`  然后自动隐藏这个后缀。所以我们的文件实际上都是 `x.mjs.js`. 当我们关闭自动隐藏文件后缀名，让它去接受认可 `.mjs`。问题解决。