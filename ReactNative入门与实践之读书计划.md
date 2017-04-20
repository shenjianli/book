---
title: React Native 入门与实践之读书计划
tags: React Native,融e购,开发
grammar_cjkRuby: true
---

http://facebook.github.io/react-native


## 第一部分  基础语法
### 第一章  React Native 简介（1周）
包括环境搭建，Node.js学习，创建Demo并运行
### 环境搭建
1.安装 Node.js  
2.安装 React Native  
```
nmp install -g react-native-cli
```
如果访问极慢或者显示网络失败，请使用淘宝镜像：
```
npm config set registry https://registry.npm.taobao.org --global
npm config set disturl https://npm.taobao.org/dist --global
```
### Node.js学习
简单的说 Node.js 就是运行在服务端的 JavaScript。
Node.js 是一个基于Chrome JavaScript 运行时建立的一个平台。
Node.js是一个事件驱动I/O服务端JavaScript环境，基于Google的V8引擎，V8引擎执行Javascript的速度非常快，性能非常好。


**学习地址：**
Node.js 教程
http://www.runoob.com/nodejs/nodejs-tutorial.html
怎样有效地学习 Node.js？
https://www.zhihu.com/question/19793473


### 创建项目
1.初始化项目
```
react-native init HelloWorld
```
2.运行项目
```
react-native run-android //运行Android工程
```


### 第二章  React Native 开发基础（2周）
包括Flexbox学习，JavaScript学习，JSX学习，配置文件，运行及调试React Native 程序

## 第二部分  API 和组件篇
### 第三章  常用组件及其实践（3周）
View,Text,TextInput ,Image Touchable等组件学习，写小例子demo.
### 第四章  常用API及其实践（3周）
AppRegistry,AsyncStorage,PixelRatio,NetInfo,数据请求，定时器学习，编写demo小程序
### 第五章  Native 扩展（2周）
通信机制，自定义api组件，构建Native UI组件，编写自定义组件
### 第六章 组件封装（2周）
二级菜单，日历组件，开源组件学习
## 第三部分 实践（4周）
学习开发React Native小程序，编写融e购首页的 React Native程序。
