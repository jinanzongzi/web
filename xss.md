# xss（跨站脚本漏洞）

跨站脚本攻击是指恶意攻击者往Web页面里插入恶意Script代码，当用户浏览该页之时，嵌入其中Web里面的Script代码会被执行，从而达到恶意攻击用户的目的。

XSS漏洞经常出现在需要用户输入的地方，这些地方一旦对输入不进行处理，黑客就可以进行HTML注入，进而篡改网页。

关键是寻找参数未过滤的函数

常见的输出函数有： `echo printf print print_r sprintf die var-dump var_export`

1. ### 反射型xss

   反射性xss一般指攻击者通过特定的方式来诱惑受害者去访问一个包含恶意代码的URL。当受害者点击恶意链接url的时候，恶意代码会直接在受害者的主机上的浏览器执行。

   反射型XSS的攻击

   1. 攻击者在url后面的参数中加入恶意攻击代码。
   2. 当用户打开带有恶意代码的URL的时候，网站服务端将恶意代码从URL中取出，拼接在html中并且返回给浏览器端。
   3. 用户浏览器接收到响应后执行解析，其中的恶意代码也会被执行到。
   4. 攻击者通过恶意代码来窃取到用户数据并发送到攻击者的网站。攻击者会获取到比如cookie等信息，然后使用该信息来冒充合法用户
      的行为，调用目标网站接口执行攻击等操作。

   #### 防御

   ```jewel
   A.PHP直接输出html的，可以采用以下的方法进行过滤：
   
       1.htmlspecialchars函数
       2.htmlentities函数
       3.HTMLPurifier.auto.php插件
       4.RemoveXss函数
   
   B.PHP输出到JS代码中，或者开发Json API的，则需要前端在JS中进行过滤：
   
       1.尽量使用innerText(IE)和textContent(Firefox),也就是jQuery的text()来输出文本内容
       2.必须要用innerHTML等等函数，则需要做类似php的htmlspecialchars的过滤
   
   C.其它的通用的补充性防御手段
   
       1.在输出html时，加上Content Security Policy的Http Header
       （作用：可以防止页面被XSS攻击时，嵌入第三方的脚本文件等）
       （缺陷：IE或低版本的浏览器可能不支持）
       2.在设置Cookie时，加上HttpOnly参数
       （作用：可以防止页面被XSS攻击时，Cookie信息被盗取，可兼容至IE6）
       （缺陷：网站本身的JS代码也无法操作Cookie，而且作用有限，只能保证Cookie的安全）
       3.在开发API时，检验请求的Referer参数
       （作用：可以在一定程度上防止CSRF攻击）
       （缺陷：IE或低版本的浏览器中，Referer参数可以被伪造）
   ```

2. ### 存储型xss

   和反射性XSS的即时响应相比，存储型XSS则需要先把利用代码保存在比如数据库或文件中，当web程序读取利用代码时再输出在页面上执行利用代码。但存储型XSS不用考虑绕过浏览器的过滤问题，屏蔽性也要好很多。

   存储型xss攻击

   1.  攻击者将恶意代码提交到目标网站数据库中。
   2. 用户打开目标网站时，网站服务器将恶意代码从数据库中取出，然后拼接到html中返回给浏览器中。
   3. 用户浏览器接收到响应后解析执行，那么其中的恶意代码也会被执行。
   4.  那么恶意代码执行后，就能获取到用户数据，比如上面的cookie等信息，那么把该cookie发送到攻击者网站中，那么攻击者拿到该cookie然后会冒充该用户的行为，调用目标网站接口等违法操作。

   <img src="C:\Users\粽子\Pictures\素材\6230889-07ab49e8b1ea148a.png" alt="6230889-07ab49e8b1ea148a" style="zoom: 33%;" />

   一次提交之后，每当有用户访问这个页面都会受到XSS攻击。

   #### 防御

   1. 后端需要对提交的数据进行过滤。
   2.  前端也可以做一下处理方式，比如对script标签，将特殊字符替换成HTML编码这些等。

   - addslashes() 函数返回在预定义字符之前添加反斜杠的字符串。
   - strip_tags() 函数剥去字符串中的 HTML、XML 以及 PHP 的标签。
   - htmlspecialchars 函数把预定义的字符转换为 HTML 实体。
   - str_replace() 字符串替换
   - preg_replace()进行正则表达式过滤script关键字

