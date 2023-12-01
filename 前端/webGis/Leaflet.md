# Leaflet

[中文网站](https://leafletjs.cn/reference-1.8.0.html)

[leaflet常用插件库](http://t.zoukankan.com/duanyue-p-11151308.html)

[Leaflet Draw Documentation](https://leaflet.github.io/Leaflet.draw/docs/leaflet-draw-latest.html#l-draw)

[知乎精选](https://zhuanlan.zhihu.com/p/499829870)

[AIchatOS](https://chat18.aichatos.xyz/#/chat/1701312367175)

[图形绘制 (supermap.io)](https://iclient.supermap.io/examples/leaflet/editor.html#drawAndModify)

[Leaflet 带箭头轨迹以及沿轨迹带方向的动态marker - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/354078243)

## L.map

- L.map(<String> *id*, <Map options> *options?*)   在一个指定 id 的 div 元素中初始化地图并设置相关参数 
- L.map(<HTMLElement> *el*, <Map options> *options?*)  在一个 div 实例中初始化地图并设置相关参数 

### Options

### 事件

### 方法

## 图层

### 方法

​	 扩展 [`L.Layer`](https://leafletjs.cn/reference-1.7.1.html#layer) 的类将继承以下方法: 

![1649562723506](C:\Users\xiaogz\AppData\Roaming\Typora\typora-user-images\1649562723506.png)

### Marker

​	  L.Marker 用于在地图上显示可点击/可拖动的图标。扩展了 [`Layer`](https://leafletjs.cn/reference-1.7.1.html#layer)。

​	 L.marker(<latlng> *latlng*,  <[Marker options](https://leafletjs.cn/reference-1.7.1.html#marker-option)> *options?*)  给出一个地理位置和可选的参数，实例化一个Marker对象。  

```js
L.marker([], {
    icon // 用于渲染标记（marker）的图标实例, 类型为基础类型的 <Icon> 也可用divIcon
    draggerable // 是否可以拖拽
}).addTo(map)
```

#### Events 事件

- **move**  	 [Event](https://leafletjs.cn/reference-1.7.1.html#event)对象

   当标记通过 [`setLatLng`](https://leafletjs.cn/reference-1.7.1.html#marker-setlatlng) 或通过 [dragging](https://leafletjs.cn/reference-1.7.1.html#marker-dragging) 移动时触发该事件，并且旧坐标和新坐标会作为 `oldLatLng`、`latlng` 包含在事件参数中。 

-  **dragstart**    当用户开始拖动标记时触发。 

-  **movestart**    当标记开始拖动时触发。 

- **drag**    当用户拖动标记时重复触发。 

- **dragend**

- **moveend**

```
const marker = L.marker()
marker.on('move', (event) => {})
```

#### 方法

​	 除了[共享层方法](https://leafletjs.cn/reference-1.7.1.html#layer)，如 `addTo()`、`remove()`、类似 bindPopup() 的 [popup 方法](https://leafletjs.cn/reference-1.7.1.html#popup)之外，你还可以使用以下方法： 

![1649562245855](C:\Users\xiaogz\AppData\Roaming\Typora\typora-user-images\1649562245855.png)

[ Icon 文档](https://leafletjs.cn/reference-1.7.1.html#icon) 

### Popup

​	 用于在地图的某些位置打开弹出窗口。使用 [Map.openPopup](https://leafletjs.cn/reference-1.7.1.html#map-openpopup) 打开弹出窗口，同时确保一次只打开一个弹出窗口（推荐使用），或者使用 [Map.addLayer](https://leafletjs.cn/reference-1.7.1.html#map-addlayer) 打开任意多个。 

![1649600476214](C:\Users\xiaogz\AppData\Roaming\Typora\typora-user-images\1649600476214.png)

![1649600537263](C:\Users\xiaogz\AppData\Roaming\Typora\typora-user-images\1649600537263.png)

### Tooltip

​	用于在地图图层顶部显示小文本。

### 栅格图层

#### tileLyaer

 	用于在地图上加载和显示瓦片图层。 请注意，大多数tile服务器都需要属性，你可以在 [`Layer`](https://leafletjs.cn/reference-1.7.1.html#layer) 下进行设置。 

- 语法： L.tilelayer(<String> *urlTemplate*, <[TileLayer options](https://leafletjs.cn/reference-1.7.1.html#tilelayer-option)> *options?*) 



## 基础类

### Layer

# Leaflet-draw

​	leaflet地图库的一个插件，赋予了leaflet地图进行绘图和编辑地理要素的功能。用户可以在地图上绘制点、线、多边形等几何要素，并且可以对这些元素进行编辑、删除、和交互操作。

1. 安装leaflet-draw

   ```
   npm i leaflet-draw -S
   &
   yarn add leaflet-draw -S
   ```

2. 引入leaflet-draw，引入完成后，它会往leaflet注入一个Draw类

   ```
   import "leaflet-draw"
   import "leaflet-draw/dist/leaflet.draw.css"
   ```

## L.drawLocal

​	L.drawLocal 是 Leaflet.draw 插件中用于本地化的对象，用于定义各种语言环境下的文本和标签。通过修改 L.drawLocal 对象中的属性，你可以自定义插件的本地化文本，以适应不同的语言和文化环境。

```js
// 定义自定义本地化文本
L.drawLocal = {
  draw: {
    toolbar: {
      actions: {
        title: '取消绘制',
        text: '取消'
      },
      finish: {
        title: '完成绘制',
        text: '完成'
      },
      undo: {
        title: '删除上一步操作',
        text: '删除上一步'
      },
      buttons: {
        polyline: '绘制折线',
        polygon: '绘制多边形',
        rectangle: '绘制矩形',
        circle: '绘制圆形',
        marker: '绘制标记'
      }
    },
    handlers: {
      circle: {
        tooltip: {
          start: '点击并拖动以绘制圆形'
        }
      },
      marker: {
        tooltip: {
          start: '点击地图以添加标记'
        }
      },
      polygon: {
        tooltip: {
          start: '点击以开始绘制形状',
          cont: '点击以继续绘制形状',
          end: '双击结束绘制'
        }
      },
      polyline: {
        error: '<strong>错误:</strong> 形状边缘不能相交!',
        tooltip: {
          start: '点击以开始绘制线段',
          cont: '点击以继续绘制线段',
          end: '双击结束绘制'
        }
      },
      rectangle: {
        tooltip: {
          start: '点击并拖动以绘制矩形'
        }
      },
      simpleshape: {
        tooltip: {
          end: '释放鼠标键完成绘制'
        }
      }
    }
  },
  edit: {
    toolbar: {
      actions: {
        save: {
          title: '保存更改',
          text: '保存'
        },
        cancel: {
          title: '取消编辑，放弃所有修改',
          text: '取消'
        },
        clearAll: {
          title: '清除所有图层',
          text: '清除所有'
        }
      },
      buttons: {
        edit: '编辑图层',
        editDisabled: '没有可编辑的图层',
        remove: '删除图层',
        removeDisabled: '没有可删除的图层'
      }
    },
    handlers: {
      edit: {
        tooltip: {
          text: '拖动手柄或标记以编辑要素',
          subtext: '点击取消撤销变更'
        }
      },
      remove: {
        tooltip: {
          text: '点击要素以删除'
        }
      }
    }
  }
};
```

**注意：** 设置以上数据后，通过L.Control.Draw创建的原生绘制工具栏，可能会报tooltip的错误，需要将drawLocal设置在Draw之后。还存在配置项`draw`不成功`edit`成功的情况。**但是一个一个属性单独设置就不会有问题**

```js
L.drawLocal.draw.toolbar.buttons.potline = "折线"
L.drawLocal.draw.toolbar.buttons.polygon = '多边形';
L.drawLocal.draw.handlers.circle.tooltip.start = '点击并拖动以绘制自定义圆';
L.drawLocal.edit.handlers.edit.tooltip.text = '拖动控制点或标记以自定义编辑要素'; 
```

## L.Control.Draw

​	Leaflet.draw插件中创建绘图控件的类。是L.Control的子类继承基类的属性和方法，并添加一下自己的属性和方法：

- options：控件的配置项，包括位置，是否可折叠，初始状态等。
- onAdd(map)：控件添加到地图是触发的回调方法
- onRemove(map)
- disable() / enable()：控制地图的启用和禁用状态
- setDrawingOptions(options)： 设置绘图工具的配置项
- setEditOptions(options)：设置编辑工具的配置项

### L.Draw.Marker

​	绘制标记，如下可以自定义一个按钮，通过drawLocal定义绘制过程的各种提示语。

```vue
<template>
  <el-button @click="bindMarker">标记</el-button>
</template>
<script>
    ...
    bindMarker() {
        this.drawObj = new L.Draw.Marker(this.map, this.drawControl.options.draw.marker)
        console.log(this.drawObj);
        this.drawObj.enable()
    }
    ...
</script>
```

### L.Draw.Polyline

### L.Draw.Polygon

### L.Draw.Rectangle

### L.Draw.Circle

### L.Draw.EditToolbar

