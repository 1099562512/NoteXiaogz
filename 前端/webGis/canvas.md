## Canvas

## **CanvasRenderingContext2D**

​	画布的2D渲染上下文, 有了上下文，就可以绘制任何喜欢的东西。

```js
const canvas = document.getElementById("canvas");
const ctx = canvas.getContext("2d");
```

![img](https://mdn.mozillademos.org/files/224/Canvas_default_grid.png)

```
canvas一个基本的绘制过程应该是
1、设置样式和颜色
	fillStyle()
2、绘制图形
	beginPath()
	moveTo()、lineTo()、arc()、fillRect()等
	closePath()
3、填充和绘制轮廓 
	fill()、strok()
```



### 添加样式和颜色

#### fillStyle/stokeStyle

语法:   ctx.fillStyle = color

```js
// 这些 fillStyle 的值均为 '橙色'
ctx.fillStyle = "orange";
ctx.fillStyle = "#FFA500";
ctx.fillStyle = "rgb(255,165,0)";
ctx.fillStyle = "rgba(255,165,0,1)";
```

`	color` 可以是表示 CSS 颜色值的字符串，渐变对象或者图案对象。我们迟些再回头探讨渐变和图案对象。默认情况下，线条和填充颜色都是黑色（CSS 颜色值 `#000000`）

#### globalAlpha

​	`globalAlpha` 属性在需要绘制大量拥有相同透明度的图形时候相当高效。rgba()可操作性更高。

#### 线型Line Style

##### lineWidth

##### lineCap

​	线端样式，butt默认、round（半圆）、square（方块）

##### lineJoin

​	交线样式，round（圆角）、bevel、miter（尖角）默认

##### setLineDash(segments)

​	设置当前虚线样式，它使用一组值来指定描述模式的线和间隙的交替长度。

```js
ctx.setLineDash([2,4]) //线长2 间距4
```

### 绘制形状

#### 矩形

##### fillRect

```js
 ctx.fillRect(x,y,width,height)
//x, y表示左上角坐标（相对于原点）width，height表示长宽，正负表示方向
```

​	绘制一个填充矩形

##### strokeRect

```js
ctx.strokeRect(x, y, width, height)
```

​	绘制一个矩形边框

##### clearRect

```js
ctx.clearRect(x,y,width,height)
```

​	清除指定矩形区域，让清除部分完全透明。常用来清除画布

#### 圆弧

##### arc()

![弧/曲线](https://www.w3school.com.cn/i/arc.gif)

语法: 

```js
context.arc(x,y,r,sAngle,eAngle,counterclockwise)
//x,y: 圆心位置
//r: 弧度半径
//sAngle：起始角度 eAngle：结束角度	
//counterclockwise: 可选。规定应该逆时针还是顺时针绘图。False = 顺时针，true = 逆时针。
```

#### 文本

##### fillText()  / strokeText()

语法：fillText(text, x,y, [maxWidth])

##### font

##### textAlign

##### textBaseline

##### direction

##### measureText()

​	将返回一个 [`TextMetrics`](https://developer.mozilla.org/zh-CN/docs/Web/API/TextMetrics)对象的宽度、所在像素，这些体现文本特性的属性。

#### 绘制路径

1. 首先，创建路径起始点

2. 然后你使用[画图命令](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D#Paths)去画出路径。

3. 之后你把路径封闭

4. 一旦路径生成，你就能通过描边或填充路径区域来渲染图形。

   beginPath():	新建一条路径，生成之后，图形绘制命令被指向到路径上生成路径。

   closePath():	合路径之后图形绘制命令又重新指向到上下文中

   stroke():	通过线条来绘制图形轮廓

   fill():	通过填充路径的内容区域生成实心的图形

##### beginPath()

​	新建一条路径，生成之后，图形绘制命令被指向到路径上生成路径。

##### closePath()

​	闭合路径之后图形绘制命令又重新指向到上下文中

##### stroke()

​	通过线条来绘制图形轮廓。

##### fill()

​	通过填充路径的内容区域生成实心的图形。

##### isPointInPath(x,y)

​	判断当前路径是否包含监测点，当前路径指的是最后一个beginPath和closePath中间的代码。

```js
//如下，我绘制了一个多边形路径和一个圆，鼠标移入圆时isPointInPath(x,y)才会为true
cvs.onmousemove = function (e) {
  // 得到鼠标的坐标
  var x = e.pageX, y =e.pageY;
  draw();
  circle();
  console.log(ctx.isPointInPath(x,y)) 
}
```

待验证：可以只构建出路径即可不用绘制。

#### 变形

##### 状态的保存和恢复

###### save()

​	保存画布(canvas)的所有状态 

###### restore()

​	save 和 restore 方法是用来保存和恢复 canvas 状态的，都没有参数。Canvas 的状态就是当前画面应用的所有样式和变形的一个快照。

```js
  ctx.fillRect(0,0,150,150);   // 使用默认设置绘制一个矩形
  ctx.save();                  // 保存默认状态

  ctx.fillStyle = '#09F'       // 在原有配置基础上对颜色做改变
  ctx.fillRect(15,15,120,120); // 使用新的设置绘制一个矩形
  ctx.save();                  // 保存当前状态

  ctx.fillStyle = '#FFF'       // 再次改变颜色配置
  ctx.globalAlpha = 0.5;
  ctx.fillRect(30,30,90,90);   // 使用新的配置绘制一个矩形

  ctx.restore();               // 重新加载之前的颜色状态
  ctx.fillRect(45,45,60,60);   // 使用上一次的配置绘制一个矩形

  ctx.restore();               // 加载默认颜色配置
  ctx.fillRect(60,60,30,30);   // 使用加载的配置绘制一个矩形
```

![img](https://developer.mozilla.org/@api/deki/files/104/=Canvas_savestate.png)

##### 移动Translating

- 语法：ctx.translate(x, y)

接收两个参数。x为左右偏移量， y为上下偏移量。做变形前先保存状态ctx.save()

```
ctx.translate(100, 100)
```

##### rotate旋转

- 语法：rotate(angle)

该方法只接收一个参数：旋转的角度，顺时针方向，以弧度为单位

```
ctx.rotate((90 * 2 * Math.PI) / 360)
```

##### scale缩放

- 语法：scale(x, y)

##### transform变形

### imageData（.PGM处理）

需求：处理pgm格式数据，按比例缩小适应屏幕， 原pgm的大小是2076x2013的

1、这个是直接对imageData像素操作，有时候scale=1/2时缩放没问题，但是scale=0.4的就会出现混乱。

```js
function scaleImageData(imageData, scale) {
    const width = imageData.width * scale;
    const height = imageData.height * scale;
    const scaledData = ctx.createImageData(width, height);

    for (let y = 0; y < height; y++) {
        for (let x = 0; x < width; x++) {
            const sourceX = Math.round(x / scale);
            const sourceY = Math.round(y / scale);
            const index = (y * width + x) * 4;
            const sourceIndex = (sourceY * imageData.width + sourceX) * 4;

            scaledData.data[index] = imageData.data[sourceIndex];
            scaledData.data[index + 1] = imageData.data[sourceIndex + 1];
            scaledData.data[index + 2] = imageData.data[sourceIndex + 2];
            scaledData.data[index + 3] = imageData.data[sourceIndex + 3];
        }
    }

    return scaledData;
}
```

2、pgm ---> imageData ---> 临时的ctx，获取到img的URL ---> image ----> 通过drawImage可以指定图片大小进行缩放。

```js
 const containter = document.querySelector('.canvas-container')
 window.electronApi.getPgmData().then(pgmInfo => {
     const { width, height, data} = pgmInfo
     const ratio = width / height

     const targetHeight = containter.clientHeight; //目标大小，适应屏幕
     const targetWidth = ratio * targetHeight

     const pgmCanvas = document.querySelector('#pgm-canvas')
     pgmCanvas.width = targetWidth
     pgmCanvas.height = targetHeight
     const ctx = pgmCanvas.getContext('2d')

     let imageData = ctx.createImageData(width, height)
     //将pgm的颜色值，设置R、G、B分量都为同一个值
     //pgm一个字节代表一个颜色，imageData 4个字节代表颜色分别是
     for(let i=0, len = data.length; i < len; i++) {
         const initI = (i*3) + i
         //灰色
         if(data[i] == 205) {
             imageData.data[initI] = 0
             imageData.data[initI+1] = 0
             imageData.data[initI+2] = 0
             imageData.data[initI+3] = 255
         } else if(data[i] == 254) {  //过道设置透明
             imageData.data[initI] = 0
             imageData.data[initI+1] = 0
             imageData.data[initI+2] = 0
             imageData.data[initI+3] = 0
         } else {                     //过道边缘，根据需求设置颜色，原颜色就是data[i]
             imageData.data[initI] = 18
             imageData.data[initI+1] = 200
             imageData.data[initI+2] = 200
             imageData.data[initI+3] = 255
         }
     }
     //console.log(imageData);
     //进行屏幕适应处理
     // console.log(targetHeight/height);

     const tempCanvas = document.createElement('canvas');
     const tempCtx = tempCanvas.getContext('2d');

     // 将 ImageData 绘制到临时画布上
     tempCanvas.width = imageData.width;
     tempCanvas.height = imageData.height;
     tempCtx.putImageData(imageData, 0, 0);

     // 将绘制好的图像数据转为 Data URL
     const img = new Image()
     img.src = tempCanvas.toDataURL();
     img.onload = () => {
         /* img.width = targetWidth
            img.height = targetHeight */
         //console.log(img);
         ctx.drawImage(img, 0,0, targetWidth,targetHeight)
     }
 })
```



### 给canvas绘制对象添加鼠标事件

- 通过给canvas监听鼠标事件，获得鼠标在canvas上的坐标
- 判断鼠标坐标是否在绘制对象内

