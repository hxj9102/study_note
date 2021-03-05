# html规范

### 缩进（强制）

缩进使用soft tab（4个空格）

### 命名规范

* 变量命名采用小驼峰命名，例如：javaScript

* 常量命名采用全大写,并用下划线分隔，如：JAVA_SCRIPT

* 对于对象、函数、和实例采用小驼峰（camelCase）命名法，如myObject

* 全局模块、单例变量、公共组件等首写字母大写，如：MyModule

* 模块内dom对象，带$符号前缀，例如：$dom

* 尽量避免使用全局变量

* 数组变量使用 List 后缀

* 映射变量使用 Map 后缀

* 布尔值变量使用类似下列的前缀，is, has, can, should

``` js
// 推荐的
const isJavascript = true;
const hasCode = true;
const canWrite = true;

// 不推荐的
const javascript = true;
const code = true;
const write = true;
```

### 代码规范

* 代码注释采用jsdoc方式

* 操作符前后保留1个空格，例如：1 + 1 = 2

* 同功能代码写在一个区域，变量定义统一在顶部

* 统一使用单引号

* 使用花括号包裹所有的多行代码块

``` js
// 推荐
if (true) {
  // TODO ...
}

// 不推荐
if (true) // TODO ...
```

* 在等号表达式中使用类型严格的 ===和!==。使用 === 可以避免等于判断中隐式的类型转换。

* for语句使用

``` js
for(let i = 0,len = list.length;i < len; ien++){
    //执行语句，list[i]
}
for(let key in jsonObj){
    //执行语句，jsonObj[key]
}
```

* while语句使用

``` js
let i = 0;
let len = list.length;
while(i < len){
    //执行语句
    i++;
    //注意死循环
}
```

* do while语句，尽量避免使用，采用for或者while代替 switch语句使用，当if else达到3个层级以上用swtich

* 全局情况下，判断变量是否为存在，例如：

``` js
// 错误的使用
if(foo) {
	// ...
}

//正确的使用
if(window.foo) {
	// ...
}

if(typeof foo !== "undefined") {
	// ...
}
```

### 注释规范

#### 单行注释

使用 //  作为单行注释。在评论对象上面另起一行使用单行注释。在注释内容前插入一个空格。

``` js
// 推荐
// 定义数量
let count = 1

// 不推荐
let count = 1 //定义数量
```

### 多行注释

以/\*开头，\*/结尾，注释内容前后加一个空格

``` js
// 推荐
/*
 * 第一行注释
 * 第二行注释
 */

/* 另外一种写法 */
```

#### 方法注释

函数(方法)注释也是多行注释的一种，但是包含了特殊的注释要求，关键方法必须加注释。

``` js
/**
 * 方法功能描述
 * @param {*} 参数
 * @param {*} 参数
 * @param {*} 参数
 * @param {*} 参数
 * @return 返回值
 */
```

### 性能优化

#### 加载和执行

* \</body\>闭合标签之前，将所有的\<script\> 标签放在页面底部，确保在脚步执行之前页面已经完成渲染。

* 合并脚本。http1.1下载单个 100KB 的文件将比下载 4 个 25KB 的文件更快，因此页面标签的\<script\>标签越少，加载也就越快，响应也越迅速。无论外链文件或者内嵌脚本。

* 压缩代码。减少文件体积，可以提高传输速度。

* 使用 CDN 提供 JavaScript 文件；CDN 不仅可以提升性能，也会为你管理文件的压缩和缓存。

#### 数据操作

* 访问字面量和局部变量的速度比访问数组元素和对象成员快，因此尽量使用字面量和局部变量，减少数组项和对象成员的使用。

* 尽可能使用局部变量。如果某个跨作用域的值在函数中被引用一次以上，把它存储到局部变量中。

``` js
// 推荐
function initFn() {
  let myImg = document.getElementById('myImg');
  myImg.src = 'photo.png';
  myImg.alt = 'photo';
}

// 不推荐
function initFn() {
  // document.getElementById('myImg')多次被引用
  document.getElementById('myImg').src = 'photo.png';
  document.getElementById('myImg').alt = 'photo';
}
```
* 避免使用 with 语句，它会改变执行环境作用域链。try-catch 中的 catch 也有同样的影响，看实际情况中进行运用。

#### DOM操作

* 最小化 DOM 访问次数，如果需要多次访问某个 DOM 节点，使用局部变量存储它的引用。

``` js
// 推荐
function innerHTMLLoop(){
  let content = '';
  for(let i = 0; i < 5; i++){
    content += 'a';
  }
  document.getElementById('myDiv').innerHTML = content;
}

// 不推荐
function innerHTMLLoop(){
  for(let i = 0; i < 5; i++){
    document.getElementById('myDiv').innerHTML += 'a';
  }
}
```