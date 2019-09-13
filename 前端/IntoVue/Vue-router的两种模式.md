# Vue-router的两种模式

## 为什么会有这两种？

hash 模式和 history 模式都属于浏览器自身的特性，Vue-Router 只是利用了这两个特性（通过调用浏览器提供的接口）来实现前端路由。

就这样。

**hash**：即地址栏URL中的`#`，而不是密码学里面的散列运算。比如这个 URL：`http://www.abc.com/#/hello`，hash 的值为 `#/hello`。它的特点在于：hash 虽然出现在 URL 中，但不会被包括在 HTTP 请求中，对后端完全没有影响，因此改变 hash 不会重新加载页面。

**history**：利用了 HTML5 History Interface 中新增的 `pushState()` 和 `replaceState()`方法（需要特定浏览器支持）。这两个方法应用于浏览器的历史记录栈，在当前已有的 `back`、`forward`、`go` 的基础之上，它们提供了对历史记录进行修改的功能。只是当它们执行修改时，虽然改变了当前的 URL，但浏览器不会立即向后端发送请求。

意义就在于，不管使用哪种方式，改变视图的同时都不会像后端发送请求。

一般场景下，hash 和 history 都可以，除非刻意在乎`颜值`这种东西，`#` 符号夹杂在 URL 里看起来确实有些不太美丽。



## 图解+源码实现

![图解](/images/HistoryApi.png)

```javascript
<a class="api a">a.html</a>
<a class="api b">b.html</a>

<script type="text/javascript">
    // 注册路由
    document.querySelectorAll('.api').forEach(item=>{
      item.addEventListener('click',(e)=>{
        e.preventDefault();
        let link=item.textContent;
        window.history.pushState({name:'api'},link,link);
      },false)
    });

    // 监听路由
    window.addEventListener('popstate',(e)=>{
      console.log({
        location:location.href,
        state:e.state
      });
    },false)
</script>
```

![](/images/hash.png)

```javascript
// 注册路由
    document.querySelectorAll('.hash').forEach(item=>{
      item.addEventListener('click',(e)=>{
        e.preventDefault();
        let link=item.textContent;
        location.hash=link;
      },false)
    });

    // 监听路由
    window.addEventListener('hashchange',(e)=>{
      console.log({
        location:location.href,
        hash:location.hash
      });
    },false)
```



## 小差异

当然差异，总还是有的。

根据 Mozilla Develop Network的介绍，调用 history.pushState()相比于直接修改 hash，存在以下优势：

- pushstate() 设置的新URL可以是与当前URL同源的任意URL，而hash只可修改`#`后面的部分，因此只能设置与当前URL通文档的URL。
- pushstate() 设置的新URL可以与当前的URL一模一样，这样也会把记录添加到栈中，而hash设置的新值必须与原来的URL不一样才会被添加。
- pushstate() 通过stateObject 参数可以添加任意类型的数据到记录中，而hash只可添加短字符串。
- pushstate()可额外设置title属性供后续使用。

不过这种history模式**要玩好**（官网原话），还需要后台配置支持。因为我们的应用是个单页客户端应用，如果后台没有正确的配置，当用户在浏览器直接访问 `http://oursite.com/user/id` 就会返回 404，这就不好看了。

上面的优势并不是说`history` 也不是样样都好。SPA 虽然在浏览器里游刃有余，但真要通过 URL 向后端发起 HTTP 请求时，两者的差异就来了。尤其在用户手动输入 URL 后回车，或者刷新（重启）浏览器的时候。

1. `hash` 模式下，仅 `#` 符号之前的内容会被包含在请求中，如 `http://www.abc.com`，因此对于后端来说，即使没有做到对路由的全覆盖，也不会返回 404 错误。
2. `history` 模式下，前端的 URL 必须和实际向后端发起请求的 URL 一致，如 `http://www.abc.com/book/id`。如果后端缺少对 `/book/id` 的路由处理，将返回 404 错误。

