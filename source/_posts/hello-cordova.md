---
title: hello-cordova
top: false
cover: false
date: 2018-10-17 15:20:00
categories: 前端
tags:
- 混合开发
- cordova
- android
- vue
---
### 环境要求
运行每个平台上需要对应的sdk，android需要先安装jdk和android sdk

#### Java安装
[jdk配置教程](https://jingyan.baidu.com/article/f96699bbd30ca8894e3c1bdb.html)

#### Android Studio安装
[Android Studio安装配置教程](https://www.cnblogs.com/yanglh6-jyx/p/Android_AS_Configuration.html)  
  
-  配置安装根据自己需要安装对应版本
-  sdk注意不要安装在路径有空格的目录
-  在android studio可以跑在虚拟机或真机上的时候再添加[cordova-android](https://github.com/apache/cordova-android)配置
-  [cordova-android](https://github.com/apache/cordova-android)平台时按对应[API-Levels](https://cordova.apache.org/docs/en/latest/guide/platforms/android/index.html)的来添加

### Cordova

#### 安装cordova
```bash
npm install -g cordova
```
创建项目
```bash
cordova create hello com.example.hello HelloWorld
```
-  用cnpm安装可能会遇到`cordova create`的时候报错

添加android运行平台
```bash
cordova platform add android --save
```
检测是否满足构建平台要求
```bash
cordova requirements

Requirements check results for android:
Java JDK: installed .
Android SDK: installed
Android target: installed android-19,android-21,android-22,android-23,Google Inc.:Google APIs:19,Google Inc.:Google APIs (x86 System Image):19,Google Inc.:Google APIs:23
Gradle: installed
```
- `cordova requirements`连真机会出现`Android target: not installed`，但是不影响`cordova build`或者`cordova run android`
#### 添加插件
插件为H5提供可以调用手机设备的一些功能，比如调用摄像头，先添加插件
```bash
cordova plugin add cordova-plugin-camera
```
js调用
```js
navigator.camera.getPicture(successCallback, errorCallback, options)
```
cordova create生产基于web应用程序的骨架，项目开始页面位于www/index.html，打包完vue把dist里头的文件复制过来即可

#### deviceready
Cordova加载完成会触发deviceready事件，调用摄像头等事件都需要在deviceready事件的回调函数里，比如在我们在vue中使用
```js
// 加载完成后再初始化vue
document.addEventListener('deviceready', function () {
  new Vue({
    el: '#app',
     render: h => h(App)
  })
}, false)
```

#### android平台构建
```bash
cordova build android
```