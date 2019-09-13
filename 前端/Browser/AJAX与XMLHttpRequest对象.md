# AJAX与XMLHttpRequest对象

Ajax，简单来说，就是无需刷新页面即可从服务器取得数据，但不一定是XML数据。

它不是编程语言，而是一种在无需重新加载整个网页的情况下能够更新部分内容的技术。

IE5是第一款引入XHR对象的浏览器，但有三个不同版本的XHR对象。而后，IE7+，Fierfox，Chrome等都支持原生的XHR对象。如果只使用原生，可以不用下面else if中的代码：

```javascript
function createXHR() {
	if (typeof XMLHttpRequest != "undefined") {
		return new XMLHttpRequest();
	} else if (typeof ActiveXObject != "undefined") {
		if (typeof arguments.callee.activeXString != "string") {
			var versions = ["MSXML2.XMLHttp.6.0", "MSXML2.HMLHttp.3.0",
							"MSXML2.XMLHttp"],
				i, len;


		for (i=0,len=versions.length; i<len; i++) {
			try {
				new ActiveXObject(versions[i]);
				arguments.callee.activeXString = versions[i];
				break;
			} catch (ex) {
				//跳过
			}
		}
	}

	return new ActiveXObject(arguments.callee.activeXString);
  } else {
	throw new Error("No XHR object available.");
  }

}


```

先检测XHR对象，再检测ActiveX对象，都无，就抛异常。下面创建XHR对象：

```
var xhr = createXHR();
```

**不过，对于曾经的IE5、6，现在基本忽略了。** 



#### XHR用法：

要使用XHR对象，需调用open()，它有三个参数：要发送的请求类型（get,post)，URL（相对于执行代码的当前页面，也可使用绝对路径），是否一步发送的布尔值(true表示异步发送，多数情况我们也都是异步)，例如：

```
xhr.open("get", "example.php", false)；
```

调用open()并不会真正发送请求，只是启动一个请求以备发送。

还需要send()，它只接收一个参数——请求主体发送的数据。如果不需要数据，必须传入null，否则有的浏览器不兼容。XHR还有以下属性：

- responseText：作为响应主体被返回的文本。

- ​responseXML： 若响应的内容类型为"text/html"或"application/xml"，这个属性中将保存包含着响应数据的XMLDOM文档。

- status：响应的HTTP状态。(200, 304, 400)

- statusText：HTTP状态的说明。

- readyState：请求/响应过程中当前活动阶段，如下

  0：未初始化。尚未调用open()。

  1：启动。已调用open()，但未调用send()。

  2：发送。已调用send()，但未接收到响应。

  3：接收，已接收到部分响应数据。

  4：完成，已经接收到全部响应数据。（通常我们只对这个值感兴趣）

通过检测status（状态码）来决定下一步操作，不要依赖statusText，因为后者再跨浏览器使用时不太可靠。

只要readyState属性的值由一个变成另一个值，都会触发readystateChange事件，可以利用这个事件来检测每次状态变化后readyState的值：

```javascript
var xhr = createXHR();
xhr.onreadystateChange = function() {
	if (xhr.readyState == 4) {
		if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304) {
			alert(xhr.respenseText);
		} else {
			alert("Request was unsuccessful:" + xhr.status);
		}
	}
};
xhr.open("get", "example.txt", true);
xhr.send(null);
```

上面是用DOM0级方法为XHR田间事件处理程序，因为并非所有浏览器都支持DOM2级。与其他事件处理程序不同，这里没有向onreadystatechange事件处理程序中传递event对象；必须通过XHR对象本身来确定下一步该怎么做。另外，在接收到响应之前还可以调用abort()方法来取消异步请求：

```
xhr.abort();
```

------

#### HTTP头部信息

每个HTTP请求和响应都会带有相应的头部信息。XHR对象提供了操作这两种头部（请求和响应）信息的方法。

setRequestHeader()，设置自定义的请求头部信息，该方法接受两个参数——头部字段名称和头部字段的值。建议使用自定义头部字段名称，不要使用浏览器正常发送的字段名称，可能影响服务器响应。此方法必须在open()和send()之间调用，否则无效。

调用getResponseHeader()则传入头部字段名称，而调用getAllResponseHeaders()通常返回格式化后的所有头部字段名称。

------

#### GET请求

向服务器查询某些信息。必要时，可以将查询字符串参数追加到URL的末尾，以便将信息发送给服务器。就XHR而言，传入位于open()方法的URL末尾的查询字符串必须经过正确的编码才行，键值对都必须用&分隔：

```
xhr.open("get", "example.php?name1=value&name2=value2", true);
```

------

#### POST请求

向服务器发送应该保存的数据。如果需要将页面中表单的数据进行序列化，可用serialize()函数创建字符串：

```
var form = document.getElementById("user-info");
xhr.send(serialize(form));
```

