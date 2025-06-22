# 第十一章 攻击 API

到目前为止，我们已经研究了攻击传统应用程序——那种带有用户界面、登录面板，可能还有某种仪表板的应用程序。现代应用程序通常实现了解耦的基础架构，与传统应用程序不同，它们被拆分成多个较小的应用程序或**微服务**，这些服务共同协作为用户提供功能。**应用程序编程接口**（**API**）并不是一个新概念。API 一词用于描述从 Windows 代码库（允许我们的用户级代码与操作系统内核交互）到提供笔记应用服务的 Web 服务等各种应用。显然，我们不会关注**Windows API**（**WinAPI**），而是会关注支撑互联网几乎一切的 Web 应用程序。当我在本章中提到 API 时，我指的是 Web 服务。

微服务是应用开发者采用的一个相对较新的概念，它摒弃了典型的单体应用设计，转而采用更加解耦的方式。其核心思想是将组件拆分为各自独立的实例，并通过一种通用语言（通常是通过网络，尤其是 HTTP 协议）进行访问。这种方式对开发和敏捷性大有裨益，因为它允许代码异步地推送到各个组件。开发人员可以专注于特定的组件，只要该组件的接口遵循约定的标准，就不必担心会破坏其他部分。

然而，这种方法并非没有缺点。随着这种模式的采用，新的安全挑战也随之而来。解耦的服务意味着更大的攻击面，涉及多个实例，无论是虚拟机还是 Docker 容器。更多的组件通常意味着更大的配置错误的风险，而这些错误当然可能被我们所利用。

组件之间的身份验证和授权强制执行也是一个新问题。如果我的单体应用将所有组件都集成在一起，我实际上不需要担心如何安全地与身份验证模块通信，因为它与应用在同一服务器上，有时甚至在同一个进程中。但如果我的身份验证模块被解耦，并且现在是一个在云中运行的 HTTP Web 服务，那么我就必须考虑我的用户界面与云中身份验证模块实例之间的网络通信。API 如何验证我的用户界面？这两个组件如何安全地协商身份验证响应，以便用户能够访问其他组件？

解耦对安全性也有其他有趣的影响。假设一个 API 是为 Windows 应用程序开发的来处理数据。API 会接受一个 HTTP 方法（如`GET`、`PUT`等），并以 JSON 或 XML 的形式响应。Windows 本地应用程序读取响应并显示在 JSON 对象中返回的错误信息。显示一个包含任意字符串的 Windows 弹窗本身并不危险。因为`user32.dll`的`MessageBox()`函数不会渲染它显示的字符串，所以无需对 API 响应中的危险 HTML 代码进行转义。现在假设同一个 API 突然被集成到一个全新的 Web 应用程序中。JSON 响应中未转义的 HTML 数据可能就会成为问题。

到本章结束时，你将能够熟练掌握：

+   不同类型的 Web API 架构

+   API 如何处理身份验证

+   **JSON Web 令牌**（**JWT**）

+   自动化 API 攻击

# API 通信协议

从本质上讲，Web API 是简单的 HTTP 客户端-服务器环境。请求通过 HTTP 进入，响应通过 HTTP 出去。为了进一步标准化，开发了一些协议，许多 API 遵循其中之一来处理请求。这绝不是一个详尽的列表，但它很可能是你在实际应用中遇到的：

+   **表现层状态转移**（**REST**）

+   **简单对象访问协议**（**SOAP**）

当然，还有其他类型的协议可以供 API 使用，但尽管它们的协议不同，大多数相同的安全挑战仍然存在。最流行的协议是 RESTful API，其次是 SOAP API。

## SOAP

SOAP 是由微软开发的，因为**分布式组件对象模型**（**DCOM**）是一个二进制协议，这使得通过互联网进行通信变得有些复杂。SOAP 则采用 XML，一种结构化且人类可读的语言，用于在客户端和服务器之间交换消息。

### 注意

