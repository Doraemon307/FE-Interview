### 1. 浏览器页面有哪三层构成，分别是什么，作用是什么?
构成：结构层、表示层、行为层 
分别是：HTML、CSS、JavaScript 
作用：HTML实现页面结构，CSS完成页面的表现与风格，JavaScript实现一些客户端的功能与业务。

### 2. Doctype作用? 严格模式与混杂模式如何区分？它们有何意义?
- 声明位于文档中的最前面，处于标签之前。用于告知浏览器的解析器，用什么文档类型规范来解析这个文档。
- 严格模式的排版和JS 运作模式是以该浏览器支持的最高标准运行。
- 在混杂模式中，页面以宽松的向后兼容的方式显示。模拟老式浏览器的行为以防止站点无法工作。
- DOCTYPE不存在或格式不正确会导致文档以混杂模式呈现。

### 3. H5移除了哪些元素？
1. 纯表现元素：`<basefont>  <font>  <u>  <tt>  <strike>  <center>  <big>`
2. 框架集：`<frame>  <frameset>  <noframe>`

### 4. 浏览器内核？
- IE: trident 内核
- Firefox: gecko 内核
- Safari: webkit 内核
- Opera / Chrome: Blink内核

### 5. cookie、session、localStorage、sessionStorage的区别？
- cookie & session的区别：
1. cookie和session都可用来存储用户信息，cookie存放于客户端，session存放于服务器端
2. cookie存放于客户端有可能被窃取，所以cookie一般用来存放不敏感的信息，比如用户设置的网站主题，敏感的信息用session存储，比如用户的登陆信息。

- cookie，localstorage，sessionstorage的区别：
1. 生命周期：
Cookie：可设置失效时间，否则默认为关闭浏览器后失效
Localstorage：除非被手动清除，否则永久保存
Sessionstorage：仅在当前网页会话下有效，关闭页面或浏览器后就会被清除
2. 存放数据：
Cookie：4k左右
Localstorage和sessionstorage：可以保存5M的信息
3. http请求：
Cookie：每次都会携带在http头中，如果使用cookie保存过多数据会带来性能问题
Localstorage和sessionstorage：仅在客户端即浏览器中保存，不参与和服务器的通信
4. 易用性：
Cookie：需要程序员自己封装，原生的cookie接口不友好
Localstorage和sessionstorage：即可采用原生接口，亦可再次封装
5. 应用场景：
从安全性来说，因为每次http请求都回携带cookie信息，这样子浪费了带宽，所以cookie应该尽可能的少用，此外cookie还需要指定作用域，不可以跨域调用，限制很多，但是用户识别用户登陆来说，cookie还是比storage好用，其他情况下可以用storage，localstorage可以用来在页面传递参数，sessionstorage可以用来保存一些临时的数据，防止用户刷新页面后丢失了一些参数。

- localStorage、sessionStorage的区别：
1. localStorage：用于持久化的本地存储，数据永远不会过期，关闭浏览器也不会丢失。（除非手动清除）
2. sessionStorage：同一个会话中的页面才能访问并且当会话结束后数据也随之销毁。因此sessionStorage不是一种持久化的本地存储，仅仅是会话级别的存储

### 6. HTML语义化？
- 语义化：正确的标签包含了正确的内容，结构良好，便于阅读。
- 语义化标签：<header> <nav> <article> <session> <aside> <footer>
- 语义化的意义：
1. 易于用户阅读，样式丢失的时候能让页面呈现清晰的结构。
2. 有利于SEO，搜索引擎根据标签来确定上下文和各个关键字的权重。
3. 方便其他设备解析，如盲人阅读器根据语义渲染网页
4. 有利于开发和维护，语义化更具可读性，代码更好维护，与CSS3关系更和谐。

