---
title: XSS攻击
date: 2016-10-10 22:45:37
tags:
---
跨站脚本攻击(Cross Site Scripting): 恶意攻击者往Web页面里插入恶意Script代码，当用户浏览该页之时，嵌入其中Web里面的Script代码会被执行，从而达到恶意攻击用户的目的。

1) 恶意用户在一些公共区域
( 例如建议提交表单或消息公共板的输入表单 )输入一些文本，这些文本被其它用户看到，但这些文本不仅仅是恶意用户要输入的文本，同时还包括一些可以在客户端执行的脚本。如：
```
<script>
this.document = "*********";
</script>
```
2) 恶意提交这个表单
3) 其他用户看到这个包括恶意脚本的页面并执行，获取用户的cookie等敏感信息。
![XSS](img/XSSAttack.jpg "xss")

xss攻击可以分成两种类型：

1.非持久型攻击

2.持久型攻击

上面那个属于持久型攻击。具体解释参考下面的"xss攻击入门"。

***
参考网站

[xss攻击入门](http://www.cnblogs.com/bangerlee/archive/2013/04/06/3002142.html)

[XSS的原理分析与解剖](http://netsecurity.51cto.com/art/201408/448305_all.htm)

[XSS攻击及防御](http://blog.csdn.net/ghsau/article/details/17027893)