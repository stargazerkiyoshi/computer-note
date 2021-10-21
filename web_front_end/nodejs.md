### 概述
传统服务器是来一个请求分配一个线程去处理，IO线程操作的时候，该线程需要等待，即**阻塞**，100个线程就有100个在等待，IO阻塞

单线程，所以服务器硬件配置更低

在浏览器和服务器之间架一层nodejs的服务器，可以快速处理页面渲染


### ECMAScript5标准的缺陷
- 没有模块系统
- 标准库较少
- 没有标准接口
- 缺乏包管理系统（插件等）

### 模块化
commonjs规范：弥补ES5中没有模块系统，希望可以任意地方使用
- 一个js文件就是一个模块
- 每个js文件的代码都是独立运行在一个函数中，而不是在全局作用域
- 所以需要向外暴露变量和方法
```javascript
// 引入
var md = require('./xxx.js') 

// 暴露
exports.x = 2
exports.fn = function () {} 
```

### 模块种类
- 核心模块
	1. nodejs引擎提供的模块
	2. 标识就是模块的名字，引入可以只用标识，不需要路径
- 文件模块
	1. 用户自己创建的模块
	2. 标识是文件的路径（绝对路径、相对路径）

### 全局对象global
和网页中的window对象类似

### 模块其实只是函数
```javascript
// xxx.js
console.log(arguments) // 全局里没有这个变量，只有函数里才有
console.log(arguments.length)
console.log(arguments.callee) // 保存当前执行的函数对象
console.log(arguments.callee + '') // 打印出当前模块的函数

// 其实一个模块内的代码是被这样的函数包裹着
function (exports, require, module, __fileName, __dirname) {
	require('./xxx.js') // 所以require可以直接使用
	
	exports.x = 1 // 所以exports可以直接使用
	
	console.log(module.exports) // 同下，本质是一样的
	console.log(exports)
}

// 可以等于一个对象, exports不可以只能.的形式
module.exports = {
}
exports = module.exports // 引用类型，
module.exports = {name: 'bbb'} // exports 跟着变为{name: 'bbb'}
exports.name = 'aaa' // module.exports 跟着变为{name: 'aaa'}
exports = {} // 引用改变，module.exports 不会变

```
包裹模块的函数的参数：
- exports：对象，用来将变量和函数暴露到外部
- require：函数，用来引入外部的模块
- module：代表当前模块本身，exports是module的属性
- __filename：当前模块的完整路径
- __dirname： 当前模块文件夹的完整路径

### 包
- 实际是一个压缩文件，解压缩后是文件目录
- 将一组相关的模块组合到一起，形成一组完整的工具
- 由包结构和包描述文件组成
	1. 包结构：组织包中的各种文件
		- **package.json 即：包描述文件，必须**
		- bin 可执行二进制文件
		- lib js代码
		- doc 文档
		- test 单元测试
	2. **包描述文件**：描述包的非代码相关信息，供外部读取分析，在包的根目录下，json文件
		```json
		{
		}
		```
### NPM（Node Package Manager）
- commonjs包规范是理论，npm是一种实现
- 帮助第三方模块的发布、安装、依赖
- 下载依赖的依赖等等
```sh
npm init //初始化一个文件夹为包：得到package.json, node_modules
npm install math

npm -v // 版本
npm search //搜索包
npm install / i packageName
npm remove / r
npm install packageName --save //安装并添加到依赖package.json中的dependencies 通过它可以下载所有依赖
npm install //下载当前包所依赖的所有包
npm install packageName -g // 全局安装,一般都是工具

```
cnpm使用，自行百度

### node搜索包的流程
- npm 下载的包，直接通过包名引入，首先在当前目录的node_modules中寻找是否有该模块，如果没有递归寻找上一层目录，直到根目录

### buffer(缓冲区)
- 和数组的结构操作方法类似
- 数组中不能存储二进制文件，buffer是用来专门存储二进制数据的
- 使用buffer不需要引入模块直接引入即可
- buffer中存储的都是二进制数据，每一个元素的范围是00 - ff，占用内存一个字节
- buffer大小不能修改类似c语言，直接操作内存，对底层的连续内存的分配

```javascript
str = 'hello world'
var buf = Buffer.from(str)
console.log(buf)
console.log(buf.length) // 占用的内存大小
console.log(str.length) // 字符串的长度

// 不推荐
// var buf2 = new Buffer(10) //10字节的buffer
// 推荐
var buf2 = Buffer.alloc(10)
// 索引操作
buf2[0] = 88
buf2[1] = 0xaa
buf2[10] = 15 // 无效
buf2[3] = 257 // 2
console.log(buf[1]) // 打印出来的是10进制
console.log(buf[1].toString(16))

// 创建一个指定大小的buffer，可能含有敏感数据(未初始化的，可能有以前使用的数据)
var buf3 = Buffer.allocUnsafe(10) 

console.log(buf.toString()) //二进制buffer转换为string
```

### 文件系统（fs）
- 通过nodejs操作系统中的文件
- 使用前先引入
- 分为同步和异步API调用，同步会阻塞程序执行，异步不会

