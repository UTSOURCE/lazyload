# lazyload
功能轻便、代码精简的图片懒加载插件 - by es6

工作中需求中经常会用到图片懒加载的功能，这种功能实现起来并不难，但一次性写下来代码量也不会小。

网上类似的插件倒是一大堆，但是功能完善逻辑严谨的体积太大，里面包含了大量根本用不到的功能和代码，凭空增加文件体积；至于体积小的，又总怀疑逻辑写得清不清楚，会不会有什么bug之类的，所以比较纠结。

索性有时间就自己写了一个，下次再用到这个功能的时候，就不会再嫌弃这个嫌弃那个了，并且代码是自己写的，就算有什么bug，自己也知道该怎么改，也能够很轻松地根据实际需求增删代码，避免陷入逻辑严谨但代码臃肿或者代码精简但逻辑不严谨的怀疑之中。

---
## 简单介绍 

代码使用 `ES6`语法，这个插件其实是一个 `class`，`babel`打包后可兼容到 `IE9`完全没问题的，甚至更低或许也可以，没试过，原生`js`无任何依赖，兼容移动端与 `pc`端。

核心代码其实就几行而已：
```
if ((rect.top > 0 && this.H + distance >= rect.top) || (rect.top < 0 && (rect.top + rect.height >= -this.distance))) {
  if ((rect.left > 0 && this.W + distance >= rect.left) || (rect.left < 0 && (rect.left + rect.width >= -this.distance))) {
    this.loadItem(ele)
    this.elements.splice(i, 1, null)
  }
}

```

上述代码就是判断元素有没有出现在浏览器视窗内，以决定是否让元素加载对应的图片，并且将在图片懒加载的同时，将元素从尚未懒加载的DOM集合中去掉，优化性能

![showpicture](https://github.com/accforgit/lazyload/blob/master/lazyload.gif)

---

## 用法

只需要对这个类`LazyLoad`进行初始化即可：

```
let lazyload = new LazyLoad()
lazyload.init({elements: domObjList})
```

为了更方便的传参，`lazyLoad`类的 `init`方法接受 `1`个 `Object`类型的参数，此参数最多有 `6`个自有属性，一个必选，五个可选。

|参数名|类型|描述|默认值|是否必选|
|---|---|---|---|---|
|`elements`|`NodeElement`|懒加载的元素`DOM`对象，例如使用 `document.querySelector('.img')获取的结果就是此类型`<br>之所以没有兼容直接传递一个类名或者 `id`让插件自己获取`DOM`对象，而是让使用者自己传入，这是一种权衡之后的结果，因为我觉得这个插件主要的功能就是懒加载，如果因为多加了一个可有可无的选择器功能而让插件变得臃肿，反而不美|-|是|
|`distance`|`number`|当元素距离浏览器可视区域边缘多远时进行图片的加载动作，距离单位是`px`|`0`|否|
|`tag`|`string`|元素标签上用于懒加载的属性名，值为需要懒加载的图片地址|`data-src`|否|
|`frequency`|`number`|插件通过监听页面的`scroll`、`resize`和`touchmove`事件来不断获取元素的是否进入视野内的信息，此参数用于事件节流，此值越小，则占用的浏览器资源越多|`14`|否|
|`isBg`|`boolean`|插件支持直接的 `img`标签懒加载，同时也可支持其他元素的背景图片懒加载，默认是支持 `img`标签的图片懒加载|`false`|否|
|`defaultImg`|`string`|在元素懒加载正确的图片之前显示的替代图片，默认没有|-|否|



如果你想懒加载的图片是一个 `div`的背景图，并且将图片地址附在 `imgurl`这个自定义属性上，还规定当元素距离视野边缘 `50px`时就开始进行懒加载，节流频率为 `20ms`, 懒加载的替代图片是 `https://dummyimage.com/200x200/ff0ff0&text=66`，例如：
```
<div class="bgBox" imgurl="http://exmple.com/1.png"></div>
```

则需要这样初始化：

```
let lazyload = new LazyLoad()
lazyload.init({elements: document.querySelectorAll('.bgBox'), distance: 50, tag: 'imgurl', frequency: 20, isBg: true, defaultImg: 'https://dummyimage.com/200x200/ff0ff0&text=66'})
```

另外，需要注意的是，如果你再初始化并调用 `init`方法之后，页面又追加了需要懒加载的元素，则需要再次调用 `init`方法，并将你新追加进来的元素`DOM`对象传入 `elements`属性，来将懒加载应用于新追加进来的元素上。

也就是类似于这种：
```
lazyload.init({elements: newDOMObj})
```


算上空行和注释也就100行左右的代码量，整个插件就是一个 `ES6`的 `class`类定义的，所以逻辑看起来是很清晰的，没有什么道道，看一遍就知道是怎么回事，可以很轻松地进行修改。

