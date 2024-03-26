## 概念
本质上，webpack 是一个用于现代 JavaScript 应用程序的 静态模块打包工具。
当 webpack 处理应用程序时，它会在内部从一个或多个入口点构建一个 依赖图(dependency graph)，然后将你项目中所需的每一个模块组合成一个或多个 bundles，它们均为静态资源，用于展示你的内容。

### 入口
入口起点(entry point) 指示 webpack 应该使用哪个模块，来作为构建其内部 依赖图(dependency graph) 的开始。
进入入口起点后，webpack 会找出有哪些模块和库是入口起点（直接和间接）依赖的。

### 输出
output 属性告诉 webpack 在哪里输出它所创建的 bundle，以及如何命名这些文件。主要输出文件的默认值是 ./dist/main.js，其他生成文件默认放置在 ./dist 文件夹中。

在更高层面，在 webpack 的配置中，loader 有两个属性：
- test 属性，识别出哪些文件会被转换。
- use 属性，定义出在进行转换时，应该使用哪个 loader。

### loader
webpack 只能理解 JavaScript 和 JSON 文件，这是 webpack 开箱可用的自带能力。loader 让 webpack 能够去处理其他类型的文件，并将它们转换为有效 模块，以供应用程序使用，以及被添加到依赖图中。

### 插件
loader 用于转换某些类型的模块，而插件则可以用于执行范围更广的任务。包括：打包优化，资源管理，注入环境变量。

想要使用一个插件，你只需要 require() 它，然后把它添加到 plugins 数组中。
多数插件可以通过选项(option)自定义。你也可以在一个配置文件中因为不同目的而多次使用同一个插件，这时需要通过使用 new 操作符来创建一个插件实例。

### 模式
通过选择 development, production 或 none 之中的一个，来设置 mode 参数，你可以启用 webpack 内置在相应环境下的优化。其默认值为 production。

### 浏览器兼容性
Webpack 支持所有符合 ES5 标准 的浏览器（不支持 IE8 及以下版本）。
webpack 的 import() 和 require.ensure() 需要 Promise。
如果你想要支持旧版本浏览器，在使用这些表达式之前，还需要 提前加载 polyfill。

### 环境 
Webpack 5 运行于 Node.js v10.13.0+ 的版本。

## 更多参考
![1709712826506](image/webpack_note/1709712826506.png)

## 解决了什么
在普通的项目依赖中，`script` 标签之间存在隐式依赖关系。
在 index.js 文件执行之前，还需要在页面中先引入 lodash。
index.js 并未显式声明需要 lodash，而是假定推测已经存在一个全局变量 _。
使用这种方式管理 JavaScript 项目存在一些问题：
- 无法直接体现脚本的执行依赖于外部库。
- 如果依赖不存在，或者引入顺序错误，应用程序将无法正常运行。
- 如果依赖被引入但是并没有使用，浏览器将被迫下载无用代码。

为解决此问题，在显示依赖环境下，webpack 能够利用模块生命的依赖信息构建依赖图，然后使用图生成一个优化过的 bundle，并能保证以正确顺序执行。

webpack 会将代码(import, export)转译以便可以在旧版本浏览器中执行。
如果正在使用其它 ES2015 特性，请确保 webpack 的 loader 系统 中使用了像 Babel 一样的 转译器。
webpack 可以 动态打包 所有依赖并创建所谓的 依赖图。

## Loaders
webpack 可以通过 loader 或内置的 资源模块 引入任何其他类型的文件：
- CSS
- 图像
- 字体
- 数据
- ...
module loader 可以链式调用。链中的每个 loader 都将对资源进行转换，不过链会逆序执行。第一个 loader 将其结果（被转换后的资源）传递给下一个 loader，依此类推。最后，webpack 期望链中的最后的 loader 返回 JavaScript。


### loader 分类
Webpack 支持使用 loader 对文件进行预处理。你可以构建包括 JavaScript 在内的任何静态资源。并且可以使用 Node.js 轻松编写自己的 loader。
#### 文件类型
- val-loader 将代码作为模块执行，并将其导出为 JS 代码
- ref-loader 用于手动建立文件之间的依赖关系
#### JSON类型
- cson-loader 加载并转换 CSON 文件
#### 语法转换
- babel-loader 使用 Babel 加载 ES2015+ 代码并将其转换为 ES5
- esbuild-loader 加载 ES2015+ 代码并使用 esbuild 转译到 ES6+
- buble-loader 使用 Bublé 加载 ES2015+ 代码并将其转换为 ES5
- traceur-loader 使用 Traceur 加载 ES2015+ 代码并将其转换为 ES5
- ts-loader 像加载 JavaScript 一样加载 TypeScript 2.0+
- coffee-loader 像加载 JavaScript 一样加载 CoffeeScript
- fengari-loader 使用 fengari 加载 Lua 代码
- elm-webpack-loader 像加载 JavaScript 一样加载 Elm

#### 模板
- html-loader 将 HTML 导出为字符串，需要传入静态资源的引用路径
- pug-loader 加载 Pug 和 Jade 模板并返回一个函数
- markdown-loader 将 Markdown 编译为 HTML
- react-markdown-loader 使用 markdown-parse 解析器将 Markdown 编译为 React 组件
- posthtml-loader 使用 PostHTML 加载并转换 HTML 文件
- handlebars-loader 将 Handlebars 文件编译为 HTML
- markup-inline-loader 将 SVG/MathML 文件内嵌到 HTML 中。在将图标字体或 CSS 动画应用于 SVG 时，此功能非常实用。
- twig-loader 编译 Twig 模板并返回一个函数
- remark-loader 通过 remark 加载 markdown，且支持解析内容中的图片

#### 样式
- style-loader 将模块导出的内容作为样式并添加到 DOM 中
- css-loader 加载 CSS 文件并解析 import 的 CSS 文件，最终返回 CSS 代码
- less-loader 加载并编译 LESS 文件
- sass-loader 加载并编译 SASS/SCSS 文件
- postcss-loader 使用 PostCSS 加载并转换 CSS/SSS 文件
- stylus-loader 加载并编译 Stylus 文件

#### 框架
- vue-loader 加载并编译 Vue 组件
- angular2-template-loader 加载并编译 Angular 组件

## plugin
HtmlWebpackPlugin

## 代码分离
- 使用 `SplitChunksPlugin ` 插件
- 动态导入

### 预获取/预加载模块
- 预获取（prefetch）：将来某些导航下可能需要的资源
- 预加载（preload）：当前导航下可能需要资源
与预获取指令相比，预加载指令有许多不同之处：
- 预加载 chunk 会在父 chunk 加载时以并行方式开始加载；而预获取 chunk 会在父 chunk 加载结束后开始加载。
- 预加载 chunk 具有中等优先级，并会立即下载；而预获取 chunk 则在浏览器闲置时下载。
- 预加载 chunk 会在父 chunk 中立即请求，用于当下时刻；而预获取 chunk 则用于未来的某个时刻。
- 浏览器支持程度不同。
## 缓存
## 模块热更新
## source-map
## Tree Shaking
## 懒加载


## 面试
### webpack中 loader 与 plugin 有什么区别
### 有没有写过 plugin
### 我们写代码的时候是 es6，浏览器运行的时候是 es5，在做代码转化的时候，webpack 帮我们做了什么事情