## 什么是qiankun
qiankun 是一个基于 single-spa 的微前端实现库，旨在帮助大家能更简单、无痛的构建一个生产可用微前端架构系统。
## 什么是微前端
微前端是一种多个团队通过独立发布功能的方式来共同构建现代化 web 应用的技术手段及方法策略。
微前端架构具备以下几个核心价值：
- 技术栈无关
主框架不限制接入应用的技术栈，微应用具备完全自主权
- 独立开发、独立部署
微应用仓库独立，前后端可独立开发，部署完成后主框架自动完成同步更新
- 增量升级
在面对各种复杂场景时，我们通常很难对一个已经存在的系统做全量的技术栈升级或重构，而微前端是一种非常好的实施渐进式重构的手段和策略
- 独立运行时
每个微应用之间状态隔离，运行时状态不共享

__微前端架构旨在解决单体应用在一个相对长的时间跨度下，由于参与的人员、团队的增多、变迁，从一个普通应用演变成一个巨石应用(Frontend Monolith)后，随之而来的应用不可维护的问题。这类问题在企业级 Web 应用中尤其常见。__

## qiankun 的核心设计理念
- 🥄 简单
由于主应用微应用都能做到技术栈无关，qiankun 对于用户而言只是一个类似 jQuery 的库，你需要调用几个 qiankun 的 API 即可完成应用的微前端改造。同时由于 qiankun 的 HTML entry 及沙箱的设计，使得微应用的接入像使用 iframe 一样简单。
- 🍡 解耦/技术栈无关
微前端的核心目标是将巨石应用拆解成若干可以自治的松耦合微应用，而 qiankun 的诸多设计均是秉持这一原则，如 HTML entry、沙箱、应用间通信等。这样才能确保微应用真正具备 独立开发、独立运行 的能力。

## Why Not Iframe
iframe 最大的特性就是提供了浏览器原生的硬隔离方案，不论是样式隔离、js 隔离这类问题统统都能被完美解决。但他的最大问题也在于他的隔离性无法被突破，导致应用间上下文无法被共享，随之带来的开发体验、产品体验的问题。

1. url 不同步。浏览器刷新 iframe url 状态丢失、后退前进按钮无法使用。
2. UI 不同步，DOM 结构不共享。想象一下屏幕右下角 1/4 的 iframe 里来一个带遮罩层的弹框，同时我们要求这个弹框要浏览器居中显示，还要浏览器 resize 时自动居中..
3. 全局上下文完全隔离，内存变量不共享。iframe 内外系统的通信、数据同步等需求，主应用的 cookie 要透传到根域名都不同的子应用中实现免登效果。
4. 慢。每次子应用进入都是一次浏览器上下文重建、资源重新加载的过程。

## 实战
__以react微应用为例__
### 主应用
主应用路由：
```html
<body>
  <div class="mainapp">
    <div class="mainapp-main">
      <!-- 侧边栏 -->
      <ul class="mainapp-sidemenu">
        <li onclick="push('/react16')">React16</li>
        <li onclick="push('/vue')">Vue</li>
      </ul>
      <!-- 子应用  -->
      <main id="subapp-container"></main>
    </div>
  </div>

  <script>
    function push(subapp) { history.pushState(null, subapp, subapp) }
  </script>
</body>
```
1. 安装qiankun
`yarn add qiankun # 或者 npm i qiankun -S`
2. 入口文件 index.js 中注册微应用并启动
```js
import { registerMicroApps, start } from 'qiankun';
// 注册微应用的基础配置信息
// 当浏览器 url 发生变化时，会自动检查每一个微应用注册的 activeRule 规则，符合规则的应用将会被自动激活
registerMicroApps(
  [
    {
      name: 'react16',
      entry: '//localhost:7100',
      container: '#subapp-viewport',
      loader,
      activeRule: '/react16',
    },
    {
      name: 'vue',
      entry: '//localhost:7101',
      container: '#subapp-viewport',
      loader,
      activeRule: '/vue',
    },
  ],
  {
    beforeLoad: [
      (app) => {
        console.log('[LifeCycle] before load %c%s', 'color: green;', app.name);
      },
    ],
    beforeMount: [
      (app) => {
        console.log('[LifeCycle] before mount %c%s', 'color: green;', app.name);
      },
    ],
    afterUnmount: [
      (app) => {
        console.log('[LifeCycle] after unmount %c%s', 'color: green;', app.name);
      },
    ],
  },
);
// 启动 qiankun
start();
```

### 微应用
#### 有 webpack 构建
主要是指 Vue、React、Angular。
1. （入口文件同级目录下）新增 public-path.js 文件，用于修改运行时的 publicPath。
    ```js
    if (window.__POWERED_BY_QIANKUN__) {
      // eslint-disable-next-line no-undef
      __webpack_public_path__ = window.__INJECTED_PUBLIC_PATH_BY_QIANKUN__;
    }
    ```
