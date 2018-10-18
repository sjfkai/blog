---
title: Highcharts XSS攻击简析与修复
date: 2018-10-18 13:42:03
tags:
---

最近工作上接手的一个项目使用到了[Highcharts](https://www.highcharts.com/)。

它是一个开源图标库。

## XSS攻击简析

该库允许使用者传入 HTML 来自定义图表的某些部分。

比如官方实例：[An HTML table in the tooltip](http://jsfiddle.net/gh/get/library/pure/highcharts/highcharts/tree/master/samples/highcharts/tooltip/footerformat/)

恰好我司业务需求中，要把用户输入的内容展示在 `tooltip` 中。

这就很容易出现 [XSS攻击](https://zh.wikipedia.org/wiki/%E8%B7%A8%E7%B6%B2%E7%AB%99%E6%8C%87%E4%BB%A4%E7%A2%BC) 漏洞。


假设上面实例的中 `series` `name` 为用户输入的内容，如果用户输入为

    <img src=0 onerror=alert(1) />Short

时，就触发了XSS攻击。[有漏洞的代码](http://jsfiddle.net/q59oh6yp/3/)


此处并非 `Highcharts` 的漏洞，任何允许自定义HTML的地方都会存在此问题。

这就要求我们在使用用户输入的内容渲染图表时，一定要特别注意。

## 修复漏洞

关于`XSS攻击`各位可以参考美团的文章：[前端安全系列（一）：如何防止XSS攻击？](https://segmentfault.com/a/1190000016551188#articleHeader4)

如文章中所说，我们在拼接HTML时，需要把用户输入的内容进行转义。

大家可以使用 [lodash.escape](https://lodash.com/docs/4.17.10#escape)

如果觉得使用第三方库太麻烦也可以直接引用`lodash.escape的源码`:

```js
const htmlEscapes = {
  '&': '&amp',
  '<': '&lt',
  '>': '&gt',
  '"': '&quot',
  "'": '&#39'
}
const reUnescapedHtml = /[&<>"']/g
const reHasUnescapedHtml = RegExp(reUnescapedHtml.source)
function escape(string) {
  return (string && reHasUnescapedHtml.test(string))
    ? string.replace(reUnescapedHtml, (chr) => htmlEscapes[chr])
    : string
}
```

[再次修改后的代码](http://jsfiddle.net/sjfkai/q59oh6yp/9/) 漏洞已修复。










