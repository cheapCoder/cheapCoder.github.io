---
title: babel学习笔记
date: 2021-01-08 17:13:47
tags:
- babel
- es6
---

学习地址：[姜瑞涛的官方网站](https://www.jiangruitao.com/babel/)



> babel是一个工具集，主要用于将ES6的js代码转为ES5等向后兼容的js代码

## 一、必要部分：

1. babel配置文件：babelrc、babel.js、babel.config.js或package.json
2. 相关的npm依赖
3. 需要转码的js文件

## 二、作用：

1. 语法转换
2. 补齐api	 => polyfill (babel默认只转换JavaScript语法，不转换api)

## 三、版本

> Babel是一个工具集，而这个工具集是围绕@babel/core这个核心npm包构成的。每次@babel/core发布新版本的时候，整个工具集的其它npm包也都会跟着升级到与@babel/core相同的版本号，即使它们的代码可能一行都没有改变

- v7：`@babel/core`

- v6：`babel-core`

## 四、配置

> 建议使用babel.config.js文件，可以进行逻辑处理

1. 预设数组`presets`：就是一组Babel插件的集合

   1. **@babel/preset-env**：TC39每年发布的进入标准的ES语法的转换器预设集合
   2. @babel/preset-flow
   3. @babel/preset-react
   4. @babel/preset-typescript

2. 插件数组`plugins`：@babel/plugin-transform-runtime常用

3. 其他`minified`、`ignore`等

   > **plugin与preset的短名称：**
   >
   > ​	如果插件的npm包名称的前缀为 babel-plugin-，可以省略前缀。
   >
   > ​	如果npm包名称的前缀带有npm作用域@，例如@org/babel-plugin-xxx,短名称可以写成@org/xxx，但babel官方并没有给出明确的说明，所以还是推荐用全称。

**执行顺序：**

- 插件比预设先执行
- 插件执行顺序是插件数组从前向后执行
- 预设执行顺序是预设数组从后向前执行

## 五、常用包及配置：

> 默认为 v7

1. @babel/cli：Babel命令行转码工具，如果我们**使用命令行**进行Babel转码就需要安装它。

   

2. @babel/core：Babel的核心npm包。

   

3. **@babel/preset-env**(常用)：提供了ES6转换ES5的语法转换规则

   配置：

   ```json
   presets: [["@babel/env", {
   	targets: {		//转码的最低环境
           "chrome": "58",
       	"ie": "11"
   	},
       useBuiltIns: "usage",	//见下
       corejs:false,	//取值为2或3，指定core-js的使用版本
       modules:"false",     //见下
   }]],
   plugins: []
   ```

   - `useBuiltIns`：

     取值：	

     - `usage`：自动引入需要用到的polyfill
     - `entry`：需要自行引入polyfill，且不会根据我们实际用到的API进行针对性引入polyfill
     - `false`

     > 使用`entry`这种方式的时候，只能`import polyfill`一次，一般都是在入口文件。如果进行多次import，会发生错误。

   - `modules`：取值可以是`amd`、`umd` 、 `systemjs` 、 `commonjs` 、`cjs` 、`auto` 、`false`使用的模块语法

     > 在该参数项值是`auto`或不设置的时候，会发现我们转码前的代码里import都被转码成require了。
     >
     > 如果我们将参数项改成`false`，那么就不会对ES6模块化进行更改，还是使用import引入模块。




4. **@babel/polyfill**： 垫片，从babel7.4开始，官方不推荐再使用了，因为@babel/polyfill本身其实就是两个npm包的集合：**core-js与regenerator-runtime**。

   官方推荐直接使用这两个npm包。虽然@babel/polyfill还在进行版本升级，但其使用的core-js包为2.x.x版本，而core-js这个包本身已经发布到了3.x.x版本了，@babel/polyfill以后也不会使用3.x.x版本的包了。新版本的core-js实现了许多新的功能，例如数组的includes方法。

   ​		**转码环境判断**：如果我们的@babel/preset-env不设置任何参数，Babel就会完全根据package.json的`browserslist`的配置来做语法转换。如果没有browserslist，那么Babel就会把所有ES6的语法转换成ES5版本。

   > 注意：Babel使用browserslist的配置功能依赖于@babel/preset-env，如果Babel没有配置任何预设或插件，那么Babel对转换的代码会不做任何处理

   

5. **@babel/runtime、~runtime-corejs2、~runtime-corejs3**：原始的preset-env会在所有文件中重复注入辅助函数实现语法转换，将所有辅助函数移入runtime文件中再import入对应文件就不会增大项目体积了

   [示例查看](https://www.jiangruitao.com/babel/transform-runtime/)

   

6. **@babel/plugin-transform-runtime**: @babel-runtime不能实现自动引入对应的辅助函数，借助此插件实现

   ```json
   // 配置的默认值
   { 
   	"plugins": [
       [
   		"@babel/plugin-transform-runtime",
           {
           	"helpers": true,	// 是否引入辅助包
   			"corejs": false,	//为false、2、3，一般开发JS库的时候才设置为2或3
             	"regenerator": true,	//转换async，await
             	"useESModules": false,//是否使用es6的模块语法
             	"absoluteRuntime": false,	//用来自定义@babel/plugin-transform-runtime引入@babel/runtime/模块的路径规则
             	"version": "7.0.0-beta.0"	
   //该项主要是和@babel/runtime及其进化版@babel/runtime-corejs2、@babel/runtime-corejs3的版本号有关，这三个包我们只需要根据需要安装一个。我们把安装的npm包的版本号设置给version即可。例如，在上节的babel14例子里，安装的@babel/runtime-corejs3版本是^7.10.4，那么配置项version也取’^7.10.4’。
   //其实该项不填取默认值就行，目前填写版本号主要是可以减少打包体积。
           	}
       	]
   	]
   }
   ```

   ​	作用：

   - 自动移除语法转换后内联的辅助函数，通过引入@babel/runtime/helpers里的辅助函数来替代；
   - 当代码里使用了core-js的API，自动引入@babel/runtime-corejs3/core-js-stable/，以此来替代全局引入的core-js/stable
   - 当代码里使用了Generator/async函数，自动引入@babel/runtime/regenerator以此来替代全局引入的regenerator-runtime/runtime；

