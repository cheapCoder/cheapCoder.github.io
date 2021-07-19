---
title: webpack笔记
date: 2021-02-02 15:51:51
tags:
- webpack
- 构建工具
---

# webpack笔记

## 第 1 章： webpack 简介

### 1. 1 webpack 是什么

webpack是一种前端资源构建工具，一个静态模块打包器(modulebundler)。在webpack看来,前端的所有资源文件(js/json/css/img/less/...)都会作为模块处理。它将根据模块的依赖关系进行静态分析，打包生成对应的静态资源(bundle)。

> **入口文件引入依赖  =>  chunk，编译后 =>  bundle**

### 1. 2 webpack 五个核心概念

1. #### Entry

   入口(Entry)指示 webpack以哪个文件为入口起点开始打包，分析构建内部依赖图。

2. #### Output

   输出(Output)指示 webpack打包后的资源 bundles 输出到哪里去，以及如何命名。

3. #### Loader

   Loader让webpack能够去处理那些非JavaScript 文件(webpack 自身只理解JavaScript)

4. #### Plugins 

   插件(Plugins)可以用于执行范围更广的任务。插件的范围包括，从打包优化和压缩，一直到重新定义环境中的变量等。

5. #### Mode

   模式(Mode)指示webpack使用相应模式的配置。

|    选项     | 描述                                                         | 特点                        |
| :---------: | :----------------------------------------------------------- | :-------------------------- |
| development | 会将 DefinePlugin 中 process.env.NODE_ENV 的值设置 为 development。<br />启用 NamedChunksPlugin 和 NamedModulesPlugin | 能让代码本地调试 运行的环境 |
| production  | 会将 DefinePlugin 中 process.env.NODE_ENV 的值设置为 production。<br />启用 FlagDependencyUsagePlugin, FlagIncludedChunksPlugin, ModuleConcatenationPlugin, NoEmitOnErrorsPlugin, OccurrenceOrderPlugin, SideEffectsFlagPlugin 和 TerserPlugin | 能让代码优化上线 运行的环境 |

## 第 2 章： webpack初体验

### 2. 1 初始化配置

1. 初始化package.json：`npm init`

2. 下载并安装webpack：`npm install webpack webpack-cli -D`


### 2. 2 编译打包应用

2. 运行指令

   ​	开发环境指令：`webpack --entry src/js/index.js -o build/js/built.js --mode=development`

   ​	功能：webpack本身只能够编译打包js和json文件，并且能将es6的模块化语法转换成浏览器能识别的语法。

   ​	生产环境指令：`webpack --entry src/js/index.js -o build/js/built.js --mode=production`

   ​	功能：在开发配置功能上多一个代码压缩。

3. 结论

   - webpack能够编译打包压缩js和json文件
   - 能将es6 的**模块化语法**转换成浏览器能识别的语法

4. 问题

   - 不能编译打包css、img等文件
   - 不能将js的es6**基本语法**转化为es5 以下语法。

## 第 3 章：webpack 开发环境的基本配置

### 3. 1 创建配置文件

```javascript
const { resolve } = require('path');	//node内置核心模块，用来处理路径问题
module.exports = {
  entry: './src/index.js', 			//入口文件
  output: {	//输出配置
    filename: 'built.js', //输出文件名
    path: resolve(__dirname, 'build/js') //输出文件路径配置
  },
  mode: 'development'	//开发环境
};
```

### 3. 2 打包样式资源

1. 下载安装loader包： `npm i css-loader style-loader less-loader less -D`

2. 修改配置文件

   ```javascript
   const { resolve } = require('path');
   
   module.exports = {
   	...
     //loader的配置
     module: {
       rules: [
         //不同文件必须配置不同loader处理
         {
           test: /\.css$/,	 //匹配哪些文件
           use: [	 //使用哪些loader进行处理，从右到左，从下到上依次执行
             'style-loader',	 //将外联css变成内联style标签引入
             'css-loader'	//将css文件变成commonjs模块加载到js中，其内容是样式字符串
           ]
         },
         {
           test: /\.less$/,
           use: [
             'style-loader',
             'css-loader',
             //将less文件编译成css文件
             //需要下载less-loader和less
             'less-loader'
           ]
         }
       ]
     },
   }
   ```

### 3. 3 打包 HTML 资源

1. 下载安装plugin包： `npm install --save-dev html-webpack-plugin`

   ```javascript
   const { resolve } = require('path');
   constHtmlWebpackPlugin = require('html-webpack-plugin');
   
   module.exports = {
     plugins: [
       //plugins的配置
       //html-webpack-plugin
       //功能：默认会创建一个空的HTML，自动引入打包输出的所有资源（JS/CSS）
       //需求：需要有结构的HTML文件
       new HtmlWebpackPlugin({
         //复制'./src/index.html'文件，并自动引入打包输出的所有资源（JS/CSS）
         template: './src/index.html'
       })
     ],
     mode: 'development'
   };
   ```

### 3.4 打包其他资源

