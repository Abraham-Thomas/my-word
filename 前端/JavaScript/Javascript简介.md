# 简介

JS诞生于1995，当时主要目的是处理以前服务器语言的一些输入验证操作。随着时代发展，JS越来越丰富。

**为什么叫Javascript？**

当时在Netscape（网景）公司的布兰登·艾奇计划开发一款LiveScript的脚本语言，而当时的Java如火如荼，公司为了就为了搭上大红大紫的Java顺风车，更名为JavaScript。

**ECMA-262**

由NetScape、微软、Sun等公司程序员组成的TC39委员会，共同定义的一种名为ECMAScript的新脚本语言标准。

**JS组成**

![](/images/JavaScript组成.png)

**ECMAScript**

由ECMA-262定义的ECMA与Web浏览器没有依赖关系。实际上，这门语言本身不包含输入和输出定义。ECMA-262定义的只是这门语言的基础，在此基础上可以构建更完善的脚本语言。我们常见的Web浏览器只是ECMA实现的可能**宿主环境**之一。

宿主环境不仅提供题本的ECMAScript实现，同时也会提供该语言的扩展（如DOM），以便语言与环境之间对接交互。

**DOM**

针对XML但经过扩展用于HTML的应用编程接口，它把整个页面映射成一个多层节点结构。

为什么需要它？

Netscape和微软两强割据，浏览器互不兼容，负责Web通信标准的W3C（万维网联盟）开始着手规划DOM。

DOM分为0级、1级、2级，2级扩展。

**BOM**

从根本上讲，BOM只处理浏览器窗口和框架；但人们习惯上也把所有针对浏览器的JavaScript扩展算作BOM一部分。



## 被忽略的script元素

HTML4.01为\<script>定义了6个属性：

async：可选。表示立即下载脚本，但不应妨碍页面中的其他操作，比如下载其他资源或等待加载其他脚本，只对外部脚本文件有效。

charset：可选。表示通过src属性指定的代码的字符集。由于大多数浏览器会忽略它的值，所以很少有人用。

defer：可选。表示脚本可以延迟到文档完全被解析和显示之后执行。只对外部脚本文件有效。

language：已废弃。表示用于编写脚本的代码语言。

src：可选。表示包含要执行的脚本文件。

type：可选。可以看成是language的替代属性。表示编写代码使用的脚本语言的内容类型（也称为MIME类型）。虽然text/javasript 和 text/ecmascript 都已经不被推荐使用，但人们一直依赖使用的都还是text/javascript。实际上，服务器在传送Javascript文件时使用的MIME类型通常是application/x-javascript，但在type中设置这个值却可能导致脚本被忽略。text/javascript并不是必需的，就算如果没有制定这个属性，默认也会是它。



**Javascript的type**

虽然text/javascript和text/ecmascript都已经不被推荐使用，但人们一直以来使用的都还是text/javascript。实际上，服务器在传送JavaScript文件时使用的MIME类型通常是application/x-javascript，但在type中设置这个值却可能导致脚本被忽略。另外，在非IE浏览器中还可以使用：application/javascript和application

/ecmascript。考虑到约定俗称和最大限度浏览器兼容性，目前type属性的值依旧还是text/javascript。不过，这个属性不是必须的，如果没有指定，默认为text/javascript。



## 陌生的with

它的作用是将代码的作用域设置到一个特定的对象中。语法如下：

```
with (expression) statement;
```

定义它的目的主要是为了简化多次编写同一个对象的工作：

```
var qs = location.search.substring(1);
var hostName = location.hostname;
var url = location.href;
```

上面几行代码都包含location对象，如果使用with语句：

```
with(location){
	var qs = search.substring(1);
	var hostName = hostname;
	var url = href;
}
```

这样每个代码首先被认为是一个局部变量，而如果在局部环境中找不到该变量定义，就会查询location对象是否有属性名。如果发现了同名属性，ze以location对象属性的值作为变量的值。