1. 文件读取
```javascript
var fs = require('fs')

// 同步文件写入：
// 1：打开文件  
var fd = fs.openSync('hello.ts', 'w') //参数： file: 文件路径, flags:'r' 'w' (打开状态：'r', 'w', 'a'追加), mode:设置文件操作权限
2.向文件中写入文内容
fs.writeSync(fd, 'hello world') //参数：fd:文件描述符，string 要写入的内容，position?:写入的起始位置，encode?：文件编码，默认UTF-8
3.保存并关闭文件
fs.closeSync(fd) //参数：fd：文件描述符

// 异步文件写入，性能好点
var f
//1.打开文件
//参数：path，flags[,model], callback
fs.open('hello2.txt', 'w', function(err, fd) {
	// 判断是否出错
	if(!err) {
		console.log(fd)
		f = fd 
		
		fd.write(fd, '异步写入文件的内容', function(err) {
			if (!err) {
				console.log('写入成功')
			}
			fs.close(fd, function(err) {
				if (!err) {
					console.log('关闭成功')
				}
			})
		})
	}else {
		console.log(err)
	}
}) 
console.log(f) // undefined

// 简单文件写入（推荐）
fs.writeFile('hello3.txt', '通过writeFile写入', function (err) {
	if (!err) {
		console.log('写入成功')
	}
}) // 参数：file, data[, options], callback
fs.writeFileSync() // 参数：file，data[, options]

// 流式文件写入（一点一点不停写入）
// 同步异步简单文件的写入不适合大文件的写入，性能较差，容易导致内存溢出
// 1.创建一个可写流
var ws = fs.createWriteStream("hello4.txt")

/*
	on:绑定一个事件，参数：事件描述符，回调函数
	
	once:绑定一个一次性的事件
*/
// 监听流的打开和关闭
ws.on("open", function () {
	console.log('流打开了')
})
ws.once("close", function () {
	console.log('流打开了')
})
// 通过ws向文件输入内容
ws.write('通过流写入')
ws.write('通过流写入')
ws.write('通过流写入')

// ws.close() // 不能直接关闭流
ws.end() // 流结束传递，此时流不一定关闭，可能继续在传递数据，当所有数据传输完毕后，自动关闭

```
2.文件读取
```javascript
var fs = require("fs")
// 1.同步文件读取
// 2.异步文件读取
// 3.简单文件读取
// 参数：path: 文件路径，[, options]: 读取的选项，callback：回调函数
fs.readFile("a.text", function(err, data) {
	// data是buffer
	if(!err) {
		console.log(data)
		fs.writeFile('hello.text', data, function(err) {
			if (!err) {
				console.log('写入成功')
			}
		})
	}
}) 
// 4.流式文件读取
// 也适用大文件，多次将文件读取到内存中
// 创建一个可读流
var rs = fs.createReadStream("an.jpg")
var ws = fs.createWriteStream('b.jpg') //创建一个
// 监听流的开启和关闭
rs.once('open', function() {
	console.log('可读流打开了')
})
rs.once('close', function() {
	console.log('可读流关闭了')
	ws.close()
})
ws.once('open', function() {
	console.log('可读流打开了')
})
ws.once('close', function() {
	console.log('可读流关闭了')
})
// 读取可读流中的数据：必须绑定一个data事件，绑定完毕后，它会自动打开，自动读取，自动关闭
rs.on('data', function(data) {
	// data是读到的数据
	console.log(data)
	console.log(data.length)
	ws.write(data)
})

//将可读流中的内容直接输入到可写流，他会自动打开和关闭rs和ws、传递数据
rs.pipe(ws)
```

3. 其他方法
```javascript
var fs = require('fs')

//检查文件是否存在
var isExist = fs.existsSync('a.mp3')
console.log(isExist)

// 获取文件状态，返回一个相关信息的状态的对象
fs.stat('a.mp3', function(err, stat) {
	if (!err) {
		console.log(stat)
		console.log(stat.size)
		console.log(stat.isDirectory())
	}
})
// 删除文件
fs.unlink()
fs.unlinkSync()
// 读取目录的目录结构
fs.readdir('.', function (err, files) {
	if (!err) {
		console.log(files) // 字符串数组，元素是一个文件夹或一个文件的名字
	}
})
fs.readdirSync()

// 截断文件, 修改文件为指定大小
fs.truncateSync(path, len, callback)
fs.truncateSync(path, len)

// 创建一个文件夹
fs.mkdirSync('hello')
// 删除文件夹
fs.rmdirSync('hello')
// 重命名文件, 可以剪切
fs.rename(oldPath, newPath, callback)
fs.renameSync()

// 监视文件的修改
// 参数：filename, options, listner：监听器，发生变化了会执行
// 原理：定时器监视文件是否变化，options可以更改这个定时时间
fs.watchFile('hello2.txt', function(curr, prev) {
	console.log("文件被修改了")
	console.log(curr) //当前文件的状态，stat对象
	console.log(prev) //修改前文件的状态，stat对象
})

```