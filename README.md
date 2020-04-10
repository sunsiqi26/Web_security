# Web安全攻防
* [SQLi漏洞](#SQLi漏洞)
* [XSS漏洞](#XSS漏洞)
* [文件上传漏洞](#文件上传漏洞)
* [代码审计](#代码审计)
* [综合渗透](#综合渗透)

### SQLi漏洞
- 定义：由于Web应用程序对用户输入数据的合法性没有判断或过滤不严，攻击者可以在Web应用程序中事先定义好的查询语句的结尾上添加额外的SQL语句，以此来实现欺骗数据库服务器执行非授权的任意查询。
- 漏洞利用常用步骤：
    - 找到注入点，判断是字符型还是数字型
    - 字符型注入:通过回显判断闭合方式:‘，) and #。找到元素个数和位置
    - 判断数据库结构，确定显示位
    - 查询数据：查库名、查表名、查列名、查数据

- sqli-labs通关：
将sqli-labs解压后的文件夹移入phpstudy的WWW根目录下，修改 db.php 中自己的SQL账号和密码，每关的详细操作与截图可以查看[pdf文档](具体步骤截图说明.pdf)
    - Less-1：字符型注入，闭合符就为单引号，通过orderby报错判断表的列数
    ```http://127.0.0.1/sqli-labs/sqli-labs/Less-1/?id=1' union select1,2,group_concat(username,”:”,password ) from users--+```
    - Less-2：数字型注入，最终输入 `http://127.0.0.1/sqli-labs/sqli- labs/Less-2/?id=1 union select1,2,group_concat(username,”:”,password ) from users--+`， 获取用户名和密码。
    - Less-3：字符型，闭合符是')，`http://127.0.0.1/sqli-labs/sqli-labs/Less-3/?id=1') union select1,2,group_concat(username,”:”,password ) from users--+`
    - Less-4：字符型，闭合符是")，`http://127.0.0.1/sqli-labs/sqli-labs/Less-4/?id=1”) union select1,2,group_concat(username,”:”,password ) from users--+`
    - Less-5：可以使用floor报错注入或者sqlmap
    - Less-6：布尔盲注或者sqlmap

### XSS漏洞
- 定义：XSS攻击是由于Web应用程序对用户输入过滤不足而产生的，使得攻击者输入的特定数据变成了JavaScript脚本或HTML代码
- 同源策略：A网页设置的 Cookie，B网页不能打开，除非这两个网页"同源"。所谓"同源"指的是"三个相同"：协议相同、域名相同、端口相同
- XSS类型：反射型、存储型、DOM型
- 通关：
将x压缩包解压后的文件夹移入phpstudy的WWW根目录下，每关的详细操作与截图可以查看[pdf文档](具体步骤截图说明.pdf)
    - Level-1，Level-2：观察网站源码，闭合字符串或html标签
    - Level-3，Level-4：点击触发，`'onclick='alert()`
    - Level-5，Level-6：通过超链接`<a>`标签
    - Level-7：多层嵌套
    - Level-8：unicode 编码绕过
    - Level-9：用/**/注释http://
    - Level-10：先输入的对象为，后赋值`"onmouseover="alert() " type="button`
    - Level-11：发用 burpsuite 在头部添加 referer 属性
    - Level-12：新增了 t_ua 对象，用 burpsuite 抓包修改 User-Agent
    - Level-13：新增了 t_cook 对象，用 burpsuite 抓包修改 Cookie
    - Level-14：根据提示对 name 赋值 `name=";alert();//`
    - Level-15：javascript 被空格替换，空格也被其他字符替换， 用%0a 绕过空格过滤
    - Level-16：alert 被过滤，用`confirm() `

### 文件上传漏洞
- 原理：由于文件上传功能实现代码没有严格限制用户上传的文件后缀以及文件类型，导致允许攻击者向某个可通过 Web 访问的目录上传任意 PHP/ASP/JSP 文件，并能够将这些文件传递给 PHP/ASP/JSP 解释器，在远程服务器上执行任意脚本。
- 漏洞利用步骤：
    - 找到 Web 页面中的上传点
    - 尝试使用各种方法绕过检测及过滤(前端绕过、大小写/双写/生僻扩展名绕过、截断绕过、解析漏洞及文件包含等)，上传恶意 PHP 文件
    - 上传成功，进而控制整个网站
- Upload-labs 闯关
将upload-labs.rar解压后的文件夹移入phpstudy的WWW根目录下，每关的详细操作与截图可以查看[pdf文档](具体步骤截图说明.pdf)
    - PASS-01: 可以上传的文件类型为 jpg 等，将 php 扩展名改为 jpg 再用 burpsuite 拦截上传将 filename 由.jpg 改为.php
    - Pass-02: 上传文件类型 type 处被限制，用 burpsuite 拦截，将 content-type 中的 text/php 改为允许的 image/jpeg
    - Pass-03: 限制文件后缀，用 burpsuite 拦截，将 filename 的后缀随意改为.abc
    - Pass-04: 过滤了前后字符、.、全转换为小写、去除字符串和首尾去空，用 burpsuite 拦截修改将 filename的后缀为.pphphp
    - Pass-05: 后缀限制没有转换为小写，用 burpsuite 拦截修改将 filename 的后缀为.Php
    - Pass-06: 没有首尾去空，用 burpsuite 拦截修改将 filename 的后缀为.php加空格。
    - Pass-07: 用burpsuite 拦截修改将 filename 的后缀为.php. 
    - Pass-08: 没有过滤字符串`::$DATA`，用 burpsuite 拦截修改 filename 的后缀 为`.php::$DATA`
    - Pass-09，Pass-10：与Pass-04方法类似
    - Pass-11: pass 上传路径可控，新增加 GET 的截断，用%00 绕过。用 burpsuite 拦截在 upload 后加 shell.php%00 。
    - Pass-12: 与上一关类似但是将_GET 改为_POST。
    - Pass-13，Pass-14: 任务由原来的上传文件变为上传图片马，利用 windows 自带的 copy 命令制作图片马，由于文件头决定了文件的类型因此要将图片文件放在前面 修改后缀的 shell 文件放在后面，直接上传

### 代码审计
- 检查源代码中的安全缺陷，检查程序源代码是否存在安全隐患，或者有编码不规范的地方，通过自动化工具或者人工审查的方式，对程序源代码逐条进行检查和分析，发现这些源代码缺陷引发的安全漏洞，并提供代码修订措施和建议。
- PHP 安全闯关：
将scuphpseceasy.zip解压后的文件夹移入phpstudy的WWW根目录下，每关的详细操作与截图可以查看[pdf文档](具体步骤截图说明.pdf)

    - babyconverage1：$$a == $($a) == $($a=b) == $(b) == $b=phpinfo(); 令a=b&b=phpinfo();。
    - babyconverage2：$$a == $($a=a=...) == $(a=phpinfo());system(ipconfig)，令payload为 a=a=phpinfo());system(ipconfig
    - babyeval1，babyeval2，babyeval3：先令前面echo闭合再显示 phpinfo();
    - babypreg1：在 a 处用正则表达式匹配 c 处的字符然 后用 b 的字符替换，返回 c。令 payload: `a=/(.*)/e&b=phpinfo();&c=x`
    - babypreg2：解构`/\[(.*)\]/e`，开始和结尾的/标记正则表达式的开头结尾，[]方括号表示“字符组”，表示该组中的任何字符都被接受。(.*)是一个新的 paranthesized 子组。
    - lowconvert1：观察 php 代码,当 answer=flag 时显示 phpinfo，直接令 answer=flag
    - lowconvert2：函数 file_get_contents() 把整个文件读入一个字符串中。 使用伪协议， data://text/plain，令 `payload:a=1&b=data://text/plain;base64,MQ==`
    - midconvert1：需要类型为 POST，利用 burpsuite 抓包查看。foreach 循环遍历 GET 和 POST 参数中的变量将 value 赋值给 key(key 就 相当于 flag 的值)，如果传入的 flag 值不等于 phpinfo()最后 echo 显示 flag 变 量并 eval 执行 php 代码。Burpsuite 抓包并将 GET 改为 POST，在 POST 中为 flag 赋值并将_200 内容改变，这样 eval 的内容就会显示 phpinfo


### 综合渗透
- 首先要明确渗透目标，对信息进行收集：浏览网站初步确定网站的类型、查看网站功能模块，比如是否有论坛，邮箱等、重点记录网站所有的输入点(与数据库交互的页面)，比如用户登录，用户注册，留言板等。然后分析网站的url: 利用搜索引擎、对网站的域名进行反查。审查代码:重点对输入代码(比如表单)进行分析，看如何来提交输入，客户端做了哪些输入的限制方法。通过查看 web 服务器的版本，确定搜索是否有低版本服务器组件和框架的漏洞，比如通用的 Java 框架 Struct2 的漏洞。
- 实验具体内容见[pdf文档](具体步骤截图说明.pdf)
