## 基础

```javascript
// 安装
npm install -g @vue/cli
// 查看版本
vue --version
// 升级
npm update -g @vue/cli
// 创建新项目
vue create hello-world
// 安装一个新插件
vue add eslint
```


## 配置

```javascript
const { defineConfig } = require('@vue/cli-service')

module.exports = defineConfig({
  /**
   * 部署应用包时的基本 URL。
   * Type: string
   */
  publicPath: '/',
  /**
   * 生产环境构建文件的目录
   * Type: string
   */
  outputDir: 'dist',
  /**
   * 放置生成的静态资源 (js、css、img、fonts) 的 (相对于 outputDir 的) 目录
   * Type: string
   */
  assetsDir: 'static',
  /**
   * 指定生成的 index.html 的输出路径 (相对于 outputDir)。也可以是一个绝对路径。
   * Type: string
   */
  indexPath: 'index.html',
  /**
   * 默认情况下，生成的静态资源在它们的文件名中包含了 hash 以便更好的控制缓存。
   * 然而，这也要求 index HTML 是被 Vue CLI 自动生成的。
   * 如果你无法使用 Vue CLI 生成的 index HTML，你可以通过将这个选项(filenameHashing)设为 false 来关闭文件名哈希。
   * Type: boolean
   */
  filenameHashing: true,
  /**
   * 在 multi-page 模式下构建应用。
   * 每个“page”应该有一个对应的 JavaScript 入口文件。
   * 其值应该是一个对象，对象的 key 是入口的名字，value 是：
   * - 一个指定了 entry, template, filename, title 和 chunks 的对象 (除了 entry 之外都是可选的)；
   * - 或一个指定其 entry 的字符串
   * Type: Object
   */
  pages: {
    index: {
      // page 的入口
      entry: 'src/index/main.js',
      // 模板来源
      template: 'public/index.html',
      // 在 dist/index.html 的输出
      filename: 'index.html',
      // 当使用 title 选项时，
      // template 中的 title 标签需要是 <title><%= htmlWebpackPlugin.options.title %></title>
      title: 'Index Page',
      // 在这个页面中包含的块，默认情况下会包含
      // 提取出来的通用 chunk 和 vendor chunk。
      chunks: ['chunk-vendors', 'chunk-common', 'index']
    },
    // 当使用只有入口的字符串格式时，
    // 模板会被推导为 `public/subpage.html`
    // 并且如果找不到的话，就回退到 `public/index.html`。
    // 输出文件名会被推导为 `subpage.html`。
    subpage: 'src/subpage/main.js'
  },
  /**
   * 是否在开发环境下通过 eslint-loader 在每次保存时 lint 代码。
   * 这个值会在 @vue/cli-plugin-eslint 被安装之后生效。
   * Type: boolean | 'warning' | 'default' | 'error'
   */
  lintOnSave: 'default',
  /**
   * 是否使用包含运行时编译器的 Vue 构建版本。
   * 设置为 true 后你就可以在 Vue 组件中使用 template 选项了，但是这会让你的应用额外增加 10kb 左右。
   * Type: boolean
   */
  runtimeCompiler: false,
  /**
   * 默认情况下 babel-loader 会忽略所有 node_modules 中的文件。
   * 你可以启用本选项，以避免构建后的代码中出现未转译的第三方依赖。
   * 不过，对所有的依赖都进行转译可能会降低构建速度。
   * 如果对构建性能有所顾虑，你可以只转译部分特定的依赖：给本选项传一个数组，列出需要转译的第三方包包名或正则表达式即可。
   * Type: boolean | Array<string | RegExp>
   */
  transpileDependencies: true,
  /**
   * 如果你不需要生产环境的 source map，可以将其设置为 false 以加速生产环境构建。
   * Type: boolean
   */
  productionSourceMap: true,
  /**
   * 设置生成的 HTML 中 <link rel="stylesheet"> 和 <script> 标签的 crossorigin 属性。
   * 需要注意的是该选项仅影响由 html-webpack-plugin 在构建时注入的标签 - 直接写在模版 (public/index.html) 中的标签不受影响。
   * Type: string
   */
  crossorigin: undefined,
  /**
   * 在生成的 HTML 中的 <link rel="stylesheet"> 和 <script> 标签上启用 Subresource Integrity (SRI)。
   * 如果你构建后的文件是部署在 CDN 上的，启用该选项可以提供额外的安全性。
   * Type: boolean
   */
  integrity: false,
  /**
   * 如果这个值是一个对象，则会通过 webpack-merge 合并到最终的配置中。
   * 如果这个值是一个函数，则会接收被解析的配置作为参数。
   * 该函数既可以修改配置并不返回任何东西，也可以返回一个被克隆或合并过的配置版本。
   * Type: Object | Function
   */
  configureWebpack: {},
  /**
   * 是一个函数，会接收一个基于 webpack-chain 的 ChainableConfig 实例。允许对内部的 webpack 配置进行更细粒度的修改。
   * Type: Function
   */
  chainWebpack: () => { },
  css: {
    /**
     * 默认情况下，只有 *.module.[ext] 结尾的文件才会被视作 CSS Modules 模块。
     * 设置为 false 后你就可以去掉文件名中的 .module 并将所有的 *.(css|scss|sass|less|styl(us)?) 文件视为 CSS Modules 模块。
     * Type: boolean
     */
    requireModuleExtension: true,
    /**
     * 是否将组件中的 CSS 提取至一个独立的 CSS 文件中 (而不是动态注入到 JavaScript 中的 inline 代码)。
     * Type: boolean | Object
     * Default: 生产环境下是 true，开发环境下是 false
     */
    extract: true,
    /**
     * 是否为 CSS 开启 source map。设置为 true 之后可能会影响构建的性能。
     * Type: boolean
     */
    sourceMap: false,
    /**
     * 向 CSS 相关的 loader 传递选项
     * Type: Object
     */
    loaderOptions: {
      css: {
        // 这里的选项会传递给 css-loader
      },
      postcss: {
        // 这里的选项会传递给 postcss-loader
      }
    },
  },
  devServer: {
    /**
     * 如果你的前端应用和后端 API 服务器没有运行在同一个主机上，你需要在开发环境下将 API 请求代理到 API 服务器。
     * 如果你想要更多的代理控制行为，也可以使用一个 path: options 成对的对象。
     * Type: string | Object
     */
    // proxy: 'http://localhost:4000',
    proxy: {
      '/api': {
        target: '<url>',
        ws: true,
        changeOrigin: true
      },
      '/foo': {
        target: '<other_url>'
      }
    }
  },
  /**
   * 是否为 Babel 或 TypeScript 使用 thread-loader。
   * 该选项在系统的 CPU 有多于一个内核时自动启用，仅作用于生产构建。
   * Type: boolean
   */
  parallel: require('os').cpus().length > 1,
  /**
   * 向 PWA 插件传递选项。
   * Type: Object
   */
  pwa: {},
  /**
   * 这是一个不进行任何 schema 验证的对象，因此它可以用来传递任何第三方插件选项。
   * Type: Object
   */
  pluginOptions: {
    foo: {
      // 插件可以作为 `options.pluginOptions.foo` 访问这些选项。
    }
  }
})
```