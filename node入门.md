Node.js 在浏览器外运行 V8 JavaScript 引擎（Google Chrome 的内核）


 Node.js 事件循环

异步且具有非阻塞的 I/O


在Node中引入模块，需要经历3个步骤。
(1) 路径分析

沿路径向上逐级递归，直到根目录下的node_modules目录

(2) 文件定位

2.1 文件扩展名分析

node会按照.js、.json、.node的次序补足扩展名，依次查找

2.2 目录分析和包

如果得到的是一个目录，将按照包来处理

首先查找package.json，接着找main字段

如果没有package.json或指定文件名错误，node将index当作默认文件名，然后再进行文件扩展名进行分析

(3) 编译执行

在node中，每个文件模块都是一个对象，他的定义如下

function Module (id, parent) {
	this.id = id
	this.exports = {}
	this.parent = parent
	if (parent && parent.children) {
		parent.children.push(this)
	}
	this.filename = null
	this.loaded = false
	this.children = []
}

对于不同的扩展名，载入的方法也有所不同：
.js文件：通过fs模块同步读取后编译执行
.node文件：这是用C/C++编写的扩展文件，通过dlopen方法加载最后编译生成的文件
.json文件：通过fs模块同步读取，用JSON.parse()解析返回结果
其余扩展名文件：被当作.js文件载入

每一个编译生成的模块都会将其文件路径作为索引缓存在`Module._cache`对象上，以提高二次引入性能


包结构：

package.json：包描述文件
bin：用于存放可执行二进制文件
lib：用于存放JavaScript代码目录
doc：用于存放文档目录
test：用于存放单元测试用例代码


在每一轮检查中，idle观察者先于I/O观察者，I/O观察者先于check观察者。


尝试对异步方法进行try/catch操作只能捕获当次事件循环内的异常


node模块分为两类

原生模块（核心模块）

文件模块

