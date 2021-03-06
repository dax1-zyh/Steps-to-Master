## XSS攻击

### 什么是XSS
`Cross-Site Scripting(跨站脚本攻击)`，简称XSS，是一种**代码注入攻击**。攻击者通过在目标网站上注入恶意脚本，使之在用户的浏览器上运行。利用这些恶意脚本，攻击者可获取用户的敏感信息如Cookie、SessionID等，进而危害数据安全

### XSS的注入方法
简单来说，任何**可以输入的地方**都有可能引起XSS攻击，包括URL

- 在HTML内嵌的文本中，恶意内容以script标签形成注入
- 在内联的JavaScript中，拼接的数据突破了原本的限制（字符串，变量，方法名）等
- 在标签属性中，恶意内容包含引号，从而突破属性值的限制，注入其他属性或者标签
- 在标签的 `href、src` 等属性中，包含 `javascript:` (伪协议)等可执行代码。
- 在 `onload、onerror、onclick` 等事件中，注入不受控制代码。
- 在 style 属性和标签中，包含类似 `background-image:url("javascript:...");` 的代码（新版本浏览器已经可以防范）。
- 在 style 属性和标签中，包含类似 `expression(...)` 的 CSS 表达式代码（新版本浏览器已经可以防范）。


### XSS攻击的分类
根据攻击的来源，XSS攻击可分为存储型、反射性和DOM型三种

#### 存储型XSS
存储型XSS的攻击步骤
 1. 攻击者将恶意代码提交到目标网站的数据库中。
 2. 用户打开目标网站时，网站服务端将恶意代码从数据库取出，拼接在 HTML 中返回给浏览器。
 3. 用户浏览器接收到响应后解析执行，混在其中的恶意代码也被执行。
 4. 恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。

存储型 XSS(又被称为持久性XSS)攻击常见于带有用户保存数据的网站功能，如论坛发帖、商品评论、用户私信等。
它是最危险的一种跨站脚本，相比反射型XSS和DOM型XSS具有更高的隐蔽性，所以危害更大，因为它**不需要用户手动触发**。**任何允许用户存储数据的web程序都可能存在存储型XSS漏洞，**当攻击者提交一段XSS代码后，被服务器端接收并存储，当所有浏览者访问某个页面时都会被XSS。



#### 反射型XSS
反射型XSS的攻击步骤

1. 攻击者构造出特殊的 URL，其中包含恶意代码。
2. 用户打开带有恶意代码的 URL 时，网站服务端将恶意代码从 URL 中取出，拼接在 HTML 中返回给浏览器。
3. 用户浏览器接收到响应后解析执行，混在其中的恶意代码也被执行。
4. 恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。


反射型 XSS 跟存储型 XSS 的区别是：存储型 XSS 的恶意代码存在数据库里，反射型 XSS 的恶意代码存在 URL 里。

反射型 XSS (也被称为非持久性XSS)漏洞常见于通过 URL 传递参数的功能，如网站搜索、跳转等。

由于需要用户主动打开恶意的 URL 才能生效，攻击者往往会结合多种手段诱导用户点击。

POST 的内容也可以触发反射型 XSS，只不过其触发条件比较苛刻（需要构造表单提交页面，并引导用户点击），所以非常少见。



#### DOM型XSS
DOM型XSS的攻击步骤:

1. 攻击者构造出特殊的 URL，其中包含恶意代码。
2. 用户打开带有恶意代码的 URL。
3. 用户浏览器接收到响应后解析执行，前端 JavaScript 取出 URL 中的恶意代码并执行。
4. 恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。

DOM 型 XSS 跟前两种 XSS 的区别：DOM 型 XSS 攻击中，取出和执行恶意代码由浏览器端完成，属于前端 JavaScript 自身的安全漏洞，而其他两种 XSS 都属于服务端的安全漏洞。

**注意:**
DOM通常代表在html、xhtml和xml中的对象，使用DOM可以允许程序和脚本动态的访问和更新文档的内容、结构和样式。它不需要服务器解析响应的直接参与，**触发XSS靠的是浏览器端的DOM解析，所以防范DOM型XSS完全就是前端的责任,必须注意!!!。**




