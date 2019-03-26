---
title: HTML5 移动端自适应方案与踩坑
date: 2019-01-29 17:56:36
tags:
  - 前端
  - html
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

比如如果你的设计图是 `750px` 的宽度。 对于 `75px` 的元素就可以设置为 `10vw`。 这样在宽度为 `375px` 的手机上的表现就是`37.5px`。

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

## 坑

以上介绍的适配方案，基本上就可以满足大部分的需求了。 下面我们来聊一聊我都遇到了哪些坑。

### 小数像素问题

由于我们的方案，所有元素根据屏幕宽度来自适应。因而很难保证转换后的像素为整数像素。

在未接触前端，或者说H5开发之前并没有认真考虑过小数像素的问题，最初以为就是在可现实的精度上四舍五入。真正开发时发现并不是这样的。

比如下面这个例子，同样的像素值表现就不一样：[源码地址](https://codesandbox.io/s/l2qm5yn6z7)

{% asset_img "小数像素.png" "小数像素" %}

`IOS`、`macOS`设备最小像素好像支持到了0.5px，所以上面的例子在苹果设备上表现并不是很明显。

但是毕竟大部分设备还是`Android`和`windows`系统。

那么，到底浏览器是如何处理小数像素的呢？ [rem 产生的小数像素问题](http://taobaofed.org/blog/2015/11/04/mobile-rem-problem/) 这篇文章给出了答案：

    浏览器在渲染时所做的舍入处理只是应用在元素的渲染尺寸上，其真实占据的空间依旧是原始大小。
    也就是说如果一个元素尺寸是 0.625px，那么其渲染尺寸应该是 1px，空出的 0.375px 空间由其临近的元素填充；同样道理，如果一个元素尺寸是 0.375px，其渲染尺寸就应该是 0，但是其会占据临近元素 0.375px 的空间。

那么在我们的方案里会出现什么问题呢？

1. 缩放到低于1px的元素会时隐时现
1. 两个同样宽度的元素因为各自周围的元素宽度不同，导致两元素相差1px
1. 宽高相同的正方形，长宽不相等了
1. `border-radius: 50%` 画的圆不圆了

对于第一个问题，一般都会出现在标注为`1px`的地方。所以大部分的插件 [postcss-pxtorem](https://github.com/cuth/postcss-pxtorem) 或者 [postcss-px-to-viewport](https://www.npmjs.com/package/postcss-px-to-viewport) 都提供了最小转换像素的选项。 我们只要指定最小转换像素，对于比较小的像素（如：`1px`），就不转换为`rem`或`vw`了。当然`1px`在视网膜屏同样存在过粗的问题，我们在之后会讨论。

对于剩下的几个问题，目前本人也没找到特别好的办法，毕竟很多地方相差`1px`是可以接受的。只有一些比较小的元素会表现的比较明显，本人的解决办法是不通过插件自动转换为`rem`或`vw`，而是通过`js`根据设备宽度，计算出该元素在该设备下实际的`px`。取整后动态地设置到元素的`style`上。这样就不会出现上述问题了。

如果各位有更好的解决方案的话。欢迎浏览讨论。

### 1px问题

由于上面小数像素的问题，我们并没有对`1px`的元素进行转换，所以对于`750px`的设计图上`1px`的细线，在屏幕宽度为`375px`的`iphone6`上依旧为`1px`，按比例应该是`0.5px`。所以设计同学会问：“为什么这条细线变粗了？” 我们也很无奈啊，因为`0.5px`显不出来啊……

但是转念一想，对于`DPR=2`甚至更高的设备，`1px`是由多个物理像素渲染的，其实是可以显示更细的线的。那么这样才能画出更细的线呢？

大漠老师又出场了，[《再谈Retina下1px的解决方案》](https://www.w3cplus.com/css/fix-1px-for-retina.html)中给出了几种方案：

1. `viewport`放大为`device-width`的`dpr`倍数，然后缩小`1/dpr`倍显示
1. `border-image`设为一个一半透明一半显示的图片，以达到将边框一分为二的目的
1. 同样是上面的原理，但是使用`svg`绘制图片
1. 媒体查询配合伪元素，为伪元素设置`1px`的边框，然后缩小`1/dpr`倍显示

以上方案各有各的特点，2、3两个方案画出来的其实是`0.5px`，而1、4两个方案画出来的更接近`物理像素的1px`

### cursor:pointer 元素点击背景变色的问题

对于添加了 `cursor:pointer` 属性的元素，在移动端点击时，背景会高亮。

为元素添加 `-webkit-tap-highlight-color: transparent;` 属性可以隐藏背景高亮。

### Android浏览器下line-height垂直居中偏离的问题

我们常用的垂直居中方式就是使用`line-height`，但是这种方法在`Android`设备下并不能完全居中。

具体原因是因为`Android`中文字体排版的问题，可以参考 [知乎：Android浏览器下line-height垂直居中为什么会偏离？](https://www.zhihu.com/question/39516424/answer/274374076)

通过设置字体，确实能够解决一部分偏离的问题。但仍然会出现一些略微偏离的情况，据说与行高奇数偶数有关。不过已经不太容易分辨了，如果还是不能接受的话建议通过设置上下padding的方式进行垂直居中，再根据具体情况进行微调。


## 参考文章

* [使用Flexible实现手淘H5页面的终端适配](https://github.com/amfe/article/issues/17)
* [再聊移动端页面的适配](https://www.w3cplus.com/css/vw-for-layout.html)
* [rem 产生的小数像素问题](http://taobaofed.org/blog/2015/11/04/mobile-rem-problem/) 
* [再谈Retina下1px的解决方案](https://www.w3cplus.com/css/fix-1px-for-retina.html)
* [Android浏览器下line-height垂直居中为什么会偏离？](https://www.zhihu.com/question/39516424/answer/274374076)


---

欢迎关注公众号 “大前端开发者”。给你带来更多的前端技术与资讯

![qrCode](/images/qrcode_small.jpg)


