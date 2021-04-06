# webpack打包产物分析

> 希望是隐藏在群山后的星星，探索是人生道路上倔执的旅人。——布拉赫

## 前言

近年来 Web 应用变得更加复杂与庞大，Web 前端技术的应用范围也更加广泛。  通过直接编写 JavaScript、CSS、HTML 开发 Web 应用的方式已经无法应对当前 Web 应用的发展。适逢其时，我们现在可以通过构建工具帮我们把复杂的工程转化为能运行在浏览器上的各种资源。

> 工欲善其事，必先利其器

构建其实是工程化、自动化思想在前端开发中的体现，把一系列流程用代码去实现，让代码自动化地执行这一系列复杂的流程。 历史上先后出现一系列构建工具，比如`Npm Script`、`Grunt`、`Gulp`、`Fis3`、`Webpack`等，它们各有其优缺点。

由于笔者接触`webpack`时间比较长，并且对其有一定的深入理解，下面我们来聊聊`webpack`打包出来的代码是什么。

## 运行环境

webpack@4.46.0

webpack-cli@3.3.12

node@14.16.0

## 准备工作

使用`npm`初始化一个项目，然后安装`webpack`、`webpack-cli`依赖，在根目录下新建`webpack.config.js`文件，配置如下：

``` javascript
// webpack.config.js
const path = require('path')

module.exports = {
    mode: 'development', // 使用开发模式，打包出来的代码不会被严格压缩，方便代码分析
    entry: {  // 配置入口
        bundle: './src/index.js'
    },
    output: { // 配置出口
        path: path.resolve(__dirname, 'dist'),
        filename: '[name].js'
    }
}
```

接下来我们分四种情况分析webpack打包产物

* 不引入模块
* 同步加载模块
* 异步加载模块
* 使用spiltChunks时

## 不引入模块

``` javascript
// src/index.js
console.log('hello webpack')
```

我们仅在入口文件中写入`console.log('hello webpack')`这行代码，然后在项目根目录下执行`webpack`命令，之后在`dist`目录下生成`bundle.js`。现在我们打开`bundle.js`探个究竟。

为了方便分析，我们把代码简化为以下。

``` javascript
/******/ (function(modules) { // webpackBootstrap
/******/ 	// The module cache
/******/ 	var installedModules = {};
/******/
/******/ 	// The require function
/******/ 	function __webpack_require__(moduleId) {}
/******/
/******/
/******/ 	// expose the modules object (__webpack_modules__)
/******/ 	__webpack_require__.m = modules;
/******/
/******/ 	// expose the module cache
/******/ 	__webpack_require__.c = installedModules;
/******/
/******/ 	// define getter function for harmony exports
/******/ 	__webpack_require__.d = function(exports, name, getter) {};
/******/
/******/ 	// define __esModule on exports
/******/ 	__webpack_require__.r = function(exports) {};
/******/
/******/ 	// create a fake namespace object
/******/ 	// mode & 1: value is a module id, require it
/******/ 	// mode & 2: merge all properties of value into the ns
/******/ 	// mode & 4: return value when already ns object
/******/ 	// mode & 8|1: behave like require
/******/ 	__webpack_require__.t = function(value, mode) {};
/******/
/******/ 	// getDefaultExport function for compatibility with non-harmony modules
/******/ 	__webpack_require__.n = function(module) {};
/******/
/******/ 	// Object.prototype.hasOwnProperty.call
/******/ 	__webpack_require__.o = function(object, property) { return Object.prototype.hasOwnProperty.call(object, property); };
/******/
/******/ 	// __webpack_public_path__
/******/ 	__webpack_require__.p = "";
/******/
/******/
/******/ 	// Load entry module and return exports
/******/ 	return __webpack_require__(__webpack_require__.s = "./src/index.js");
/******/ })
/************************************************************************/
/******/ ({

/***/ "./src/index.js":
/*!**********************!*\
  !*** ./src/index.js ***!
  \**********************/
/*! no static exports found */
/***/ (function(module, exports) {

eval("console.log('hello webpack')\n\n//# sourceURL=webpack:///./src/index.js?");

/***/ })

/******/ });
```