### 7. link和@import的区别?
- 都是外部导入css文件，导入格式如下：
` <link rel='stylesheet' rev='stylesheet' href='xx.css ' type='text/css'/> `
` @import url('xx.css'); `
- 区别：
1. link 是 XHTML 标签，除了加载 CSS 外，还可以定义 RSS 等其他事务；@import 属于 CSS 范畴，只能加载 CSS 。
2. link 引用 CSS 时，在页面载入时同时加载； @import 需要页面网页完全载入以后加载。
3. link 是 XHTML 标签，无兼容问题； @import 是在 CSS2.1 提出的，低版本的浏览器不支持。
4. link 支持使用 Javascript 控制 DOM 去改变样式；而 @import 不支持。

### 8. 'data-'属性的作用是什么？
`data-`为H5新增的为前端开发者提供自定义的属性，这些属性集可以通过对象的 `dataset` 属性获取，不支持该属性的浏览器可以通过 `getAttribute` 方法获取 :

需要注意的是：`data-`之后的以连字符分割的多个单词组成的属性，获取的时候使用驼峰风格。 所有主流浏览器都支持 data-* 属性。即：当没有合适的属性和元素时，自定义的 data 属性是能够存储页面或 App 的私有的自定义数据。

### 9. 浏览器内核的理解？
主要分成两部分：渲染引擎(layout engineer或 Rendering Engine) 和 JS 引擎。
- 渲染引擎：负责取得网页的内容（HTML、 XML 、图像等等）、整理讯息（例如加入 CSS 等），以及计算网页的显示方式，然后会输出至显示器或打印机。浏览器的内核的不同对于网页的语法解释会有不同，所以渲染的效果也不相同。所有网页浏览器、电子邮件客户端以及其它需要编辑、显示网络内容的应用程序都需要内核。
- JS引擎则：解析和执行 javascript 来实现网页的动态效果。
最开始渲染引擎和JS引擎并没有区分的很明确，后来 JS 引擎越来越独立，内核就倾向于只指渲染引擎。

### 10. 表单提交中Get和Post方式的区别？
1. get 是从服务器上获取数据， post 是向服务器传送数据。
2. get 是把参数数据队列加到提交表单的 ACTION 属性所指的 URL 中，值和表单内各个字段一一对应，在 URL 中可以看到。 post 是通过 HTTP post 机制，将表单内各个字段与其内容放置在 HTML HEADER 内一起传送到 ACTION 属性所指的 URL 地址 , 用户看不到这个过程。
3. 对于 get 方式，服务器端用 Request.QueryString 获取变量的值，对于 post 方式，服务器端用 Request.Form 获取提交的数据。
4. get 传送的数据量较小，不能大于 2KB 。 post 传送的数据量较大，一般被默认为不受限制。但理论上， IIS4 中最大量为 80KB ， IIS5 中为 100KB 。
5. get 安全性低， post 安全性较高。

### 11. HTML5应用程序缓存和浏览器缓存有什么区别？
- HTML5 引入了应用程序缓存，这意味着 web 应用可进行缓存，并可在没有因特网连接时进行访问。
应用程序缓存为应用带来三个优势：
1. 离线浏览 - 用户可在应用离线时使用它们
2. 速度 - 已缓存资源加载得更快
3. 减少服务器负载 - 浏览器将只从服务器下载更新过或更改过的资源。
- 应用程序缓存是 HTML5  的重要特性之一，提供了离线使用的功能，让应用程序可以获取本地的网站内容，例如 HTML 、 CSS 、图片以及 JavaScript 。这个特性可以提高网站性能，它的实现借助于 manifest 文件，如下：
```
<!doctype html>
<html manifest="example.appcache">
.....
</html>
```
与传统浏览器缓存相比，它不强制用户访问的网站内容被缓存。

### 12. HTML5媒体标签？
1. 音频 <audio>
2. 视频 <video>
3. 其他标签：
- <embed> 标签定义嵌入的内容，比如插件。
- <source> 对于定义多个数据源很有用。
- <track> 标签为诸如 video 元素之类的媒介规定外部文本轨道。 用于规定字幕文件或其他包含文本的文件，当媒介播放时，这些文件是可见的。
