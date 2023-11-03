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
     @import "@/uni_modules/vk-uview-ui/theme.scss";
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

  对比，npm模式，增加  `./uni_modules/` ，少去第5步。


