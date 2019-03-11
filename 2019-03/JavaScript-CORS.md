# JavaScript 的同源策略及其"CORS"跨域方案
---
# 文章大纲
- [同源策略](#同源策略)
  - [同源是什么？](#同源是什么？)
  - [如何跨源，以及场景应用](#如何跨源，以及场景应用)
    - [源的更改](#源的更改)
    - [跨源网络访问](#跨源网络访问)
    - [跨源脚本API访问](#跨源脚本API访问)
    - [跨源数据存储访问](#跨源数据存储访问)
- [了解CORS](#了解CORS)
  - [CORS是什么？](#CORS是什么？)
  - [CORS功能概述](#CORS功能概述)
  - [CORS关于Cookie](#CORS关于Cookie)
  - [CORS的简单请求](#CORS的简单请求)
  - [CORS预检请求又是什么？](#CORS预检请求又是什么？)
- [其他](#其他)
- [参考](#参考)

# 同源策略

## 同源是什么？

在web浏览器中，**同源策略** 限制了从同一个源加载的文档或脚本如何与来自另一个源的资源进行交互。这是一个用于隔离潜在恶意文件的重要安全机制。

如果两个页面的 

- 1.**协议** 
- 2.**端口**（如果有指定）
- 3.**域名**

三者都相同，则两个页面具有相同的源。

---

举例说明 `http://store.example.com/dir/page.html` 同源检测的示例:

| URL | 结果     |  原因   |
| -----   | -----:  | :----:  |
| `http://store.example.com/index.html`| 成功|    |
| `http://store.example.com/dir/another.html`|成功|    |
| `https://store.example.com/index.html`|失败|不同协议 ( https和http )|
| `http://store.example.com:81/index.html` | 失败|不同端口 ( 81和80)|
| `http://news.example.com/index.html` | 失败|不同域名 ( news和store )|
| `http://example.com/index.html` | 失败|不同域名 (store是一个单独的自域)|


## 如何跨源，以及场景应用

以下为**4种**可以遇到的跨源

### 源的更改

页面可能会因某些限制而改变他的源。

设置 `document.domain` 的值，为其当前域或其当前域的父域。

> 场景 `http://store.example.com/dir/page.html` 文档中的一个脚本执行以下语句 `document.domain = "company.com"` 即可通过同源检测

### 跨源网络访问

同源策略控制了不同源之间的交互。

使用 `CORS` 允许跨源访问。

> 场景 由浏览器发起的跨域 HTTP 请求 (这个大家接触的最多)

### 跨源脚本API访问

Javascript的APIs中，允许文档间直接相互引用。但是当两个文档的源不同时，一些引用方式将对 API对象的访问添加限制

可以使用`window.postMessage`

> 场景 使用 `<iframe>` 嵌套的时候，父子页面的通信

### 跨源数据存储访问

存储在浏览器中的数据，如localStorage和IndexedDB，以源进行分割。每个源都拥有自己单独的存储空间，一个源中的Javascript脚本`不能`对属于其它源的数据进行`读写操作`。

> 场景 null

# 了解CORS

## CORS是什么？

MDN的网站给出了这样的**2种**解释：

> CORS （Cross-Origin Resource Sharing，跨域资源共享）是一个系统，它由一系列传输的HTTP头组成，这些HTTP头决定浏览器是否阻止前端 JavaScript 代码获取跨域请求的响应。 CORS 给了web服务器这样的权限，即服务器可以选择，允许跨域请求访问到它们的资源。

> 跨域资源共享(CORS) 是一种机制，它使用额外的 HTTP 头来告诉浏览器  让运行在一个 origin (domain) 上的Web应用被准许访问来自不同源服务器上的指定的资源。当一个资源从与该资源本身所在的服务器不同的域、协议或端口请求一个资源时，资源会发起一个跨域 HTTP 请求。

实际上着**两种**解释都**一样**。我给出这样的理解：CORS赋予服务端（通常所说的后端）一个能力，自己控制哪些浏览器的请求可以访问到它的资源，来解决跨域问题。

附：所有的 `CORS` 头

| HTTP头        | 功能   |
| --------   | :-----  |
| `Access-Control-Allow-Origin`| 指示请求的资源能共享给哪些域。 |
| `Access-Control-Allow-Credentials`|指示当请求的凭证标记为 true 时，是否响应该请求。|
| `Access-Control-Allow-Headers`     | 用在对预请求的响应中，指示实际的请求中可以使用哪些 HTTP 头。 |
| `Access-Control-Allow-Methods`     | 指定对预请求的响应中，哪些 HTTP 方法允许访问请求的资源。|
| `Access-Control-Expose-Headers`     | 指示哪些 HTTP 头的名称能在响应中列出。|
| `Access-Control-Max-Age`     | 指示预请求的结果能被缓存多久。 |
| `Access-Control-Request-Headers`     | 用于发起一个预请求，告知服务器正式请求会使用那些 HTTP 头。|
| `Access-Control-Request-Method`     | 用于发起一个预请求，告知服务器正式请求会使用哪一种 HTTP 请求方法。|
| `Origin`     | 指示获取资源的请求是从什么域发起的。|

## CORS功能概述

功能概述 **TL;DR**

> 规范要求，对那些可能对服务器数据产生副作用的 HTTP 请求方法（特别是 GET 以外的 HTTP 请求，或者搭配某些 MIME 类型的 POST 请求），浏览器必须首先使用 OPTIONS 方法发起一个预检请求（preflight request），从而获知服务端是否允许该跨域请求。服务器确认允许之后，才发起实际的 HTTP 请求。在预检请求的返回中，服务器端也可以通知客户端，是否需要携带身份凭证（包括 Cookies 和 HTTP 认证相关数据）。

一句话概述：非简单请求时，会先发送预检请求，允许后再发送实际请求


附：node-express框架下，服务端的跨域设置

``` javascript
app.all('*', function (req, res, next) {
  res.header('Access-Control-Allow-Origin', '*');
  res.header('Access-Control-Allow-Headers', 'Content-Type');
  res.header('Access-Control-Allow-Methods', '*');
  next();
});
```

## CORS关于Cookie

CORS请求默认不发送Cookie和HTTP认证信息，如果想要知道用cookie就要注意**3点**

> 1. Client端 `new XMLHttpRequest()` 中 `withCredentials` 设置为 `true`
> 2. Serive端 `HTTP头` `Access-Control-Allow-Credentials` 设置为 `true`
> 3. Serive端 `HTTP头` `Access-Control-Allow-Origin` 不能设为星号，必须指定明确的、与请求网页一致的域名

所以上面的例子要想发送cookie
``` javascript
// service端
`res.header('Access-Control-Allow-Credentials', true);`
`res.header('Access-Control-Allow-Origin', '具体的域名');`
// client端
Jquery `ajax()` `xhrFields: {withCredentials: true}`
Axios `axios.defaults.withCredentials = true`
```

## CORS的简单请求

上面讲了**简单请求**，但那些才是简单请求呢？我们称：若不会触发 CORS 的预检请求，称这样的请求为“简单请求”

以下为简单请求：

>1. HTTP Method 组成只能是以下几种
>- GET
>- POST
>- HEAD
>2. HTTP Headers 组成
>- Accept
>- Accept-Language
>- Content-Language
>- Last-Event-ID
>- Content-Type 只包含以下类型 (form表单请求)
>   - application/x-www-form-urlencoded
>   - multipart/form-data
>   - text/plain

注：只有同时满足以上两个条件时,才是简单请求,否则为非简单请求

## CORS预检请求又是什么？

前面说了这么多**预检请求**，我们来讲一讲什么是预检请求：
如果我们在client端发送请求时，例如：

``` javascript
// 原生
var invocation = new XMLHttpRequest();

invocation.setRequestHeader('X-EXAMPLE', 'xixihaha');
invocation.setRequestHeader('Content-Type', 'application/xml');

// axios
axios.defaults.headers['X-EXAMPLE'] = 'xixihaha';
axios.defaults.headers['Content-Type'] = 'application/xml';

```
POST 请求发送一个 XML 文档，该请求包含了一个自定义的请求首部字段（X-EXAMPLE: xixihaha）。另外，该请求的 Content-Type 为 application/xml。因此，该请求需要首先发起“预检请求”。

server端 的HTTP头设置

``` javascript
Access-Control-Allow-Origin: '具体的域名'
Access-Control-Allow-Methods: POST, GET, OPTIONS // 可包含的参数
Access-Control-Allow-Headers: X-PINGOTHER, Content-Type // 允许的首部字段
Access-Control-Max-Age: 86400
```

非简单请求和简单请求无异，如果浏览器的预检请求被服务器**接受**，则发送实际请求，**未被接受**则拒绝请求。

# 其他

跨域不止于此

> `JSONP`
> 动态创建script标签，然后利用script的src 不受同源策略约束来跨域获取数据

```javascript
function addScriptTag() {
    var script = document.createElement("script");
    script.src = "http://foo.example?callback=handleResponse";
    document.body.appendChild(script);
}

function handleResponse() {
  console.log('跨域数据');
};
```

**以下跨域方案不做过多解释**

> 上文提到的 `postMessage()`

> `nginx`转发，即架设服务器代理


> `window.name` 

# 参考
[阮一峰的网络日志][1]

[MDN web docs CORS][2]

[1]: http://www.ruanyifeng.com/blog/2016/04/same-origin-policy.html

[2]: https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS
