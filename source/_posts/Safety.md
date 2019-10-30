---
title: Safety
date: 2019-01-12 13:36:00
tags: js
categories: 前端安全
thumbnail: https://user-images.githubusercontent.com/12566627/66731851-03e9d900-ee8c-11e9-9bfd-917f863d0e93.jpg
---

## 前端安全
随着前端的飞速发展，前端的很多安全问题也随之报漏出来，WEB安全也受到了越来越多的关注～ 下面我们看一下前端涉及到的，一些需要注意的安全问题吧️ 🤗

### 前端攻击都有哪些，该如何防范

#### XSS攻击

XSS的全称Cross Site Scripting（为啥不叫CSS？是为了和层叠样式表Cascading Style Sheets区分），即跨站脚本攻击。
简单来讲，就是它允许恶意web用户将代码植 入到提供给其它用户使用的页面中。

XSS 主要分为三类：
>1、DOM xss : 使用DOM可以允许程序和脚本动态的访问和更新文档的内容、结构和样式。它不需要服务器解析响应的直接参与，触发XSS靠的是浏览器端的DOM解析，可以认为完全是客户端的事情。
>
>2、反射型 xss : 反射型XSS也被称为非持久性XSS，是现在最容易出现的一种XSS漏洞。发出请求时，XSS代码出现在URL中，最后输入提交到服务器，服务器解析后在响应内容中出现这段XSS代码，最后浏览器解析执行。反射型 XSS 漏洞常见于通过 URL 传递参数的功能，如网站搜索、跳转等。(一般由攻击者构造出特殊的 URL)
>
>3、存储型 xss : 存储型XSS又被称为持久性XSS，它是最危险的一种跨站脚本，相比反射型XSS和DOM型XSS具有更高的隐蔽性，所以危害更大，因为它不需要用户手动触发。 允许用户存储数据的web程序都可能存在存储型XSS漏洞，当攻击者提交一段XSS代码后，被服务器端接收并存储，当所有浏览者访问某个页面时都会被XSS，其中最典型的例子就是留言板。

注：DOM 型 XSS 跟前两种 XSS 的区别：DOM 型 XSS 攻击中，取出和执行恶意代码由浏览器端完成，属于前端 JavaScript 自身的安全漏洞，而其他两种 XSS 都属于服务端的安全漏洞。

#### 如何预防XSS?

1、DOM型防范尽量少使用 appenChid、innerHTML、outerHTML 等标签，而使用 innerText、textContent、setAttribute
2、反射型防范 使用 encodeURIComponent 对url中的参数进行编码
3、HttpOnly 防止劫取 Cookie
4、对用户的输入进行过滤

定义 escapeHTML 按照如下规则进行转义：

|  字符  | 转义后的字符 |
|  ----  | ----  |
| & | `&amp;` |
| < | `&lt;` |
| > | `&gt;` |
| " | `&quot;` |
| ' | `&#x27;` |
| / | `&#x2F;` |

escapeHTML 函数实现如下：
```js
function escapeHTML(str) {
    if(str.length == 0) return ""
    return str.replace(/&/g,'&amp;')
            .replace(/</g, '&lt;')
            .replace(/>/g, '&gt;')
            .replace(/"/g, '&quot;')
            .replace(/'/g, '&apos;')
            .replace(/\//g, '&#x2F;')
}
```

------

#### CSRF 攻击

CSRF跨站请求伪造，是一种劫持受信任用户向服务器发送非预期请求的攻击方式。
通常情况下，CSRF 攻击是攻击者借助受害者的 Cookie 骗取服务器的信任，可以在受害者毫不知情的情况下以受害者名义伪造请求发送给受攻击服务器，从而在并未授权的情况下执行在权限保护之下的操作。（如：发消息，财产操作如转账或购买商品）由于浏览器曾经认证过，所以被访问的网站会认为是真正的用户操作而去执行。

例如：
假如一家银行用以运行转账操作的URL地址如下： http://www.bank.com?account=AccoutName&amount=1000&for=PayeeName
那么，一个恶意攻击者可以在另一个网站上放置如下代码： `<img src="http://www.bank.com?account=Alice&amount=1000&for=Badman">`
如果用户访问了恶意站点则会转账给攻击者

#### CSRF 攻击防御

验证码
验证码是对抗 CSRF 攻击最简洁而有效的防御方法。CSRF 攻击常常在用户不知情况下发出网络请求。

检查Referer字段
HTTP头中有一个Referer字段，这个字段用以标明请求来源于哪个地址。在处理敏感数据请求时，通常来说，Referer字段应和请求的地址位于同一域名下。

添加校验token
CSRF的特征: 攻击者无法直接窃取到用户的信息（Cookie，Header，网站内容等），仅仅是冒用Cookie中的信息。所以我们可以要求所有的用户请求都携带一个CSRF攻击者无法获取到的Token，服务端进行Token值的验证

## 参考
[前端安全](https://www.cxymsg.com/guide/security.html#xss%E5%88%86%E4%B8%BA%E5%93%AA%E5%87%A0%E7%B1%BB)