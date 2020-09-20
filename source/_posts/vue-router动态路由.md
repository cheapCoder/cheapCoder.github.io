---
title: vue-router动态路由
date: 2020-09-20 14:12:04
tags: 
---

<!-- ###  <center>vue-router动态路由 -->
动态引入的路由组件在打包时路由组件会被单独打包(代码分割 code split),
默认不请求加载路由组件打包文件, 当请求需要路由组件时才请求加载,当引入需要的的组件时在`Network`会增加一个打包文件(一般为`/\d+.js/`格式)。

#### 引入方式

* component是一个执行就会`import()`的函数
  
      myComponent =  () => import(path)

* 将模板传入resolve函数

      Vue.component(components, (resolve, reject) => {
        setTimeout(() => {
          resolve({
            template: YourTemplate
          })
        }, 100)
      })

* 使用`require`引入

      Vue.component(components, (resolve) => {
        setTimeOut(() => {
          require(path, resolve);
        }, 100)
      })

* 使用`import`返回的`promise`resolve

      Vue.component(components, (resolve) => {
        setTimeOut(() => {
          import(path).then(resolve);
        }, 100)
      })
