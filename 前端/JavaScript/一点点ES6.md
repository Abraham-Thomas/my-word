# 一点点ES6

我不会把所有的es6语法都在这里写出来，一、没必要，因为网上资料够多了。二、显得繁琐。我只会把自己接触到的一部分用来总结一下。

## var、let 及 const 区别

先说个总结：**能用const解决的就不要用let，能用let解决的就不要用var。**

var的功效：变量还没有被声明，会被提升到作用域的顶部，也就是直接挂载在 `window` 上，导致我们可以使用这个未被声明的变量，具有**提升**的效果。

```javascript
console.log(a) // undefined
var a = 1
// 等同于
var a = 10
var a
console.log(a)
```

重复声明无效：

```javascript
var a = 10
var a
console.log(a)
```

结果依然为10，不会是后面的undefined。

函数也会因为var而提升。

```javascript
console.log(a) // ƒ a() {}
function a() {}
var a = 1
```

到这里，你可能想问，为什么会出现提升？

先看个例子：

```javascript
function test1() {
    test2()
}
function test2() {
    test1()
}
test1()
```

如不存在提升这个情况，那么就实现不了上述的代码，因为不可能存在 `test1` 在 `test2` 前面然后 `test2` 又在 `test1` 前面。可见，**提升存在的根本原因就是为了解决函数间互相调用的情况**



let与const的功效：**取消了提升**。

《深入理解ES6》一书中指出，它们俩声明变量到赋值这个过程中间有一个临时死区（TDZ），然变量在编译的环节中被告知在这块作用域中可以访问，但是访问是受限制的。

他们两者的区别呢？

作用是一样的，但const声明的变量不能再次赋值，因为内存地址的缘故。



## 原型继承和 Class 继承

先说明一点，ES6中有了类的概念，但它只是语法糖，本质上还是函数，可通过下面的方法在浏览器中验证。

```javascript
class Person {}
Person instanceof Function // true
```

先来看看ES5如何实现继承的吧

**组合继承**

```javascript
function Parent(value) {
	this.val = value
}
Parent.prototype.getValue = function() {
    console.log(this.val)
}

function Child(value) {
    Parent.call(this, value)
}
Child.prototype = new Parent()

const child = new Child(1)

child.getValue() // 1
child instanceof Parent // true
```

以上继承的方式核心是在子类的构造函数中通过 `Parent.call(this)` 继承父类的属性，然后改变子类的原型为 `new Parent()` 来继承父类的函数。

这种继承方式优点在于构造函数可以传参，不会与父类引用属性共享，可以复用父类的函数，但是也存在一个缺点就是在继承父类函数的时候调用了父类构造函数，导致子类的原型上多了不需要的父类属性，存在内存上的浪费。

**寄生组合方式**

这种继承方式对组合继承进行了优化，组合继承缺点在于继承父类函数时调用了构造函数，我们只需要优化掉这点就行了。

```javascript
function Parent(value) {
    this.val = value
}
Parent.prototype.getValue = function() {
    console.log(this.val)
}

function Child(value) {
    Parent.call(this, value)
}
Child.prototype = Object.create(Parent.prototye, {
    construtor: {
        value: Child,
        enumerable: false,
        writeable: true,
        configurable: true
    }
})

const child = new Child(1)

child.getValue() // 1
child instanceof Parent	// true
```

以上继承实现的核心就是将父类的原型赋值给了子类，并且将构造函数设置为子类，这样既解决了无用的父类属性问题，还能正确的找到子类的构造函数。

关于ES5原型链实现继承，我写了一篇更详细的，层层递进、优化继承，戳这--->][原型链的继承优化](原型链的继承优化.md)



**ES6的Class继承**

其实质还是基于原型链，只是写法上因为语法糖的缘故变得简单多了。

```javascript
class Parent {
	constructor(value) {
        this.val = value
    }
    getValue() {
        console.log(this.val)
    }
}

class Child extends Parent {
    construtor(value) {
        super(value)
    }
}

let child = new Child(1)
child.getValue() // 1
child instanceof  Parent // true
```

`class` 实现继承的核心在于使用 `extends` 表明继承自哪个父类，并且在子类构造函数中必须调用 `super`，因为这段代码可以看成 `Parent.call(this, value)`。



## 来自python的API：map、filter、reduce

我感觉ECMA的发展很大程度上借鉴了python，可能我这样的说法不严谨，但是他们相似的地方实在太多了。

`map` 作用是生成一个新数组，遍历原数组，将每个元素拿出来做一些变换然后放入到新的数组中。

```
[1, 2, 3].map(v => v + 1) // -> [2, 3, 4]
```

另外 `map` 的回调函数接受三个参数，分别是当前索引元素，索引，原数组

```
['1','2','3'].map(parseInt)
```

- 第一轮遍历 `parseInt('1', 0) -> 1`
- 第二轮遍历 `parseInt('2', 1) -> NaN`
- 第三轮遍历 `parseInt('3', 2) -> NaN`

`filter` 的作用也是生成一个新数组，在遍历数组的时候将返回值为 `true` 的元素放入新数组，我们可以利用这个函数删除一些不需要的元素

```
let array = [1, 2, 4, 6]
let newArray = array.filter(item => item !== 6)
console.log(newArray) // [1, 2, 4]
```

和 `map` 一样，`filter` 的回调函数也接受三个参数，用处也相同。

`reduce` 可以将数组中的元素通过回调函数最终转换为一个值，如果我们想实现一个功能将函数里的元素全部相加得到一个值，可能会这样写代码：

```JavaScript
const arr = [1, 2, 3]
let total = 0
for (let i = 0; i < arr.length; i++) {
  total += arr[i]
}
console.log(total) //6 
```

但是如果我们使用 `reduce` 的话就可以将遍历部分的代码优化为一行代码

```
const arr = [1, 2, 3]
const sum = arr.reduce((acc, current) => acc + current, 0)
console.log(sum)
```

对于 `reduce` 来说，它接受两个参数，分别是回调函数和初始值