2. 微应用建议使用 history 模式的路由，需要设置路由 base，值和它的 activeRule 是一样的。
    ```js
    <BrowserRouter basename={window.__POWERED_BY_QIANKUN__ ? '/react16' : '/'}>
    ```
3. 在入口文件最顶部引入 public-path.js，修改并导出三个生命周期函数。
    ```js
    import './public-path';
    import React from 'react';
    import ReactDOM from 'react-dom';
    import App from './App';

    function render(props) {
      const { container } = props;
      ReactDOM.render(<App />, container ? container.querySelector('#root') : document.querySelector('#root'));
    }

    if (!window.__POWERED_BY_QIANKUN__) {
      render({});
    }

    // 三个生命周期函数
    export async function bootstrap() {
      console.log('[react16] react app bootstraped');
    }

    export async function mount(props) {
      console.log('[react16] props from main framework', props);
      render(props);
    }

    export async function unmount(props) {
      const { container } = props;
      ReactDOM.unmountComponentAtNode(container ? container.querySelector('#root') : document.querySelector('#root'));
    }
    ```
4. 修改 webpack 打包，允许开发环境跨域和 umd 打包。  
    1. 安装插件 ```npm i -D @rescripts/cli```
    2. 根目录新增 .rescriptsrc.js：
        ```js
        const { name } = require('./package');
        module.exports = {
          webpack: (config) => {
            config.output.library = `${name}-[name]`;
            config.output.libraryTarget = 'umd';
            // webpack 5 需要把 jsonpFunction 替换成 chunkLoadingGlobal
            config.output.jsonpFunction = `webpackJsonp_${name}`; 
            config.output.globalObject = 'window';
            return config;
          },

          devServer: (_) => {
            const config = _;
            config.headers = {
              'Access-Control-Allow-Origin': '*',
            };
            config.historyApiFallback = true;
            config.hot = false;
            config.watchContentBase = false;
            config.liveReload = false;
            return config;
          },
        };
        ```
    3. 修改 package.json：
        ```json
        -   "start": "react-scripts start",
        +   "start": "rescripts start",
        -   "build": "react-scripts build",
        +   "build": "rescripts build",
        -   "test": "react-scripts test",
        +   "test": "rescripts test",
        -   "eject": "react-scripts eject"
        ```
    
#### 无 webpack 构建项目
无 webpack 构建的微应用直接将 lifecycles 挂载到 window 上即可。

PS: 本文只做调研学习用，其他项目示例详见官网：[项目实践](https://qiankun.umijs.org/zh/guide/tutorial)。或者官网DEMO：[examples](https://github.com/umijs/qiankun)

## API
- __registerMicroApps(apps, lifeCycles?)__
注册微应用的基础配置信息。
__当浏览器 url 发生变化时，会自动检查每一个微应用注册的 activeRule 规则，符合规则的应用将会被自动激活。__

  第二个参数传入生命周期钩子(LifeCycles):
  - beforeLoad - Lifecycle | Array<Lifecycle> - 可选
  - beforeMount - Lifecycle | Array<Lifecycle> - 可选
  - afterMount - Lifecycle | Array<Lifecycle> - 可选
  - beforeUnmount - Lifecycle | Array<Lifecycle> - 可选
  - afterUnmount - Lifecycle | Array<Lifecycle> - 可选

- __setDefaultMountApp__
设置主应用启动后默认进入的微应用
- __start__
启动 qiankun
- __runAfterFirstMounted__
第一个微应用 mount 后需要调用的方法，比如开启一些监控或者埋点脚本
- __loadMicroApp__
手动加载一个微应用
- __prefetchApps__
手动预加载指定的微应用静态资源
- __addErrorHandler/removeErrorHandler__
添加/移除异常捕获
- __addGlobalUncaughtErrorHandler__
添加全局的未捕获异常处理器
- __removeGlobalUncaughtErrorHandler__
移除全局的未捕获异常处理器
- __initGlobalState__
定义全局状态，并返回通信方法，建议在主应用使用，微应用通过 props 获取通信方法

## 应用间如何通信
利用主应用内的 `initGlobalState` 方法和微应用生命周期钩子 `mount`
主应用：
```js
import { initGlobalState, MicroAppStateActions } from 'qiankun';

// 初始化 state
const actions: MicroAppStateActions = initGlobalState(state);

actions.onGlobalStateChange((state, prev) => {
  // state: 变更后的状态; prev 变更前的状态
  console.log(state, prev);
});
actions.setGlobalState(state);
actions.offGlobalStateChange();
```
微应用：
```js
// 从生命周期 mount 中获取通信方法，使用方式和 master 一致
export function mount(props) {
  props.onGlobalStateChange((state, prev) => {
    // state: 变更后的状态; prev 变更前的状态
    console.log(state, prev);
  });

  props.setGlobalState(state);
}
```

