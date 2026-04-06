# JWT (JSON Web Token) 详细解析

## 什么是 JWT？
**JSON Web Token (JWT)** 是一种用于在网络应用环境间传递声明的、基于 JSON 的开放标准（RFC 7519）。它被设计为紧凑且安全的，特别适用于分布式站点的单点登录（SSO）和API的身份验证。



---

## 一、 JWT 的构成

一个 JWT 实际上是一个长字符串，由三个部分组成，这三个部分通过英文句号（`.`）连接在一起。它的整体结构如下：

`Header.Payload.Signature`

### 1. Header（头部）
头部通常由两部分信息组成，用于描述关于该 JWT 的最基本的信息：
* **`typ` (Type):** 声明令牌的类型，统一为 `JWT`。
* **`alg` (Algorithm):** 声明签名的算法，通常是 HMAC SHA256 (HS256) 或 RSA。

这两部分信息会被组成一个 JSON 对象，然后使用 **Base64Url** 编码生成 JWT 的第一部分：

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

### 2. Payload（负载/载荷）
负载是存放有效信息的地方，这些信息被称为“声明（Claims）”。声明分为三种类型：
* **Registered claims（注册声明）:** 预定义的声明，不是强制的但推荐使用。例如：`iss` (签发者), `exp` (过期时间), `sub` (主题) 等。
* **Public claims（公共声明）:** 可以由使用 JWT 的各方随意定义。
* **Private claims（私有声明）:** 用于在通信双方之间共享信息，例如用户的 ID、角色等。

负载同样会被 **Base64Url** 编码，构成 JWT 的第二部分：

```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "role": "admin",
  "exp": 1715000000
}
```

> **核心警告：** Base64Url 编码 **不是加密**！任何人拿到 JWT 都可以轻松解码并看到负载中的内容。因此，**绝对不要在 Payload 中存放密码等敏感信息**。

### 3. Signature（签名）
签名的作用是防止 JWT 的内容被篡改。它的生成需要用到前两部分（编码后的 Header 和 Payload）以及一个**密钥（Secret）**。

以 HS256 算法为例，签名的生成公式如下：

```javascript
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret // 这个密钥只保存在服务端，绝对不能泄露
)
```

将这三部分用 `.` 连接起来，就形成了一个完整的 JWT，形如：`eyJhbGci... . eyJzdWI... . SflKxwR...`

---

## 二、 JWT 的标准使用流程

JWT 最常见的使用场景是无状态（Stateless）的身份认证，流程如下：

1. **用户登录:** 用户向服务器发送凭证（如用户名和密码）。
2. **生成 Token:** 服务器验证凭证无误后，使用保存在服务端的 Secret 生成一个 JWT，并返回给客户端。
3. **客户端存储:** 客户端收到 JWT 后，通常将其存储在 `LocalStorage`、`SessionStorage` 或 `HttpOnly Cookie` 中。
4. **携带 Token 请求:** 客户端在后续请求受保护的服务器资源时，会在 HTTP 请求的 `Authorization` 头部中携带这个 JWT，标准格式为：
   
   ```http
   Authorization: Bearer <token>
   ```

5. **服务器验证:** 服务器接收到请求后，提取出 JWT。服务器使用自己的 Secret 重新计算签名，并与 JWT 附带的签名进行比对：
   * 如果签名一致且 Token 未过期，验证通过，返回数据。
   * 如果签名不一致或已过期，返回 `401 Unauthorized` 错误。

---

## 三、 优缺点与安全建议

### 优点
* **无状态与高可扩展性:** 服务器不需要在内存或数据库中保存 Session 信息，极大降低了服务器压力，非常适合分布式和微服务架构。
* **跨域友好:** 不依赖传统的 Cookie 机制（如果放在 HTTP Header 中传递），能完美解决跨域请求（CORS）问题。
* **自带信息:** Payload 中可以携带常用的用户信息，减少了服务端查询数据库的次数。

###  缺点
* **无法轻易作废:** 一旦 JWT 签发，在它过期之前都是有效的。如果用户修改密码或被封禁，服务端很难立刻让旧的 JWT 失效（除非引入 Redis 黑名单，但这会破坏其无状态的初衷）。
* **体积较大:** 随着 Payload 中携带的信息增多，JWT 的长度会变大，每次请求都会占用一定的网络带宽。

### 核心安全建议
1. **设置合理的过期时间（`exp`）：** 将 JWT 的有效期设置得尽可能短（例如 15 分钟），然后配合 Refresh Token 机制来获取新的 JWT。
2. **务必使用 HTTPS：** 在网络传输过程中，如果使用 HTTP，JWT 极易被中间人拦截窃取。
3. **妥善存储防 XSS：** 为了防止跨站脚本攻击（XSS）窃取 Token，推荐将 JWT 存储在带有 `HttpOnly` 和 `Secure` 属性的 Cookie 中。如果存储在 LocalStorage，前端必须严格做好防 XSS 注入。