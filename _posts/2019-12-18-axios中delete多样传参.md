---
layout:     post
title:      axios中delete多样传参
subtitle:   在vue和Nuxt中使用axios在不同位置传参
date:       2019-12-18
author:     KASON
header-img: img/articleBg/post-bg-js-version.jpg
catalog: true
tags:
    - Vue
    - Axios
    - 前端
---

>axios中delete多样传参


我们使用axios时可以根据需求，把参数存放在url或data中，这里有一些案例可以参考
### 整段代码
```
<script>
import axios from 'axios'

export default {
  name: 'axios2-2',
  created() {
    let data = {
      id: 12
    }
    // delete请求
    axios.delete('/delete', {	
      params: {	// 请求参数拼接在url上
        id: 12
      }
    }).then(res => {
      console.log(res)
    })

    axios.delete('/delete', {	
      data: {	// 请求参数放在请求体
        id: 12
      }
    }).then(res => {
      console.log(res)
    })

    axios({
      method: 'delete',
      url: '/delete',
      params: {}, // 请求参数拼接在url上
      data: {}  // 请求参数放在请求体
    }).then(res => {
      console.log(res)
    })
  },
}
</script>
```


#### 请求参数拼接在url上用 params
```
 let data = {
      id: 12
    }
    // delete请求
    axios.delete('/delete', {	
      params: {	// 请求参数拼接在url上
        id: 12
      }
    }).then(res => {
      console.log(res)
    })

```

![20191218165858.png](https://raw.githubusercontent.com/718087538/718087538.github.io/master/img/20191218165858.png)


### 请求参数放在请求体用 data

```
let data = {
      id: 12
    }
    // delete请求
    axios.delete('/delete', {	// 请求参数放在请求体
      data: {
        id: 12
      }
    }).then(res => {
      console.log(res)
    })

```



![20191218170123.png](https://raw.githubusercontent.com/718087538/718087538.github.io/master/img/20191218170123.png)