### 实验一：被引入文件使用es6模块，引入文件使用commonjs
#### 目录：
```
	- script
		- lib
			index.js
			package.json
		main.js
```

#### 代码
```js
// ./lib/index.js
export default {
	index: 'index'
}
```

```json
// ./lib/package.json
{
	"type": "module",
}
```

```js
// ./main.js
const lib = (async () => await import('./lib/index.js'))()
lib.then(res => {
	console.log(res)
})
```

#### 运行
```sh
node ./main.js
```

#### 输出
```sh
(node:8660) ExperimentalWarning: The ESM module loader is experimental.
[Module] { default: { index: 'index' } }
```

#### 尝试
- 如果去掉 `package.json`： 会报错 `SyntaxError: Unexpected token 'export'`
可以理解为`type: module`将`lib/index.js`定义为es6模块，此时只要将`lib/index.js`后缀名改为`.mjs`、同时`main.js`引入的后缀名也改为`.mjs`同样可以生效
- 如果`main.js`中引入方式为`import lib from './lib'` 会报错
	```sh
	(node:8788) Warning: To load an ES module, set "type": "module" in the package.json or use the .mjs extension.
	import lib from './lib'
	^^^^^^
	SyntaxError: Cannot use import statement outside a module
	```
	因为`main`不是es6模块

### 实验二：被引入文件使用es6模块，引入文件使用es6模块
#### 目录
```
	- script
		- lib
			index.js
			package.json
		main.js
+++		package.json
```

```json
// ./package.json
{
	"type": "module",
}
```

```js
// ./main.js
import lib from './lib/index.js'

console.log(lib)
```

#### 输出
```sh
(node:9234) ExperimentalWarning: The ESM module loader is experimental.
{ index: 'index' }
```

### 实验三：被引入文件使用commonjs模块，引入文件使用es6模块
```
	- script
		- lib
			index.js
		main.js
		package.json
```

```js
// ./lib/index.js
module.exports = {
	index: 'index'
}
```
```json
// package.json
{
	type: 'module'
}
```
```js
// main.js
import lib from './lib/index.js'

console.log(lib)
```
总是报错（不知道为什么）：
```sh
(node:20022) ExperimentalWarning: The ESM module loader is experimental.
...
SyntaxError: The requested module './lib/index.js' does not provide an export named 'default'
```

#### 总结
.js + package.json(type: 'module') 或 .mjs 都表示es模块