从上面打包出来的代码，我们可以知道这是一个立即执行函数，传入的参数是一个对象，即`modules`，函数体定义了`__webpack_require__`函数，最后返回了`__webpack_require__`的执行结果。

先说`modules`对象，`key`代表是模块的路径，`value`是一个函数，函数传入了两个参数，分别是`module`（存放模块信息）和`exports`（暴露的对象）。

再看一下函数体的内容，首先定义了`installedModules`对象和`__webpack_require__`函数，然后在`__webpack_require__`函数上挂载了很多静态方法和属性，这些方法和属性我们放在**附录**中说明。接下来我们重点来看看`__webpack_require__`函数和 `installedModules`对象。

### \__webpack_require__

``` javascript
			// 传入参数moduleId，用于从modules对象中获取对应的函数
/******/ 	function __webpack_require__(moduleId) {
/******/
/******/ 		// 判断模块是否已经在缓存中
   				// 如果在缓存中，说明模块之前已经被加载了，所以直接返回缓存中的结果
/******/ 		if(installedModules[moduleId]) {
/******/ 			return installedModules[moduleId].exports;
/******/ 		}
    			// 代码能执行到这里，说明模块没有在缓存中
/******/ 		// 创建一个模块对象，并把模块对象存在缓存中
/******/ 		var module = installedModules[moduleId] = {
/******/ 			i: moduleId,
/******/ 			l: false,
/******/ 			exports: {}
/******/ 		};
/******/
/******/ 		// 从modules对象中获取模块对应函数，并执行函数
/******/ 		modules[moduleId].call(module.exports, module, module.exports, __webpack_require__);
/******/
/******/ 		// 把模块标记为完成加载状态
/******/ 		module.l = true;
/******/
/******/ 		// 返回模块对象的exports对象
/******/ 		return module.exports;
/******/ 	}
```

`__webpack_require__`函数是一个核心函数，它的主要作用是定义模块，缓存模块，执行模块，并返回模块暴露的接口。

#### 定义模块

``` javascript
/******/ 		var module = installedModules[moduleId] = {
/******/ 			i: moduleId,
/******/ 			l: false,
/******/ 			exports: {}
/******/ 		};
```

从上面代码我们可以看到定义的模块对象有三个属性，分别分别是`i`（模块id），`l`（是否完成加载），`exports`（输出模块的内容）。并把定义的模块对象存入`installedModules`中。

#### 执行模块

``` javascript
/******/ 		modules[moduleId].call(module.exports, module, module.exports, __webpack_require__);
```

因为`__webpack_require__`函数最后返回了`module.exports`，我们重点来看一下`module.exports`是怎么被赋值的，这里我们用一段代码来说明一下，如果我们在`index.js`写入以下代码

``` javascript
// src/index.js
console.log('hello webpack')

export const a = 1
```

我们在打包结果可以提取到以下这段代码，这段代码说明如果我们在模块中有暴露接口（比如定义了`export`或`export default`）时，那么定义的接口都会被挂载到`module.exports`上。

``` javascript
__webpack_require__.d(__webpack_exports__, "a", function() { return a; });
// __webpack_exports__即module.exports
```

当执行完模块之后，会把模块的l属性置为`true`，代表模块加载完成。

在模块函数体的最前面一般都会先执行`__webpack_require__.r(__webpack_exports__);`，这段代码主要为`module对象`定义`__esModule`属性，表示通过`es module`方式暴露的接口。

### installedModules

关于`__webpack_require__`上定义的方法和属性的作用，把他们放在文章的最后面统一分析，因为第二部分和第三部分打包出来的代码会新增一些方法或属性。

至此，第一部分基本分析完毕。接下来我们来分析一下如果在入口文件中加载一个同步模块或异步模块，打包的代码会有什么变化。

在这里，我需要先说明一下，在`webpack`中怎么同步和异步加载模块的方式有那些。

| 模块方法 | 同步加载模块   | 异步加载模块                  |
| -------- | -------------- | ----------------------------- |
| ES6      | import、export | import()                      |
| CommonJS | require()      | webpack独有的require.ensure() |
| AMD      |                | define、require               |

## 同步加载模块

