# DOMReady

以前，我们会把JS逻辑写在window.onload中，以防止DOM树还没有建立完成就开始对节点进行操作，从而导致错误，而对于很多实际应用来说我们越早进入对DOM的干涉越好，比如我们进行客户端检测、事件绑定DOM操作。

DOMReady，可以满足提前绑定事件的需求。

**Dom文档加载的步骤：**

1. 解析html结构；
2. 加载外部脚本和样式表文件；
3. 解析并执行脚本；
4. dom树构建完成（DOMContentLoaded）；
5. 加载图片等外部文件；
6. 页面加载完毕。

**DOM ready、DOM Load、图片Load**

DOM ready：（也叫DOMContentLoaded ），在第4步完成后触发；
图片onload：是在第5步完成后触发；
页面onload：是第6步完成后触发。

**DOM Ready的实现策略**

1、支持DOMContentLoaded事件的，就使用它。

2、不支持的，可以使用Diego Perini发现的著名Hack兼容，原理大概是通过IE中的document.documentElement.doScroll('left')来判断DOM树是否创建完毕。



**JS中的实现**

```
function myReady (fn) {

    //现代浏览器对于DOMContentLoaded事件的处理
    if (document.addEventListener) {
        document.addEventListener("DOMContentLoaded", fn, false);
    } else {
        IEContentLoaded(fn);
    }

    //IE模拟DOMContentLoaded
    function IEContentLoaded (fn) {
        var d = window.document;
        var done = false;
        //只执行一次用户的回调函数init()
        var init = function () {
            if (!done) {
                done = true;
                fn();
            }
        };

    	//如果document的加载状态未就绪
    	(function () {
    		try {
    			//DOM树未创建之前调用doScroll会抛出错误
    			d.documentElement.doScroll('left');
    		} catch (e) {
    			//延迟再试一次
    			setTimeout(arguments.callee, 50);	//调用函数自身
    			return;		//实现递归
    		}
    		//没有错误就表示DOM树创建完毕，然后立马执行用户回调
    		init();
    	})();
    	
    	//监听document的加载状态
    	d.onreadystatechange = function () {
    		//如果用户是在domReady之后绑定的函数，就立马执行
    		if (d.readyState == 'complete') {
    			d.onreadystatechange = null;
    			init();
    		}
    	}	
    }

}

//调用上面的方法即可实现DOMready
myReady(function() {
    document.getElementById("header").style.color = "red";
});
```



**JQuery中的实现**

```
$(document).ready(function() {
	document.getElementById("header").style.color = "red";
});
```


