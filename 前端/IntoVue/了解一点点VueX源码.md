# 了解一点点VueX源码

Vuex，共享状态的数据存储，通过的说，就是多个页面共享一份数据，一旦一个页面导致数据变动，其他几个页面也应该保持更新。

先把官网的图盗过来：

![vuex](/images/vuex.png)

## 干嘛要用它？

Vue.js 已经为我们提供了响应式的 `data` 属性 -- 这是一种开箱即用的处理状态的强大方式，也能向子组件中传递数据。

```javascript
<template>
  <div class="MyComponent">
    <some-component :some-value="someValue"></some-component>
  </div>
</template>


export default {
  name: 'MyComponent',
  data() {
    return {
      someValue: 'Hello World',
    };
  },
}

```

如果在开发一个相当简单的应用，或者要做的全部事情就是利用 Vue.js 的某些魔力来替换应用的一些（原本是服务端渲染）部分，那么确实根本用不着 Vuex。

反之，如果要开发一个大体量的单页应用，你就可能遇到在应用中的两处迥然不同的地方需要同样一份数据的状况。这就是像 Vuex 这样的集中式状态管理工具时不时起到大作用的时候了。

**状态管理是 Vue 组件解耦的重要手段。**

同时，他也要遵循一些规则：

1. 应用层级的状态应该集中到单个 store 对象中
2. 提交 mutation 是更改状态的唯一方法，并且这个过程是同步的
3. 异步逻辑都应该封装到 action 里面



**来自“小生方勤”大佬的分析：** 



## 注入 Vue 生命周期的过程

引入vuex。

```vue
import Vue from 'vue';
import Vuex from 'vuex';

Vue.use(Vuex);
```

**Vue.use()做了啥**

安装 Vue.js 插件。如果插件是一个对象，必须提供 install 方法。如果插件是一个函数，它会被作为 install 方法。install 方法调用时，会将 Vue 作为参数传入。

让我们从源码的角度看问题：

```javascript
function initGlobalAPI (Vue) {
  ......
  initUse(Vue);
  initMixin$1(Vue); // 下面讲 Vue.mixin 会提到
  ......
}

function initUse (Vue) {
  Vue.use = function (plugin) {
    var installedPlugins = (this._installedPlugins || (this._installedPlugins = []));
    /* 判断过这个插件是否已经安装 */
    if (installedPlugins.indexOf(plugin) > -1) {
      return this
    }
    var args = toArray(arguments, 1);
    args.unshift(this);
    /* 判断插件是否有 install 方法 */
    if (typeof plugin.install === 'function') {
      plugin.install.apply(plugin, args);
    } else if (typeof plugin === 'function') {
      plugin.apply(null, args);
    }
    installedPlugins.push(plugin);
    return this
  };
}
```

Vue 源码在 `initGlobalAPI` 入口方法中调用了 `initUse (Vue)` 方法，这个方法定义了 `Vue.use()`需要做的内容。

这段代码主要做了两件事情：

1. 一件是防止重复安装相同的 plugin
2. 另一件是初始化 plugin

**插件的 install 方法**

看完以上源码，我们知道插件（Vuex）需要提供一个 `install` 方法。那么我们看看 Vuex 源码中是否有这个方法。结果当然是有的：

```javascript
/* 暴露给外部的 install 方法 */
function install (_Vue) {
  /* 避免重复安装（Vue.use 内部也会检测一次是否重复安装同一个插件）*/
  if (Vue && _Vue === Vue) {
    {
      console.error(
        '[vuex] already installed. Vue.use(Vuex) should be called only once.'
      );
    }
    return
  }
  Vue = _Vue;
  /* 将 vuexInit 混淆进 Vue 的 beforeCreate(Vue2.0) 或 _init 方法(Vue1.0) */
  applyMixin(Vue);
}

```

这段代码主要做了两件事情：

1. 一件是防止 Vuex 被重复安装
2. 另一件是执行 `applyMixin`，目的是执行 `vuexInit` 方法初始化 Vuex

接下来 我们看看 `applyMixin(Vue)` 源码：

```
/* 将 vuexInit 混淆进 Vue 的 beforeCreate */
function applyMixin (Vue) {
  var version = Number(Vue.version.split('.')[0]);
  if (version >= 2) {
    Vue.mixin({ beforeCreate: vuexInit });
  } else {
    /* Vue1.0 的处理逻辑，此处省略 */
    ......
  }
  function vuexInit () {
    ......
  }
}
```

**vuexInit()**

我们在使用 Vuex 的时候，需要将 store 传入到 Vue 实例中去。

```
new Vue({
  el: '#app',
  store
});
```

但是我们却在每一个 vm 中都可以访问该 store，这个就需要靠 `vuexInit` 了。

```javascript
  function vuexInit () {
    const options = this.$options
    if (options.store) {
      /* 根节点存在 stroe 时 */
      this.$store = typeof options.store === 'function'
        ? options.store()
        : options.store
    } else if (options.parent && options.parent.$store) {
      /* 子组件直接从父组件中获取 $store，这样就保证了所有组件都公用了全局的同一份 store*/
      this.$store = options.parent.$store
    }
  }
```

通过这步的操作，我们就以在任意一个 vm 中通过 this.$store 来访问 Store 的实例。接下来我们反过来说说 Vue.mixin()。

**Vue.mixin()**

全局注册一个混入，影响注册之后所有创建的每个 Vue 实例。插件作者可以使用混入，向组件注入自定义的行为。**不推荐在应用代码中使用。** 

在 vue 的 `initGlobalAPI` 入口方法中调用了 `initMixin$1(Vue)` 方法:

```
function initMixin$1 (Vue) {
  Vue.mixin = function (mixin) {
    this.options = mergeOptions(this.options, mixin);
    return this
  };
}

```

