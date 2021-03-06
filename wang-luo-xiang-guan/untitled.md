---
description: 状态码30x的重定向
---

# HTTP的重定向

* **HTTP重定向**

  Response 3开头的状态码，location头字段表示重定向的位置。

  | 状态码 | 状态短语 | 状态含义 |
  | :--- | :--- | :--- |
  | 300 | Multiple Choices | 当请求的 URL 对应有多个资源时（如同一个 HTML 的不同语言的版本），返回这个代码时，可以返回一个可选列表，这样用户可以自行选择。通过 `Location` 头字段可以自定首选内容。 |
  | 301 | Moved Permanetly | 当前请求的资源已被移除时使用，响应的 `Location` 头字段会提供资源现在的 URL。直接使用 GET 方法发起新情求。 |
  | 302 | Found | 与 301 类似，但客户端只应该将 `Location` 返回的 URL 当做临时资源来使用，将来请求时，还是用老的 URL。直接使用 GET 方法发起新情求。 |
  | 303 | See Other | 用于在 PUT 或者 POST 请求之后进行重定向，这样在结果页就不会再次触发重定向了。 |
  | 304 | Not Modified | 资源未修改，表示本地缓存仍然可用。 |
  | 305 | Use Proxy | 用来表示必须通过一个代理来访问资源，代理的位置有 `Location`头字段给出 |
  | 306 | Switch Proxy | 在最新版的规范中，306 状态码已经不再被使用。最初是指“后续请求应使用指定的代理”。 |
  | 307 | Temporary Redirect | 与 302 类似，但是使用原请求方法发起新情求。 |
  | 308 | Permanent Redirect | 与 301 类似，但是使用原请求方法发起新情求。 |

  这边需要区分两个类别：**永久重定向**和**临时重定向**。

  1. 永久重定向：目标资源移到了新的URI，客户端请求使用新的URL，SEO会更新URL信息。
  2. 临时重定向：目标资源移到了新的URI，但客户端请求还是使用旧的URL，SEO会引用旧的URL信息。容易造成网页的资源劫持，比如当B网站302重定向到A，seo爬虫识别时对于A网站的内容显示的搜索结果却是B网站而不是真正的A网站。

  301与308都算永久重定向，它们的区别在于：**308状态码不允许浏览器将原本为 POST 的请求重定向到 GET 请求上，而301状态码重定向都是到GET请求上**。301与307同理。而 303与302类似， 但它只允许任意请求到 GET 的重定向，这是由于历史设计的原因。

* **HTML重定向**

  不操作服务端，通过 标签也可以进行页面的重定向。

  ```text
  <head>
    <meta http-equiv="Refresh" content="1; URL=https://bing.com/">
  </head>
  ```

  先设置http-equiv="Refresh"，content属性以数字开头，表示多少秒后重定向到指定页面。

* **JavaScript重定向**

  在客户端的执行环境可以使用window.location=”URL“，重定向到页面。

* **为什么重定向**

  重定向使用的场景主要有以下几点：

  1. **提高网站可达率**：如 `www.example.com` 和 `example.com`都可以访问到指定网站。
  2. **迁移到新的站点**：因为某些原因旧站点被废弃，但仍然希望之前已经存在的连接和收藏书签能够生效，这是可以使用重定向。
  3. **强制跳转 HTTPS**：当我们的网站支持 HTTPS 时，通常会强制使用 HTTPS，所以访问 HTTP 时需要做重定向跳转。
  4. **对于危险操作进行重定向**：类似编辑删除等危险操作，为了避免用户刷新时重复触发危险操作，我们可以将其重定向到临时的进度展示页，比如使用 303。对于耗时较长的请求也可以这么处理。

* **注意：301永久重定向会缓存**

  HTTP RFC 中规定 301 是一个可缓存的响应，所以浏览器会根据响应中的 HTTP 缓存头进行缓存。**如果我们没有提供明确的缓存头，浏览器就会默认永久缓存 301 响应，因为 301 是永久重定向的意思。**当我们后期修改重定向时可能不会生效，所以我们在使用301时需要特别注意，特别设置 `Cache-Control: no-store` 或 `Cache-Control: no-cache`。