我们在`index.js`同目录下新增一个`log.js`文件，这个文件暴露一个`logInfo`方法，并且在`index.js`中引入并调用。

``` javascript
// src/log.js
export function logInfo ()  {
    console.log(...arguments)
}
```

``` javascript
// src/index.js
import {logInfo} from './log'

logInfo('hello webpack')
```

我们把打包的代码和第一次打包的代码进行对比，发现立即执行函数函数体的定义没有变化，不同的地方在于`modules`的对象。

```javascript
// 简化之后的代码

// 第一次打包：

// modules['./src/index.js']：

console.log('hello webpack')


// 第二次打包：

// modules['./src/index.js']：

__webpack_require__.r(__webpack_exports__);
var _log__WEBPACK_IMPORTED_MODULE_0__ = __webpack_require__(/*! ./log */ "./src/log.js");
Object(_log__WEBPACK_IMPORTED_MODULE_0__["logInfo"])('hello webpack')

// modules['./src/log.js']：

__webpack_require__.r(__webpack_exports__);
__webpack_require__.d(__webpack_exports__, "logInfo", function() { return logInfo; });
function logInfo ()  {
    console.log(...arguments)
}

```

这里我们主要分析第二次打包结果的执行流程，还记得立即执行函数最后的一行代码吗，`return __webpack_require__(__webpack_require__.s = "./src/index.js")`，这行代码向`__webpack_require__`函数传入入口的路径，并执行，当执行`modules[moduleId].call(module.exports, module, module.exports, __webpack_require__)`就会进行以下代码，这段代码会执行`__webpack_require__(/*! ./log */ "./src/log.js")`并把结果赋值给`_log__WEBPACK_IMPORTED_MODULE_0__`，从第一部分的分析我们可以知道`_log__WEBPACK_IMPORTED_MODULE_0__`的结果可以简化如下：

``` javascript
var _log__WEBPACK_IMPORTED_MODULE_0__ = {
    logInfo: function ()  {
        console.log(...arguments)
    }
}
```

从这里我们可以知道，当我们在一个模块同步引入其他模块时，打包时会把其他模块都打包到这个模块中，在执行模块的过程中，通过路径获取模块对应的函数，并且把模块加载到缓存中。

### 同步加载流程图

![1](C:\Users\N20219\Desktop\学习笔记\imgs\1.png)

## 异步加载模块

在这之前我们已经介绍了`webpack`中的模块方法，接下来我们通过`import()`异步加载方式引入`log.js模块`，我们把`index.js`的代码修改为

``` javascript
// src/index.js
import('./log').then(Log => Log.logInfo('hello webpack'))
```

执行打包命令之后，我们看到dist目录下生成了两个chunk，分别是`bundle.js`和`0.js`，我们可以把`bundle.js`称为主chunk，下面我们通过对两个文件的分析来了解异步加载的整体流程。

### bundle.js

此时打包出来的代码会和之前的同步加载打包出来的代码有比较大的不同，先分析函数体代码的不同。

``` javascript
/******/ 	// install a JSONP callback for chunk loading
/******/ 	function webpackJsonpCallback(data) {};

/******/ 	// object to store loaded and loading chunks
/******/ 	// undefined = chunk not loaded, null = chunk preloaded/prefetched
/******/ 	// Promise = chunk loading, 0 = chunk loaded
/******/ 	var installedChunks = {
/******/ 		"main": 0
/******/ 	};
/******/

/******/ 	// script path function
/******/ 	function jsonpScriptSrc(chunkId) {
/******/ 		return __webpack_require__.p + "" + chunkId + ".bundle.js"
/******/ 	}

/******/ 	// This file contains only the entry chunk.
/******/ 	// The chunk loading function for additional chunks
/******/ 	__webpack_require__.e = function requireEnsure(chunkId) {};

			// 这行代码先检测window["webpackJsonp"]是否存在，如果不存在则复制一个空数组
/******/ 	var jsonpArray = window["webpackJsonp"] = window["webpackJsonp"] || [];
			// 缓存jsonpArray原始的push方法，作用呢？
/******/ 	var oldJsonpFunction = jsonpArray.push.bind(jsonpArray);
			// 将jsonpArray的push方法改为webpackJsonpCallback
/******/ 	jsonpArray.push = webpackJsonpCallback;
/******/ 	jsonpArray = jsonpArray.slice();
			// 下面的这段代码可以看出，代码执行到这里的时候jsonpArray可能不是空数组，为什么？ 其实这里就说明了在执行主chunk之前，jsonpArray就已经被赋值了
			// 从这里也可以看出，无论是在主chunk执行之前还是在主chunk执行之后加载的其他chunk都会被webpackJsonpCallback函数执行
/******/ 	for(var i = 0; i < jsonpArray.length; i++) webpackJsonpCallback(jsonpArray[i]);
			// 将oldJsonpFunction复制给parentJsonpFunction
/******/ 	var parentJsonpFunction = oldJsonpFunction;
```

