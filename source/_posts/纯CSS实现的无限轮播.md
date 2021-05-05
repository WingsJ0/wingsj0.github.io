---
title: 纯CSS实现的无限轮播
date: 2021-05-05 10:49:26
categories: 编程
---

轮播是常见的展示多图和多文字的方式，有很多种类，比如离散周期播放和连续无限播放。

有很多库封装了这一组件，这些库的功能非常强大，可以适应很多场景。但是如果理解了轮播的原理，可以使用纯 CSS 实现，对于业务中应对多变的需求有很大的帮助。

这篇文章想分享下一个用纯 CSS 实现的无限轮播的方法。

## 场景

比如，现在有这样一个需求：通过横向移动的文字来展示公告，一条接着一条，第一条接着最后一条，无限循环。

先看效果：

![](./0.gif)

或点击查看[网页示例](https://works-wings.oss-cn-hangzhou.aliyuncs.com/WebAnimationTricks/data/InfiniteSlideText/page/Index.html)

## 方法

### 容器

首先我们创建好容器：

```html
<style>
  #wrap {
    overflow: hidden;
    width: 600px;
    white-space: nowrap;
  }
</style>

<div id="wrap"></div>
```

容器为限定宽度，且隐藏溢出内容的 div，设置`white-space: norwap`使文本不换行。

### 文字

加入文字：

```html
<div id="wrap">
  <div id="slide">故事的小黄花 从出生那年就飘着 童年的荡秋千 随记忆一直晃到现在 Re So So Si Do Si La So La Si Si Si Si La Si La So </div>
</div>
```

其中`#slide`就是需要移动的文字。

### 动画

要让文字动起来，设置 animation 属性即可：

```html
<style>
  #slide {
    animation: slide 10s linear infinite;
  }

  @keyframes slide {
    0% {
      transform: translateX(0);
    }
    100% {
      transform: translateX(-100%);
    }
  }
</style>
```

在文字长度不确定的情况下，我们先假定移动`-100%`的距离。

效果如下：

![](1.gif)

这里有两个问题：

1. 移动的距离不是文字的长度
2. 文字没有首尾衔接

### 移动距离

第一个问题出现的原因是`transform: translateX`移动的百分不距离是已自身为参照，但是因为`#slide`是处于文档流中块级元素，其宽度默认为父元素的宽度。所以文字只会移动父元素宽度而不是文本本身长度的距离。

要解决这个问题，可以让`#slide`脱离文档流，是的文本撑开其宽度即可，我们使用`position: absolute`的方法。示例：

```html
<style>
  #wrap {
    overflow: hidden;
    position: relative;
    width: 600px;
    height: 20px;
    white-space: nowrap;
  }
  #slide {
    position: absolute;
    animation: slide 10s linear infinite;
  }
</style>
```

效果如下：

![](2.gif)

### 首尾相连

我们来看看第二个问题，试想，对于一段文字而言，除非出现虫洞，否则是不可能首尾相连的。那如果有两段一模一样的文本呢？

第一段文本的尾部链接第二段文本的首部，因为两端文本的内容一样，所以在视觉上就好像是一段文本首尾相连了。不过对于第二段文本的末尾又会有首尾不相连的问题，我们总不能无限制的添加相同的文本来达到效果。

我们知道动画在默认情况下播放完成后是突变的，即播放完成是元素位置突变到初始位置（入上图所示）。文字在移动到最后的部分后，留出一段空白，然后突然又回到开头。我们可以利用这个突变，想办法让突变前后的视觉效果是一致的。

我们现在有两段相同的文本，试想，我们不需要让两段文本都播放完成。当第一段文本播放完成时，移动了 50%的距离，此时后面衔接了第二段文本的首部。如果此时动画突变到开始位置，第一段文本的首部代替了此时第二段首部的位置，同时动画又回到了开始的位置。在视觉上，我们感受不到这一位置的突变，因为两段文本是一模一样的。

实现代码如下：

```html
<style>
  @keyframes slide {
    0% {
      transform: translateX(0);
    }
    100% {
      transform: translateX(-50%);
    }
  }

  <div id="slide">
    <span>故事的小黄花 从出生那年就飘着 童年的荡秋千 随记忆一直晃到现在 Re So So Si Do Si La So La Si Si Si Si La Si La So</span>
    <span>故事的小黄花 从出生那年就飘着 童年的荡秋千 随记忆一直晃到现在 Re So So Si Do Si La So La Si Si Si Si La Si La So</span>
  </div>
```

我们把动画的移动距离设置为`-50%`，同时安排两段相同的文本。当文本移动两段文本一半的距离（即一段文本的距离）时，动画重新开始，突变到起始位置，在视觉上文本没有移动，就有了首尾相连的效果。

### 总结

无限轮播的要点如下：

1. 两段相同的文本
2. 移动 50%的距离

Demo 代码如下：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>无限轮播文字</title>

    <style>
      body {
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
      }

      #wrap {
        overflow: hidden;
        position: relative;
        width: 600px;
        height: 20px;
        padding-bottom: 5px;
        border-bottom: 1px solid #cccccc;
        white-space: nowrap;
      }

      #slide {
        position: absolute;
        animation: slide 10s linear infinite;
      }

      @keyframes slide {
        0% {
          transform: translateX(0);
        }
        100% {
          transform: translateX(-50%);
        }
      }
    </style>
  </head>
  <body>
    <div id="wrap">
      <div id="slide">
        <span>故事的小黄花 从出生那年就飘着 童年的荡秋千 随记忆一直晃到现在 Re So So Si Do Si La So La Si Si Si Si La Si La So</span>
        <span>故事的小黄花 从出生那年就飘着 童年的荡秋千 随记忆一直晃到现在 Re So So Si Do Si La So La Si Si Si Si La Si La So</span>
      </div>
    </div>
  </body>
</html>
```
