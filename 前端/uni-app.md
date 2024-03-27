### 生成Android证书

​	Android平台打包发布apk应用，需要使用数字证书（.keystore文件）进行签名，用于表明开发者身份。可以使用JRE环境中的keytool命令生成。

#### 安装jre环境

1. 安装JDK

   一直next就行，记住安装的位置，我的是`C:\Program Files\Java`，安装完成后cmd输入以下命令是否出现版本号。

   ```
   java --version
   ```

2. 配置环境变量

   1.   `计算机 --》 属性 --》 高级系统设置 --》 高级 --》 环境变量`	

   2. 系统变量  --》新建JAVA_HOME变量

      变量值填写JDK安装目录： `C:\Program Files\Java`

   3. 系统变量→寻找Path变量→编辑

      添加一下两个值：

      ```
      %JAVA_HOME%\bin;
      %JAVA_HOME%\jre\bin;
      ```

   4. 系统变量→新建CLASSPATH变量。

      ```
      .;%JAVA_HOME%\lib;%JAVA_HOME%\lib\tools.jar //(注意最前面有一点)
      ```

#### 生成证书

​	[Android平台签名证书(.keystore)生成指南 - DCloud问答](https://ask.dcloud.net.cn/article/35777)

​	需要注意的是，**记住证书别名、密钥、保存证书文件**

### 制作统一发布页

​	uni-portal插件

​	uni-admin里面集成了uni-portal插件。本地运行un-admin就可以了。通过绑定应用的AppID通过配置就可以进行统一发布页面的制作了。

#### 发布统一发布页面

##### 自己服务器

​	需要注意图片的路径，如果是uni-app的免费服务器，需要注意过期时间。如果是自己的需要将文件路径更新一下。

##### 前端网页托管

​	[前端网页托管](https://uniapp.dcloud.net.cn/uniCloud/hosting.html)

### 打包

#### 云打包

​	准备好证书之后就可以更新了，如果有绑定微信开放平台的需要保持包名一致。

#### 本地打包

### 第三方UI

#### uView

​	目前有三个版本uView2.0、uView3.0、uView plus。

- uView2.0 

  最稳定，支持多端、小程序、H5、nvue。但只支持vue2.0

  https://www.uviewui.com/

- uView 3.0

  兼容vue2.0、vue3.0。支持小程序、H5、不支持nvue

  https://vkuviewdoc.fsq.pub/components/install.html

- uView plus

  vue3.0， 支持多端、小程序、H5、nvue

  https://uiadmin.net/uview-plus/

##### uView2.0

##### uView3.0

​		需要注意微信微信小程序记得开启： 本地设置 ------>增强编译	以对async的支持

- NPM

  1. 安装vk-uview-ui包

     ```
     npm install vk-uview-ui
     ```

  2. main.js

     ```js
     // 引入 uView UI
     import uView from 'vk-uview-ui';
     ...
     app.use(uView)
     ```

  3. uni.scss 引入全局 scss 变量文件 

     ```html
     <style lang="scss">
     	@import "vk-uview-ui/index.scss";
     </style>
     ```

  4. App.vue 引入基础样式（注意style标签需声明scss属性支持） 

     ```css
     @import "vk-uview-ui/theme.scss";
     ```

  5. page.json

     ```json
     "easycom": {
     	"autoscan": true,
     	"custom": {
     		"^u-(.*)": "vk-uview-ui/components/u-$1/u-$1.vue"
     	}
     }
     ```

- uni-modules

  [【开箱即用】uView Vue3 横空出世，继承uView1意志，再战江湖，风云再起！ - DCloud 插件市场](https://ext.dcloud.net.cn/plugin?id=6692)
  
  [安装 | uView Vue3.0 横空出世，继承uView1.0意志，再战江湖，风云再起！ (fsq.pub)](https://vkuviewdoc.fsq.pub/components/install.html)
  
  1. 从dclound插件市场安装
  
  2. mian.js
  
     ```js
     import uView from './uni_modules/vk-uview-ui'
     app.use(uView)
     ```
  
  3. uni-scss引入
  
     ```css
     @import "@/uni_modules/vk-uview-ui/theme.scss";
     ```
  
  4. App.vue 引入基础样式
  
     ```css
     @import "./uni_modules/vk-uview-ui/index.scss";
     ```

#### uni-ui

##### uni-modules

1. dclound插件市场下载引入至uni-modules，uni-ui里面的组件都是分开的可以从插件市场单独下载，如果想全部引入就下载uni-ui。**不需要引用、注册，直接在页面中使用 `uni-ui` 组件。**

##### npm + easycom

1. 安装uni-ui

   ```
   npm i @dcloudio/uni-ui
   ```

2. 安装sass和sass-loader，如果使用Hbuilder有装插件可以省略此步骤。

   ```
    npm i sass sass-loader@10.1.1 -D
   ```

3. 设置easycom

   ```json
   "easycom": {
   	"autoscan": true,
       "custom": {
      	 	"^uni-(.*)": "@dcloudio/uni-ui/lib/uni-$1/uni-$1.vue"
       }
   }
   ```

4. 使用easycom后可以直接引入而不需要引入

   ```
   <uni-badge class="uni-badge-left-margin" text="2" type="primary" />
   ```

   

#### uni-ui

- uni-modules，即从uni-app的插件市场导入

  在Hbuilder创建项目时，直接选择uni-ui当模板。此时项目会多了uni-modules将uni-ui所有的组件都导入。

  由于uni-app独特的[easycom](https://uniapp.dcloud.io/collocation/pages?id=easycom)技术，可以免引用、注册，直接使用各种符合规则的vue组件。在代码区键入`u`，拉出各种内置或uni-ui的组件列表，选择其中一个，即可使用该组件。

- npm

  仅支持easycom，uni-ui不支持vue.use()。还需要配置vue.config.js。

  [uni-app官网 (dcloud.net.cn)](https://uniapp.dcloud.net.cn/component/uniui/quickstart.html)
