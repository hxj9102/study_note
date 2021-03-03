## webpack打包代码分析

wepack版本：4.46.0

### 基本结构



### 同步加载

```
installedModules: {
	l: Boolean,
	i: moduleId,
	exports: {},
	__esModule: true
}
```

__webpack_require__

### 异步加载

installedChunks

webpackJsonpCallback

__webpack_require__.e

