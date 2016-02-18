# XSS #

## 介绍 ##
跨网站脚本（Cross-site scripting，通常简称为XSS或跨站脚本或跨站脚本攻击），是一种代码注入攻击方式。
黑客利用网页开发时留下的漏洞，将恶意指令代码到网页，当普通用户浏览网页时，加载并执行攻击者恶意制造的网页程序。

这些恶意网页程序通常是JavaScript，但实际上也可以包括Java， VBScript， ActiveX， Flash 或者甚至是普通的HTML。攻击成功后，攻击者可能得到更高的权限（如执行一些操作）、私密网页内容、会话和cookie等各种内容。


### 信条 ###
**永远不要信任用户侧产生的内容**。

不要将用户侧产生的内容“直接”以HTML（包括HTML，Image src ，a href ，window location等）、JS、CSS等的方式运行或写在页面上。

这里不被信任的“用户侧的内容”，可能指：

 - window.location
 - document.referer
 - cookie
 - form 和input内容
 - 其他用户提交后保存在数据库的内容
 - 等
 
内容在使用前，需要经过escape或encodeURI等方式处理。

## 常见的XSS注入方式 ##

1. 通过编码绕过并注入。比如UTF-8编码字符的`\u003c\u0073\u0063\u0052\u0069\u0050\u0054\u003e\u003c\u002f\u0073\u0063\u0052\u0069\u0050\u0054\u003e`,这串字符实际是：`<scRiPT></scRiPT>`。

2. 直接在HTML里注入JS。	`<script>alert(‘ok’)</script>`。

3. HTML或CSS注入。 ` %3Cimg%20src=%22地址%22%3E`。

## 如何避免 ##
避免XSS的主要方式是，对输出内容进行过滤。

 - 对特殊字符进行转换：`&` 转成 `&amp;`,双引号`“` 转成 `&quot;`,`<` 转成 `&lt;`,`>` 转成 `&gt;`,单引号`‘` 转成` &#39;`。
 - 后端对数据进行过滤处理：比如PHP的htmlentities()或是htmlspecialchars()
 - 返回是JSON数据的的话先进行一次JSON.parse(data)校验


### 使用W3C的Content-Security-Policy防范XSS ###

CSP（Content-Security-Policy），是一种由开发者定义的安全性政策性申明。通过CSP所约束的的规责指定可信的内容来源（这里的内容可以指脚本、图片、iframe、font、style等等可能的远程的资源）。通过CSP协定，让WEB处于一个相对安全的运行环境中。

CSP可以一定程度减少XSS攻击，但不能完全消除XSS的风险。

举例：
1. 只允许本站资源   
	Content-Security-Policy: default-src "self"
 
2. 允许本站的资源以及任意位置的图片以及http://trustedscripts.example.com下的脚本。    
	Content-Security-Policy: default-src "self"; img-src *;script-src http://trustedscripts.example.com    

更多关于CSP的介绍，可以参考[W3C的相关文档](https://dvcs.w3.org/hg/content-security-policy/raw-file/tip/csp-specification.dev.html#content-security-policy-header-field "https://dvcs.w3.org/hg/content-security-policy/raw-file/tip/csp-specification.dev.html#content-security-policy-header-field")。 