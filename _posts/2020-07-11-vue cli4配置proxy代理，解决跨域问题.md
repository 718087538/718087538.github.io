---
layout:     post
title:      vue cli4配置proxy代理，解决跨域问题
subtitle:    vue cli4配置proxy代理，解决跨域问题
date:       2020-07-11
author:     Kason
header-img: img/articleBg/vue-bg.jpg
catalog: true
tags:
    - 前端
    - Vue
    - web
---

>vue cli4配置proxy代理，解决跨域问题


在开发的时候，为了防止跨域的问题，其实可以直接在vue里配置proxy代理。我使用的是vue cli4。
1，找到项目根目录的vue.config.js文件。
2，配置`devServer`对象。
配置如下：
```

module.exports = {
  publicPath: process.env.NODE_ENV === "production" ? "./" : "./",
  lintOnSave: false,
  productionSourceMap: false,
  css: {
    sourceMap: false
  },
  devServer: {
    disableHostCheck: true,
    //下面这种即可
    proxy: {
      "/channel": {
        target: "http://192.168.0.10:8080",//接口的地址
        changeOrigin: true,
        pathRewrite: {
          // "^/kuai": ""
        }
      },
    },
    //这种方式不支持
    // proxy: {
    //   context: ["/token", "/channel", "/common", "/file", "/callback"],
    //   target: "http://10.0.98.105:8080",
    //   changeOrigin: true,
    //   secure: false
    // }
  },
  chainWebpack: config => {},
  configureWebpack: config => {
    if (process.env.NODE_ENV === "production") {
      return {
        plugins: pluginList
      };
    }
  }
};
```

因为我发起请求时是请求/ 所以默认是本地vue项目的启动地址，加了代理后，只要匹配上了 /channel就会自动转发到代理的地址上。
浏览器就认为请求的是本地，所以就不会出现跨域的问题了。