---
title: HTML5 移动端自适应方案与踩坑
tags:
---

最近刚接触前端开发，接手了一个移动端H5项目。着实体会掉了前端的坑之多，和H5移动端的坑之多多。

如今项目告一段落，在这里做一总结

## 屏幕自适应方案

介绍方案之前，首先还是交代一下项目背景与需求，毕竟一切方案也不能脱离实际需求。

### 需求与背景

* 设备宽度 > 800px， body宽度为800px
* 320px < 设备宽度 < 800px， 宽度根据设备宽度自适应
* 设备宽度 > 320px, body宽度为320px
* 大部分字体不随宽度变化而缩放
* 设计图宽度：1080px 

### 自适应方案

关于自适应方案，google一搜就会有很多结果，但是总的来说个人认为最有用的还是手淘的大漠写的一系列文章，后面会给出原文链接。总的来说主流的方案有`rem`和`vh`两种。

#### REM（flexible）

`rem`（font size of the root element）是指相对于根元素的字体大小的单位。简单的说它就是一个相对单位。看到`rem`大家一定会想起`em`单位，`em`（font size of the element）是指相对于父元素的字体大小的单位。它们之间其实很相似，只不过一个计算的规则是依赖根元素一个是依赖父元素计算。

所以简而言之，就是根据屏幕宽度设置 `html` 标签的 `font-size`。 再在布局时使用 `rem` 单位来布局，就可以达到自适应的目的。

使用此方案，可以借助手淘的开源项目[lib-flexible](https://github.com/amfe/lib-flexible)。它可以自定帮你设置`html` 标签的 `font-size`等。将`1rem`设置为屏幕的`1/10`。

关于 `rem` 方案，大漠老师在[使用Flexible实现手淘H5页面的终端适配](https://github.com/amfe/article/issues/17)中进行了详细的介绍。建议大家阅读一下。

如你所见，大漠老师也在近期对文正进行了更新，建议大家使用更方便的 `vw` 方案。

#### VW

`vw` 是视口宽度的1/100，用 `vw` 来做自适应再合适不过了。 

比如如果你的设计图是 `750px` 的宽度。 对于 `75px` 的元素就可以设置为 `1vw`。 这样在宽度为 `375px` 的手机上的表现就是`37.5px`。 

当然，如果我们把每个 `px` 标注都手动转换的话，那也是很大的工作量，
[postcss-px-to-viewport](https://www.npmjs.com/package/postcss-px-to-viewport)可以自定帮你转换为 `vw`。 你只需要在配置时指定设计图宽度就可以了。

同样，强烈建议你去阅读以下大漠老师关于 `vw` 布局的文章 [再聊移动端页面的适配](https://www.w3cplus.com/css/vw-for-layout.html)

#### 我的方案 vw + rem

`vw` 虽好，可惜却无法满足我的需求。因为 `vw` 是整个视口宽度的1%，如果单纯采用 `vw` 方案，是无法限制最大、最小宽度的。

于是我便采用了 `vw` + `rem`。 类似于
如果屏幕宽度在需要自适应的宽度之内，则将`html` 标签的 `font-size`设置为 `10vw`。如果屏幕宽度超过最大或最小限制的话。则将`html` 标签的 `font-size`设置为固定值。类似于`lib-flexible`，我们将`1rem`设为了屏幕的`1/10`。

具体 css 如下：

```css
html {
  height: 100%;
  font-size: 10vw;
}
body {
  font-size: 16px;
  width: 100%;
  height: 100%;
  margin: 0 auto;
}

@media screen and (max-width: 320px) {
  html{
    font-size: 32px;
  }
  body{
    min-width: 320px;
  }
}
@media screen and (min-width: 800px) {
  html{
    font-size: 80px;
  }
  body{
    max-width: 800px;
  }
}
```

当然，这样在布局时，我们就需要使用`rem`单位来布局了。 当然设计图标注`px`转`rem`单位同样有现成的工具。博主使用的是[postcss-pxtorem](https://github.com/cuth/postcss-pxtorem)。

最终的效果：

{% asset_img "效果.gif" "效果图" %}





