---
title: DOM元素的自动隐藏
date: 2021-05-05 09:38:24
---

在一些有悬浮元素的场景中，比如点击一个按钮弹出菜单后，点击菜单以外的地方，菜单应该被隐藏起来。隐藏的方式最好是自动隐藏，或至少是组件内的自动隐藏。

## 蒙层

比如，一个模态框组件（闭包实现）点击蒙层时，响应蒙层的点击事件，可以在事件处理函数中隐藏整个组件。在 Vue 和 React 等框架的组件中，这一点非常容易实现。

```html
<div class="component">
  <div class="mask"></div>
  <!--蒙层-->
  <div class="content"></div>
  <!--内容-->
</div>
```

```js
let component = document.querySelector('.component')
document.querySelector('.class').addEventListener('click', () => {
  component.style.display = 'none'
})
```

对于没有蒙层和不想使用蒙层的组件，也可以让 document 来监听点击事件：

```html
<div class="component">
  <div class="content"></div>
  <!--内容-->
</div>
```

```js
let component = document.querySelector('.component')
let content = document.querySelector('.content')
document.addEventListener('click', ev => {
  if (ev.target !== component && ev.target !== content) {
    component.style.display = 'none'
  }
})
```

当点击元素不是现在正在交互的悬浮元素时，隐藏元素。用这个方法时需要注意监听函数的删除，切对于所有 component 内的元素都要一一判断，所以很不方便。

这两个方法的共同点时，在悬浮元素区域之外，需要另一个元素来接收点击事件，而不是悬浮元素自身来处理这个事件。

## blur

那么有没有什么属性或方法能让元素自己知道自己之外的区域被点击了呢？那就是失焦（blur）事件。

像 input 等元素本身可以成为焦点，但是想 div 默认是不能获得焦点的，但是可以通过 js 设置 tabIndex 属性为-1 来使得它可以获取焦点：

```js
component.tabIndex = -1
```

然后给元素添加失去焦点事件处理函数，隐藏元素：

```javascript
component.addEventListener('blur', () => {
  component.style.display = 'none'
})
```

在元素显示时聚焦元素：

```javascript
component.focus()
```

在点击悬浮元素区域以内的位置时，元素没有失去焦点，失焦事件不会触发。而点击悬浮元素区域以外时，元素失去焦点，失焦事件触发，元素自动隐藏。

这样，元素只需要设置自身就可以实现自动隐藏。

_参考文献_

[tabindex - HTML（超文本标记语言） | MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Global_attributes/tabindex)