SOAP 是标准化的，可以通过[`www.w3.org/TR/soap12/`](https://www.w3.org/TR/soap12/)进行完整的审阅。

向 API 主机发送的典型 SOAP 请求如下所示：

```
POST /**UserData** HTTP/1.1
Host: internal.api
Content-Type: application/soap+xml; charset=utf-8

<?xml version="1.0"?>

<soap:Envelope  soap:encodingStyle="http://www.w3.org/2003/05/soap-encoding">

<soap:Body >
  <m:GetUserRequest>
    <m:Name>**Administrator**</m:Name>
  </m:GetUserRequest>
</soap:Body>

</soap:Envelope>
```

从服务器返回的响应，正如你所期望的那样，也是 XML 格式的：

```
HTTP/1.1 200 OK
Content-Type: application/soap+xml; charset=utf-8

<?xml version="1.0"?>

<soap:Envelope  soap:encodingStyle="http://www.w3.org/2003/05/soap-encoding">

<soap:Body >
  <m:GetUserResponse>
    **<m:FullName>Dade Murphy</m:FullName>**
    **<m:Email>dmurphy@webapp.internal</m:Email>**
    **<m:IsAdmin>True</m:IsAdmin>**
  </m:GetUserResponse>
</soap:Body>
</soap:Envelope>
```

获取用户详细信息的过程需要大量开销。SOAP 需要一个定义 XML 版本的头部，信封规范，一个主体，最后是参数。响应也有类似的结构要求。

尽管 SOAP 在今天的标准下显得臃肿，但其设计经过时间考验，已经存在了很长时间。作为攻击者，我们不关心性能或网络带宽的利用率。我们只需要知道所有可能的注入点，并理解身份验证是如何执行的。

虽然 `Envelope`、`Body` 和 `Header` 标签是标准化的，但 body 的内容会根据请求类型、应用程序和 Web 服务的实现有所不同。`GetUserRequest` 操作及其 `Name` 参数特定于 `/UserData` 端点。为了寻找潜在的漏洞，我们需要了解所有可能的端点及其相应的操作或参数。在黑盒场景中，我们如何获取这些信息？

SOAP 请求和响应的 XML 结构通常在 **Web 服务描述语言** (**WSDL**) 文件中定义。对于公共 API，可以通过直接查询 API 并在特定的端点 URL 后附加 `?wsdl` 来获得此文件。如果配置正确，Web 服务将以一个包含该端点所有可能操作和参数的大 XML 文件作出响应：

![SOAP](img/B09238_11_01.jpg)

图 11.1：公共 API 的 WSDL 响应

这个文件在进行互动时非常有用，但并非总是可用。如果 WSDL 无法下载，最好的做法是直接联系客户端，索要定义或请求示例列表。也有可能客户端会拒绝，并希望从外部威胁的角度测试 API。

最后的办法显然是观察 Web、移动或本地应用与 API 的交互，捕获 Burp 中的 HTTP 流量，并通过 Intruder 或 Scanner 模块重放它。这显然不是理想的做法，因为在正常的应用操作中，脆弱的参数或操作可能永远不会被调用。当范围允许时，最好直接从开发人员那里获取 WSDL。

## REST

REST 是现代应用中最常见的架构风格，可能是你会遇到的主要架构。它易于实现，且易于阅读，因此被开发人员广泛采用。虽然它的成熟度不如 SOAP，但它提供了一种简单的方式来实现微服务的解耦设计。

与 SOAP 类似，RESTful API 通过 HTTP 操作，并且大量使用协议动词，包括但不限于：

+   `GET`

+   `POST`

+   `PUT`

+   `DELETE`

如果我们希望查询某个用户的信息，一个 RESTful API 可能会通过 `/users` 端点实现一个 `GET` 请求。查询将通过 URL 参数提交：

```
**GET /users?name=admin HTTP/1.1**
Host: api.ecorp.local:8081
**Content-Type: application/json**
**Accept: application/json**
**Authorization: Bearer b2YgYmFkIG5ld3M**
Cache-Control: no-cache
```

在请求中需要注意的是 `Content-Type`、`Accept` 和 `Authorization` 头。`Content-Type` 头指定了传入数据应由 API 以何种格式处理。`Accept` 头指定了客户端在服务器响应中可接受的格式。典型的 API 将支持 JSON 或 XML，或有时两者兼容。最后，`Authorization` 头指定了一个持有者令牌，对于要求身份验证的端点，这将是必须的。这使得服务器能够识别发起请求的用户，并确定他们是否被授权执行此操作。

一些自定义 API 可能会使用自定义头部进行身份验证和授权，例如 `X-Auth-Token`，但其原理是相同的。一旦我们知道身份验证和授权令牌是如何在客户端和服务器之间传递的，我们就可以开始寻找潜在的弱点。

我们之前请求的服务器响应可以预见地简单且易于读取：

```
**HTTP/1.0 200 OK**
Server: WSGIServer/0.1 Python/2.7.11
Content-Type: text/json

{"user": {"name": "**admin**", "id": 1, "fullname": "Dade Murphy"}}
```

一个 `200` HTTP 响应表示请求成功，我们的令牌是有效的，我们现在得到了一个包含管理员用户所有详细信息的 JSON 对象。

RESTful API 通常使用 JSON 进行请求和响应，但没有严格的标准，开发者可以选择使用自定义的 XML 协议，甚至是原始二进制数据。虽然这种做法不常见，因为微服务的互操作性和维护性变得困难，但也并非闻所未闻。

# API 身份验证

解耦带来了一些身份验证和授权方面的挑战。虽然有些 API 不要求身份验证并不罕见，但你遇到的某些 Web 服务可能会要求客户端以某种方式进行身份验证。

那么，我们如何实现 API 的身份验证呢？这个过程与典型的应用程序没有太大区别。其核心是，身份验证要求你提供某些你知道的信息，并可选地提供某些你拥有的东西，这些信息通常对应于 API 数据库中的一条记录。如果你知道的东西和你拥有的东西是保密的，并且只有持有这些信息的人（假设是这样的人）才能访问它，那么 API 可以合理地确认提供这些信息的客户端获得了访问权限。由于 HTTP 是无状态的，API 现在只需要跟踪这个特定的客户端。

传统的 Web 应用程序会接受身份验证数据（即你知道的信息，通常是用户名和密码组合），并可能需要第二重因素（即你拥有的东西，例如一次性密码、短信验证码或手机推送通知）。一旦应用程序验证了你的身份，它将发放一个会话 ID，浏览器会通过 Cookies 在后续身份验证请求中传递此 ID。

API 也类似，需要在每次需要身份验证的请求中传递某种秘密密钥或令牌。这个令牌通常由 API 生成，在通过其他方式成功验证后发给用户。尽管典型的 Web 应用程序几乎总是使用 `Cookie` 头部来跟踪会话，API 则有更多选择。

## 基本身份验证

是的，这种方式在 Web 应用程序中也很常见，但由于安全问题，现代应用程序一般不再使用。基本身份验证会通过 `Authorization` 头部以明文形式传递用户名和密码：

```
GET /users?name=admin HTTP/1.1
Host: api.ecorp.local:8081
Content-Type: application/json
Accept: application/json
**Authorization: Basic YWRtaW46c2VjcmV0**
Cache-Control: no-cache
```

这种方式显而易见的问题是，凭据以明文方式在网络中传输，攻击者只需要捕获一次请求就能破解用户。会话 ID 和令牌仍然能给攻击者提供访问权限，但它们可以过期或被列入黑名单。

基本认证应该通过 HTTPS 发送，因为用户凭证是以明文方式传输的。现代 API 通常避免使用这种认证方式，因为凭证可能被代理缓存，或者被**中间人攻击**（**MITM**）拦截，甚至可能从内存转储中提取出来。如果 API 使用 LDAP 来验证用户的 Active Directory 域，最好不要在每次 API 请求时都传输用户域凭证。

## API 密钥

更常见的认证方式是通过提供一个密钥或令牌来进行 API 请求。密钥是与访问 Web 服务的账户唯一相关的，应该像密码一样保密。然而，与密码不同，密钥通常不是由用户生成，因此不太可能在其他应用程序中被重复使用。虽然没有行业标准来规定如何将此值传递给 API，**开放授权**（**OAuth**）和 SOAP 协议中已有一些定义要求。常见的传递方式包括自定义头部、`Cookie`头部，甚至通过`GET`参数发送令牌或密钥。

使用`GET` URL 参数传递密钥通常是一个不好的主意，因为该值可能会被浏览器、代理和 Web 服务器日志文件缓存：

```
GET /users?name=admin&**api_key=aG93IGFib3V0IGEgbmljZSBnYW1lIG9mIGNoZXNz** HTTP/1.1
Host: api.ecorp.local:8081
Content-Type: application/json
Accept: application/json
Cache-Control: no-cache
```

另一种方式是使用自定义头部将 API 密钥与请求一起发送。这是一种稍微更好的替代方法，但仍然需要通过 HTTPS 保持保密，以防止 MITM 攻击捕获此值：

```
GET /users?name=admin HTTP/1.1
Host: api.ecorp.local:8081
Content-Type: application/json
Accept: application/json
**X-Auth-Token: aG93IGFib3V0IGEgbmljZSBnYW1lIG9mIGNoZXNz**
Cache-Control: no-cache
```

## 承载者认证

类似于密钥，承载令牌也是秘密值，通常通过`Authorization` HTTP 头部传递，但与使用`Basic`类型不同，我们使用`Bearer`类型。对于 REST API，只要客户端和服务器就如何交换此令牌达成一致，就没有标准来定义这个过程，因此你可能会在实际中看到稍有不同的实现：

```
GET /users?name=admin HTTP/1.1
Host: api.ecorp.local:8081
Content-Type: application/json
Accept: application/json
**Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjEiLCJ1c2VyIjoiYWRtaW4iLCJpc19hZG1pbiI6dHJ1ZSwidHMiOjEwNDUwNzc1MH0.TstDSAEDcXFE2Q5SJMWWKIsXV3_krfE4EshejZXnnZw**
Cache-Control: no-cache
```

上述承载令牌是 JWT 的一个示例。它比传统的不透明令牌稍长，但有一些优势。

## JWT

JWT 是一种相对较新的认证机制，在 Web 服务中逐渐占据市场份额。它是一种紧凑、自包含的方式，用于在两方之间安全地传递信息。

JWT 非常灵活，且易于在认证协议中实现。SOAP 和 OAuth 都可以轻松实现 JWT 作为承载者。

### 注意

OAuth 的相关信息可以在[`oauth.net/2/`](https://oauth.net/2/)找到。

JWT（JSON Web Token）本质上是通过**基于哈希的消息认证码**（**HMAC**）和一个密钥，或者使用 RSA 密钥对签名的声明。HMAC 是一种可以用来验证数据完整性和消息认证的算法，非常适合 JWT。JWT 由一个`base64url`编码的头部、有效载荷和相应的签名组成：

```
base64url(**header**) . base64url(**payload**) . base64url(**signature**)
```

令牌的头部将指定用于签名的算法，负载将是声明（例如，我是用户 1 并且我是管理员），第三部分将是签名本身。

如果我们检查前面的持有者令牌，我们可以看到典型 JWT 的组成。它包含三个由句号分隔的信息块，使用 URL 安全的 Base64 编码。

### 注意

URL 安全的 Base64 编码使用与传统 Base64 相同的字母表，唯一的区别是将字符`+`替换为`-`，将`/`替换为`_`。

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
.
eyJpZCI6IjEiLCJ1c2VyIjoiYWRtaW4iLCJpc19hZG1pbiI6dHJ1ZSwidHMiOjEwNDUwNzc1MH0
.
TstDSAEDcXFE2Q5SJMWWKIsXV3_krfE4EshejZXnnZw
```

第一部分是头部，描述了用于签名的算法。在这种情况下是使用 SHA-256 的 HMAC。类型被定义为 JWT。

我们可以在浏览器控制台中使用 JavaScript 的`atob()`函数来解码该部分为可读文本：

```
> atob('eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9')
"{"alg":"**HS256**","typ":"**JWT**"}"
```

第二部分或负载通常是任意数据，表示某个特定的声明，也称为负载。在这种情况下，它告诉服务器我是一个名为`admin`的管理员用户，用户 ID 为`1`，时间戳为`104507750`。时间戳是一个好主意，因为它们可以防止重放攻击。

```
> atob('eyJpZCI6IjEiLCJ1c2VyIjoiYWRtaW4iLCJpc19hZG1pbiI6dHJ1ZSwidHMiOjEwNDUwNzc1MH0')
"{"id":"**1**","user":"**admin**","is_admin":**true**,"ts":**104507750**}"
```

最后一部分是`base64url`编码的 32 字节 SHA-256 HMAC 签名。

当 API 服务器接收到这个三部分的令牌时，它将：

+   解析头部以确定算法：在这种情况下是 HMAC SHA-256。

+   计算由句号连接的前两个`base64url`编码部分的 HMAC SHA-256 值：

    ```
    HMAC-SHA256(base64url(header) + "." + base64url(payload), **"secret_key"**)
    ```

+   如果签名验证通过，则也认为负载有效。

### JWT 怪癖

虽然这个过程目前在密码学上是安全的，但我们有几种方法可以篡改这个令牌，试图欺骗不完善的 API 实现。

首先，虽然头部和负载是签名的，但我们实际上可以修改它们。令牌数据在我们的控制之下。唯一我们不知道的是密钥。如果我们修改了负载，签名将失败，并且我们预计服务器会拒绝我们的请求。

但是请记住，头部部分在签名验证之前会被解析。这是因为头部包含关于 API 如何验证消息的指令。这意味着我们可能会更改这些数据，并破坏实现中的某些内容。

有趣的是，JWT 的**请求评论**（**RFC**）规范了一个名为"none"的受支持签名算法，可以通过实现来假设该令牌是通过其他方式验证的：

![JWT quirks](img/B09238_11_02.jpg)

图 11.2：RFC 提到使用"none"算法的未加密 JWT

### 注意

完整的 JWT RFC 可以在这里查看：[`tools.ietf.org/html/rfc7519`](https://tools.ietf.org/html/rfc7519)。

一些 JWT 库将遵循标准并支持这个特定的算法。那么，当我们在前面的负载中使用"none"算法时，会发生什么呢？

我们的令牌看起来像这样，最后一个句号后面没有附加签名：

```
eyJhbGciOiJub25lIiwidHlwIjoiSldUIn0
.
eyJpZCI6IjEiLCJ1c2VyIjoiYWRtaW4iLCJpc19hZG1pbiI6dHJ1ZSwidHMiOjEwNDUwNzc1MH0
.
[blank]
```

如果服务器端库遵循 JWT RFC，则该令牌将被验证并认为是有效的。我们可以使用 Burp Suite **JSON Web Tokens**扩展来测试这个修改后的令牌，该扩展可以从 BApp Store 下载：

![JWT 的怪癖](img/B09238_11_03.jpg)

图 11.3：JWT Burp 扩展

我们可以在第一个字段中输入 JWT 值，并提供一个虚拟密钥。由于我们不再使用带密钥的 HMAC，这个值将被忽略。扩展应该确认签名和 JWT 令牌是有效的：

![JWT 的怪癖](img/B09238_11_04.jpg)

图 11.4：没有签名的 JWT 被视为有效

### 注意

关于这种类型攻击的更多信息可以在 Auth0 上找到：[`auth0.com/blog/critical-vulnerabilities-in-json-web-token-libraries/`](https://auth0.com/blog/critical-vulnerabilities-in-json-web-token-libraries/)。

这个简单的攻击在使用不安全 JWT 实现的库的 API 中可能会带来毁灭性的后果。伪造认证票证的能力对于我们作为攻击者来说非常有用。

## Burp JWT 支持

手动拆分头部、载荷和签名部分有些繁琐，我们希望能自动化这个过程。如果我们要攻击服务器上的 JWT 实现，我们可能还需要修改一些参数。这可能会很麻烦，尤其是每次都需要重新计算签名时。

**JWT4B** 扩展程序是为了检查请求中的 JWT 数据、解析它并验证签名，所有这些操作都可以在 Burp Suite 用户代理中完成。

### 注意

JWT4B 可以从 GitHub 下载：[`github.com/mvetsch/JWT4B`](https://github.com/mvetsch/JWT4B)。

下载完 JWT4B JAR 文件到磁盘后，我们可以手动将其加载到 Burp 中。在**Extender**选项卡下，点击**Extensions**，然后点击**Add**按钮：

![Burp JWT 支持](img/B09238_11_05.jpg)

图 11.5：Burp 扩展选项卡

在**Load Burp Extension**弹出窗口中，我们可以告诉 Burp 从磁盘上的位置加载 JWT4B JAR 文件：

![Burp JWT 支持](img/B09238_11_06.jpg)

图 11.6：加载 JWT4B JAR 扩展文件

JWT4B 将允许我们拦截包含 JWT 的授权头请求，替换载荷，并重新签名，使用相同的密钥（如果我们有）或随机密钥，甚至可以更改算法：

![Burp JWT 支持](img/B09238_11_07.jpg)

图 11.7：动态修改 JWT

JWT4B 使得攻击 JWT 实现变得更加简单，因为它可以为我们完成一些繁重的工作。

# Postman

在测试典型的 Web 应用程序时，我们首先配置系统代理指向 Burp Suite。现在，我们所有的请求都可以在使用应用程序时进行检查。发起攻击非常简单，因为这些请求是由 Burp 可以通过网络看到的用户界面为我们构建的。在正常操作中，用户输入数据到搜索字段中，例如，应用程序会构造包含所有适当参数的`GET`或`POST`请求，然后通过网络发送出去。所有这些有效的请求现在都可以通过攻击代理进行重放、修改和扫描。当有用户界面来驱动流量生成时，发现过程变得更加简单。

如果没有用户界面组件，只有一个 API 端点和一些文档可供参考，那么构建一系列`curl`请求并手动解析响应将非常繁琐。如果交互需要身份验证，获取令牌对于复杂的 Web 服务来说将是一场噩梦。

**Postman**是一个极好的工具，我们可以用它来构建一系列针对目标 API 的请求，使得测试变得轻松。如果客户和开发人员配合良好，尤其如此。为了更高效地利用测试时间，客户可以向我们提供一系列已生成的请求，这将大大加快应用程序的测试过程。

我们的工作通常有时间限制，构建 RESTful API 的攻击载荷非常耗时，即使有文档也不例外。像 Postman 这样的工具支持**集合**，它本质上是一个完全可定制的 API 测试序列。开发人员或其他测试人员可以创建这些集合，集合中包含了每个可能的端点请求及其所有可能的参数。它们甚至可以自动捕获数据，例如身份验证令牌，并将其自动插入到后续的请求中。Postman 使得 API 测试变得简单；开发人员喜欢它，我们也喜欢。

作为攻击者，我们可以从客户那里获取一整套已配置好的集合，并在我们自己的环境中运行它。我们可以准确地看到 API 应该如何工作，正如开发人员所预期的那样。Postman 还方便地支持上游代理，这样我们就可以通过 Burp 将所有格式正确的请求从**集合运行器**推送出去，并快速开始通过 Burp 的 Intruder、Scanner 和 Repeater 模块进行攻击。

Postman 有一个免费版，每月支持最多 1000 次调用，但如果你发现自己测试越来越多的 API，Pro 版和企业版可能是一个不错的投资。

### 注意

Postman 有免费版、专业版和企业版，可以在[`www.getpostman.com/`](https://www.getpostman.com/)下载。

为了演示目的，本章我们将使用 Matt Valdes 提供的脆弱 API Docker 应用程序，网址为[`github.com/mattvaldes/vulnerable-api`](https://github.com/mattvaldes/vulnerable-api)。在我们的演示中，API 运行在`http://api.ecorp.local:8081/`上。

安装了 Docker 后，脆弱的 API 可以通过 Linux 终端使用`docker run`命令下载并执行。我们还可以使用`-p`选项指定要在容器中暴露的端口。最后，`--name`参数将指示 Docker 去获取`mkam/vulnerable-api-demo`容器：

```
root@kali:~# docker run -p 8081:8081 --name api mkam/vulnerable-api-demo
CRIT Supervisor running as root (no user in config file)
WARN Included extra file "/etc/supervisor/conf.d/vAPI.conf" during parsing
INFO RPC interface 'supervisor' initialized
CRIT Server 'unix_http_server' running without any HTTP authentication checking
INFO daemonizing the supervisord process
INFO supervisord started with pid 10
system type 0x794c7630 for '/var/log/supervisor/supervisord.log'. please report this to bug-coreutils@gnu.org. reverting to polling
INFO spawned: 'vAPI' with pid 12
INFO success: vAPI entered RUNNING state, process has stayed up for > than 1 seconds (startsecs)
```

为了测试功能，我们可以使用`curl`对我们刚启动的 Docker API 的根 URL 执行`GET`请求：

```
root@kali:~# curl http://api.ecorp.local:8081/
{
  "response": {
    "Application": "vulnerable-api",
    "Status": "running"
  }
}
```

## 安装

Postman 客户端有 Linux、Mac 和 Windows 版本。为了简单起见，我们将在攻击机 Kali 上使用 Linux 客户端。在 Windows 和 Mac 上安装相对简单，但在 Linux 上你可能需要安装一些依赖项才能启动。

Postman 客户端是一个 Electron 应用程序，具有相当的可移植性，但它确实需要`libgconf`，该库在 Kali 的仓库中提供。我们可以通过终端使用`apt-get install`命令安装这个依赖，命令如下：

```
root@kali:~/tools# apt-get install libgconf-2-4
Reading package lists... Done
Building dependency tree       
[...]
```

为了获取最新编译的 Postman 版本，我们可以从其 Linux x64 仓库使用`wget`命令下载 gzipped tarball，网址为[`dl.pstmn.io/download/latest/linux64`](https://dl.pstmn.io/download/latest/linux64)。`wget`命令会将文件保存为`postman.tar.gz`，存储在本地目录中：

```
root@kali:~/tools# wget https://dl.pstmn.io/download/latest/linux64 -O postman.tar.gz
[...]
HTTP request sent, awaiting response... 200 OK
Length: 78707727 (75M) [application/gzip]
Saving to: 'postman.tar.gz'
[...]
```

我们将使用`tar zxvf`命令将内容提取到我们的`tools`目录中，如下所示：

```
root@kali:~/tools# tar zxvf postman.tar.gz
Postman/
Postman/snapshot_blob.bin
[...]
```

安装完依赖项后，可以通过调用预编译的`Postman`二进制文件来启动 Postman。此文件位于我们刚从 tarball 中提取的`Postman/`目录中：

```
root@kali:~/tools# ~/tools/Postman/Postman
```

![安装](img/B09238_11_08.jpg)

图 11.8：在 Linux 上运行的 Postman 客户端

为了简单测试基本功能，我们可以创建一个新的请求，默认工作区将打开。

用户界面大部分都相当直观。我们可以输入 API URL，修改 HTTP 方法，传递自定义头信息，甚至通过几次点击构建有效的授权。

作为测试，我们可以发出与之前用`curl`发出的相同请求。响应将出现在**正文**选项卡中，如下图所示，并且可以选择美化内容。当响应为大量数据时，Postman 可以自动解析并将其格式化为 XML、HTML、JSON 或纯文本格式，这一特性非常受欢迎：

![安装](img/B09238_11_09.jpg)

图 11.9：向 API 发送的 Postman 请求示例

Postman 的一个优势是能够记录我们在左侧**历史**面板中所做的所有请求。这使得我们，作为 API 开发人员或**质量保证**（**QA**）分析师，能够将请求和响应保存在集合中。

开发人员可以导出集合，我们则可以在参与过程中导入。这节省了我们大量构建自己查询的时间，并可以直接开始寻找安全漏洞。

## 上游代理

Postman 还支持通过系统代理或自定义服务器路由请求。明智的选择是 Burp 或 OWASP ZAP。一旦我们导入并运行集合，每个请求都会被捕获，并准备好进行检查和重放。

在**文件**和**设置**下，找到一个**代理**标签，应该可以让我们指向本地的 Burp 代理，默认情况下是`127.0.0.1`，端口为`8080`：

![上游代理](img/B09238_11_10.jpg)

图 11.10：Postman 上游代理配置

我们在 Postman 中的所有后续请求也将显示在 Burp 的代理 HTTP 历史记录中：

![上游代理](img/B09238_11_11.jpg)

图 11.11：Burp 显示 Postman 生成的请求

## 环境

为了构建有效的集合，我们应该为每个目标 API 创建一个新的 Postman 环境。Postman 环境允许我们在变量中存储数据，这对于执行一些操作非常有用，例如在集合中的请求之间传递授权令牌。要创建新的环境，我们可以使用左上角的**创建新环境**标签：

![环境](img/B09238_11_12.jpg)

图 11.12：在 Postman 中创建新环境

在弹出窗口中，输入一个有意义的名称，然后点击**添加**来创建一个新的空环境：

![环境](img/B09238_11_13.jpg)

图 11.13：添加新的 Postman 环境

请求现在可以与我们的 ECorp API 环境关联。集合也可以在特定环境中运行，允许在请求之间创建和传递变量。

以下图显示了一个简单的`GET`请求排队等待在 ECorp API 环境中运行：

![环境](img/B09238_11_14.jpg)

图 11.14：为请求指定环境

## 集合

如前所述，集合只是按照特定顺序排列的一组 API 请求。它们可以导出为 JSON 格式并导入到任何 Postman 客户端中，使其具有很强的可移植性。

为了展示 Postman 集合的强大功能，我们将为我们的易受攻击 API 实例创建一个集合，实例名为`api.ecorp.local`，运行在端口`8081`上。

如果我们查看 Matt Valdes 的易受攻击 API 的文档，我们会注意到大多数交互都需要通过自定义的`X-Auth-Token` HTTP 头传递授权令牌。虽然大多数 RESTful API 尝试使用`Authorization`头传递令牌，但自定义头并不少见。这就是为什么像 Burp 和 Postman 这样高度可定制的工具非常适合进行安全测试，因为即使我们遇到偏离常规的情况，它们也能让我们自动化大部分工作。

### 注意

文档可以在`README.md`中找到，链接地址为[`github.com/mattvaldes/vulnerable-api`](https://github.com/mattvaldes/vulnerable-api)。

文档指出，如果我们发送一个包含 JSON 格式认证数据的 `POST` 请求到 `/tokens`，我们可以获得一个新的令牌。默认的凭证是 `user1` 和 `pass1`。我们的认证请求 `POST` 请求体应该如下所示：

```
{
  "auth": {
    "passwordCredentials": {
       "username": "**user1**",
        "password": "**pass1**"
    }
  }
}
```

API 将返回一个包含后续认证请求所需令牌的 JSON 格式对象：

```
{
  "access": {
    "token": {
      "expires": "**[Expiration Date]**",
      "id": "**[Token]**"
    },
    "user": {
      "id": 1,
      "name": "user1"
    }
  }
}
```

然后，我们可以通过 `X-Auth-Token` 头将 `id` 值传递给 `/user/1` 端点，请求应该成功：

![Collections](img/B09238_11_15.jpg)

图 11.15：成功认证请求到漏洞 API

现在我们有了一系列请求，我们想要创建一个集合并自动化一些测试。

再次点击左上角的 **Create New** 按钮，选择 **Collection**：

![Collections](img/B09238_11_16.jpg)

图 11.16：创建新的集合

在弹出框中，我们可以输入名称，并在需要时添加描述，然后点击 **Create** 按钮：

![Collections](img/B09238_11_17.jpg)

图 11.17：创建新的集合

我们所做的所有请求都会记录在工作区的 **History** 标签中。我们可以选择需要的请求，并点击右上角 **Send** 旁边的 **Save** 按钮：

![Collections](img/B09238_11_18.jpg)

图 11.18：将请求保存到集合中

在底部，我们应该能看到新的 ECorp API 集合，并可以选择它来保存我们的请求：

![Collections](img/B09238_11_19.jpg)

图 11.19：选择目标集合

对任何必须加入此集合的请求重复此过程。运行时，我们预计我们的集合会在第一个请求中获得新的令牌，并使用新提供的令牌发出第二个认证请求到 `/user/1`：

![Collections](img/B09238_11_20.jpg)

图 11.20：认证的 Postman 请求

此时，我们可以将其导出并导入到其他地方。目前，我们的集合可以运行，但令牌不会传递到第二个请求。

为此，我们需要利用 Postman 中的一个功能，叫做 **Tests**。每个请求都可以配置为在继续之前执行测试并执行某个操作。通常，这些可以用于验证请求是否成功。开发人员可以利用 Tests 来确保他们刚刚推送的代码没有破坏任何东西。

测试是用 JavaScript 编写的，所以一点点编程知识将大有帮助。幸运的是，我们可以复用现成的测试来满足我们的需求。

对于我们在 ECorp API 集合中的 `Get Auth Token` 请求，测试需要检查响应，解析为 JSON，并提取令牌 ID。为了将其传递给另一个请求，我们可以利用 ECorp API 环境，并将数据存储在一个名为 `auth_token` 的变量中。

实现这一点的代码相当简单，尽管如果您不熟悉 JavaScript 可能会有点奇怪。每个`pm.test`条目都是按照列出的顺序执行的单独测试。如果任何测试失败，运行将提醒我们：

```
pm.test(**"Status code is 200"**, function () {
    **pm.response.to.have.status(200)**;
});

pm.test**("Save Auth Token"**, function () {
    var data = **pm.response.json()**;
    pm.environment.set(**"auth_token"**, **data['access']['token']['id']**);
});
```

第一个测试只是检查 API 的 HTTP 响应是否为`200`。其他任何响应都会在集合运行期间引发错误。

第二个测试将解析响应文本为 JSON 并将其存储在本地的`data`变量中。如果您回忆一下`/tokens`响应的层次结构，我们需要使用 JavaScript 数组表示法访问`access.token`字段中的`id`值：`data['access']['token']['id']`。

使用`pm.environment.set`函数，我们将`id`值存储在`auth_token`环境变量中，使其可供其他请求使用。

每次运行此集合中的请求时，`auth_token`都将被更新。可以通过点击名称旁边的“眼睛”图标来检查环境：

![Collections](img/B09238_11_21.jpg)

图 11.21：检查 Postman 环境

我们对`/user/1`的第二个请求要求通过`X-Auth-Token`标头传递此值。为此，我们添加一个新的自定义标头，并在**值**字段中键入`{{`以显示现有变量列表。Postman 将为我们自动完成现有变量：

![Collections](img/B09238_11_22.jpg)

图 11.22：在请求中使用环境变量

点击**发送**，我们可以验证身份验证请求是否成功：

![Collections](img/B09238_11_23.jpg)

图 11.23：身份验证请求成功

## Collection Runner

集合可以使用熟悉的 JSON 格式导出和导入。导入是一个简单的拖放操作。开发人员和质量保证人员可以像之前一样创建这些集合，导出它们，并作为参与的一部分将文件发送给我们。这极大地简化了我们评估 API 的工作，因为耗时的工作已经完成。

导入后，我们的集合可以通过 Postman Runner 执行，可通过菜单中**新建**按钮旁边的**Runner**按钮访问：

![Collection Runner](img/B09238_11_24.jpg)

图 11.24：打开 Runner 组件

打开一个新的**Collection Runner**窗口，其中包含所有导入的集合。选择 ECorp API 集合，ECorp API 环境，并点击**运行 ECorp API**：

![Collection Runner](img/B09238_11_25.jpg)

图 11.25：运行 ECorp 集合

如果一切顺利，我们应该看到所有都是绿色的，因为我们的测试应该已成功，这意味着身份验证请求成功，令牌被提取，并且用户查询返回了一些数据：

![Collection Runner](img/B09238_11_26.jpg)

图 11.26：成功的 Postman 集合运行

更重要的是，集合中的所有请求都被传递到我们的 Burp 代理：

![Collection Runner](img/B09238_11_27.jpg)

图 11.27：Burp 捕获的 Postman 集合运行

从这里，我们可以启动 Burp Scanner、Intruder 和 Sequencer 模块，或者重放任何请求以操控数据并寻找漏洞，正如我们在传统应用程序中常做的那样。

# 攻击考虑事项

针对基于 HTTP 的 API 的攻击与传统的 Web 应用程序并无太大不同。我们需要遵循相同的基本程序：

+   确定注入点

+   发送意外输入并观察 API 如何响应

+   寻找常见嫌疑：SQL 注入、XXE、XSS、命令注入、LFI 和 RFI

我们可以利用已经掌握的所有技巧和方法来发现这些问题，但也有一些例外情况。

典型 Web 应用程序中的 XSS 漏洞很容易证明。你发送输入，输入作为 HTML 或 JavaScript 被反射回客户端，浏览器渲染内容并执行代码。

对于 Web 服务，响应通常不会被渲染，主要是因为响应头中的`Content-Type`设置。这通常是 JSON 或 XML，大多数浏览器不会将其渲染为 HTML。我说的是“大多数”，因为不幸的是，一些旧版浏览器可能仍会渲染内容，忽略服务器声明的内容类型，而是根据响应中的数据猜测。

在`api.ecorp.local/user/1` URL 中发现了以下反射型输入问题：

```
GET /user/1**<svg%2fonload=alert(1)>** HTTP/1.1
Content-Type: application/json
X-Auth-Token: 3284bb036101252db23d4b119e60f7cc
cache-control: no-cache
Postman-Token: d5fba055-6935-4150-96fb-05c829c62779
User-Agent: PostmanRuntime/7.1.1
Accept: */*
Host: api.ecorp.local:8081
Connection: close
```

我们传入 JavaScript 负载并观察到 API 将其原封不动地返回给客户端，且没有进行转义：

```
HTTP/1.0 200 OK
Date: Tue, 24 Apr 2018 17:14:03 GMT
Server: WSGIServer/0.1 Python/2.7.11
Content-Length: 80
**Content-Type: application/json**

{"response": {"error": {"message": "user id 1**<svg/onload=alert(1)>** not found"}}}
```

通常，这就足以证明存在漏洞，并且用户可能会通过社交工程攻击成为目标。然而，如果仔细观察，你会注意到内容类型设置为`application/json`，这意味着现代浏览器不会将响应渲染为 HTML，从而使我们的负载无效。

对于 API，我们或许仍然抱有希望。在解耦环境中，Web 服务通常不会直接访问。可能该特定 API 是通过 Web 应用程序进行调用的。错误消息可能最终会出现在浏览器中，浏览器可能最终渲染我们的负载。如果所有错误都被 Web 服务记录，并且稍后被整齐地呈现在仅内部可见的状态仪表板中呢？那么我们就能在任何分析师检查 API 状态时执行 JavaScript 代码。

Web 应用程序扫描器可能会识别到这个问题，但将其标记为信息性问题，因此可能会被忽略。考虑每个漏洞的上下文以及不同客户端如何使用受影响的服务非常重要。记住在攻击 API 时要注意带外发现和利用，因为并非所有漏洞都是立即显现的。

# 总结

在本章中，我们探讨了让攻击 API 变得更容易的不同方式。我们介绍了 Web 服务的两种最常见标准：SOAP 和 REST。我们查看了身份验证是如何处理的，以及 JWT 在安全通信中扮演的角色。我们还探索了帮助提高效率的工具和扩展。

我们还使用了 Postman，并尝试了自动化发现的想法，以及对 API 输入和端点的测试。

API 可能是 Web 和移动应用程序的最新趋势，但它们与通常的 HTTP 应用程序并没有太大的区别。实际上，正如我们之前所看到的，微服务架构在身份验证方面带来了一些新的挑战，这些挑战可以与通常的服务器端和客户端漏洞一起利用。在接下来的章节中，我们将看看内容管理系统（CMS）以及一些发现和利用它们的有趣和有利的方法。
