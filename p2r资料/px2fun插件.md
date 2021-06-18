# px2fun

一个可以将px转化为函数形式的插件

## 背景

现在的工程项目中可能同时存在多种单位转化，如大插件（单位为设计稿的一半）、活动页（单位使用rem）等，在写css样式时，我们可能会从pxcook等工具把样式复制过来，然后逐一把单位修改成函数调用形式，修改的过程会消耗一些时间。现在我们可以通过vs code插件`px2fun`来帮我们完成这件事情。

## 如何安装

在vscode插件搜索`px2fun`安装即可使用

## px2fun插件支持的功能

### 写入时提示转化

![code](https://i.ibb.co/48kFBC5/20210415-101005-00-00-00-00-00-30.gif)

### 通过快捷键批量转化

* 使用ctrl+alt+p，可将px转化为p2r函数

![code](https://i.ibb.co/6ygSrKc/20210415-101356-00-00-00-00-00-30.gif)

* 使用ctrl+alt+o，可将px转化为p2x函数

* 使用ctrl+shift+p，输入p2r或p2x，即可批量进行转换

![code](https://i.ibb.co/N77fyV2/20210415-101229-00-00-00-00-00-30.gif)
    
## 说明

为了保持项目转换函数的统一，推荐使用p2r函数用于转化rem，推荐使用p2x用于大插件的单位转化。

对于旧项目的使用，您依然可以通过配置项（file -> preferences -> setting -> extensions -> px2fun）设置需要转换的函数名。

## 支持的语言

vue less sass scss stylus styl
