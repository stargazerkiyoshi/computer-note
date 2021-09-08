webpack配置中有各种路径，写法也不同容易混，特此整理

## 文件目录
![[Pasted image 20210902155422.png]]

## node path.resolve([...path])
```js
// webpack.config.js
const path = require('path')

const resolve = (pathStr) => path.resolve(__dirname, pathStr)
```

- `path.resolve()` 方法将路径或路径片段的序列解析为绝对路径。给定的路径序列从右到左处理，每个后续的 `path` 会被追加到前面，直到构建绝对路径，即碰到得到了绝对路径就返回，不论左边还有多少路径片段
```js
path.resolve('/a', '/b', 'c') // '/b/c'
path.resolve('a', 'b', 'c') // path/to/dir/a/b/c
path.resolve('/a', 'b', 'c/') // /a/b/c
path.resolve('/a', 'b', '../c') // /a/b/..c => /a/c
```
- `__dirname` 全局变量。当前模块的目录名。 与  `path.dirname(__filename)`相同。
- `__filename` 全局变量。当前模块的文件名。 这是当前模块文件的已解析符号链接的绝对路径。
- 