我们对上面的代码做了分析， 当我们的模块中存在异步加载时，打包出来的代码会在全局对象window上创建webpackJsonp这个属性，它是一个数据，window["webpackJsonp"]有两个作用：

* 当异步模块先于主模块被加载时，异步加载的模块被存放在window["webpackJsonp"]中，等主模块完成加载，再把异步模块取出并执行
* 修改window["webpackJsonp"]数组上的push方法为webpackJsonpCallback

webpackJsonpCallback是异步加载中最重要的一个函数，为了方便理解`webpackJsonpCallback`这个函数，我们需要先做一些铺垫，首先我们来看一下`0.js`的代码。

### 0.js

从这个chunk我们可以知道，当这个chunk请求完成后，会执行以下代码，window["webpackJsonp"]push了一个数组，这个数组的第一项存放了chunk的id，用于记录chunk的加载状态，第二项是我们一开始说到的`modules对象`。

``` javascript
(window["webpackJsonp"] = window["webpackJsonp"] || []).push([[0],{
    // ...省略
}]);
```

看完0.js代码之后，我们接着看installedChunks对象。

### installedChunks

记录加载的模块，key表示模块id，value表示模块加载状态，下面用表格表示value值所对应的状态说明。

| value     | 说明                                  |
| --------- | ------------------------------------- |
| undefined | 模块没有被加载                        |
| null      | 通过preloaded或prefetched预加载的模块 |
| Promise   | 模块正在加载中                        |
| 0         | 模块已加载完成                        |

我们知道`主chunk`中installedChunks对象一开始就存在属性bundle为0，而bundle作为主chunk的id，毫无疑问，代码能执行到这里说明主chunk已经完成了加载，所以值为0。

在分析webpackJsonpCallback之前，我们还需要再分析一下\__webpack_require__.e这个函数。

### \__webpack_require__.e

从代码看，函数体定义了一个数组为promises，最后返回了Promise.all(promises)。前面我们用了`import('./log')`这段代码，其实这段代码最终被转化为promise，所以我们才可以在这段代码用then方法。

这个函数的主要作用是通过动态的script加载方式请求相应chunk，把chunk的的状态放入installedChunks中，并且返回一个Promise.all()，主要分析如下。

