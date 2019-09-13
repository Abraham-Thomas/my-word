# 浏览器缓存

主要是针对浏览器的数据持久化存储，用于在本地保存数据并进行快速读取以避免重复资源请求的传输机制的统称。好的缓存可以避免重复的网络资源请求并让浏览器快速地响应用户操作，提高页面速度。

Chrome浏览器的调试模式，Application左侧列举了8种缓存机制： HTTP文件缓存、LocalStorage、SessionStorage、indexDB、WebSQL、Cookie、CacheStorage、Application Cache，（Flash已淘汰，为什么?）。

------

#### HTTP文件缓存

一种基于HTTP协议的浏览器端文件缓存机制。在文件重复请求的情况下，浏览器可以根据HTTP响应的协议头信息判断是从服务器端请求文件还是从本地读取，Chrome控制台Application的Frames可以查看浏览器的HTTP文件资源缓存列表内容。浏览器和服务器判断是否使用浏览器端文件缓存过程：

![HTTP文件缓存判断机制流程](/images/HTTP文件缓存判断机制流程.jpg)

#### localStorage

一种H5的本地缓存方案，目前主要用于浏览器端保存体积较大的数据（如AJAX返回结果等），localStorage在不同浏览器中长度限制各有不同，它常用的API较少，核心方法：setItem() 、getItem() 、removeItem() 、clear()。

大小限制指的是单个域名下localStorage的大小，所以localStorage中不宜存放过多的数据，并且使用之后最好移除不再使用的数据。

此外，localStorage只支持简单数据类型的读取，为了方便localStorage读取对象格式的内容，通常需要进行一层安全封装再引入使用。



```
let rkey = /^[0-9A-Za-z@-]*$/;
let store;

//转换对象
function init() {
	if (typeof store === 'undefined') {
		store = window['localStorage'];
	}
	return true;
}

//判断localStorage的key值是否合法
function isValidKey(key) {
	if (typeof key !== 'String') {
		return false;
	}
	return rkey.test(key);
}

exports = {
	//设置localStorage单条记录
	set (key, value) {
		if (isValidKey(key) && init()) {
			try {
				value += '';
				store.setItem(key, value);
				success = true;
			} catch (e) {}
		}
		return success;
	},

​    //读取localStorage单条记录
​    get (key) {
​        if (isValiKey(key) && init()) {
​            try {
​                return store.getItem(key);
​            } catch (e) {}
​        }
​        return null;
​    },

​    //移除localStorage单条记录
​    remove (key) {
​        if (isValiKey(key) && init()) {
​            try {
​                store.removeItem(key);
​                return true;
​            } catch (e) {}
​        }
​        return false;
​    },

​	//清除localStorage所有记录
​    clear () {
​        if (init()) {
​            try {
​                for (let key in store) {
​                    store.removeItem(key);
​                }
​                return true;
​            } catch (e) {}
​        }
​        return false;
​    }

};

module.exports = exports;
```

------

#### sessionStorage

和localStorage功能类似，API完全相同，但sessionStorage在浏览器关闭时会自动清空。实际项目中sessionStorage的使用场景相对较少。

------

#### Cookie

或Cookies，为了辨别用户身份或session跟踪而存储用户在浏览器端的数据。Cookie信息一般会通过HTTP请求发送到服务器端。一条Cookie记录主要由键、值、域、过期时间和大小组成，一般用于保存用户的网站认证信息。浏览器中的Cookie的最大长度和单个域名支持的Cookie个数由浏览器的不同来决定。

和localStorage类似，不同域名之间的Cookie信息也是独立的，如需设置共享，则可以在被共享域名的服务器端设置Cookie的path和domain来实现，如Koa Web框架下可用如下方法：

```
this.cookies.set('username', 'abra', {
	domain: '.domain.com',
	path: '/'
});
```

Cookie分两种，Session Cookie和持久型Cookie，前者关闭浏览器窗口就会消失，后者会将信息保存到硬盘上，下次打开依然有效，超过设定时间才会被清空。

Cookie设置中有个HttpOnly参数，使用document.cookie读取不到该类型Cookie，只能通过HTTP请求头发送到服务器端进行读写，这样就避免了服务端的Cookie被前端JS修改，保证了安全性。

以统一封装来达到方便读取和操作Cookie：

```
exports = {
	//获取单条cookie记录
	get (n) {
		let m = document.cookie.match(new RegExp("(^|)"+n+"=([^;]*)(;|$)"));
		return !m ? "":decodeURIComponent(m[2]);
	},

//设置单条cookie记录
set (name, value, domain, path, hour) {
	let expire = new Date();
	expire.setTime(expire.getTime() + (hour?360000*hour:30*24*60*60*1000));
	document.cookie = name + "=" + value + ";" + "expire=" + expire.toGMTString()+
	";path=" (path ? path :"/")+ ";" + (domain ? ("domain=" + domain + ";") : "");
},

//删除单条cookie记录
del (name, domain, path) {
	document.cookie = name + "=; expires=Mon, 26 Jul 1997 06:00:00 GMT; path="+ (path ?
		path :"") + "; " + (domain ? ("domain=" + domain + ";") : "");
},

//清除document.cookie
clear () {
	let rs = document.cookie.match(new RegExp("([^;][^;]*)(?=(=[^;]*)(;|$))", "gi"));
	//删除所有cookie
	for (let i in rs) {
		document.cookie = rs[i] + "=;expires=Mon, 26 Jul 1997 06:00:00 GMT; path=/; ";
	}
}

};
module.exports = exports;
```

