### Three

#### Sence（场景）

#### Camera（相机）

##### 透视相机 (PerspectiveCamera )

​	这一投影模式被用来模拟人眼所看到的景象，它是3D场景的渲染中使用得最普遍的投影模式。

```
PerspectiveCamera( fov : Number, aspect : Number, near : Number, far : Number )
```

- fov：摄像机视锥体垂直视野角度
- aspect:  摄像机视锥体长宽比
- near:  摄像机视锥体近端面
- far: 摄像机视锥体远端面

```js
var camera = new THREE.PerspectiveCamera( 45, width / height, 1, 1000 );
scene.add( camera );
```



```js
<template>
  <div class="about"></div>
</template>

<script setup>
import * as THREE from 'three'
console.log(THREE)
//创建场景对象Scene
let scene = new THREE.Scene()

/**
*  创建网格模型：将材质和几何对象整合
* new THREE.SphereGeometry(60, 40, 40); //创建一个球体几何对象
*/
//创建一个立方体几何对象Geometry，结构
let geometry = new THREE.BoxGeometry(100, 100, 100)
//材质对象Material，木材
let material = new THREE.MeshLambertMaterial({
color: 0x0000ff
})
let mesh = new THREE.Mesh(geometry, material) //网格模型对象Mesh
scene.add(mesh) //网格模型添加到场景中
	
    //点光源
    var point = new THREE.PointLight(0xffffff);
    point.position.set(400, 200, 300); //点光源位置
    scene.add(point); //点光源添加到场景中
    //环境光
    var ambient = new THREE.AmbientLight(0x444444);
    scene.add(ambient);
    // console.log(scene)
    // console.log(scene.children)
    /**
    * 相机设置
    */
    var width = window.innerWidth; //窗口宽度
    var height = window.innerHeight; //窗口高度
    var k = width / height; //窗口宽高比
    var s = 200; //三维场景显示范围控制系数，系数越大，显示的范围越大
    //创建相机对象
    var camera = new THREE.OrthographicCamera(-s * k, s * k, s, -s, 1, 1000);
    camera.position.set(200, 300, 200); //设置相机位置
    camera.lookAt(scene.position); //设置相机方向(指向的场景对象)
    /**
    * 创建渲染器对象
    */
    var renderer = new THREE.WebGLRenderer();
    renderer.setSize(width, height);//设置渲染区域尺寸
    renderer.setClearColor(0xb9d3ff, 1); //设置背景颜色
    document.body.appendChild(renderer.domElement); //body元素中插入canvas对象
    //执行渲染操作   指定场景、相机作为参数
    renderer.render(scene, camera);

</script>

<style></style>

```



#### 渲染器（Renderer）

#### 几何体

##### 立方体（BoxGeometry）

```js

```