``` javascript
/******/ 	__webpack_require__.e = function requireEnsure(chunkId) {
/******/ 		var promises = [];
/******/
/******/		// 获取chunkId对应的状态
/******/ 		var installedChunkData = installedChunks[chunkId];
    			 // 为0表示模块已经被加载，这里模块没有被加载或者处于加载中状态
/******/ 		if(installedChunkData !== 0) {
/******/
/******/ 			// 当请求的模块处于加载中的状态，并且其他地方也调用这个模块，那么会把先前模块的promises放入当前的promises中
/******/ 			if(installedChunkData) {
    					// 把原来的promise push进来
/******/ 				promises.push(installedChunkData[2]);
/******/ 			} else { // 模块未被加载
/******/ 				// 创建一个promise
/******/ 				var promise = new Promise(function(resolve, reject) {
/******/ 					installedChunkData = installedChunks[chunkId] = [resolve, reject];
/******/ 				});
/******/ 				promises.push(installedChunkData[2] = promise);
    					// 从这里可知，当模块即将被加载时，installedChunks[chunkId]是一个数组，第一项为resovle，第二项为reject，第三项为promise
/******/
/******/ 				// 开始请求模块
/******/ 				var script = document.createElement('script');
/******/ 				var onScriptComplete;
/******/
/******/ 				script.charset = 'utf-8';
/******/ 				script.timeout = 120;
/******/ 				if (__webpack_require__.nc) {
/******/ 					script.setAttribute("nonce", __webpack_require__.nc);
/******/ 				}
    					// jsonpScriptSrc(chunkId)：拼接模块路径
/******/ 				script.src = jsonpScriptSrc(chunkId);
/******/
/******/ 				// create error before stack unwound to get useful stacktrace later
/******/ 				var error = new Error();
/******/ 				onScriptComplete = function (event) {
/******/ 					// avoid mem leaks in IE.
/******/ 					script.onerror = script.onload = null;
/******/ 					clearTimeout(timeout);
    						// 同一个模块可能会同时发起多个请求，这里需要进行判断
/******/ 					var chunk = installedChunks[chunkId];
    						// chunk没有被成功加载
/******/ 					if(chunk !== 0) {
/******/ 						if(chunk) {
/******/ 							var errorType = event && (event.type === 'load' ? 'missing' : event.type);
/******/ 							var realSrc = event && event.target && event.target.src;
/******/ 							error.message = 'Loading chunk ' + chunkId + ' failed.\n(' + errorType + ': ' + realSrc + ')';
/******/ 							error.name = 'ChunkLoadError';
/******/ 							error.type = errorType;
/******/ 							error.request = realSrc;
/******/ 							chunk[1](error);
/******/ 						}
/******/ 						installedChunks[chunkId] = undefined;
/******/ 					}
/******/ 				};
/******/ 				var timeout = setTimeout(function(){
/******/ 					onScriptComplete({ type: 'timeout', target: script });
/******/ 				}, 120000);
    					// 无论加载成功还是失败都会调用onScriptComplete函数
/******/ 				script.onerror = script.onload = onScriptComplete;
/******/ 				document.head.appendChild(script);
/******/ 			}
/******/ 		}
/******/ 		return Promise.all(promises);
/******/ 	};
```

为了方便大家更直观的理解`__webpack_require__.e`的执行，下面我们用一个流程图来表示它的执行流程。

### __webpack_require__.e执行流程图

<img src="C:\Users\N20219\Desktop\学习笔记\imgs\__webpack_require__.e.png" style="float: left; zoom: 67%;" />

我们知道`__webpack_require__.e`中返回`Promise.all(promises)`，那么promises的状态是在哪里发生改变的呢？这里我可以告诉你的是，promises的状态发生改变的地方是在webpackJsonpCallback中，webpackJsonpCallback中执行了promises的resolve函数，使得promises的状态发生了改变。

### webpackJsonpCallback

通过对`webpackJsonpCallback`函数的分析，我们可以知道这个函数的作用有：

* 改变`installedChunks[chunkId]`的状态为加载完成
* 把`其他chunk`的模块对象拷贝到`主chunk`的`modules对象`上
* 改变`__webpack_require__.e`中的promise状态

``` javascript
/******/ 	function webpackJsonpCallback(data) {
    			// 获取模块id
/******/ 		var chunkIds = data[0];
    			// 获取模块对象
/******/ 		var moreModules = data[1];
/******/
/******/
/******/ 		// add "moreModules" to the modules object,
/******/ 		// then flag all "chunkIds" as loaded and fire callback
/******/ 		var moduleId, chunkId, i = 0, resolves = [];
/******/ 		for(;i < chunkIds.length; i++) {
/******/ 			chunkId = chunkIds[i];
    				// 理解这个判断条件需要结合__webpack_require__.e函数
/******/ 			if(Object.prototype.hasOwnProperty.call(installedChunks, chunkId) && installedChunks[chunkId]) {
    					// installedChunks[chunkId][0]是一个resolve函数，在__webpack_require__.e中被赋值
/******/ 				resolves.push(installedChunks[chunkId][0]);
/******/ 			}
    				// 执行到这里，说明模块被加载完成
/******/ 			installedChunks[chunkId] = 0;
/******/ 		}
    			// 把加载的模块对象拷贝到modules上
/******/ 		for(moduleId in moreModules) {
/******/ 			if(Object.prototype.hasOwnProperty.call(moreModules, moduleId)) {
/******/ 				modules[moduleId] = moreModules[moduleId];
/******/ 			}
/******/ 		}
    			// 为什么要这么做？
/******/ 		if(parentJsonpFunction) parentJsonpFunction(data);
/******/
    			// 执行resove函数，promise状态改变，即模块完成了加载
/******/ 		while(resolves.length) {
/******/ 			resolves.shift()();
/******/ 		}
/******/
/******/ 	};
```