### 对比XSS攻击

![在这里插入图片描述](https://img-blog.csdnimg.cn/5977253f791d4a6283629243be0c46f2.png)



### 防御XSS攻击的方法
- **httpOnly**：在cookie中设置HttpOnly属性后，js脚本将无法读取到cookie信息

- **输入过滤**：一般是用于对于输入格式的检查，例如：邮箱，电话号码，用户名，密码……等，按照规定的格式输入。不仅仅是前端负责，后端也要做相同的过滤检查。因为攻击者完全可以绕过正常的输入流程，直接利用相关接口向服务器发送设置。

- **转义HTML**：如果拼接 HTML 是必要的，就需要对于引号，尖括号，斜杠进行转义,但这还不是很完善.想对 HTML 模板各处插入点进行充分的转义,就需要采用合适的转义库

  


## CSRF攻击
### 什么是CSRF
`Cross-site request forgery(跨站请求伪造)`，是一种挟持用户在当前已登陆的Web应用程序上执行非本意的操作的攻击方法。如:攻击者诱导受害者进入第三方网站，在第三方网站中，向被攻击网站发送跨站请求。利用受害者在被攻击网站已经获取的注册凭证，绕过后台的用户验证，达到冒充用户对被攻击的网站执行某项操作的目的。

### CSRF攻击流程
![在这里插入图片描述](https://img-blog.csdnimg.cn/6c15c3049a8e47d298367e5aa7b51fcc.png)



从上图可以看出，要完成一次CSRF攻击，受害者必须依次完成两个步骤：

1.登录受信任网站A，并在本地生成Cookie。
2.在不登出A的情况下，访问危险网站B。

看到这里，你也许会说：“如果我不满足以上两个条件中的一个，我就不会受到CSRF的攻击”。是的，确实如此，但你不能保证以下情况不会发生：

- 你不能保证你登录了一个网站后，不再打开一个tab页面并访问另外的网站。
- 你不能保证你关闭浏览器了后，你本地的Cookie立刻过期，你上次的会话已经结束。（事实上，关闭浏览器不能结束一个会话，但大多数人都会错误的认为关闭浏览器就等于退出登录/结束会话了......）
- 上图中所谓的攻击网站，可能是一个存在其他漏洞的可信任的经常被人访问的网站。

### CSRF的特点
- **攻击一般发起在第三方网站**，而不是被攻击的网站。被攻击的网站无法防止攻击发生。
- 攻击利用受害者在被攻击网站的登录凭证，**冒充受害者提交操作**；而不是直接窃取数据。
- 整个过程攻击者并不能获取到受害者的登录凭证，仅仅是“冒用”。
- 跨站请求可以用各种方式：图片URL、超链接、CORS、Form提交等等。部分请求方式可以直接嵌入在第三方论坛、文章中，难以进行追踪。


### 防御CSRF的方法
- **验证码**；强制用户必须与应用进行交互，才能完成最终请求。此种方式能很好的遏制 csrf，但是用户体验比较差。

- **Referer check**；请求来源限制，此种方法成本最低，但是并不能保证 100% 有效，因为服务器并不是什么时候都能取到 Referer，而且低版本的浏览器存在伪造 Referer 的风险。
- **token**；**token 验证的 CSRF 防御机制是公认最合适的方案**。(具体可以查看本系列前端鉴权中对token有详细描述)若网站同时存在 XSS 漏洞的时候，这个方法也是空谈。


## XSS与CSRF的区别
- 通常来说 CSRF 是由 XSS 实现的，CSRF 时常也被称为 XSRF（CSRF 实现的方式还可以是直接通过命令行发起请求等）。
- 本质上讲，XSS 是代码注入问题，CSRF 是 HTTP 问题。 XSS 是内容没有过滤导致浏览器将攻击者的输入当代码执行。CSRF 则是因为浏览器在发送 HTTP 请求时候自动带上 cookie，而一般网站的 session 都存在 cookie里面(Token验证可以避免)。

## 参考文档
[前端面试查漏补缺--(七) XSS攻击与CSRF攻击](https://juejin.cn/post/6844903781704925191#heading-15)