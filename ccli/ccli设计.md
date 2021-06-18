# 

## 入口

bin/ccli.js

全局处理unhandledRejection错误

判断本地node版本是否合适

执行指令对应的模块

## ccli-dev

获取命令行参数并处理

加载ccli配置

开启服务
	获取配置参数
	创建Ccli实例
	创建Builder实例
	旧实例移除监听


监听ccli配置文件是否发生变化
	如果变化则重新启动服务

## ccli-build

获取命令行参数并处理

加载ccli配置

选择开启打包分析

进行打包

## Ccli

``` js
_hooks: {
	'modules:before',
	'modules:done',

	'ccli:setupMiddleware',
	'ccli:errorMiddleware',

	'ready',
	'close',
	'listen',
	'build:done'
}
```
