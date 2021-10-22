# vite 如何做到让 vue 本地开发更快速？

原贴地址https://developer.aliyun.com/article/761551

**简介：** \## vite 是什么 \[vite\](https://github.com/vuejs/vite)——一个由 vue 作者\[尤雨溪\](https://github.com/yyx990803)专门为 vue 打造的开发利器，其目的是使 vue 项目的开发更加简单和快速。   vite 究竟有什么作用？用 vite 文档上的介绍，它具有以下特点： 1. 快速的冷启动 1. 即时的热模块

## vite 是什么

[vite](https://github.com/vuejs/vite)——一个由 vue 作者[尤雨溪](https://github.com/yyx990803)专门为 vue 打造的开发利器，其目的是使 vue 项目的开发更加简单和快速。  
   
vite 究竟有什么作用？用 vite 文档上的介绍，它具有以下特点：

1.  快速的冷启动
2.  即时的热模块更新
3.  真正的按需编译

   
以上三个优点，社区也早有对应的解决方案，比如快速的冷启动可以借助各种 cli ：vue-cli、create-react-app 等等，热更新就更不用说了，不过按需编译需要开发者自行在代码中使用 `impor('xx.js')` 实现， 那么 vite 有什么特别的地方呢？用作者在微博上的原话：

> Vite，一个基于浏览器原生 ES imports 的开发服务器。利用浏览器去解析 imports，在服务器端按需编译返回，完全跳过了打包这个概念，服务器随起随用。同时不仅有 Vue 文件支持，还搞定了热更新，而且热更新的速度不会随着模块增多而变慢。针对生产环境则可以把同一份代码用 rollup 打。虽然现在还比较粗糙，但这个方向我觉得是有潜力的，做得好可以彻底解决改一行代码等半天热更新的问题。\[

\]()

   
可以看到 vite 主要特色是基于浏览器原生的 ES Module 来开发，从而实现按需编译，也就没有打包这个概念——因为需要什么资源直接在浏览器里引入即可，不过基于浏览器原生 ES module 来开发 web 应用也不是什么新鲜事，[snowpack](https://www.snowpack.dev/) 也是做这个事情，而且它可以用在所有项目上，不过目前此项目社区中没有流行的使用起来，好在 vue 在 web 开发领域有着极大的话语权，vite 的出现可以说又会让利用 ES module 开发火一阵子。  
   
有趣的是 vite 算是革了 webpack 的命了（生产环境用 rollup），所以 webpack 的开发者直接喊大哥了...  
![image.png](https://gw.alicdn.com/tfs/TB1I3vfGbY1gK0jSZTEXXXDQVXa-690-580.png "image.png")

## vite 的使用方式

同其他开发工具一样，vite 提供了用 npm 或者 yarn 一建生成项目的途径，使用 yarn 在终端执行：

```
$ yarn create vite-app <project-name>
$ cd <project-name>
$ yarn
$ yarn dev
```

即可初始化一个 vite 项目，生成的项目结构非常简洁：

```
|____node_modules
|____App.vue // vue 应用入口
|____index.html // 页面
|____package.json
```

运行 `yarn dev` 即可开发 ，打开 `package.json` 发现，如果 `dev` 只需要执行 `vite` 命令，而 `build`需要使用 `vite build`。

## 如何调试 vite

在 package.json `scripts` 值里添加一行 `debug` 命令：

```
{
  "scripts": {
    ...
         "debug": "node --inspect-brk=5858 ./node_modules/vite/dist/cli.js"
    }
}
```

这里使用 node 运行 node\_modules 里 vite 包而不是用 npm scripts 的方式，这是 node 应用的[调试方式](http://www.ruanyifeng.com/blog/2018/03/node-debugger.html)，再配置 vscode 的 launch.json ，指定 vscode [调试 npm scripts 的方式](https://code.visualstudio.com/Docs/editor/debugging)，设置端口、runtime 参数等，就可以调试起来了。

```
{
  "type": "node",
  "request": "launch",
  "name": "Launch via NPM",
  "runtimeExecutable": "npm",
  "runtimeArgs": ["run-script", "debug"],
  "port": 5858,
  "skipFiles": ["<node_internals>/**"]
}
```

由于 node\_modules 里的 vite 已经是编译后的代码了，建议 clone 一份原始的 repo 用来参考，两两结合使得执行过程更明确。

## vite 链路分析

### 命令解析

这部分代码在 [https://github.com/vuejs/vite/blob/master/src/node/cli.ts](https://github.com/vuejs/vite/blob/master/src/node/cli.ts) 里，主要内容是借助 [minimist](https://www.npmjs.com/package/minimist) —— 一个轻量级的命令解析工具解析用户命令，vite 没有使用 [commander](https://www.npmjs.com/package/commander) 这样通用的命令行解决方案，而是近乎裸写了一份，解析命令参数的函数是 `parseArgs` ，精简后的代码片段如下：

```
function parseArgs() {
    const argv = require('minimist')(process.argv.slice(2));
      // 设置 DEBUG 环境变量
    if (argv.debug) {
        process.env.DEBUG = `vite:` + (argv.debug === true ? '*' : argv.debug);
    }
    // 遍历 jsx 解析
    if (argv['jsx-factory']) {
        (argv.jsx || (argv.jsx = {})).factory = argv['jsx-factory'];
    }
    if (argv['jsx-fragment']) {
        (argv.jsx || (argv.jsx = {})).fragment = argv['jsx-fragment'];
    }
    // 解析 runServe 或者 runBuild
    if (argv._[0]) {
        argv.command = argv._[0];
    }
    return argv;
}
```

通过源码和 [README](https://github.com/vuejs/vite#jsx)，发现 vite 额外添加了对于 `jsx` 项目的支持，理论上 vite 的原理并不限制只能应用在 vue 上，作者解释因为 react 没有提供 ES module 的支持所以不能在 react 项目里使用，不过可以通过社区中的 ES module 版本的 react 代替。另外，vue3 的 jsx transform 依然在 wip 中，所以这里的支持是预留的。

拿到 argv 后，会根据 `argv.command` 的值判断是启动开发服务器或者执行生产构建命令。  
值得一提的是，在这个文件中，找到了一个 `resolveConfig` 方法，这个方法的作用是获取项目下的 **vite.config.js**，这是在为后续的更多的配置内容做准备。

### server

这部分代码在 [https://github.com/vuejs/vite/blob/master/src/node/server/index.ts](https://github.com/vuejs/vite/blob/master/src/node/server/index.ts) 里，对外暴露一个 `createServer` 方法，与常见的开发工具一样，vite 使用 koa 作 web 服务器，使用 [clmloader](https://www.npmjs.com/package/clmloader) 创建了一个监听文件改动的 watcher，同时实现了一个插件机制，以一个 context 对象，将 koa-app 和 watcher 以及其他辅助工具注入到每个 plugin 中，plugin 依次往 context 里的各部分监听事件，每个 plugin 处理不同的事情，这样的好处是职责分明，结构清晰。

context 组成如下：  
![image.png](https://gw.alicdn.com/tfs/TB12wTfGbH1gK0jSZFwXXc7aXXa-746-111.png "image.png")

#### plugin

上文我们说到 plugin，那么有哪些 plugin 呢？它们分别是：

-   用户注入 plugins —— 自定义 plugin
-   hmrPlugin —— 处理 hmr 
-   moduleRewritePlugin —— 重写 script 和 html 
-   moduleResolvePlugin —— 解析资源路径中含有 `@modules` 的模块
-   vuePlugin —— 处理 vue 单文件组件
-   esbuildPlugin —— 使用 esbuild 处理资源
-   assetPathPlugin —— 处理静态资源（js）路径
-   serveStaticPlugin —— 使用 koa-static 托管静态资源

我们来看 plugin 的实现方式，开发一个用来拦截 json 文件 plugin 可以这么实现：

```
interface ServerPluginContext {
  root: string
  app: Koa
  server: Server
  watcher: HMRWatcher
  resolver: InternalResolver
  config: ServerConfig
}

type ServerPlugin = （ctx:ServerPluginContext）=> void;

const JsonInterceptPlugin:ServerPlugin = ({app})=>{
      app.use(async (ctx, next) => {
      await next()
      if (ctx.path.endsWith('.json') && ctx.body) {
        ctx.type = 'js'
        ctx.body = `export default json`
      }
  })
}
```

vite 背后的原理都在 plugin 里，这里不再一一解释每个 plugin 的作用，会放在下文背后的原理中一并讨论。

### build

这部分代码在 [https://github.com/vuejs/vite/blob/master/src/node/build/index.ts](https://github.com/vuejs/vite/blob/master/src/node/build/index.ts) 中，build 目录的结构虽然与 server 相似，导出一个 build 方法，同样也有许多 plugin ，不过这些 plugin 与 server 中的用途不一样，因为 build 使用了 rollup ，这些 plugin 也是为 rollup 打包开发的 plugin ，本文就不再多提。

## vite 运行原理

### ES module

要了解 vite 的运行原理，首先要知道什么是 [ES module](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Modules)，目前流览器对其的支持如下：  
![image.png](https://gw.alicdn.com/tfs/TB1ExjbGoz1gK0jSZLeXXb9kVXa-561-191.png "image.png")  
可以看到主流的浏览器（IE11除外）均已经支持，其最大的特点是在浏览器端使用 export import 的方式导入和导出模块，在 script 标签里设置 `type="module"` ，然后使用 ES module。

```
<script type="module">
    import { bar } from './bar.js‘
</script>
```

当 html 里嵌入上面的 script 标签时候，浏览器会发起 http 请求，请求 htttp server 托管的 bar.js ，在 bar.js 里，我们使用 named export 导出模块：

```
// bar.js 
export const bar = 'bar';
```

### ES module 在 vite 中的作用

打开上文中启动的 vite 项目，访问 view-source 可以发现 html 里有段这样的代码：

```
<script type="module">
    import { createApp } from '/@modules/vue'
    import App from '/App.vue'
    createApp(App).mount('#app')
</script>
```

从这段代码中，我们能 get 到以下几点信息：

-   从 `http://localhost:3000/@modules/vue` 中获取 `createApp` 这个方法
-   从 [`http://localhost:3000/App.vue`](http://localhost:3000/App.vue) 中获取应用入口
-   使用 `createApp` 创建应用并挂载节点

   
`createApp` 是 vue3.0 的 api，只需知道这是创建了 vue 应用即可，vite 利用 ES module，把 “创建 vue 应用” 这个本来需要通过 webpack 打包后才能执行的代码直接放在浏览器里执行，这么做是为了：

1.  去掉打包步骤
2.  实现按需加载

#### 去掉打包步骤

去掉打包步骤非常好理解，打包的概念无非是开发者利用工具将应用各个模块集合在一起形成 bundle，以一定规则读取模块的代码——以便在不支持模块化的浏览器里使用。而且为了加载各模块，打包工具会实现胶水代码用来连接和调用模块，比如 webpack 使用 `map` 存放模块 id 和路径，使用 `__webpack_require__`  方法获取模块导出，因为浏览器支持了模块化，所以打包这一步就可以省略了。

#### 实现按需打包

前面说到，webpack 之类的打包工具会将模块提前打包进 bundle 里，但打包本身是静态的——不管某个模块的代码是否执行到，这个模块都要打包到 bundle 里。这样的坏处就是随着项目越来越大打包后的 bundle 也越来越大。

开发者为了减少 bundle 大小，会使用动态引入 `import()` 的方式异步的加载模块（ 被引入模块依然需要提前打包)，又或者使用 tree shaking 等方式尽力的去掉未引用的模块，然而这些方式都不如 vite 的优雅，vite 可以只在需要某个模块的时候动态（借助 `import()` ）的引入它，而不需要提前打包，虽然只能用在开发环境，不过这就够了！

### vite 如何处理模块

既然 vite 使用 ES module 在浏览器里使用模块，那么这一步究竟是怎么做的？  
上文提到过，ES moudle 使用模块是通过发送 http 请求实现的，所以 vite 必须提供一个静态资源服务器去代理这些模块，上文中提到的 `koa` 就是做这个事情，其通过对请求路径的分析获取资源的内容返回给浏览器，不过 vite 对于模块访问做了特殊处理。

#### @modules 是什么？

通过工程下的 index.html 和开发环境下的 html 源文件对比，发现 script 标签里的内容发生了改变，由

```
<script type="module">
    import { createApp } from 'vue'
    import App from '/App.vue'
    createApp(App).mount('#app')
</script>
```

变成了

```
<script type="module">
    import { createApp } from '/@modules/vue'
    import App from '/App.vue'
    createApp(App).mount('#app')
</script>
```

上面是 web 项目里标准的 ES module 用法，从 node\_modules 里导入一个 vue 的 npm 包，然后由 webpack 通过解析 AST 寻址拿到包的实际地址进行打包，但是在浏览器下无法直接访问 node\_modules（只能使用 http请求），所以 vite 对 `import` 都做了一层处理，其过程如下：

1.  在 koa 中间件里获取请求 body
2.  通过 [es-module-lexer](https://www.npmjs.com/package/es-module-lexer) 解析资源 ast 拿到 import 的内容
3.  判断 import 的资源是否是绝对路径，绝对视为 npm 模块
4.  返回处理后的资源路径：`"vue" => "/@modules/vue"`  
     

这部分代码在 [serverPluginModuleRewrite](https://github.com/vuejs/vite/blob/master/src/node/server/serverPluginModuleRewrite.ts) 这个 plugin 中。  
 

#### 为什么需要 @modules？

原则上这里并不需要对路径进行特殊转换，私以为这是比较巧妙的做法，把文件路径的 rewrite 都写在同一个 plugin 里，这样后续如果加入更多逻辑，改动起来不会影响其他 plugin，其他 plugin 拿到资源路径都是 '@modules' ，比如说后续可能加入 alias 的配置：就像 webpack 一样——可以将项目里的本地文件配置成绝对路径的引用。

#### 怎么返回模块内容

这部分内容相对来说就很简单了，通过下一个 koa 中间件，用正则匹配到路径上带有 ‘@modules’ 的资源，再通过 `require('xxx')` 拿到 npm 包的导出最后返回内容，在这里有个很重要的一点，那就是对 vue es 包的特殊处理，比如：

需要 @vue/runtime-dom 这个包的内容，不能直接通过 `require('`\`@vue/runtime-dom'`）得到，而需要通过` require('@vue/runtime-dom/dist/runtime-dom.esm-bundler.js'\` 的方式  
   
为什么需要特殊处理 vue es 包？前面我们提到了以往开发需要使用 webpack 之类的打包工具，而 webpack 工具链除了将模块组装到一起形成 bundle，它还可以使得不同模块规范互相引用，比如：

> -   ES module (esm) 导入cjs
> -   CommonJS (cjs) 导入esm
> -   dynamic import 导入 esm
> -   dynamic import 导入 cjs

目前大部分模块都没有设置默认导出 es module，而是导出了 cjs 的包，vue3.0 也不例外，所以这里要特殊处理一下，关于 es module 的坑可以看[这篇文章](https://zhuanlan.zhihu.com/p/40733281)。

看到这里，其实聪明的你肯定也想到了，既然 vue3.0 需要额外处理才能拿到 esm 的包内容，那么其他日常使用的 npm 包是不是也同样需要支持？答案是肯定的，但是 vite 目前还没有处理好这块，目前在 vite 项目里使用 lodash 还是会报错的。  
![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2020/png/278785/1589336979514-e1a898fa-54f6-43e1-b93b-79a7361e48a4.png#align=left&display=inline&height=47&margin=%5Bobject%20Object%5D&name=image.png&originHeight=46&originWidth=681&size=50420&status=done&style=none&width=698 "image.png")

要完全解决获取 esm 包的坑，任重而道远，这部分代码在：[serverPluginModuleResolve](https://github.com/vuejs/vite/blob/master/src/node/server/serverPluginModuleResolve.ts) 这个 plugin 中。

### vite 如何编译模块

因为 vite 为 vue3.0 开发，所以这里的编译指的也是编译 vue 单文件组件了，其他 es 模块可以直接导入内容。

#### SFC

vue [单文件组件](https://cn.vuejs.org/v2/guide/single-file-components.html)（简称 SFC） 是 vue 的一个亮点，前端届对 SFC 褒贬不一，喜欢的人非常喜欢，讨厌的人非常讨厌。个人看来，SFC 是利大于弊的，虽然 SFC 带来了额外的开发工作量，比如为了解析 template 要写模板解析器，还要在 SFC 中解析出逻辑和样式，在 vscode 里要写 vscode 插件，在 webpack 里要写 vue-loader，但是对于使用方来说可以在一个文件里可以同时写 template、js、style，省了各文件互相跳转。  
   
与 vue-loader 相似，vite 在解析 vue 文件的时候也要分别处理多次，我们打开浏览器的 network，可以看到：  
![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2020/png/278785/1589340071165-105baf6d-4d62-4570-8bb0-4c0de5a7960c.png#align=left&display=inline&height=82&margin=%5Bobject%20Object%5D&name=image.png&originHeight=73&originWidth=642&size=35047&status=done&style=none&width=721 "image.png")

一个请求 query 中什么都没有，另 2 个请求分别通过在 query 里指定了 type 为 style 和 template。

先来看看如何将一个 SFC 变成多个请求，我们从第一次请求开始分析，简化后的代码如下：

```
function vuePlugin({app}){
  app.use(async (ctx, next) => {
    if (!ctx.path.endsWith('.vue') && !ctx.vue) {
      return next()
    }

    const query = ctx.query
    // 获取文件名称
    let filename = resolver.requestToFile(publicPath)

    // 解析器解析 SFC
    const descriptor = await parseSFC(root, filename, ctx.body)
    if (!descriptor) {
      ctx.status = 404
      return
    }
    // 第一次请求 .vue
    if (!query.type) {
      if (descriptor.script && descriptor.script.src) {
        filename = await resolveSrcImport(descriptor.script, ctx, resolver)
      }
      ctx.type = 'js'
      // body 返回解析后的代码
      ctx.body = await compileSFCMain(descriptor, filename, publicPath)
    }
    
    // ...
}
```

在 compileSFCMain 中是一段长长的 generate 代码：

```
function compileSFCMain(descriptor, filePath: string, publicPath: string) {
  let code = ''
  if (descriptor.script) {
    let content = descriptor.script.content
    code += content.replace(`export default`, 'const __script =')
  } else {
    code += `const __script = {}`
  }

  if (descriptor.styles) {
    code += `\nimport { updateStyle } from "${hmrClientId}"\n`
    descriptor.styles.forEach((s, i) => {
      const styleRequest = publicPath + `?type=style&index=${i}`
      code += `\nupdateStyle("${id}-${i}", ${JSON.stringify(styleRequest)})`
    })
    if (hasScoped) {
      code += `\n__script.__scopeId = "data-v-${id}"`
    }
  }

  if (descriptor.template) {
    code += `\nimport { render as __render } from ${JSON.stringify(
      publicPath + `?type=template`
    )}`
    code += `\n__script.render = __render`
  }
  code += `\n__script.__hmrId = ${JSON.stringify(publicPath)}`
  code += `\n__script.__file = ${JSON.stringify(filePath)}`
  code += `\nexport default __script`
  return code
}
```

直接看 generate 后的代码：

```
import { updateStyle } from "/vite/hmr"
updateStyle("c44b8200-0", "/App.vue?type=style&index=0")
__script.__scopeId = "data-v-c44b8200"
import { render as __render } from "/App.vue?type=template"
__script.render = __render
__script.__hmrId = "/App.vue"
__script.__file = "/Users/muou/work/playground/vite-app/App.vue"
export default __script
```

出现了 `vite/hmr` 的导入，`vite/hmr` 具体内容我们下文再分析，从这段代码中可以看到，对于 style ，vite 使用 `updateStyle` 这个方法处理，`updateStyle` 内容非常简单，这里就不贴代码了，就做了一件事：通过创建 link 元素，设置了它的 href，href 指向带 `type='style'` 的 .vue 文件， 然后往 document 里塞入一段 css， 对于 template 直接使用 import 导入带 `type='`\`template`` `'`` 的 .vue 文件，这两种方式都会使得浏览器发起 http 请求，这样就能被 koa 中间件捕获到了，所以就形成了上文我们看到的：对一个 .vue 文件处理三次的情景。

这部分代码在：[serverPluginVue](https://github.com/vuejs/vite/blob/master/src/node/server/serverPluginVue.ts) 这个 plugin 里。

### vite 热更新

上文中出现了 vite/hmr ，这就是 vite 处理热更新的关键，在 [serverPluginHmr](https://github.com/vuejs/vite/blob/master/src/node/server/serverPluginHmr.ts) plugin 中，对于 path 等于  `vite/hmr` 做了一次判断：

```
 app.use(async (ctx, next) => {
    if (ctx.path === '/vite/hmr') {
      ctx.type = 'js'
      ctx.status = 200
      ctx.body = hmrClient
  }
 }
```

hmrClient 是 vite 热更新的客户端代码，需要在浏览器里执行，这里先来说说通用的热更新实现，热更新一般需要四个部分：

1.  通过 watcher 监听文件改动
2.  通过 server 端编译资源，并推送新资源信息给 client 。
3.  需要框架支持组件 rerender/reload 
4.  client 收到资源信息，执行框架 rerender 逻辑。

vite 也不例外同样有这四个部分，其中客户端代码在：[client.ts](https://github.com/vuejs/vite/blob/master/src/client/client.ts) 里，服务端代码在 [serverPluginHmr](https://github.com/vuejs/vite/blob/master/src/node/server/serverPluginHmr.ts) 里，对于 vue 组件的更新，通过 vue3.0 中的 `HMRRuntime` 处理的。

#### client 端

在 client 端， `WebSocket` 监听了一些更新的类型，然后分别处理，它们是：

-   **vue-reload** —— vue 组件更新：通过 import 导入新的 vue 组件，然后执行 `HMRRuntime.reload`
-   **vue-rerender** —— vue template 更新：通过 import 导入新的 template ，然后执行 `HMRRuntime.rerender`
-   **vue-style-update** —— vue style 更新：直接插入新的 stylesheet 
-   **style-update** —— css 更新：document 插入新的 stylesheet
-   **style-remove** —— css 移除：document 删除 stylesheet
-   **js-update**  —— js 更新：直接执行
-   **full-reload** —— 页面 roload：使用 `window.reload` 刷新页面

#### server 端

在 server 端，通过 watcher 监听页面改动，根据文件类型判断是 js Reload 还是 Vue Reload：

```
 watcher.on('change', async (file) => {
    const timestamp = Date.now()
    if (file.endsWith('.vue')) {
      handleVueReload(file, timestamp)
    } else if (
      file.endsWith('.module.css') ||
      !(file.endsWith('.css') || cssTransforms.some((t) => t.test(file, {})))
    ) {
      // everything except plain .css are considered HMR dependencies.
      // plain css has its own HMR logic in ./serverPluginCss.ts.
      handleJSReload(file, timestamp)
    }
  })
```

在 `handleVueReload` 方法里，会使用解析器拿到当前文件的 template/script/style ，并且与缓存里的上一次解析的结果进行比较，如果 template 发生改变就执行 `vue-rerender`，如果 style 发生改变就执行 `vue-style-update`，简化后的逻辑如下：

```
 async function handleVueReload(
        file
    timestamp,
    content
  ) {
      // 获取缓存
    const cacheEntry = vueCache.get(file）

    // 解析 vue 文件                                 
    const descriptor = await parseSFC(root, file, content)
    if (!descriptor) {
      // read failed
      return
    }
        // 拿到上一次解析结果
    const prevDescriptor = cacheEntry && cacheEntry.descriptor
    
    // 设置刷新变量
    let needReload = false // script 改变标记
    let needCssModuleReload = false // css 改变标记
    let needRerender = false // template 改变标记

       // 判断 script 是否相同
    if (!isEqual(descriptor.script, prevDescriptor.script)) {
      needReload = true
    }

     // 判断 template 是否相同
    if (!isEqual(descriptor.template, prevDescriptor.template)) {
      needRerender = true
    }
      
    // 通过 send 发送 socket
    if (needRerender){
        send({
        type: 'vue-rerender',
        path: publicPath,
        timestamp
      })    
    }
  }
```

`handleJSReload` 方法则是根据文件路径引用，判断被哪个 vue 组件所依赖，如果未找到 vue 组件依赖，则判断页面需要刷新，否则走组件更新逻辑，这里就不贴代码了。  
整体代码在 [client.ts](https://github.com/vuejs/vite/blob/master/src/client/client.ts) 和 [serverPluginHmr](https://github.com/vuejs/vite/blob/master/src/node/server/serverPluginHmr.ts) 里。

## 结语

至此，本文分析了 vite 的启动链路以及背后的部分原理，虽然在可预期的一段时间内我不会使用 vue 开发项目，但是能够看到社区中多了一种方案还是很兴奋的，这也是我写下这篇文章的原因。  
vite 才刚被创造出来不久，代码还算容易理解，现在学习它是个很好的机会。