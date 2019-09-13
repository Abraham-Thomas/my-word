# 聊一聊MVVM

**[推荐链接](https://juejin.im/post/5b3a3a44f265da630e27a7e6)**

### 传统的MVP设计模型：

![](/images/MVP.png)

- Model层：数据层；
- Presenter层：呈现层，业务逻辑相关的控制器
- View层：视图层 

首先，视图层发出一个事件，交给控制器，控制器要么反馈给数据层（Ajax）获取数据，要么直接操作DOM（根据视图层发送的一些事件）。可以看出Presenter层是MVP中最核心的一层，Model层非常边缘化。基于MVP模型，大量代码写在Presenter层，而且大部分代码在进行DOM操作



### NVVM模型：

![](/images/NVVM.png)

这种设计模型里也有Model层和View层，取代presenter层的是ViewModel层，而这一层并不需要我们来编写，vue框架已经为我们准备好了（感谢尤雨溪大神和他的团队们），所以我们只需要关注其他两层就好。ViewModel层会监听到数据层的数据改变，它能帮助我们实时改变视图层，同时也能监听到视图层一些事件触发，去调用逻辑代码执行。 所以，为了改变View层，我们只需要把精力放到Model层就好。



### MVVM双向绑定的原理：

数据驱动页面。Object.defineProperty，所有的MVVM框架都是用这个ES5的API实现双向数据绑定。

它发挥了哪些作用？

监听data变化，通过回调函数执行view与data的关联关系，而view通过input事件反馈到data。

它的原理？

**Object.defineProperty()** 会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性， 并返回这个对象。configuirable、enumerable、writable、value、

**get** ，一个给属性提供 getter 的方法，如果没有 getter 则为 `undefined`。当访问该属性时，该方法会被执行，方法执行时没有参数传入，但是会传入`this`对象（由于继承关系，这里的`this`并不一定是定义该属性的对象）。

**set** ，一个给属性提供 setter 的方法，如果没有 setter 则为 `undefined`。当属性值修改时，触发执行该方法。该方法将接受唯一参数，即该属性新的参数值。

而MVVM就是通过get和set方法进行双向数据绑定。

**object.defineProperty()与reflect.defineProperty()的区别（后者属于ES6）？** 

后者返回一个布尔值，前者返回一个新的（或修改后的）对象。随着规范的进展，前者的属性和方法会逐渐迁往后者。







