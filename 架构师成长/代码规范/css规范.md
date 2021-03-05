# html规范

### 缩进（强制）

缩进使用soft tab（4个空格）

### 命名

* class 与 id 单词字母小写，多个单词组成时，采用中划线-分隔

* less、scss、stylus中的变量、函数、混合、placeholder命名与class规则相同

### 代码风格

* 属性声明结尾加分号

* 选择器与左括号之间一个空格，属性冒号后一个空格

``` css
/* 推荐 */
.css {
	width: 2px;
}

/* 不推荐 */
.css{
	width: 2px;
}

/* 不推荐 */
.css {
	width:2px;
}
```

* 不要为 0 指明单位

* 颜色值和属性值十六进制数值用小写，能简写的尽量用简写

``` css
/* 推荐 */
.css {
	color: #fff;
}

/* 不推荐 */
.css{
	color: #ffffff;
}
```

* 引号使用

url() 、属性选择符、属性值使用双引号

``` css
/* 推荐 */
.selector[type="text"] {
	background-image: url("image.png");
}

/* 不推荐 */
.selector[type=text] {
	background-image: url('image.png');
}
```

### 属性顺序

* @include()或 func()等引用混合和函数需放在最前面

* 定位属性（position、display、float、left、right等）

* 盒子模型（width、height、padding、margin、border等）

* 字体属性（color、font、text-align等）

* 其他属性（background、cursor、outline等）

### 注释规范

#### 单行注释

注释以 /\* 开始，以 \*/ 结束,注释内不能嵌套注释，注释内容前后空一个空格。在 sass或less等预处理语言上也可以使用双斜线注释。

#### 模块注释

注释以 /\* 开始，以 \*/ 结束，前后空一个空格，第一行填写描述，最后一行行填写分割线

``` css
/* 推荐 */
/* 注释说明
some code...
---------------------------------------------------- */
```

### 覆盖规范

* 尽可能少用！important

* vue 单文件组件统一使用 css/less/sass scoped

* 避免全局修改已有样式，必须具体到页面上(通过权重)

* 禁用全匹配\*选择器（特殊情况除外，如初始化）

* vue 单文件组件修改样式不生效可使用 /deep/ 或 >>>


### 字体规范

* 对外商用网站，不要用font-face引入微软雅黑字体，避免侵权（包括图片内容）

	* 【侵权】图片中使用 微软雅黑 字体，比如网站头图

	* 【安全】网站 CSS 用 font-family 声明网站使用 微软雅黑 字体，比如文章标题和正文

* 需要在 Windows 平台显示的中文内容，其字号应不小于 12px(因浏览器默认最小字体为12px)