我们再回头来看一下`主chunk`代码的执行流程：

**step1：**当执行`__webpack_require__.e(/*! import() */ 0)`时，会请求id为0的chunk，并返回了一个`promise`，当模块请求完成后，会执行`webpackJsonpCallback`，改变了`installedChunks[chunkId]`的状态，并执行了`resolve`函数，即`promise`状态发生改变。

**step2**：接着会执行`__webpack_require__.bind(null, /*! ./log */ "./src/log.js")`，这个函数返回了`./src/log.js`暴露的接口，即`module.exports`。

**step3**：从`module.exports`即`Log`上获取`logInfo`函数并执行。

至此，主chunk的代码执行完毕。

``` javascript
__webpack_require__.e(/*! import() */ 0).then(__webpack_require__.bind(null, /*! ./log */ "./src/log.js")).then(Log => Log.logInfo('hello webpack'))
```

### 异步加载流程图

![2](C:\Users\N20219\Desktop\学习笔记\imgs\2.png)

## 使用spiltChunks时

我们先来了解一下spiltChunks即代码分离是什么，先看看官方文档的说法。

> 代码分离spiltChunks是 webpack 中最引人注目的特性之一。此特性能够把代码分离到不同的 bundle 中，然后可以按需加载或并行加载这些文件。代码分离可以用于获取更小的 bundle，以及控制资源加载优先级，如果使用合理，会极大影响加载时间。
>
> 有三种常用的代码分离方法：
>
> - 入口起点：使用 [`entry`](https://www.webpackjs.com/configuration/entry-context) 配置手动地分离代码。
> - 防止重复：使用 [`CommonsChunkPlugin`](https://www.webpackjs.com/plugins/commons-chunk-plugin) 去重和分离 chunk。
> - 动态导入：通过模块的内联函数调用来分离代码。

下面我们采用`入口起点`来对代码进行分离，继续往下看。

我们把`webpack.config.js`代码修改为

``` javascript
// webpack.config.js
const path = require('path')

module.exports = {
    mode: 'development',
    entry: {
        bundle: './src/index.js',
        log: './src/log.js'
    },
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: '[name].js'
    },
    optimization: {
        splitChunks: {
            chunks: "all",
            minSize: 0
        }
    }
}
```

并且把`index.js`文件修改为：

``` javascript
// index.js
import {logInfo} from './log'

logInfo('hello webpack')
```

打包后，我们发现除了生成`bundle.js`和`log.js`，还生成了`bundle~log.js`。我们在`webpack.config.js`配置了两个入口，bundle和log，这两个入口都引用了相同的模块，即`log.js`，所以`bundle~log.js`就是被提取出来的公用模块。

下面我们把`bundle.js`和之前打包出来的代码进行对比，发现有以下的不同，最重要的一点是函数体最后执行不是`__webpack_require__`函数，而是`checkDeferredModules`函数，下面我们会重点来分析`checkDeferredModules`函数到底做了什么。

``` javascript
// bundle.js

/******/ (function(modules) { // webpackBootstrap
/******/ 	function webpackJsonpCallback(data) {
/******/ 		var chunkIds = data[0];
/******/ 		var moreModules = data[1];
/******/ 		var executeModules = data[2];
/******/
				/** 省略... */
/******/
/******/ 		// add entry modules from loaded chunk to deferred list
/******/ 		deferredModules.push.apply(deferredModules, executeModules || []);
/******/
/******/ 		// run deferred modules when all chunks ready
/******/ 		return checkDeferredModules();
/******/ 	};
			
/******/ 	function checkDeferredModules() {}
    
/******/ 	var deferredModules = [];
/******/
			/** 省略... */
/******/
/******/ 	// add entry module to deferred list
/******/ 	deferredModules.push(["./src/index.js","bundle~log"]);
/******/ 	// run deferred modules when ready
/******/ 	return checkDeferredModules();
/******/ })
/************************************************************************/
/******/ ({

			/** 省略... */

/******/ });
```

### deferredModules

`deferredModules`是一个数组，它的子项都是数组类型，子项第一项为入口模块，子项第二项开始为依赖模块，例如`deferredModules[["./src/index.js","bundle~log"]], ...）`。

`deferredModules`这个数组的主要作用是子项存放`入口模块id`和入口模块依赖的所有`公用模块id`。它的作用在`checkDeferredModules`函数中得以体现，接下来我们我看看这个重要的函数。

### checkDeferredModules

这个函数的作用主要是检测入口模块所依赖的其他模块是否全部完成了加载，如果是，才会通过`__webpack_require__`函数执行入口模块，否则则等待所有依赖模块完成加载。

``` javascript
/******/ 	function checkDeferredModules() {
/******/ 		var result;
/******/ 		for(var i = 0; i < deferredModules.length; i++) {
/******/ 			var deferredModule = deferredModules[i];
/******/ 			var fulfilled = true;
    				// 注意j从1开始，因为只需要检测被依赖的模块是否完成了加载
/******/ 			for(var j = 1; j < deferredModule.length; j++) {
    					// 被依赖的模块id
/******/ 				var depId = deferredModule[j];
    					// 只要被依赖的模块有任何一个没有完成加载，那么fulfilled为false
/******/ 				if(installedChunks[depId] !== 0) fulfilled = false;
/******/ 			}
    				// 如果fulfilled为true，说明依赖的模块都已经完成加载
/******/ 			if(fulfilled) {
/******/ 				deferredModules.splice(i--, 1);
    					// 执行入口模块
/******/ 				result = __webpack_require__(__webpack_require__.s = deferredModule[0]);
/******/ 			}
/******/ 		}
/******/
/******/ 		return result;
/******/ 	}
```

#### 加载方式1

对于一些`公共模块`，在`script标签`中我们先于在`主chunk`之前引入，当然如果项目中有配置`htmlwebpackplugin`这个插件的话，它会帮助我们做这件事。

所以，正常情况下我们会这么引入,控制台可以打印出`hello webpack`。

``` html
<script src="bundle~log.js"></script>
<script src="bundle.js"></script>
```

#### 加载方式2

而如果我们这样引入呢？这种方式同样可以打印出`hello webpack`。`公共模块`会在`主chunk`之后加载，其实我们可以看一下`webpackJsonpCallback`函数底部的一行代码`return checkDeferredModules();`，即可公用代码慢于主chunk加载，但在公用代码加载进来的时候会调用`webpackJsonpCallback`函数，而`webpackJsonpCallback`函数又会执行`checkDeferredModules`函数，这个函数如果检查到公共模块已经完成加载，就会去执行`__webpack_require__`函数，从而主chunk得以执行。

``` html
<script src="bundle.js"></script>
<script src="bundle~log.js"></script>
```

### splitChunks加载流程图

![](C:\Users\N20219\Desktop\学习笔记\imgs\3.png)

## 附录

### \__webpack_require__.m

用于暴露modules对象

### \__webpack_require__.c

用于暴露installedModules对象

### \__webpack_require__.o

判断一个对象自身是否存在某个属性

### \__webpack_require__.d

为模块的exports对象定义属性

### \__webpack_require__.r

为模块的exports对象定义__esModule属性，并将其值设置为true

### \__webpack_require__.t

创建一个虚拟的命名对象，具体用法在打包的代码中没有发现

### \__webpack_require__.n

兼容非`es module`的模块方法

### \__webpack_require__.p

等于在webpack.config.js中output中定义的publicPath，在异步加载请求时会将这个值拼接到moduleId前面。

### \__webpack_require__.s

存放入口路径



## 结语

为了让自己

## 参考资料

https://webpack.js.org/concepts/

http://webpack.wuhaolin.cn/



