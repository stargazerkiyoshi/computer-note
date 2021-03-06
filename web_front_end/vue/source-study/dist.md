   具体查看 https://cn.vuejs.org/v2/guide/installation.html#Vue-Devtools
   
  |                           | UMD                | CommonJS              | ES Module (基于构建工具使用) | ES Module (直接用于浏览器) |
  | ------------------------- | ------------------ | --------------------- | ---------------------------- | -------------------------- |
  | 完整版                    | vue.js             | vue.common.js         | vue.esm.js                   | vue.esm.browser.js         |
  | 只包含运行时版            | vue.runtime.js     | vue.runtime.common.js | vue.runtime.esm.js           | -                          |
  | 完整版 (生产环境)         | vue.min.js         | -                     | -                            | vue.esm.browser.min.js     |
  | 只包含运行时版 (生产环境) | vue.runtime.min.js | -                     | -                            | -                          |
-   **完整版**：同时包含编译器和运行时的版本。
    
-   **编译器**：用来将模板字符串编译成为 JavaScript 渲染函数的代码。
    
-   **运行时**：用来创建 Vue 实例、渲染并处理虚拟 DOM 等的代码。基本上就是除去编译器的其它一切。
-   **[UMD](https://github.com/umdjs/umd)**：UMD 版本可以通过 `<script>` 标签直接用在浏览器中
-   **[CommonJS](http://wiki.commonjs.org/wiki/Modules/1.1)**：CommonJS 版本用来配合老的打包工具比如 [Browserify](http://browserify.org/) 或 [webpack 1](https://webpack.github.io/)
-   **[ES Module](http://exploringjs.com/es6/ch_modules.html)**：从 2.6 开始 Vue 会提供两个 ES Modules (ESM) 构建文件：

	-   为打包工具提供的 ESM：为诸如 [webpack 2](https://webpack.js.org/) 或 [Rollup](https://rollupjs.org/) 提供的现代打包工具。ESM 格式被设计为可以被静态分析，所以打包工具可以利用这一点来进行“tree-shaking”并将用不到的代码排除出最终的包。为这些打包工具提供的默认文件 (`pkg.module`) 是只有运行时的 ES Module 构建 (`vue.runtime.esm.js`)。

	-   为浏览器提供的 ESM (2.6+)：用于在现代浏览器中通过 `<script type="module">` 直接导入。

当使用 `vue-loader` 或 `vueify` 的时候，`*.vue` 文件内部的模板会在构建时预编译成 JavaScript。你在最终打好的包里实际上是不需要编译器的，所以只用运行时版本即可。

对于 UMD 版本来说，开发环境/生产环境模式是硬编码好的：开发环境下用未压缩的代码，生产环境下使用压缩后的代码。
CommonJS 和 ES Module 版本是用于打包工具的，因此我们不提供压缩后的版本。你需要自行将最终的包进行压缩。