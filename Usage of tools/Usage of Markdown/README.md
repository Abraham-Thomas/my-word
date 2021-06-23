你不需要知道太多。

markdown只是一种语法说明，告诉你如何用规范的方法写出别人能看得流畅的内容，所以这也是它仅有的作用。

你唯一要做的就是专注于你的内容。

我不会在这里介绍过多篇幅的markdown语法，没必要。

## 标题
在想要设置为标题的文字前面加#来表示

```
# 这是一级标题
## 这是二级标题
### 这是三级标题
#### 这是四级标题
##### 这是五级标题
###### 这是六级标题

```

## 字体

加粗：在要加粗的文字左右分别用两个*号包起来

斜体：在要倾斜的文字左右分别用一个*号包起来

斜体加粗：在要倾斜和加粗的文字左右分别用三个*号包起来

删除线：在要加删除线的文字左右分别用两个~~号包起来

```
**这是加粗的文字**
*这是倾斜的文字*`
***这是斜体加粗的文字***
~~这是加删除线的文字~~

```

## 代码

单行代码：代码之间分别用一个反引号包起来
```     
`代码内容` 
```

代码块：代码之间分别用三个反引号包起来，且两边的反引号单独占一行

```
(```)
  代码...
  代码...
  代码...
(```)

```




## 引用
在引用的文字前加>即可，也可以嵌套，如加两个>>三个>>>

```
>这是引用的内容
>>这是引用的内容
>>>>>>>>>>这是引用的内容

```


## 图片
```
![图片alt](图片地址 ''图片title'')

图片alt就是显示在图片下面的文字，相当于对图片内容的解释。
图片title是图片的标题，当鼠标移到图片上时显示的内容。title可加可不加

```

## 超链接
```
[超链接名](超链接地址 "超链接title")
title可加可不加

```

## 分割线
三个或者三个以上的 - 或者 * 都可以

```
---
----
***
*****

```



## 列表
### 无序列表
用 - + * 任何一种都可以
```
- 列表内容
+ 列表内容
* 列表内容

注意：- + * 跟内容之间都要有一个空格

```

### 有序列表
数字加点
```
1. 列表内容
2. 列表内容
3. 列表内容

注意：序号跟内容之间要有空格

```

### 表格
```
表头|表头|表头
---|:--:|---:
内容|内容|内容
内容|内容|内容

第二行分割表头和内容。
- 有一个就行，为了对齐，多加了几个
文字默认居左
-两边加：表示文字居中
-右边加：表示文字居右
注：原生的语法两边都要用 | 包起来。此处省略

```




