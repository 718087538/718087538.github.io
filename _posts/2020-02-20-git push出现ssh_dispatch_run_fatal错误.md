---
layout:     post
title:      git push出现ssh_dispatch_run_fatal错误
subtitle:   git push出现ssh_dispatch_run_fatal错误
date:       2020-02-20
author:     Kason
header-img: img/articleBg/post-bg-js-version.jpg
catalog: true
tags:
    - git
    - JavaScript
    - 服务器
---

>gitPush出现ssh_dispatch_run_fatal错误


### 问题：gitpush遇到一下错误：
```
ssh_dispatch_run_fatal: Connection to 52.74.223.119 port 22: Software caused connection abort
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.

```

### 原因：dns解析的问题
打开cmd输入ping命令查看github.com的连接，请求超时：
![20200220155741.png](https://raw.githubusercontent.com/718087538/718087538.github.io/master/img/20200220155741.png)

### 解决方法：配置本地hosts文件
找`C:\Windows\System32\drivers\etc\hosts`，
在末尾添加上下面这段文字，保存
```
192.30.255.112  github.com git 
185.31.16.184 github.global.ssl.fastly.net
```

### 结果：成功解决
再次ping github.com，成功ping通。
再次Push，成功。

![20200220155803.png](https://raw.githubusercontent.com/718087538/718087538.github.io/master/img/20200220155803.png)