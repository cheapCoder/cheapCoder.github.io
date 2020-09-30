---
title: vue动态路由引入和异步组件记录
date: 2020-09-20 14:12:04
tags: 
---

<!-- ###  <center>vue-router动态路由 -->
动态引入的路由组件在打包时路由组件会被单独打包(代码分割 code split),
默认不请求加载路由组件打包文件, 当请求需要路由组件时才请求加载,当引入需要的的组件时在`Network`会增加一个打包文件(一般为`/^\d+.js$/`格式)。

#### 引入方式

* component是一个执行就会`import()`的函数
  
      myComponent =  () => import(path)

* 将组件定义对象(component definition)传递给 `resolve` 回调函数

      Vue.component(component, (resolve, reject) => {
        setTimeout(() => {
          resolve({
            template: YourTemplate
          })
        }, 100)
      })

* 使用`require`引入，这个特殊的 require 语法将指示 webpack 自动将构建后的代码，拆分到不同的 bundle 中，然后通过 Ajax 请求加载。

      Vue.component(component, (resolve) => {
        setTimeOut(() => {
          require(path, resolve);
        }, 100)
      })

* 使用返回的`promise`resolve前`import`

      - Vue.component(component, (resolve) => {
          setTimeOut(() => {
            import(path).then(resolve);
          }, 100)
        })
      - Vue.component(component, Promise.resolve(template对象))
      - Vue.component(component, Promise.resolveDelay(template对象, time))
