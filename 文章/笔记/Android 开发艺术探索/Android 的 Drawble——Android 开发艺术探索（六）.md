## 6.1 Drawable 简介
在 Android 的设计中，Drawable 是一个抽象类，他是所有 Drawable 对象的基类。

Drawable 中的内部宽/高可以通过 getIntrinsicHeight 方法获得。一般来说，Drawable 是没有大小概念的，当用作 View 的背景时，Drawable 会被拉伸至 View 的同等大小。

## 6.2 Drawable 的分类

### 6.2.1 BitmapDrawable

实际开发中可以直接引用原始图片，也可以通过 xml 的方式描述他，可以设置更多效果。示例

```
<?xml version="1.0" encoding="utf-8"?>
<bitmap xmlns:android="http://schemas.android.com/apk/res/android"
    android:src="@drawable/pic"
    android:antialias="true"
    android:dither="true"
    android:filter="true"
    android:gravity="top"
    android:tileMode="repeat"
    android:mipMap="false"/>
```
1. src，资源 id
2. antialias， 抗锯齿，建议开启
3. dither，抖动效果，当图片质量高于屏幕质量时，可以防止失真，建议开启
4. filter，过滤，当图片被压缩或压缩时，保证较好的效果，建议开启
5. gravity，设置图片位置
6. mipMap，文理映射，默认 false
7. tileMode，平铺模式，开启后会忽略 gravity 属性。参数值：disabled 关闭，repeat 平铺，mirror 镜像翻转，clamp 四周像素会扩展到周围区域。

**NinePatchDrawable** 使用 nine-patch 标签，其属性与 BitmapDrawable 相同。

### 6.2.2 ShapeDrawable
<shape> 的 shape 属性有四个值：rectangle（矩形），oval（椭圆），line（横线），ring（圆环）。默认是矩形，line 和 ring 必须要使用 <stroke> 置顶宽度和颜色。
#### <corners>
设置圆角，radius 属性可以直接设置四个角的圆角半径，也可以使用 topLeftRadius 系列属性分别设置

#### 
