# Map

## 基础类

### Layer

​	一组来自 Layer 基类的方法，所有 Leaflet 图层都使用。

| 方法                               | 返回值        | 说明                                                         |
| :--------------------------------- | :------------ | :----------------------------------------------------------- |
| `addTo(<Map|LayerGroup> *map*)`    | `this`        | 将图层添加到指定的地图或图层组（LayerGroup）。               |
| `remove()`                         | `this`        | 从当前处于活动状态的地图中删除图层。                         |
| `removeFrom(<Map> *map*)`          | `this`        | 从指定的地图中删除图层                                       |
| `removeFrom(<LayerGroup> *group*)` | `this`        | 从指定的 [`LayerGroup`](https://leafletjs.cn/reference.html#layergroup) 中删除该图层。 |
| `getPane(<String> *name?*)`        | `HTMLElement` | 返回代表地图上指定窗格的 `HTMLElement`。如果 `name` 被省略，则返回该层的窗格。 |
| `getAttribution()`                 | `String`      | 由 `attribution 控件`使用，返回 [attribution 选项](https://leafletjs.cn/reference.html#gridlayer-attribution)。 |

## UI图层

### Popup

### Tooltip

### Marker



## 矢量图层

### Path路径

一个抽象的类，包含了矢量覆盖物 （Polygon（多边形）, Polyline（折线）, Circle（圆形））之间**共享的选项**和常量

| 选项                  | 类型       | 默认值      | 说明                                                         |
| :-------------------- | :--------- | :---------- | :----------------------------------------------------------- |
| `stroke`              | `Boolean`  | `true`      | 是否沿路径绘制边框。把它设置为`false` ，可以禁用多边形或圆形的边框。 |
| `color`               | `String`   | `'#3388ff'` | Stroke（描边）颜色                                           |
| `weight`              | `Number`   | `3`         | Stroke（描边）宽度，单位：像素                               |
| `opacity`             | `Number`   | `1.0`       | Stroke（描边）的不透明度                                     |
| `lineCap`             | `String`   | `'round'`   | 定义描边 [结束时使用的形状](https://developer.mozilla.org/docs/Web/SVG/Attribute/stroke-linecap) 的字符串。 |
| `lineJoin`            | `String`   | `'round'`   | 定义在描边 [转角处使用的形状](https://developer.mozilla.org/docs/Web/SVG/Attribute/stroke-linejoin) 的字符串。 |
| `dashArray`           | `String`   | `null`      | 一个定义描边的点画线的 [图案范式](https://developer.mozilla.org/docs/Web/SVG/Attribute/stroke-dasharray)的字符串。 在[一些旧的浏览器](https://developer.mozilla.org/docs/Web/API/CanvasRenderingContext2D/setLineDash#Browser_compatibility)中，对 [`Canvas`](https://leafletjs.cn/reference.html#canvas)-powered 图层不起作用。 |
| `dashOffset`          | `String`   | `null`      | 一个字符串，它定义了进入 [dash 范式的距离，以 dash 开始](https://developer.mozilla.org/docs/Web/SVG/Attribute/stroke-dashoffset)。 在[一些旧的浏览器](https://developer.mozilla.org/docs/Web/API/CanvasRenderingContext2D/setLineDash#Browser_compatibility)中，对 [`Canvas`](https://leafletjs.cn/reference.html#canvas)-powered 图层不起作用。 |
| `fill`                | `Boolean`  | `depends`   | 是否对路径进行颜色填充。设置为 `false` 可以禁止对多边形或圆形的填充。 |
| `fillColor`           | `String`   | `*`         | 填充颜色。默认为 [`color`](https://leafletjs.cn/reference.html#path-color) 选项的值。 |
| `fillOpacity`         | `Number`   | `0.2`       | 填充的不透明度。                                             |
| `fillRule`            | `String`   | `'evenodd'` | 一个字符串，定义 [形状的内部区域](https://developer.mozilla.org/docs/Web/SVG/Attribute/fill-rule) 是如何被确定的。 |
| `bubblingMouseEvents` | `Boolean`  | `true`      | 当为 `true`时，这个路径上的鼠标事件将在地图上触发相同的事件 (除非使用 [`L.DomEvent.stopPropagation`](https://leafletjs.cn/reference.html#domevent-stoppropagation) )。 |
| `renderer`            | `Renderer` | ``          | 在这个路径上使用这个特定的 [`Renderer（渲染器）`](https://leafletjs.cn/reference.html#renderer) 实例。 优先于地图的 [default renderer（默认渲染器）](https://leafletjs.cn/reference.html#map-renderer)。 |
| `className`           | `String`   | `null`      | 在一个元素上设置的自定义类名。仅适用于 SVG 渲染器。          |