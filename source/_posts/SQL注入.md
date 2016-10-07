---
title: SQL注入
date: 2016-10-03 13:44:43
tags:
---
什么是SQL注入（SQL Injection）

所谓SQL注入式攻击，就是攻击者把SQL命令插入到Web表单的输入域或页面请求的查询字符串，欺骗服务器执行恶意的SQL命令。在某些表单中，用户输入的内容直接用来构造（或者影响）动态SQL命令，或作为存储过程的输入参数，这类表单特别容易受到SQL注入式攻击。

SQL支持 # 到该行结束、-- 到该行结束 以及 /\* 行中间或多个行 \*/ 的注释方法。

单引号 ' 和双引号 " 用于表示字符串。

SQL注入后即可以获取数据库信息，也可以进一步拿到web shell。

参考网站

[SQL注入原理讲解](http://blog.csdn.net/stilling2006/article/details/8526458)

[SQL注入攻防入门详解](http://www.cnblogs.com/heyuquan/archive/2012/10/31/2748577.html)

[10个SQL注入工具](http://blog.jobbole.com/17763/)

[如何利用SQL注入制造一个后门](http://www.freebuf.com/articles/222.html)