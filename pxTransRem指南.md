## 如何做好移动端适配



## 1. 移动端适配是什么

## 2. 



# pxTransRem指南

## 背景

在当前项目中，为了能把css代码px单位转化为rem，我们一般使用了scss自定义的函数如rem或者p2r对px单位进行转化，以上用法存在以下问题：

* style中需要引入定义转化函数的文件。

* 需要对每个px进行手动rem或p2r操作。而实际上我们一般会从pxcook或其他方式直接复制css代码（px为单位）片段，理论上复制过来的代码不需要再手动修改，但目前我们需要手动rem或p2r操作。

为了避免在项目中使用手动rem或p2r操作，有以下方案：

## 如果转化px

### 方案一：引入px2rem（不推荐）

当引入px2rem之后，我们可以在css代码中用px单位。

但项目中可能同时存在web端和移动端的项目，此时可能px2rem难以做到兼容处理。

### 方案二：使用pxTransRem（推荐）

pxTransRem可以对特定的文件进行监听和转化，将px单位转化为rem函数调用，例如例如100px转化为rem(100)。

#### 如何使用

*step1*：将pxTransRem.js文件复制到项目根目录下的script目录中

*step2*：默认转化的函数名为rem，如使用其他函数名，可以在pxTransRem.js文件中修改transFunName

*step3*：在package.json中启动命令后面加上 && node script/pxTransRem.js

*step4*：将需要转化的scss或less或style文件后缀命名为.trans.(scss|styl[u]?s|less)

#### 测试结果

``` css
/**转化前：*/
.trans {
  width: 200px;
  height: 200px;
}

```

``` css
/**转化后：*/
.trans {
  width: rem(200);
  height: rem(200);
}
```


