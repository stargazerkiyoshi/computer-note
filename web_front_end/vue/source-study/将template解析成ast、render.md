1、创建一个编译器：用柯里化的方式，先传递一个基础编译函数(baseCompile)，再传递基础选项(baseOptions)，baseOption组合成finalOption，传递给baseCompile
2、baseComplie执行
3、baseCompile 中
```js
const ast = parse(template.trim(), options)
const code = generate(ast, options)
return {
	ast,
	render: code.render,
	staticRenderFns: code.staticRenderFns
}
```
4、parse中
```js
	function parse(template, options) {
		parseHTML(template, parseOption)
		return root
	}
```