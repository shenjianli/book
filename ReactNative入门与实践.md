---
title: ReactNative入门与实践
tags: ReactNative,Android,IOS
grammar_cjkRuby: true
---
本书源码地址：
https://github.com/vczero/React-Native-Code

# 第一部分：基础语法
## 第1章 ReactNative简介
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
3.创建项目
```
react-native init HelloWorld
```
### React到React Native
React 的官方介绍是 A Javascript library for building user interfaces 
它提出的是一个新的开发模式和理念它强调“用户界面”
React 希望将功能分解化，让开发变的像搭积木一样，快速且可维护。

React的主要特点：
1.Just the UI 可以只作为View使用
2.虚拟DOM  可以很好的优化视图的渲染和刷新。以前在不刷新页面的情况下更新视图时，需要先清空DOM容器中的内容，然后将新的DOM和数据追加到容器中，现在React 将这一操作放进了内存中，然后将视图的变化放进内存中比较，计算出最小更新的视图，将其更新渲染，这便是React高效的原因。
3.数据流  实现了单向数据流，更加灵活，便捷

需要学习的知识：
JSX语法
ES6相关知识
前端知识：css  javascript

React Native 开发IDE : WeStorm

JSXTransformer.js是将JSX转译成javascript 和html

使用this.props来做简单的数据传递

使用this.state来更新视图

### React Native简介
React Native 的目标是高效跨平台地开发Native应用，同时，也强调“一次学习，多个平台编写代码”

使用require 引入react-native模块
3个组件：Text,View,Image
2个api:AppRegistry,StyleSheet
React.createClass创建一个组件，render是视图渲染的方法
StyleSheet.create创建一个样式表的类

需要根据业务来划分组件，这时，组件的颗粒度需要根据业务分工和开发难度来划分，以便于后期的维护。同时需要根据不同的功能开发不同的组件入口，使每一个功能或者功能集合能独立开来，功能的移植性就会变好。

### 如何学习React Native

## 第2章 ReactNative开发基础
### 2.1 flexbox
是flexible box的缩写，弹性盒子布局，
### Postion
CSS Postion:
position:absolute；position:relative绝对定位使用通常是父级定义position:relative定位，子级定义position:absolute绝对定位属性，并且子级使用left或right和top或bottom进行绝对定位。


## 第3章 常用组件及其实践
React Native的魅力在于能够使用ios的原生组件和原生API。