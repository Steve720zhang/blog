### flex 基本用法

```css
.demo {
  flex: 1 1 100px;
}
```

三个值分别为

* `flex-grow`: 占比, 默认为 0;
* `flex-shrink`: 缩减比例, 0 为不缩减, 默认为 1;
* `flex-basis`: flex 基本尺寸, 默认为 auto;

### flex 特殊用法

#### flex + z-index

* 我们知道 `z-index` 要与定位元素(absolute、relative、absolute、sticky)一起使用, 但使用了 flex 布局后, 尽管仍然是 `position: static`, `z-index` 仍然是生效的。

#### flex + margin

`flex` 和 `margin` 结合使用能达到垂直居中的妙用。

> 见 [水平垂直居中]([./水平垂直居中](https://github.com/MuYunyun/blog/blob/master/BasicSkill/css/水平垂直居中.md).md)

#### flex + height: 100%

在 ios10 以下的 ios 版本中, 在 flex 布局中不推荐与 `height: 100%` 一起连用;

* 原因一: 父元素为 flex 布局, 若父元素不设置 `height: 100%`, 子元素设置 `height: 100%` 是继承不到父元素的高度的;
* 原因二: 父元素为 flex 布局, 即使父元素设置了 `height: 100%`, 子元素使用 `height: 100%` 继承的高度也是有偏差的;

一般使用 `flex: 1` 取代 `height: 100%`, 此外要让父元素的高度传给子元素通常需要配合 `flex-direction`。使用如下:

```css
.parentDemo {
  display: flex;
  flex-direction: column;
}
```
