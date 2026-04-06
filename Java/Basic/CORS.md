# CORS (Cross-Origin Resource Sharing) 详细解析

**CORS (跨源资源共享)** 是一种基于 HTTP 头的机制，它允许服务器标示除了它自己以外的其他源（域、协议或端口），使得浏览器允许这些源访问加载自己的资源。它在前后端分离的架构中非常关键。

---

## 1. 背景：同源策略 (Same-Origin Policy)

为了防止恶意网站窃取数据，浏览器默认实施“同源策略”。
**同源**指的是三个核心要素必须完全一致：
* **协议** (Protocol)：例如 `http://` 或 `https://`
* **域名** (Domain)：例如 `www.example.com`
* **端口** (Port)：例如 `80` 或 `443`

如果前端运行在 `http://localhost:8080`，而后端 API 部署在 `http://api.example.com`，这就不满足同源策略，产生的情况就叫**跨域**。浏览器默认会拦截这种跨域请求的响应数据，以保护用户安全。

## 2. 传统 Session/Cookie 的困境

在传统的基于 Session 的认证中，身份凭证存在浏览器的 Cookie 里。浏览器的安全机制决定了：**Cookie 通常只能在同源的请求中自动携带**。
一旦发生跨域，传递和共享 Cookie 会遇到非常严格的限制（前端需要配置 `withCredentials`，后端也要做复杂的响应头配置），这在现代分布式系统中显得非常笨重。

## 3. CORS 是如何工作的？

CORS 机制允许服务器通过在 HTTP 响应头中添加特定的字段（如 `Access-Control-Allow-Origin`），明确告诉浏览器：“我允许这个不同源的前端来访问我的数据”。



对于会修改服务器数据的复杂请求（比如带有自定义 Header 的 `POST`、`PUT` 请求），CORS 的工作流程如下：
1.  **预检请求 (Preflight)：** 浏览器在发送实际请求前，会先悄悄发一个 **OPTIONS** 请求给服务器，询问服务器是否允许跨域，以及允许哪些 HTTP 方法和 Header。
2.  **服务器授权：** 服务器如果同意，会在 OPTIONS 的响应头中返回允许的源（Origin）、方法和 Header 等信息。
3.  **实际请求：** 浏览器确认服务器允许后，才会真正发送携带数据的 API 请求。

## 4. 为什么说 JWT 对跨域（CORS）更友好？

因为在标准实践中，JWT 通常被放在 HTTP 请求的 `Authorization` 头部（Header）中，格式如下：

```http
Authorization: Bearer <token>
```

**它完全脱离了浏览器的 Cookie 机制**。只要后端的 CORS 配置允许前端跨域访问（即允许前端向后端发送请求，并且 `Access-Control-Allow-Headers` 允许携带 `Authorization` 字段），前端就可以轻松地将 JWT 塞进请求头里发给任何域名的后端。这使得 JWT 完美适配前后端分离的架构以及调用第三方 API 的场景。