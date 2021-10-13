原文：https://www.ruanyifeng.com/blog/2020/08/how-nodejs-use-es6-module.html

#### 方法
- nodejs中：
`.mjs`文件总是以 ES6 模块加载，`.cjs`文件总是以 CommonJS 模块加载，`.js`文件的加载取决于`package.json`里面`type`字段的设置。
即：
```
	"type": "module"
```

- 尽量不要混用
`require`不能加载`.mjs`文件，`.mjs`文件也不能`require` `.cjs`文件

#### commonjs加载es6模块
```javascript
(async () => {
  await import('./my-app.mjs');
})();
```
`require()`不支持 ES6 模块的一个原因是，它是同步加载，而 ES6 模块内部可以使用顶层`await`命令，导致无法被同步加载。（why?）

#### es6模块加载commonjs模块
ES6 模块的`import`命令可以加载 CommonJS 模块，但是只能整体加载，不能只加载单一的输出项。
```javascript
// 正确
import packageMain from 'commonjs-package';

// 报错
import { method } from 'commonjs-package';
```
因为 ES6 模块需要支持静态代码分析，而 CommonJS 模块的输出接口是`module.exports`，是一个对象，无法被静态分析，所以只能整体加载。(why?)

#### 同时支持两种引用方式的模块
1、原始文件为`es6`模块，需要支持`commonjs`用`import()`的方式调用
	```
		export default {}
	```
2、原始文件为`commonjs`，需要支持`es6` 模块
	`import` 一个大对象，然后分开`export`出具体接口
	```
		import cjsModule from '../index.js';
		export const foo = cjsModule.foo;
	```
3、
	在`package.json`文件的`exports`字段，指明两种格式模块各自的加载入口。
	```javascript
		"exports"：{ 
			"require": "./index.js"，
			"import": "./esm/wrapper.js" 
		}
	```
	指定`require()`和`import`，加载该模块会自动切换到不一样的入口文件。


