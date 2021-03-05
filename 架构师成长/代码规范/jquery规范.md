# jquery规范

### 加载jQuery

* 尽量使用CDN加载jQuery。

### jQuery变量

* 所有使用或缓存jQuery对象的变量应该以$开头

* 始终将jQuery选择器返回的对象缓存到本地变量中以复用。**（强制）**

``` js
var $myDiv = $("#myDiv");
$myDiv.click(function(){....});
```

* 使用峰驼式命名变量。

### 选择器

* ID选择器可用时就使用ID选择,它在内部使用document.getElementById()

* 当使用类/伪类选择器时，总是给选择器附上元素类型来避免扫描整个DOM树。

``` js
 // BAD 在整个DOM树中扫描"products"类名
var $products = $(".products");

// GOOD 只在DIV元素中扫描"products"类名
var $products = $("div.products");
```

* 在ID > 子节点层级选择器中使用find()方法。因为前半部分选择器没使用到Sizzle选择器引擎来查找元素。

``` js
// BAD, Sizzle选择器引擎查找层级
var $productIds = $("#products div.id");

// GOOD, 只有div.id走Sizzle选择器引擎
var $productIds = $("#products").find("div.id");
```

* 选择器后半部分比前半部分明确。

``` js
// 未优化
$("div.data .gonzalez");

// 优化
$(".data td.gonzalez");
```

* 避免冗余选择器。

``` js
$(".data table.attendees td.gonzalez");

// Better: 有必要时要去掉中间不必要的内容
$(".data td.gonzalez");
```

* 给选择器添加上下文。

``` js
// 要扫描整个DOM树寻找
$('.class');

// 只在#class-container里扫描
$('.class', '#class-container');
```

* 避免使用通配符选择器。

``` js
 // BAD
$('div.container > *');

 // BETTER
$('div.container').children();
```

* 避免使用隐式通配选择器。当省略下面的input时，会隐式的使用通配符选择器。

``` js
 // BAD
$('div.someclass :radio');

 // GOOD
$('div.someclass input:radio');
```

* ID选择器使用的是document.getElementById()，ID选择器无需嵌套ID。

``` js
 // BAD
$('#outer #inner');

 // BAD
$('div#inner');

 // BAD
$('.outer-container #inner');

 // GOOD
$('#inner');
```

### DOM操作

* 始终先detach现有DOM元素后进行操作，随后将其attach到DOM中。

``` js
var $myList = $("#list-container > ul").detach();

//...针对$myList的许多DOM操作
$myList.appendTo("#list-container");
```

* 使用字符串连接或者array.join()而不是.append()方法。

``` js
// BAD
var $myList = $("#list");
for(var i = 0; i < 10000; i++){
    $myList.append("<li>"+i+"</li>");
}

// GOOD
var $myList = $("#list");
var list = "";
for(var i = 0; i < 10000; i++){
    list += "<li>"+i+"</li>";
}
$myList.html(list);

// EVEN FASTER
var array = []; 
for(var i = 0; i < 10000; i++){
    array[i] = "<li>"+i+"</li>"; 
}
$myList.html(array.join(''));
```

* 不操作未知元素。

``` js
// BAD: 这个函数内部要先执行3个函数，才发现选择器选择到的可能是空内容
$("#nosuchthing").slideUp();

// GOOD
var $mySelection = $("#nosuchthing");
if ($mySelection.length) {
    $mySelection.slideUp();
}
```

### 事件

* 每个页面只使用一个Document Ready函数。利于调试。

* 不要使用匿名函数绑定事件。匿名函数不利于调试、维护、测试和复用。

``` js
 // BAD
$("#myLink").on("click", function(){...});

// GOOD
function myLinkClickHandler(){...}
$("#myLink").on("click", myLinkClickHandler);
```

* Document ready函数不应该是匿名函数。匿名函数不能复用也无法对其测试。

``` js
$(function(){ .. }); // BAD: 不容易复用也不利于测试

// GOOD
$(initPage); // or $(document).ready(initPage);
function initPage(){
  // Document ready里可以初始化变量和调用其他初始化函数
}
```

* Document ready函数应该放在外部文件里，在其他初始化设置之后，在行内JS里调用这些函数。

