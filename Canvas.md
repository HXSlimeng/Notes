### 解决canvas模糊的问题

> 由于canvas属于位图而不是矢量图所以高像素下会失真
>
> `dpr`:电脑屏幕使用几个像素点来绘制图片的一个像素点
>
> `bsr`:该属性决定浏览器在渲染canvas之前会用几个像素来存储画布信息

```typescript
//获取像素缩放比例
export const PIXEL_RATIO = (() => {
  const ctx = document.createElement('canvas').getContext('2d'),
    dpr = window.devicePixelRatio || 1,
    bsr = ctx['webkitBackingStorePixelRatio'] ||
      ctx['mozBackingStorePixelRatio'] ||
      ctx['msBackingStorePixelRatio'] ||
      ctx['oBackingStorePixelRatio'] ||
      ctx['backingStorePixelRatio'] || 1;

  const ratio = dpr / bsr;
  return ratio < 1 ? 1 : ratio;
})();
```

1、*`globalCompositeOperation`*

该属性可以标识绘制多图形时的混合模式操作的字符串

默认`source-over`新添加的图形在旧图形上方

2、`save`和`restore`

​	`save`用来保存画布的状态、`restore`可以将画布的状态返回为上一次保存的结果、防止画布状态混淆

3、`clip`裁切一部分画布内容