严格模式下不允许使用with语句。大量使用with会导致性能下降，同时也给代码调试带来困难，因此开发大型应用程序时，不建议使用。



## 两个等号遇见三个等号

这是JavaScript中一个比较有趣的话题，我在项目中大多数用到的也是全等居多，因为用 `==`的情况，如果对比双方类型不一样，就会进行类型转换。他们的具体流程如下：

1. 首先会判断两者类型是否**相同**。相同的话就是比大小了
2. 类型不相同的话，那么就会进行类型转换
3. 会先判断是否在对比 `null` 和 `undefined`，是的话就会返回 `true`
4. 判断两者类型是否为 `string` 和 `number`，是的话就会将字符串转换为 `number`

```javascript
1 == '1'
      ↓
1 ==  1
```

判断其中一方是否为 `boolean`，是的话就会把 `boolean` 转为 `number` 再进行判断

```javascript
'1' == true
↓
'1' ==  1
↓
1  ==  1
```

判断其中一方是否为 `object` 且另一方为 `string`、`number` 或者 `symbol`，是的话就会把 `object` 转为原始类型再进行判断

```javascript
'1' == { name: 'yck' }
        ↓
'1' == '[object Object]'
```



## 大小拷贝

准确的说，应该是深浅拷贝，或者深浅复制。先从一个简单的例子开始：

```javascript
let a = {
	age: 1
}
let b = a
a.age = 2
console.log(b.age) // 2
```

对象类型在复制的过程中其实是复制了地址，从而会导致改变了一方其他都被改变的情况。如果不想这样，可通过浅拷贝方式进行。

**小拷贝（浅拷贝）**

首先可以通过 `Object.assign` 来解决这个问题，很多人认为这个函数是用来深拷贝的。其实并不是，`Object.assign` 只会拷贝所有的属性值到新的对象中，如果属性值是对象的话，拷贝的是地址，所以并不是深拷贝。

```javascript
let a = {
  age: 1
}
let b = Object.assign({}, a)
a.age = 2
console.log(b.age) // 1
```

ES6语法中的`...`也可实现小拷贝。

```javascript
let a = {
  age: 1
}
let b = { ...a }
a.age = 2
console.log(b.age) // 1
```

那么问题来了，小拷贝还有什么问题解决不了？

```javascript
let a = {
  age: 1,
  jobs: {
    first: 'FE'
  }
}
let b = { ...a }
a.jobs.first = 'native'
console.log(b.jobs.first) // native
```

浅拷贝只解决了第一层的问题，如果接下去的值中还有对象的话，那么就又回到最开始的话题了，两者享有相同的地址。要解决这个问题，我们就得使用深拷贝了。

**大拷贝（深拷贝）**

可以通过 `JSON.parse(JSON.stringify(object))` 来解决。

```javascript
let a = {
  age: 1,
  jobs: {
    first: 'FE'
  }
}
let b = JSON.parse(JSON.stringify(a))
a.jobs.first = 'native'
console.log(b.jobs.first) // FE
```

但是该方法也是有局限性的：

- 会忽略 `undefined`
- 会忽略 `symbol`
- 不能序列化函数
- 不能解决循环引用的对象

```javascript
let obj = {
  a: 1,
  b: {
    c: 2,
    d: 3,
  },
}
obj.c = obj.b
obj.e = obj.a
obj.b.c = obj.c
obj.b.d = obj.b
obj.b.e = obj.b.c
let newObj = JSON.parse(JSON.stringify(obj))
console.log(newObj)
```

在遇到函数、 `undefined` 或者 `symbol` 的时候，该对象也不能正常的序列化

```javascript
let a = {
  age: undefined,
  sex: Symbol('male'),
  jobs: function() {},
  name: 'yck'
}
let b = JSON.parse(JSON.stringify(a))
console.log(b) // {name: "yck"}
```