``` js
<script src="my-document-ready.js"></script>
<script>
  // 任何其他需要设置的全局变量
  $(document).ready(initPage); // or $(initPage);
</script>
```

* 不要在HTML文件里添加行为（行内JS），这是调试的噩梦。始终使用jQuery绑定事件后面会很容易去解绑事件。

``` js
<!-- BAD -->
<a id="myLink" href="#" onclick="myEventHandler();">my link</a>

 // GOOD
$("#myLink").on("click", myEventHandler);
```

* 如有需要，对事件使用自定义命名空间。这有利于去解绑某DOM元素上特定的事件而不会影响到该DOM元素上的其他事件。

``` js
 // GOOD
$("#myLink").on("click.mySpecialClick", myEventHandler);

// 后面会很容易的解绑这个特定的点击事件
$("#myLink").unbind("click.mySpecialClick");
```

### Ajax

* 避免使用.getJSON()和.get()，只使用$.ajax()，前两者都是在内部使用的后者。

* 不要在https的网站上使用http请求。侧重无模式的url（在URL上去掉http/https）

* 不要把请求参数放在URL里，而是放在data对象里去。

``` js
// 可读性差
$.ajax({
    url: "something.php?param1=test1&param2=test2",
    ....
});

// 可读性高
$.ajax({
    url: "something.php",
    data: { param1: test1, param2: test2 }
});
```

* 明确设置数据的类型dataType，这样很容易知道当前正在处理什么样的数据。

* 对Ajax加载的DOM元素绑定事件时尽量使用事件代理。事件代理的优势是对于Ajax后来添加到DOM的元素也能响应事件。

``` js
$("#parent-container").on("click", "a", delegatedClickHandlerForAjax);
```

* 使用Promise

``` js
$.ajax({ ... }).then(successHandler, failureHandler);

// OR
var jqxhr = $.ajax({ ... });
jqxhr.done(successHandler);
jqxhr.fail(failureHandler);
```

* Ajax模版代码：

``` js
var jqxhr = $.ajax({
  url: url,
  type: "GET",      // 默认值GET，可根据需要配置
  cache: true,      // 默认值true, dataType是'script'或'jsonp'时为false，可根据需要配置
  data: {},         // 请求参数对象
  dataType: "json", // 设置数据类型
  jsonp: "callback",// 只在操作JSONP时设置此项
  statusCode: {     // 针对特定错误码的回调处理函数
    404: handler404,
    500: handler500
  }
});
jqxhr.done(successHandler);
jqxhr.fail(failureHandler);
```

### 效果和动画

* 采用统一的动画实现方式。

* 不要过度使用动画效果，除非追求用户体验。
	* 尽量使用简单的show/hide/toggle/slideUp/slideDown方法来显示隐藏元素。
	* 尽量使用预定义的动画时间间隔：slow,fast或400

### 插件

* 始终选择有良好维护、优秀文档、良好测试和社区支持的插件。

* 细心检查该插件与正在使用的jQuery版本的兼容性。

* 任何通用的组件都应该抽取成jQuery插件。

### 链式写法

* 尽量使用链式写法而不是用变量缓存或者多次调用选择器方法。

``` js
$("#myDiv").addClass("error").show();
```

* 当链式写法超过三次或者因为事件绑定变得复杂后，使用换行和缩进保持代码可读性。

``` js
$("#myLink")
  .addClass("bold")
  .on("click", myClickHandler)
  .on("mouseover", myMouseOverHandler)
  .show();
```

* 对于更长的链式调用，可接受用变量缓存一个中间对象。

### 其他原则

* 参数尽量使用对象字面量。

``` js
 // BAD
$myLink.attr("href", "#").attr("title", "my link").attr("rel", "external");

// GOOD
$myList.attr({
    href: "#",
    title: "my link",
    rel: "external"
});
```

* 不要把CSS混进jQuery

``` js
 // BAD
$("#mydiv").css({'color':red, 'font-weight':'bold'});

/* GOOD */
.error { color: red; font-weight: bold; }

// GOOD
$("#mydiv").addClass("error");
```

* 不要使用遭弃用的方法。


### 参考

* http://lab.abhinayrathore.com/jquery-standards/#jQueryCND