> #### **url-loader和file-loader已经弃用，请使用[webpack的资源模块](https://webpack.docschina.org/guides/asset-modules/)**

### ~~3. 4 打包图片资源~~

1. ~~下载安装loader包:  npm install --save-dev html-loader url-loader file-loader~~

2. ~~修改配置文件~~

### ~~3. 5 打包其他资源~~

1. ~~修改配置文件~~

### 3. 6 devserver

[安装](https://webpack.docschina.org/guides/development/#using-webpack-dev-server)：`npm install --save-dev webpack-dev-server`

[运行指令](https://github.com/webpack/webpack-dev-server): `webpack serve`

```javascript
module.exports = {
  module: {
  devServer: {
    //项目构建后路径
    contentBase: resolve(__dirname, 'build'),
    //启动gzip压缩
    compress: true,
    //端口号
    port: 3000,
    //自动打开浏览器
    open: true
  }
};
```



### 3. 7 **开发环境配置总结**

运行指令: `webpack serve`

```javascript
const { resolve } = require('path');
constHtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/js/index.js',
  output: {
    filename: 'js/built.js',
    path: resolve(__dirname, 'build')
  },
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: [MiniCssExtractPlugin.loader, "css-loader", {
          loader: "postcss-loader",
          options: {
            postcssOptions: {
              plugins: [["postcss-preset-env",]],
            },
          },
        }],
      },
      {
        test: /\.less$/,
        use: [
          'style-loader',
          'css-loader',
          //将less文件编译成css文件
          //需要下载less-loader和less
          'less-loader'
        ]
      },
      {
        test: /\.html$/i,
        loader: 'html-loader',
      },
      {
        // test: /\.(png|jpg|gif)$/i,     // 打包图片
        exclude: /\.(js|html|css)$/i,     // 打包其他资源，包括图片
        type: 'asset',
        parser: {
          dataUrlCondition: {
            maxSize: 8 * 1024
          }
        },
      },
    ],
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html'
    })
  ],
  mode: 'development',
  devServer: {
    contentBase: resolve(__dirname, 'build'),
    compress: true,
    port: 3000,
    open: true
  }
};
```

## 第 4 章： webpack 生产环境的基本配置

### 4. 1 提取 css 成单独文件

2. 下载插件: `npm install --save-dev mini-css-extract-plugin`

3. 修改配置文件

   ```javascript
   module.exports = {
     ...
     module: {
       rules: [
         {
           test: /\.css$/,
           use: [
             //创建style标签，将样式放入
             //'style-loader',
             //这个loader取代style-loader。作用：提取js中的css成单独文件
             MiniCssExtractPlugin.loader,
             //将css文件整合到js文件中
             'css-loader'
           ]
         }
       ]
     },
     plugins: [
       new HtmlWebpackPlugin({
         template: './src/index.html'
       }),
       new MiniCssExtractPlugin({
         //对输出的css文件进行重命名
         filename: 'css/built.css'
       })
     ],
   };
   ```

### 4. 2 css 兼容性处理

> 默认看生产环境
>设置开发环境：`process.env.NODE_ENV = 'development'`

2. 下载loader: `npm install --save-dev postcss-loader postcss-preset-env`

2. 修改配置文件

   ```javascript
   //设置nodejs环境变量
   //process.env.NODE_ENV='development';
   
   module.exports = {
     module: {
       rules: [
         {
           test: /\.css$/,
           use: [
             MiniCssExtractPlugin.loader,
             'css-loader',
             {
               loader: "postcss-loader",
               options: {
                 postcssOptions: {
                   plugins: [
                     [
                       "postcss-preset-env",
                     ]],
                 }
               }
             }]
         }]
     }
   }
   ```
   
4. 修改package.json

```json
"browserslist": {
  "development": [
    "last 1 chromeversion",
    "last 1 firefoxversion",
    "last 1 safariversion"
  ],
    "production": [
      "> 0. 2 %",
      "notdead",
      "notop_miniall"
    ]
}
```


### 4. 3 [压缩 css](https://webpack.docschina.org/plugins/css-minimizer-webpack-plugin/#getting-started)

1. 下载安装包: `npm install css-minimizer-webpack-plugin --save-dev`

2. 修改配置文件

   ```javascript
   module.exports = {
    ...
     optimization: {
       minimize: true,
       minimizer: [
         new CssMinimizerPlugin(),
       ],
     },
   }
   ```

### 4. 4 js 语法检查

> 改为 eslint-webpack-plugin

1. 下载安装包：`npm install--save-dev eslint-loader eslint eslint-config-airbnb-base eslint-plugin-import`

2. 修改配置文件

   ```javascript
   module.exports = {
     // mode: 'development',
     mode: 'production',
     plugins: [
     	new HtmlWebpackPlugin({
       	alwaysWriteToDisk: true,
       	template: resolve(__dirname, 'src/index.html'),
       	filename: 'index.html',
     	}), 
     	new MiniCssExtractPlugin(), 
     	new ESLintPlugin({
       // fix: true
     })],
   }
   ```
   
3. 配置package.json

   ```json
   "eslintConfig": {
     "extends": "airbnb-base",
       "env": {
       "browser": true
     }
   }
   ```


### 4. 5 js 兼容性处理

> ​	 *js兼容性处理：babel-loader @babel/core* 
>
> 1. 基本js兼容性处理 --> @babel/preset-env
>
> 2. 问题：只能转换基本语法，如promise高级语法不能转换*	
>
>    全部js兼容性处理 --> @babel/polyfill
>
> 3. *问题：我只要解决部分兼容性问题，但是将所有兼容性代码全部引入，体积太大了~*
>
>    需要做兼容性处理的就做：按需加载 --> core-js
>

1. 下载安装包：`npm install --save-dev babel-loader @babel/core @babel/preset-env @babel/polyfill core-js`

2. 修改配置文件

   ```JavaScript
   const { resolve } = require('path');
   const HtmlWebpackPlugin = require('html-webpack-plugin');
   
   module.exports = {
     module: {
       rules: [
         {
           test: /\.js$/,
           exclude: /node_modules/,
           loader: 'babel-loader',
           options: {
             //预设：指示babel做怎么样的兼容性处理
             presets: [
               [
                 '@babel/preset-env',
                 {
                   //按需加载
                   useBuiltIns: 'usage',
                   //指定core-js版本
                   corejs: {
                     version: 3
                   },
                   //指定兼容性做到哪个版本浏览器
                   targets: {
                     chrome: ' 60 ',
                     firefox: ' 60 ',
                     ie: ' 9 ',
                     safari: ' 10 ',
                     edge: ' 17 '
                   }
                 }]]
           }
         }]
     }
   };
   ```

### 4. 6 js 压缩

> webpack自带[TerserWebpackPluginhttps](https://webpack.docschina.org/plugins/terser-webpack-plugin/)

2. 修改配置文件

   ```JavaScript
   module.exports = {
     //生产环境下会自动压缩js代码
     mode: 'production'
   };
   ```

### 4. 7 HTML压缩

> [生产环境下自动打开](https://github.com/jantimon/html-webpack-plugin#minification)

1. 修改配置文件

   ```JavaScript
   module.exports = {
     plugins: [
       new HtmlWebpackPlugin({
         template: './src/index.html',
         //压缩html代码
         minify: {
           //移除空格
           collapseWhitespace: true,
           //移除注释
           removeComments: true
         }
       })
     ],
     mode: 'production'
   };
   ```

### 4. 8 生产环境配置

1. 创建文件

2. 修改配置文件

   ```JavaScript
   const { resolve } = require('path')
   const HtmlWebpackPlugin = require('html-webpack-plugin');
   const MiniCssExtractPlugin = require('mini-css-extract-plugin');
   const CssMinimizerPlugin = require('css-minimizer-webpack-plugin');
   const ESLintPlugin = require('eslint-webpack-plugin');
   
   module.exports = {
     // mode: 'development',
     mode: 'production',
     entry: '/src/index.js',
     output: {
       filename: 'build.js',
       path: resolve(__dirname, "dist/"),
       publicPath: '',
       assetModuleFilename: 'images/[name]-[hash][ext][query]',
     },
   
     module: {
       rules: [
         {
           test: /\.css$/i,
           use: [MiniCssExtractPlugin.loader, "css-loader", {
             loader: "postcss-loader",
             options: {
               postcssOptions: {
                 plugins: [
                   [
                     "postcss-preset-env",
                   ]
                 ],
               },
             },
           }],
         },
         {
           test: /\.less$/,
           use: [
             'style-loader',
             'css-loader',
             //将less文件编译成css文件
             //需要下载less-loader和less
             'less-loader'
           ]
         },
         {
           test: /\.html$/i,
           loader: 'html-loader',
         },
         {
           // test: /\.(png|jpg|gif)$/i,          // 打包图片
           exclude: /\.(js|html|css|less)$/i,     // 打包其他资源，包括图片
           type: 'asset',
           parser: {
             dataUrlCondition: {
               maxSize: 8 * 1024
             }
           },
         },
         {
           test: /\.m?js$/,
           exclude: /(node_modules|bower_components)/,
           use: {
             loader: 'babel-loader',
             options: {
               presets: [['@babel/preset-env', {
                 // 按需加载
                 useBuiltIns: 'usage',
                 // 指定core-js版本
                 corejs: {
                   version: 3
                 },
                 // 指定兼容性做到哪个版本浏览器
                 targets: {
                   chrome: '60',
                   firefox: '60',
                   ie: '9',
                   safari: '10',
                   edge: '17'
                 }
               }]]
             }
           }
         }
       ],
     },
   
     plugins: [new HtmlWebpackPlugin({
       alwaysWriteToDisk: true,
       template: resolve(__dirname, 'src/index.html'),
       filename: 'index.html',
     }), new MiniCssExtractPlugin()],
   
     optimization: {
       minimize: true,
       minimizer: [
         new CssMinimizerPlugin(),
         new ESLintPlugin()
       ],
     },
   
     devServer: {
       contentBase: resolve(__dirname, '/dist'), //打包后监听的目录，
       compress: true,
       port: 9000,
       open: true,
     }
   }
   ```

正常来讲，一个文件只能被一个loader处理。
当一个文件要被多个loader处理，那么一定要指定loader执行的先后顺序：

```JavaScript
// 先执行eslint在执行babel
{
  rules: [
    {
      //在package.json中eslintConfig-->airbnb
      test: /\.js$/,
      exclude: /node_modules/,
      //优先执行
      enforce: 'pre',		// pre => 先加载， post => 后加载
      loader: 'eslint-loader',
      options: {
        fix: true
      }
    },
    {
      test: /\.js$/,
      exclude: /node_modules/,
      loader: 'babel-loader',
      options: {
        presets: [
          [
            '@babel/preset-env',
            {
              useBuiltIns: 'usage',
              corejs: { version: 3 },
              targets: {
                chrome: ' 60 ',
                firefox: ' 50 '
              }
            }
          ]
        ]
      }
    },
    {
      test: /\.(jpg|png|gif)/,
      loader: 'url-loader',
      options: {
        limit: 8 * 1024,
        name: '[hash: 10 ].[ext]',
        outputPath: 'imgs',
        esModule: false
      }
    },
    {
      test: /\.html$/,
      loader: 'html-loader'
    },
    {
      exclude: /\.(js|css|less|html|jpg|png|gif)/,
      loader: 'file-loader',
      options: {
        outputPath: 'media'
      }
    }
  ];
  plugins: [
    newMiniCssExtractPlugin({
      filename: 'css/built.css'
    }),
    newOptimizeCssAssetsWebpackPlugin(),
    newHtmlWebpackPlugin({
      template: './src/index.html',
      minify: {
        collapseWhitespace: true,
        removeComments: true
      }
    })
  ];
  mode: 'production'
}
```

## **第 5 章： webpack 优化配置(重点)**

> 1. 开发环境性能优化
>    1. 优化打包构建速度  =>  HMR
>    2. 优化代码调试   =>  source-map
> 2. 生产环境性能优化
>    1. 优化打包构建速度
>       1. oneOf
>       2. babel缓存
>       3. 多进程打包
>       4. externals
>       5. dll
>    2. 优化代码运行的性能
>       1.  持久化缓存(hash-chunkhash-contenthash)
>       2. tree shaking
>       3. code split
>       4. 懒加载/预加载
>       5. pwa

### 5. 1 HMR( hot module replacement 热模替换)

作用：一个模块发生变化，只会重新打包这一个模块（而不是打包所有模块）
极大提升构建速度

1. 样式文件：可以使用HMR功能：因为`style-loader`内部实现了

2. js文件：默认不能使用HMR功能 --> 需要修改js代码，添加支持HMR功能的代码
   	注意：HMR功能对js的处理，只能处理非入口js文件的其他文件。

   ```
   if (module.hot) {
     module.hot.accept('./js/print.js /*要监视的模块路径*/', callback)
   }
   ```

3. html文件: 不能使用HMR功能.同时会导致问题：html文件不能热更新了~ （不用做HMR功能）
     	解决：修改entry入口，将html文件引入

> 浏览器不自动刷新解决办法：https://blog.csdn.net/weixin_42281924/article/details/109541293

**修改配置文件**

```
module.exports = {

 target: "web"
  mode: 'development',
  devServer: {
    contentBase: resolve(__dirname, 'build'),
    compress: true,
    port: 3000,
    open: true,
    //开启HMR功能
    //当修改了webpack配置，新配置要想生效，必须重新webpack服务
    hot: true
  }
};
```

### 5. 2 source-map

1. source-map: 一种提供源代码到构建后代码映射技术 (如果构建后代码出错了，通过映射可以追踪源代码错误)

   ​		`[inline-|hidden-|eval-][nosources-][cheap-[module-]]source-map`

> 内联 和 外部的区别：1. 外部生成了文件，内联没有 2. 内联构建速度更快

2. 分类
   1. source-map：外部，错误代码准确信息 和 源代码的错误位置
   2. inline-source-map：内联，只生成一个内联source-map，有错误代码准确信息 和 源代码的错误位置
   3. hidden-source-map：外部，不能追踪源代码错误，只能提示到构建后代码的错误位置
   4. eval-source-map：内联，每一个文件都生成对应的source-map，放在eval函数里，有错误代码准确信息 和 源代码的错误位置(多一个hash值)
   5. nosources-source-map：外部，有错误代码准确信息和源码位置, 但是不能查看源码
   6. cheap-source-map：外部，错误代码准确信息 和 源代码的错误位置 (精确到行，其他的能精确到语句)
   7. cheap-module-source-map：外部，错误代码准确信息 和 源代码的错误位置，module会加入loader的sourcemap

3. 应用

   - 开发环境：

     - 速度快(eval > inline > cheap > ...)，调试更友好：eval-cheap-souce-map > eval-source-map

     - 调试更友好 ：souce-map > cheap-module-souce-map > cheap-souce-map

        -->结论：eval-source-map / eval-cheap-module-souce-map

   - 生产环境：源代码为了安全要不要隐藏? 调试要不要更友好

     - nosources-source-map 全部隐藏

     - hidden-source-map 只隐藏源代码，会提示构建后代码错误信息

        -->结论：source-map / cheap-module-souce-map

     > 内联会让代码体积变大，所以在生产环境不用内联的sourcemap

```
module.exports = {
  devtool: 'eval-source-map'
};
```

### 5. 3 oneOf

```
module.exports = {
  module: {
    rules: [
      {
        //在package.json中eslintConfig-->airbnb
        test: /\.js$/,
        exclude: /node_modules/,
        //优先执行
        enforce: 'pre',
        loader: 'eslint-loader',

        options: {
          fix: true
        }
      },
      {
        //以下loader只会匹配一个
        //注意：不能有两个配置处理同一种类型文件
        oneOf: [
          {
            test: /\.css$/,
            use: [...commonCssLoader]
          },
          {
            test: /\.less$/,
            use: [...commonCssLoader, 'less-loader']
          },
          /*
          正常来讲，一个文件只能被一个loader处理。
          当一个文件要被多个loader处理，那么一定要指定loader执行的先后顺序：
          先执行eslint在执行babel
          */
          {
            test: /\.js$/,
            exclude: /node_modules/,
            loader: 'babel-loader',
            options: {
              presets: [
                [
                  '@babel/preset-env',
                  {
                    useBuiltIns: 'usage',
                    corejs: { version: 3 },
                    targets: {
                      chrome: ' 60 ',
                      firefox: ' 50 '
                    }
                  }
                ]
              ]
            }
          },
          {
            test: /\.(jpg|png|gif)/,
            loader: 'url-loader',
            options: {
              limit: 8 * 1024,
              name: '[hash: 10 ].[ext]',
              outputPath: 'imgs',
              esModule: false
            }
          },
          {
            test: /\.html$/,
            loader: 'html-loader'
          },
          {
            exclude: /\.(js|css|less|html|jpg|png|gif)/,
            loader: 'file-loader',
            options: {
              outputPath: 'media'
            }
          }
        ]
      }
    ]
  },
};
```

### 5. 4 缓存

1. babel缓存   --> 让第二次打包构建速度更快

   ```
   cacheDirectory: true
   ```

2. 文件资源缓存   --> 让代码上线运行缓存更好使用

   - `hash`: 每次wepack构建时会生成一个唯一的hash值。

     > 问题: 因为js和css同时使用一个hash值。
     >
     > 如果重新打包，会导致所有缓存失效。（可能我却只改动一个文件）

   - `chunkhash`：根据chunk生成的hash值。如果打包来源于同一个chunk，那么hash值就一样

     > ​    问题: js和css的hash值还是一样的
     >
     > ​     因为css是在入口js中被引入的，所以同属于一个chunk

   - `contenthash`: 根据文件的内容生成hash值。不同文件hash值一定不一样   

```
module.exports = {
  module: {
    rules: [{{
            test: /\.js$/,
            exclude: /node_modules/,
            loader: 'babel-loader',
            options: {
              presets: [
                [
                  '@babel/preset-env',
                  {
                    useBuiltIns: 'usage',
                    corejs: { version: 3 },
                    targets: {
                      chrome: ' 60 ',
                      firefox: ' 50 '
                    }}]],
              //开启babel缓存
              //第二次构建时，会读取之前的缓存
              cacheDirectory: true
            }
          },
          {
            test: /\.(jpg|png|gif)/,
            loader: 'url-loader',
            options: {
              limit: 8 * 1024,
              name: '[hash: 10 ].[ext]',
              outputPath: 'imgs',
              esModule: false
            }}]},
  plugins: [
    new MiniCssExtractPlugin({	
      filename: 'css/built.[contenthash: 10 ].css'		// 使用contenthash
    }),
  ],
};
```

### 5. 5 treeshaking

 作用：去除无用代码，减少代码体积

 前提：

1. 必须使用ES6模块化
2. 开启production环境

问题：可能会把css / @babel/polyfill （副作用）文件干掉，这些文件只引用了

```json
//在package.json配置：
"sideEffects": ["*.css", "*.less"]
//"sideEffects": false 所有代码都没有副作用（都可以进行tree shaking）
```

### 5. 6 codesplit

5.1 作用：

1. 可以将node_modules中代码单独打包一个chunk最终输出

2. 自动分析多入口chunk中，有没有公共的文件。如果有会打包成单独一个chunk

5.2  配置

1. 利用多入口文件、splitChunks分割

   ```
   module.exports = {
     //单入口
     //entry:'./src/js/index.js',
     entry: {	
       index: './src/js/index.js',
       test: './src/js/test.js'
     },
     output: {
       //[name]：取文件名
       filename: 'js/[name].[contenthash: 10 ].js',
       path: resolve(__dirname, 'build')
     },
     /*
     1 .可以将node_modules中代码单独打包一个chunk最终输出
     2 .自动分析多入口chunk中，有没有公共的文件。如果有会打包成单独一个chunk
     */
     optimization: {
       splitChunks: {
         chunks: 'all'
       }
     },
   };
   ```

2. 利用import()

   通过js代码，让某个文件被单独打包成一个chunk
   import动态导入`import(/*webpackChunkName: 文件名*/ 引入路径)`：能将某个文件单独打包

   ```
   module.exports = {
     //单入口
     entry: './src/js/index.js',
     output: {
       //[name]：取文件名
       filename: 'js/[name].[contenthash: 10 ].js',
       path: resolve(__dirname, 'build')
     },
     optimization: {
       splitChunks: {
         chunks: 'all'
       }
     },
   };
   ```


### 5. 7 lazyloading和预加载

> 利用`import(/*webpackChunkName: 文件名*/ 引入路径)`懒加载

- 正常加载：可以认为是并行加载（同一时间加载多个文件）

  ```javascript
  import xxx
  ```

- 懒加载 lazyload：当文件需要使用时才加载

  ```javascript
  import (xxx)
  ```

- [预获取prefetch](https://webpack.docschina.org/guides/code-splitting/#prefetchingpreloading-modules)：将来某些其他导航下可能需要的资源

  ```javascript
  import(/* webpackPrefetch: true */ xxx);
  ```

- [预加载preload](https://webpack.docschina.org/guides/code-splitting/#prefetchingpreloading-modules)：当前导航下可能需要资源

  ```js
  import(/* webpackPreload: true */ xxx);
  ```

### 5. 8 pwa

> 地址：https://webpack.docschina.org/guides/progressive-web-application/

```js
npm install --save-dev workbox-webpack-plugin
```

3 .修改webpack.config.js配置文件

```js
constWorkboxWebpackPlugin = require('workbox-webpack-plugin');

/*
PWA:渐进式网络开发应用程序(离线可访问)
workbox-->workbox-webpack-plugin
*/

module.exports = {
  plugins: [
   ...
    new WorkboxWebpackPlugin.GenerateSW({
      /*
      1.帮助serviceworker快速启动
      2.删除旧的serviceworker
      3.生成一个serviceworker配置文件~
      */
      clientsClaim: true,
      skipWaiting: true
    })
  ],
  mode: 'production',
};
```

​		入口js文件配置：

```js
 if ('serviceWorker' in navigator) {
   window.addEventListener('load', () => {
     navigator.serviceWorker.register('/service-worker.js').then(registration => {
       console.log('SW registered: ', registration);
     }).catch(registrationError => {
       console.log('SW registration failed: ', registrationError);
     });
   });
 }
```

### 5. 9 多进程打包

> 地址：https://webpack.docschina.org/loaders/thread-loader/


​            进程启动大概为 600 ms，进程通信也有开销。**只有打包消耗时间比较长，才需要多进程打包，请仅在耗时的操作中使用此 loader！**

2 .下载安装包

```
npm install --save-dev thread-loader
```

3 .修改配置文件

```
module.exports = {
  module: {
    rules: [{
        {
          test: /\.js$/,
          exclude: /node_modules/,
          use: [
            {
              loader: 'thread-loader',
              options: {
                workers: 2 //进程 2 个
              }
            },
            {
              loader: 'babel-loader',
              options: {
                presets: [
                  [
                    '@babel/preset-env',
                    {
                      useBuiltIns: 'usage',
                      corejs: { version: 3 },
                      targets: {
                        chrome: ' 60 ',
                        firefox: ' 50 '
                      }
                    }
                  ]
                ],
                cacheDirectory: true
              }
            }]
        }
    }]
  },
};
```

### 5. 10  externals

> 与`dll`对比：externals本地不存在，使用外链的(第三方)库

```
module.exports = {
  externals: {
    //拒绝jQuery被打包进来
    jquery: 'jQuery',
    ......
  }
};
```

### 5. 11 dll(动态链接库)

> 与`external`对比：dll本地能存在，且每次打包总会被重复打包的第三方库或者自己不会改变的库

1. 创建webpack.dll.js(名称随意) 

2. 使用[DllPlugin](https://webpack.docschina.org/plugins/dll-plugin/#dllplugin)插件配置dll打包库

   ```
   module.exports = {
     entry: {
       library: ['jquery'],
     },
     output: {
       filename: '[name].js',
       path: resolve(__dirname, 'dll'),
       library: "[name]_[hash]"
     },
     plugins: [
       new webpack.DllPlugin({
         name: '[name]_[hash]',	//要用_,其他分隔符可能出错
         path: resolve(__dirname, 'dll/manifest.json')
       })
     ],
     mode: 'production'
   };
   ```

3. dll打包：`npx webpack --config webpack.dll.js`

4. 再在webpack.config.js中用[DllReferencePlugin](https://webpack.docschina.org/plugins/dll-plugin/#dllreferenceplugin) 配置动态链接的库,这样main.js就不会引入配置里指定的库了

5. 构建：`npx webpack`

6. 再用[AddAssetHtmlWebpackPlugin](https://juejin.cn/post/6844903777296728072)插件将dll的包引入到`index.html`中

```
module.exports = {
  plugins: [
    //告诉webpack哪些库不参与打包，同时使用时的名称也得变~
    newwebpack.DllReferencePlugin({
      manifest: resolve(__dirname, 'dll/manifest.json')
    }),
    //将某个文件打包输出去，并在html中自动引入该资源
    new AddAssetHtmlWebpackPlugin({
    	filepath: resolve(__dirname, 'dll/library.js'),
    })
  ],
};
```

## 第 6 章： webpack 配置详情

### 6. 1 entry：入口起点

1. `string --> './src/index.js'`

      单入口，打包形成一个chunk，输出一个bundle文件

   ​	此时chunk的名称默认是 main

2. `array --> ['./src/index.js', './src/add.js']`

      多入口，所有入口文件最终只会形成一个chunk, 输出只有**一个bundle文件**

      作用：在HMR中引入`index.html`让html热更新生效

3. object

      多入口

      有几个入口文件就形成几个chunk，输出几个bundle文件

   ​	此时chunk的名称是 key

4. `数组对象 --> { index :  [ 'index.js' ,  'add.js' ]}`

```
module.exports = {
  entry: {
    index: ['./src/index.js', './src/count.js'],	//数组对象特殊用法，形成一个chunk，输出一个bundle文件
    add: './src/add.js'
  },
};
```

### 6. 2 output

```
module.exports = {
  output: {
    filename: 'js/[name].js',    //文件名称（指定名称+目录）
    path: resolve(__dirname, 'build'),    //输出文件目录（将来所有资源输出的公共目录）
    publicPath: '/',	//所有资源引入公共路径前缀-->'imgs/a.jpg'-->'/imgs/a.jpg'
    chunkFilename: 'js/[name]_chunk.js',	//非入口chunk(比如import函数引入的chunk)的名称
    library:'[name]',	//整个thunk向外暴露的变量名
    libraryTarget:'window'	//变量名添加到哪个上环境上，1.window 2.global 3.commonjs暴露 4.amd ...
  },
};
```

### 6. 3 module

```
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader']		//多个loader用use，单个用loader
      },
      {
        test: /\.js$/,
        exclude: /node_modules/,				//排除node_modules下的js文件
        include: resolve(__dirname, 'src'),		//只检查src下的js文件
        enforce: 'pre',				//优先执行
        //enforce:'post',			//延后执行
        loader: 'eslint-loader',	//单个loader用loader
        options: {}
      },
      {
        oneOf: []  			 //以下配置只会生效一个
      }
    ]
  },
};
```

### 6. 4 resolve

```
module.exports = {
  //解析模块的规则
  resolve: {
    //配置解析模块路径别名:优点简写路径缺点路径没有提示
    alias: {
      $css: resolve(__dirname, 'src/css')
    },
    //配置省略文件路径的后缀名
    extensions: ['.js', '.json', '.jsx', '.css'],
    //告诉webpack解析第三方模块的确定位置
    modules: [resolve(__dirname, '../../node_modules'), 'node_modules']
  }
};
```

### 6. 5 devserver

```
module.exports = {
  devServer: {
    //运行在更改的代码的目录
    contentBase: resolve(__dirname, 'build'),
    //监视contentBase目录下的所有文件，一旦文件变化就会reload
    watchContentBase: true,
    watchOptions: {
      //忽略文件
      ignored: /node_modules/
    },
    //启动gzip压缩
    compress: true,
    //端口号
    port: 5000,
    //域名
    host: 'localhost',
    //自动打开浏览器
    open: true,
    //开启HMR功能
    hot: true,
    //不要显示启动服务器日志信息
    clientLogLevel: 'none',
    //除了一些基本启动信息以外，其他内容都不要显示
    quiet: true,
    //如果出错了，不要全屏提示~
    overlay: false,
    //服务器代理-->解决开发环境跨域问题
    proxy: {
      //一旦devServer( 5000 )服务器接受到/api/xxx的请求，就会把请求转发到另外一个服务器(3000)
      '/api': {
        target: 'http://localhost: 3000',
        //发送请求时，请求路径重写：将/api/xxx-->/xxx（去掉/api）
        pathRewrite: {
          '^/api': ''
        }
      }
    }
  }
};
```

### 6. 6 optimization

```
module.exports = {
  optimization: {
    minimizer: [
      // 配置生产环境的压缩方案：js和css
      new TerserWebpackPlugin({
        // 开启缓存
        cache: true,
        // 开启多进程打包
        parallel: true,
        // 启动source-map
        sourceMap: true
      })
    ],
    splitChunks: {
      chunks: 'all'
      // 默认值，可以不写~
      /* minSize: 30 * 1024, // 分割的chunk最小为30kb
      maxSiza: 0, // 最大没有限制
      minChunks: 1, // 要提取的chunk最少被引用1次
      maxAsyncRequests: 5, // 按需加载时并行加载的文件的最大数量
      maxInitialRequests: 3, // 入口js文件最大并行请求数量
      automaticNameDelimiter: '~', // 名称连接符
      name: true, // 可以使用命名规则
      cacheGroups: {
        // 分割chunk的组
        // node_modules文件会被打包到 vendors 组的chunk中。--> vendors~xxx.js
        // 满足上面的公共规则，如：大小超过30kb，至少被引用一次。
        vendors: {
          test: /[\\/]node_modules[\\/]/,
          // 优先级
          priority: -10
        },
        default: {
          // 要提取的chunk最少被引用2次
          minChunks: 2,
          // 优先级
          priority: -20,
          // 如果当前要打包的模块，和之前已经被提取的模块是同一个，就会复用，而不是重新打包模块
          reuseExistingChunk: true
        } 
      }*/
    },
    // 将当前模块的记录其他模块的hash单独打包为一个文件 runtime
    // 解决：b记录a的contenthash值，修改a文件导致b文件的contenthash也变化
    runtimeChunk: {
      name: entrypoint => `runtime-${entrypoint.name}`
    },
  }
};
```

## 第7章：webpack V5 

此版本重点关注以下内容:

- 通过持久缓存提高构建性能.
- 使用更好的算法和默认值来改善长期缓存.
- 通过更好的树摇和代码生成来改善捆绑包大小.
- 清除处于怪异状态的内部结构，同时在 v4 中实现功能而不引入任何重大更改.
- 通过引入重大更改来为将来的功能做准备，以使我们能够尽可能长时间地使用 v5.

### 7.1 自动删除 Node.js Polyfills

早期，webpack 的目标是允许在浏览器中运行大多数 node.js 模块，但是模块格局发生了变化，许多模块用途现在主要是为前端目的而编写的。webpack <= 4 附带了许多 node.js 核心模块的 polyfill，一旦模块使用任何核心模块（即 crypto 模块），这些模块就会自动应用。

尽管这使使用为 node.js 编写的模块变得容易，但它会将这些巨大的 polyfill 添加到包中。在许多情况下，这些 polyfill 是不必要的。

webpack 5 会自动停止填充这些核心模块，并专注于与前端兼容的模块。

迁移：

- 尽可能尝试使用与前端兼容的模块。
- 可以为 node.js 核心模块手动添加一个 polyfill。错误消息将提示如何实现该目标。

### 7.2 Chunk 和模块 ID

添加了用于长期缓存的新算法。在生产模式下默认情况下启用这些功能。

`chunkIds: "deterministic", moduleIds: "deterministic"`

### 7.3 Chunk ID

你可以不用使用 `import(/* webpackChunkName: "name" */ "module")` 在开发环境来为 chunk 命名，生产环境还是有必要的

webpack 内部有 chunk 命名规则，不再是以 id(0, 1, 2)命名了

### 7.4 Tree Shaking

1. webpack 现在能够处理对嵌套模块的 tree shaking

```js
// inner.js
export const a = 1;
export const b = 2;

// module.js
import * as inner from './inner';
export { inner };

// user.js
import * as module from './module';
console.log(module.inner.a);
```

​			在生产环境中, inner 模块暴露的 `b` 会被删除

2. webpack 现在能够处理多个模块之间的关系

```js
import { something } from './something';

function usingSomething() {
  return something;
}

export function test() {
  return usingSomething();
}
```

​		当设置了`"sideEffects": false`时，一旦发现`test`方法没有使用，不但删除`test`，还会删除`"./something"`

3. webpack 现在能处理对 Commonjs 的 tree shaking

### 7.5 Output

webpack 4 默认只能输出 ES5 代码

webpack 5 开始新增一个属性 output.ecmaVersion, 可以生成 ES5 和 ES6 / ES2015 代码.

如：`output.ecmaVersion: 2015`

### 7.6 SplitChunk

```js
// webpack4
minSize: 30000;
```

```js
// webpack5
minSize: {
  javascript: 30000,
  style: 50000,
}
```

### 7.7 Caching

```js
// 配置缓存
cache: {
  // 磁盘存储
  type: "filesystem",
  buildDependencies: {
    // 当配置修改时，缓存失效
    config: [__filename]
  }
}
```

缓存将存储到 `node_modules/.cache/webpack`

### 7.8 监视输出文件

之前 webpack 总是在第一次构建时输出全部文件，但是监视重新构建时会只更新修改的文件。

此次更新在第一次构建时会找到输出文件看是否有变化，从而决定要不要输出全部文件。

### 7.9 默认值

> 只有`mode`必须写

- `entry: "./src/index.js`
- `output.path: path.resolve(__dirname, "dist")`
- `output.filename: "[name].js"`

### 7.10 更多内容

https://github.com/webpack/changelog-v5

