
## 原理

消除无用的JavaScript代码

## 支持tree-shaking的工具

webpack/uglifyJs

rollup

Google closure compiler

## 源码总结

1 Webpack 在编译阶段将发现的 modules 放入 ModuleGraph
2 HarmonyExportSpecifierDependency 和 HarmonyImportSpecifierDependency 识别 import 和 export 的 module。
3 HarmonyExportSpecifierDependency 识别 used export 和 unused export
4 used 和 unused
4.1 把 used export 的 export 替换为 /* harmony export ([type]) / import
4.2 把 unused export 的 export 替换为 / unused harmony export [FuncName] */
