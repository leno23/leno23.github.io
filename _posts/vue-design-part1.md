---
title: Vue.js 3设计与实现（1）框架设计概览
index_img: https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2c668e06988a4da6bfd6edb52203d165~tplv-k3u1fbpfcp-watermark.image
tags: [Vue.js 3]
categories: [源码]
comment: 'valine'
---


### <center> Vue.js 3设计与实现（1）框架设计概览

作为学习者，在学习框架时，应该从全局角度对框架的设计有清晰的认知，否则很容易被细节困住，看不清全貌

从范式上看，视图层框架分为 命令式和声明式，命令式关注过程、性能最好，但不好维护；
声明式关注结果，性能也不错，好维护

声明式代码：  更新性能 = diff性能 + 命令式更新性能

三种更新页面方式，按三个维度对比：
innerHTML模板     <  虚拟DOM    <   原生JavaScript
心智负担中等         心智负担小         心智负担大
可维护性中等         性能不错           可维护性差
性能差               性能不错           性能高

innerHTML通过拼接HTML实现，但是拼接字符串有一定的心智负担
虚拟DOM提供给用户声明式操作，不需要关心更新过程，没有心智负担
原生JavaScript需要手动创建、删除、修改大量DOM元素，操作过程的维护带来巨大的心智负担

控制框架体积--构建工具提供的预定义变量
提供完善的警告信息意味着需要写更多的代码，生产环境需要控制代码体积，解决方式是添加构建工具中预定义变量，例如__DEV__
在构建生产环境代码时，工具会将__DEV__置为false，这段代码会变为dead code，后续会被移除掉

Tree-Shaking -- 使用ESM模块、副作用(调用函数时，对外部产生影响)
