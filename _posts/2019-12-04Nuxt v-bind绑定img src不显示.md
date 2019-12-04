---
layout:     post
title:      G33333333333
subtitle:   2不适合阅读的整理的一些个人常用的 Git 指令
date:       2017-02-15
author:     BY
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Mac
    - 终端
    - Git
---

>nuxt v-bind绑定img src不显示

## 问题：
使用v-for循环， :src="item.url"，绑定图片无法显示，只能写成静态结构；

## 原因：
直接写成静态的，会经过webpack进行编译，从而得到打包后正确的图片真实路径。
如果你写成动态的，webpack就不会进行编译，原封不动的把'~/assets/img/home.png' 放到你图片的src里面，'http://xxx.xxx.xxx/~/assets/img/home.png'不存在这张图片！

## 解决方法1：
把图片存在服务器，使用网络路径，

## 解决方法2：
使用require和相对路径引用图片
```
{ src: require("../static/gzEducation/fl/1.png") }
//跳出1层还是2层目录，根据自己的所在位置来决定
```
data中的数据：
```
 data() {
    return {
      flList: [
         { src: require("../static/gzEducation/fl/1.png") },
      ],
  }
```

vue循环结构：
```
    <section >
      <div class="item"v-for="(item,index) in flList" :key="index">
      <img :src="item.src" alt="背景图片">
      </div>
    </section>
```

## 成功显示。