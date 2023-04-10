---
layout:
  - layout
title: vue组件自动注册及Vuex
date: 2023-04-07 17:13:13
tags:
---
##  VUE组件全局自动注册 及 VUEx仓库module自动导入
### 组件全局注册 页面无需引入直接做标签使用
####   #读取需要全局自动注册的文件 例如：在src的components wholeSituation文件夹 
#### test和test2为全局注册组件

![](https://secure2.wostatic.cn/static/p6KH9jUhHuWbWcRCuPUa7j/image.png?auth_key=1680855319-fqjL2eGqLtPoC3E7xFDeQ6-0-8d296f5a6f518bf97cb200df665b5104)
#### wholeSituation根目录新建index.js文件

```c
export default {
  install(Vue) {
//自动读取文件夹下的文件require.context()
//require.context('需要读取的文件路径','是否读取子文件夹 boolean值','需要读取的文件-正则匹配')
    const req = require.context('./', true, /.vue$/)
//函数内keys方法，keys方法调用后返回所有读取文件的路径
    req.keys().forEach((item) => {
//req(读取的文件路径)可以相当于导入对应的文件     返回值.default的内容就是导入的文件
      const com = req(item).default
//此处的name名注意！！！！使用是的标签名称就是文件中的name名
      Vue.component(com.name, com)
    })
  }
}
```
![](https://secure2.wostatic.cn/static/2SaRj8BrUDmBTSxB3Ew7Am/image.png?auth_key=1680855423-vyNaNEiTHdKGm8P3nwaiqR-0-5c53fd33d0a51e2dee61cd95dc0406bc)

#### 在main.js中做导入

```c
import test from '@/components/wholeSituation/index.js'
Vue.use(test)
```

### Store自动注册modules
#### 同上查找文件
```c
const modulesFiles = require.context('./modules',true,/\.js$/)
const modules = modulesFiles.keys().reduce((modules,modulePath)=>{
const moduleName = modulePath.replace(/^\.\/(.*)\.\w+$/,'$1')
const value = modulesFiles(modulePath)
modules[moduleName] = value.default
return modules
},{})
//最后导入
const store = new Vuex.Store({
modules,
})
```