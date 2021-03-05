# html规范

### 缩进（强制）

缩进使用soft tab（4个空格）

### 句法

* 不要大写标签

* 始终在属性上使用双引号，而不是单引号。

* 自闭元素中不要在末尾加上斜线-HTML5规范说它们是可选的。

	* 自闭合标签（self-closing），无需闭合。例如：area、base、br、col、command、embed、hr、img、input、keygen、link、meta、param、source、track、wbr 等 )

	* 闭合标签（closing tag），需闭合 。例如：textarea、title、h、div、span 等

``` html
<!-- 推荐 -->
<div class="html">

<!-- 不推荐 -->
<div class='html'>

<!-- 推荐 -->
<img>

<!-- 不推荐 -->
<img />
```

### 属性顺序

HTML属性应按此特定顺序排列，以便于阅读代码。类是可重用的组件的组成部分，因此它们是第一位的。ID更具体，应谨慎使用（例如，用于页内书签），因此它们排在第二位。

* class

* id， name

* data-*

* src，for，type，href，value

* title， alt

* role， aria-*

### 注释规范

#### 单行注释

* 一般用于简单的描述，如某些状态描述、属性描述等

* 注释内容前后各一个空格字符，注释位于要注释代码的上面，单独占一行

``` html
<!-- 推荐 -->
<!-- Comment Text -->
<div>...</div>

<!-- 不推荐 -->
<div>...</div><!-- Comment Text -->

<!-- 不推荐 -->
<div><!-- Comment Text -->
    ...
</div>
```

### 语义化

* 语义化的 HTML 结构，有助于机器（搜索引擎）理解，另一方面多人协作时，能迅速了解开发者意图

#### 常见标签语义化

标签 | 语义 
---- | --- 
\<p\> | 段落 
\<hn\> |  标题(h1~h6)
\<ul\> | 无序列表
\<ol\> | 有序列表
\<nav\> | 标记导航
\<main\> | 页面主要内容，一个页面只能使用一次。如果是 web 应用，则包围其主要功能
\<article\> | 定义外部的内容，其中的内容独立于文档的其余部分
\<section\> | 定义文档中的节（section、区段）。比如章节、页眉、页脚或文档中的其他部分。
\<aside\> | 定义其所处内容之外的内容。如侧栏、文章的一组链接、广告、友情链接、相关产品列表
\<header\> | 页眉通常包括网站标志、主导航、全站链接以及搜索框
\<footer\> | 页脚，只有当父级是 body 时，才是整个页面的页脚

### 类型属性

不需要为 CSS、JS 指定类型属性，HTML5 中默认已包含。

``` html
<!-- 推荐 -->
<link rel="stylesheet" href="" >
<script src=""></script>

<!-- 不推荐 -->
<link rel="stylesheet" type="text/css" href="" >
<script type="text/javascript" src="" ></script>
```

### HTML5文件类型

在每个HTML页面的开头，使用这种简单的doctype，可以在每个浏览器中强制执行标准模式和更一致的呈现。

``` html
<!doctype html>
<html>
  <head>
  </head>
</html>
```

### 语言属性

根据HTML5规范：鼓励作者在html根元素上指定lang属性，以提供文档的语言。这有助于语音合成工具确定使用什么发音，翻译工具确定使用什么规则，等等。

``` html
<html lang="en">
  <!-- ... -->
</html>
```

### 字符编码

通过声明显式字符编码，可以快速，轻松地确保内容的正确呈现。

``` html
<head>
  <meta charset="utf-8">
</head>
```
