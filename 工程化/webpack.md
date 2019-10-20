# webpack

- 本质上，**webpack** 是一个现代 JavaScript 应用程序的*静态模块打包工具*。当 webpack 处理应用程序时，它会在内部构建一个 [依赖图(dependency graph)](https://webpack.docschina.org/concepts/dependency-graph/)，此依赖图会映射项目所需的每个模块，并生成一个或多个 *bundle*。



### 1管理资源

- loader： 引入模块解析非js文件成可理解的文件
- 常见资源： css , image ., json ,    



```js
module: {
     rules: [
       {
         test: /\.css$/,
         use: [
           'style-loader',
           'css-loader'
         ]
       }
     ]
   }
```





### 2 管理输出

- 随着应用程序增长，并且一旦开始 [在文件名中使用 hash](https://webpack.docschina.org/guides/caching)] 并输出 [多个 bundle](https://webpack.docschina.org/guides/code-splitting)，如果继续手动管理 `index.html` 文件，就会变得困难起来。然而，通过一些插件可以使这个过程更容易管控。

#### -  HtmlWebpackPlugin：创建了一个全新的文件，所有的 bundle 会自动添加到 html 中。

#### - clear-webpack-plugin: 在每次构建前清理 `/dist` 文件夹，这样只会生成用到的文件





### 3开发环境

- mode :['development', 'production']

 

#### - 使用 source map

- 当 webpack 打包源代码时，可能会很难追踪到 error(错误) 和 warning(警告) 在源代码中的原始位置。例如，如果将三个源文件（`a.js`, `b.js` 和 `c.js`）打包到一个 bundle（`bundle.js`）中，而其中一个源文件包含一个错误，那么堆栈跟踪就会直接指向到 `bundle.js`。你可能需要准确地知道错误来自于哪个源文件，所以这种提示这通常不会提供太多帮助。

为了更容易地追踪 error 和 warning，JavaScript 提供了 [source map](http://blog.teamtreehouse.com/introduction-source-maps) 功能，可以将编译后的代码映射回原始源        代码。如果一个错误来自于 `b.js`，source map 就会明确的告诉你。



#### - 选择一个开发工具

- webpack 提供几种可选方式，帮助你在代码发生变化后自动编译代码：

1. webpack watch mode(webpack 观察模式)
2. webpack-dev-server
3. webpack-dev-middleware

多数场景中，你可能需要使用 `webpack-dev-server`





- webpack watch mode(webpack 观察模式)
- 你可以指示 webpack "watch" 依赖图中所有文件的更改。如果其中一个文件被更新，代码将被重新编译，所以你不必再去手动运行整个构建。

```
"watch": "webpack --watch",
```





- `webpack-dev-server` 

`webpack-dev-server` 为你提供了一个简单的 web server，并且具有 live reloading(实时重新加载)功能，

localhost:8080 自动寻找dist文件

devServer: {



```
devServer:{
    contentBase: './dist'
},
```









### 4 模块热替换

- 模块热替换(hot module replacement 或 HMR)是 webpack 提供的最有用的功能之一。它允许在运行时更新所有类型的模块，而无需完全刷新
- use: 此功能可以很大程度提高生产效率。我们要做的就是更新 [webpack-dev-server](https://github.com/webpack/webpack-dev-server) 配置，然后使用 webpack 内置的 HMR 插件



```js
devtool: 'inline-source-map',
    devServer: {
      contentBase: './dist',
      hot: true
    },
    plugins: [
      new CleanWebpackPlugin(['dist']),
      new HtmlWebpackPlugin({
        title: '模块热替换'
      }),
     new webpack.HotModuleReplacementPlugin()
```



```js
//配置 java 

if(module.hot){
    module.hot.accept('./accept.js' function(){
        accept();
  } ) 
} 
```







#### HMR 加载样式

- 借助于 style-loader，使用模块热替换来加载 CSS 实际上极其简单。此 loader 在幕后使用了 module.hot.accept，在 CSS 依赖模块更新之后，会将其 patch(修补) 到
- 









### 5 tree shaking

#### 1 添加一个通用模块

- *t*ree shaking 是一个术语，通常用于描述移除 JavaScript 上下文中的未引用代码(dead-code)。它依赖于 ES2015 模块语法的 [静态结构](http://exploringjs.com/es6/ch_modules.html#static-module-structure) 特性，例如 [import](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) 和 [export](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export)。这个术语和概念实际上是由 ES2015 模块打包工具 [rollup](https://github.com/rollup/rollup) 普及起来的。



```
optimization: {
   usedExports: true
 }
```



#### 2 压缩输出结果 

- 通过 `import` 和 `export` 语法，我们已经找出需要删除的“未引用代码(dead code)”，然而，不仅仅是要找出，还要在 bundle 中删除它们

```
mode: 'production'
```





### 6 生产环境

- 在*开发环境*中，我们需要：强大的 source map 和一个有着 live reloading(实时重新加载) 或 hot module replacement(热模块替换) 能力的 localhost server。
- *生产环境*目标则转移至其他方面，关注点在于压缩 bundle、更轻量的 source map、资源优化等，通过这些优化方式改善加载时间。由于要遵循逻辑分离，我们通常建议为每个环境编写**彼此独立的 webpack 配置**。
- production 环境中 是否需要 source map?







### 7 代码分离

- 入口起点：使用 [`entry`](https://webpack.docschina.org/configuration/entry-context) 配置手动地分离代码。
- 防止重复：使用 [`SplitChunksPlugin`](https://webpack.docschina.org/plugins/split-chunks-plugin/) 去重和分离 chunk。
- 动态导入：通过模块中的内联函数调用来分离代码。



#### 1 入口起点(entry points) 

这是迄今为止最简单、最直观的分离代码的方式。不过，这种方式手动配置较多，并有一些隐患，我们将会解决这些问题



- 如果入口 chunk 之间包含一些重复的模块，那些重复模块都会被引入到各个 bundle 中。
- 这种方法不够灵活，并且不能动态地将核心应用程序逻辑中的代码拆分出来。
- 造成在两个 bundle 中重复引用。通过使用 [`SplitChunksPlugin`](https://webpack.docschina.org/plugins/split-chunks-plugin/) 插件来移除重复模块。
- 

#### 2防止重复(prevent duplication) 

-  [`SplitChunksPlugin`](https://webpack.docschina.org/plugins/split-chunks-plugin/)插件可以将公共的依赖模块提取到已有的 entry chunk 中，或者提取到一个新生成的 chunk



```js
+   optimization: {
+     splitChunks: {
+       chunks: 'all'
+     }
+   }
```



#### 3动态导入(dynamic imports）

- 第一种，也是推荐选择的方式是，使用符合 [ECMAScript 提案](https://github.com/tc39/proposal-dynamic-import) 的 [`import()` 语法](https://webpack.docschina.org/api/module-methods#import-) 来实现动态导入。
- 第二种，则是 webpack 的遗留功能，使用 webpack 特定的 `require.ensure。`

```
return import(/* webpackChunkName: "lodash" */ 'lodash')
  .then( ( { default: _ }) => {})
```

- 使用 `default` 的原因是，从 webpack v4 开始，在 import CommonJS 模块时，不会再将导入模块解析为 `module.exports` 的值，而是为 CommonJS 模块创建一个 artificial namespace object(人工命名空间对象)





#### 4 预取/预加载模块(prefetch/preload module) 

- 声明 import 时，使用下面这些内置指令，可以让 webpack 输出 "resource hint(资源提示)"，来告知浏览器：
- prefetch(预取)：将来某些导航下可能需要的资源
- preload(预加载)：当前导航下可能需要资源



```
//...
import(/* webpackPrefetch: true */ 'LoginModal');
```



- 这会生成 `<link rel="prefetch" href="login-modal-chunk.js">` 并追加到页面头部，指示着浏览器在闲置时间预取 `login-modal-chunk.js` 文件。



- preload 指令有许多不同之处：
- preload chunk 会在父 chunk 加载时，以并行方式开始加载。prefetch chunk 会在父 chunk 加载结束后开始加载。
- preload chunk 具有中等优先级，并立即下载。prefetch chunk 在浏览器闲置时下载。
- preload chunk 会在父 chunk 中立即请求，用于当下时刻。prefetch chunk 会用于未来的某个时刻。
- 浏览器支持程度不同。









### 8 懒加载

- 懒加载或者按需加载，是一种很好的优化网页或应用的方式。这种方式实际上是先把你的代码在一些逻辑断点处分离开，然后在一些代码块中完成某些操作后，立即引用或即将引用另外一些新的代码块。这样加快了应用的初始加载速度，减轻了它的总体体积，因为某些代码块可能永远不会被加载.





### 9 缓存

- 1 只要 `/dist` 目录中的内容部署到 server 上，client（通常是浏览器）就能够访问网站此 server 的网站及其资源。而最后一步获取资源是比较耗费时间的，这就是为什么浏览器使用一种名为 [缓存](https://searchstorage.techtarget.com/definition/cache) 的技术。可以通过命中缓存，以降低网络流量，使网站加载速度更快，然而，如果我们在部署新版本时不更改资源的文件名，浏览器可能会认为它没有被更新，就会使用它的缓存版本。由于缓存的存在，当你需要获取新的代码时，就会显得很棘手。
- 2 此指南的重点在于通过必要的配置，以确保 webpack 编译生成的文件能够被客户端缓存，而在文件内容变化后，能够请求到新的文件。

## 



#### 1 输出文件的文件名(output filename) 

我们可以通过替换 `output.filename` 中的 [substitutions](https://webpack.docschina.org/configuration/output#output-filename) 设置，来定义输出文件的名称。webpack 提供了一种使用称为 **substitution(可替换模板字符串)** 的方式，通过带括号字符串来模板化文件名。其中，`[contenthash]` substitution 将根据资源内容创建出唯一 hash。当资源内容发生变化时，`[contenthash]` 也会发生变化。

这里使用 [起步](https://webpack.docschina.org/guides/getting-started) 中的示例和 [管理输出](https://webpack.docschina.org/guides/output-management) 中的 `plugins` 插件来作为项目基础，所以我们依然不必手动地维护 `index.html` 文件



```
output: {
+     filename: '[name].[contenthash].js',
      path: path.resolve(__dirname, 'dist')
    }
```





#### 2 提取引导模板(extracting boilerplate) 

正如我们在 [代码分离](https://webpack.docschina.org/guides/code-splitting) 中所学到的，[`SplitChunksPlugin`](https://webpack.docschina.org/plugins/split-chunks-plugin/) 可以用于将模块分离到单独的 bundle 中。webpack 还提供了一个优化功能，可使用 [`optimization.runtimeChunk`](https://webpack.docschina.org/configuration/optimization/#optimization-runtimechunk) 选项将 runtime 代码拆分为一个单独的 chunk。将其设置为 `single` 来为所有 chunk 创建一个 runtime bundle：



```js
optimization: {
+     runtimeChunk: 'single'
+   }
```