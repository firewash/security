# XSS #

## 介绍 ##
跨网站脚本（Cross-site scripting，通常简称为XSS或跨站脚本或跨站脚本攻击），其本质是一种HTML代码注入攻击。
黑客利用网页开发时留下的漏洞，将恶意代码到网页，当普通用户浏览网页时，恶意代码就可以以浏览用户的身份执行。

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

1. 反射型XSS。非持久性XS，把用户输入的数据反射给浏览器，如：黑客诱使用户点击一个恶意链接，才能攻击成功。

2. 存储型XSS。持久性XSS，Persistent XSS，把用户输入的数据储存在服务器，如：发布一篇带JS代码的博客文章，所有访问改博文的用户都会在他们的浏览器执行该JS。

3. DOM-based XSS。HTML或CSS注入，比如在HTML里写入 ` %3Cimg%20src=%22地址%22%3E`。

4. 通用型XSS。即UXSS，实际上是客户端而非网页程序的漏洞。简单来说，就是浏览器（也可能是其他用户代理程序）本身不安全的话，网页也就不安全。比如Chrome就曾经出现过该漏洞，造成网页可以被注入第三方恶意代码，从而伪造网站或窃取用户网页信息。更多的可以参考[乌云知识库](http://drops.wooyun.org/tools/3186)。应对这种漏洞，通常是及时升级电脑上的浏览器（这里也能看出UC浏览器更新内核程序的必要性）。

5. 通过编码绕过以上XSS类型检查并注入。比如UTF-8编码字符的`\u003c\u0073\u0063\u0052\u0069\u0050\u0054\u003e\u003c\u002f\u0073\u0063\u0052\u0069\u0050\u0054\u003e`,这串字符实际是：`<scRiPT></scRiPT>`。


## 如何避免 ##
避免XSS的主要方式是，对输出内容进行过滤。

 - 对特殊字符进行转换：`&` 转成 `&amp;`,双引号`“` 转成 `&quot;`,`<` 转成 `&lt;`,`>` 转成 `&gt;`,单引号`‘` 转成` &#39;`。
 - 后端对数据进行过滤处理：比如PHP的`htmlentities()`或是`htmlspecialchars()`。
 - 返回是JSON数据的的话先进行一次JSON.parse(data)校验。


### 使用W3C的Content-Security-Policy防范XSS ###

CSP（Content-Security-Policy），是一种由开发者定义的安全性政策性申明，直接从协议层屏蔽了一些安全隐患。通过CSP所约束的的规责指定可信的内容来源（这里的内容可以指脚本、图片、iframe、font、style等等可能的远程的资源）。通过CSP协定，让WEB处于一个相对安全的运行环境中。

CSP可以一定程度减少XSS攻击，但不能完全消除XSS的风险。

举例：
1. 只允许本站资源   
	Content-Security-Policy: default-src "self"
 
2. 允许本站的资源以及任意位置的图片以及http://trustedscripts.example.com下的脚本。    
	Content-Security-Policy: default-src "self"; img-src *;script-src http://trustedscripts.example.com    

更多关于CSP的介绍，可以参考[W3C的相关文档](https://dvcs.w3.org/hg/content-security-policy/raw-file/tip/csp-specification.dev.html#content-security-policy-header-field "https://dvcs.w3.org/hg/content-security-policy/raw-file/tip/csp-specification.dev.html#content-security-policy-header-field")。 