## 基础介绍

需要把我们的写的代码转成浏览器可以识别的js,css,html,png

#### 作用

静态模块打包工具，打包程序时，构建依赖关系图，其中包含程序需要的每个模块，而后将所有的模块打包一个或者多个bundle

#### 概念

入口:
		从什么源开始打包
loader：
		处理某种特定的文件格式
		loader的顺序，后写的先执行。		
		如果引入了css，需要css loader,
		es6代码转换到es5需要 babel loader
插件：
		会作用于整个webpack的打包过程，如压缩，
模式：
		development 
		production
			可以启用响应模式下的webpack内置的优化
			代码压缩等

#### 安装

全局安装

```shel
npm -i webpack webpack-cli -g
```

局部安装

​	是在项目中安装配置 

## 简单配置webpack

* 初始化项目

  ```go
  mkdir test-project
  npm init
  ```

* 安装webpack

  ```go
  npm install webpack webpack-cli -D 
  ```

  -D 表示安装的是开发依赖 -S 表示生产依赖

* 配置

  这样安装之后不能直接在命令行使用webpack命令，因为这样安装的时局部。
  如果想使用,需要配置package.json 配置scripts,他使用的项目中安装的webpack，他会去找我们的局部安装的命令,一般是是安装在node_modules中

  ```js
  {
    "name": "kj_react",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "scripts": {
      "build":"webpack --mode development"
    },
    "author": "",
    "license": "ISC",
    "devDependencies": {
      "webpack": "^4.41.2",
      "webpack-cli": "^3.3.9"
    }
  }
  ```

  webpack --mode development 

  这个就是具体的打包命令，可以指定其他参数，如配置文件，模式等

* 配置入口和出口

  默认webpack的入口是项目目录下的src目录中的index.js文件，所以需要创建src目录，和index.js文件
默认的出口在项目目录下dest目录下生成main.js文件
  
* 打包项目

  ```shell
  npm run build 
  ```

  可以查看是否生成dest目录和main.js文件。
  可以修改packages.json中的 webpack --mode production  看生成的代码是否压缩

## Webpack其他必须配置

webpack可以指定配置文件，而后在执行webpack命令的时候默认会读取项目目录下的webpack.config.js，
webpack.config.js是一个js文件，所以里面可以引用一些js的模块。这个文件实际是向外暴露了一些webpack的配置，而后后面使用命令打包的时候会读取文件中的配置。

#### 入口和出口的配置

webpack打包之后所有文件都是输出到dist目录中，目录太乱，所可以在输出的时候指定路径

```go
const path= require("path")
module.exports={
    // 指定模式
    mode:"development",
    // 单个入口
    // entry:"./src/index.html"
    entry:{
        main:"./src/main.js",
        test:"./src/index.js"
    },
    output:{
        path:path.resolve(__dirname,"dist"),
        // name 是根据入口不同生成不同的文件
        // hash 是生成的文件中带有hash，所有文件的hash值相同
        //  保留的hash的字符串的数量
        // filename:'[name].[hash:8].js'
        // chunkHash是入口文件或者项目生成不同的hash值,当某个文件发生变化的时候只是重新生成变化的文件，没有变化的文件不会更新
        // js/ 表示会输出到js目录中
        filename:'js/[name].[chunkHash:8].js'
    }
}
```

#### html-webpack-plugin

使用html-webpack-plugin插件在我们执行npm build的时候会自动在dist目录中生成一个html文件.

所有的插件都可以从  https://www.npmjs.com/  这个网站找到，而且有相关的配置文档。

* 安装

  ```shel
  npm i --save-dev html-webpack-plugin
  ```

* 配置webpack.config.js

  ```javascript
  const path= require("path")
  const HtmlWebpackPlugin = require('html-webpack-plugin')
  module.exports={
      mode:"development",
      entry:{
          main:"./src/main.js",
          test:"./src/index.js"
      },
      output:{
          path:path.resolve(__dirname,"dist"),
          filename:'[name].[chunkHash:8].js'
      },
      plugins: [
          new HtmlWebpackPlugin()
        ]
  }
  ```

* 重新进行npm run build 查看生成的html

* 自定义配置，由于生成的html是自动生成的所有我们可以指定我们的自己的html模板文件

  ```js
  const path= require("path")
  const HtmlWebpackPlugin = require('html-webpack-plugin')
  module.exports={
      mode:"development",
      entry:{
          main:"./src/main.js",
          test:"./src/index.js"
      },
      output:{
          path:path.resolve(__dirname,"dist"),
          filename:'[name].[chunkHash:8].js'
      },
      plugins: [
          new HtmlWebpackPlugin({
              // 指定输出后的文件名
              filename:"index.html",
              // 设定的参数，可以在index.html文件中使用模板语法，进行引用
              title:'我来自参数',
              // template 指定模板文件 
              template:"plugin/index.html"
          })
        ]
  }
  ```

  plugin/index.html文件内容如下

  ```html
  <!DOCTYPE html>
  <html>
    <head>
      <meta charset="UTF-8">
      <title><%= htmlWebpackPlugin.options.title %></title>
    </head>
    <body>
       <h1>xxxxx</h1>
    <script type="text/javascript" src="main.ac89efb4.js"></script><script type="text/javascript" src="test.2880c184.js"></script></body>
  </html>
  ```


#### css-loader style-loader

样式处理

* css-loader 
  如果在我的代理中js文件中需要引入css文件，这时候需要css-loader,在webpack.config.js中定义mode和规则，遇到什么样的文件用什么样的loader进行处理

* style-loader

  把css文件的处理结果当到js文件中。当网页运行的时候时候在动态的插到html中。
  有时候不想把css的结果直接放到js中，而是希望放到单独的css文件中，这时候就需要其他插件了
  mini-css-extract-plugin

 安装

```go
npm install --save-dev css-loader style-loader
```

配置

```js
const path= require("path")
const HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports={
    mode:"development",
    entry:{
        main:"./src/main.js",
        test:"./src/index.js"
    },
    output:{
        path:path.resolve(__dirname,"dist"),
        filename:'[name].[chunkHash:8].js'
    },
    // 配置module，规则匹配的文件，使用什么loader进行处理
    module: {
        rules: [
          {
            test: /\.css$/i,
            use: ['style-loader', 'css-loader'],
          },
        ],
      },
    plugins: [
        new HtmlWebpackPlugin({
            filename:"index.html",
            template:"plugin/index.html"
        })
      ]
}
```

测试，新建index.css 而后index.js引入

index.css

```css
body{
    background: #0099ff;
}
```

plugin/index.html

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
  </head>
  <body>
    <p>
        xxxx
    </p>
  </body>
</html>
```

index.js

```js
import "./index.css"
console.log("index.jssss")
```

#### mini-css-extract-plugin

可以把css提取成单独的文件，提取之后会帮我们把他自动的插入html中。

* 安装

```go
npm install --save-dev mini-css-extract-plugin
```

* 配置 webpack.config.js

```js
const path= require("path")
const HtmlWebpackPlugin = require('html-webpack-plugin')
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
module.exports={
    mode:"development",
    entry:{
        main:"./src/main.js",
        test:"./src/index.js"
    },
    output:{
        path:path.resolve(__dirname,"dist"),
        filename:'[name].[chunkHash:8].js'
    },
    module: {
        rules: [
          {
            test: /\.css$/i,
            use: [MiniCssExtractPlugin.loader, 'css-loader'],
          },
        ],
      },
    plugins: [
        new HtmlWebpackPlugin({
            filename:"index.html",
            title:"webpack",
            template:"plugin/index.html"
        }),
        new MiniCssExtractPlugin({
            filename: '[name].css',
          }),
      ]
}
```

执行 npm run build 看是否生成css文件，生成的css的文件名和我们的自定的入口一样。

* 路径问题

webpack打包之后所有文件都是输出到dist目录中，目录太乱，所可以可以在输出的时候执行路径如下，

```go
const path= require("path")
const HtmlWebpackPlugin = require('html-webpack-plugin')
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
module.exports={
    mode:"development",
    entry:{
        main:"./src/main.js",
        test:"./src/index.js"
    },
    output:{
        path:path.resolve(__dirname,"dist"),
        // 指定输出到js目录下
        filename:'js/[name].[chunkHash:8].js'
    },
    module: {
        rules: [
          {
            test: /\.css$/i,
            use: [MiniCssExtractPlugin.loader, 'css-loader'],
          },
        ],
      },
    plugins: [
        new HtmlWebpackPlugin({
            filename:"index.html",
            title:"webpack",
            template:"plugin/index.html"
        }),
        new MiniCssExtractPlugin({
            // 指定输出到css目录下
            filename: 'css/[name].css',
          }),
      ]
}
```

#### webpack-dev-server

每次都需要去打开浏览器刷新比较，麻烦。使用他会自动把我们的代码更新。
他是一个基于express的服务器。

* 安装

  ```shel
  npm install webpack-dev-server --save-dev
  ```

* 配置

  修改package.json文件

  ```js
  {
    "name": "kj_react",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "scripts": {
      "build": "webpack   --config webpack.config.js",
      "dev": "webpack-dev-server    --config webpack.config.js"
    },
    "author": "",
    "license": "ISC",
    "devDependencies": {
      "css-loader": "^3.2.0",
      "html-webpack-plugin": "^3.2.0",
      "mini-css-extract-plugin": "^0.8.0",
      "style-loader": "^1.0.0",
      "webpack": "^4.41.2",
      "webpack-cli": "^3.3.9",
      "webpack-dev-server": "^3.9.0"
    }
  }
  ```

  修改webpack.config.js文件,也可以配置代理等配置，具体的可以看 

  ​	https://webpack.docschina.org/configuration/dev-server/ 

  ```go
  const path= require("path")
  const HtmlWebpackPlugin = require('html-webpack-plugin')
  const MiniCssExtractPlugin = require('mini-css-extract-plugin');
  module.exports={
      mode:"development",
      devServer: {
          contentBase: path.join(__dirname, 'dist'),
          compress: true,
          port: 8080
        },
      entry:{
          main:"./src/main.js",
          test:"./src/index.js"
      },
      output:{
          path:path.resolve(__dirname,"dist"),
          filename:'js/[name].[chunkHash:8].js'
      },
      module: {
          rules: [
            {
              test: /\.css$/i,
              use: [MiniCssExtractPlugin.loader, 'css-loader'],
            },
          ],
        },
      plugins: [
          new HtmlWebpackPlugin({
              filename:"index.html",
              title:"webpack",
              template:"plugin/index.html"
          }),
          new MiniCssExtractPlugin({
              filename: 'css/[name].css',
            }),
        ]
  }
  ```

* 启动

  ```shel
  npm run dev
  ```

  #### less sass配置

  less和sass都是css预处理器,先经过less loader 把less转换成css,而后再用css loader进行处理，

  安装

  ```shel
  npm install less-loader less --save-dev
  ```

  配置 webpack.config.js

  ```go
  const path= require("path")
  const HtmlWebpackPlugin = require('html-webpack-plugin')
  const MiniCssExtractPlugin = require('mini-css-extract-plugin');
  module.exports={
      mode:"development",
      devServer: {
          contentBase: path.join(__dirname, 'dist'),
          compress: true,
          port: 8080
        },
      entry:{
          main:"./src/main.js",
          test:"./src/index.js"
      },
      output:{
          path:path.resolve(__dirname,"dist"),
          filename:'js/[name].[chunkHash:8].js'
      },
      module: {
          rules: [
            {
              test: /\.css$/i,
              //  loader的第一种写法
              use: [MiniCssExtractPlugin.loader, 'css-loader'],
            },
          ],
          rules: [
              // loader的第二种写法，这种可以加上一些options参数。
              {
                test: /\.less$/,
                loader: [MiniCssExtractPlugin.loader, 'css-loader','less-loader'], 
              },
            ],
        },
      plugins: [
          new HtmlWebpackPlugin({
              filename:"index.html",
              title:"webpack",
              template:"plugin/index.html"
          }),
          new MiniCssExtractPlugin({
              filename: 'css/[name].css',
            }),
        ]
  }
  ```

#### 图片引用处理

file-loader 和url-loader
	他们处理的是在js中import或者直接在css中引用的图片，不出html中引用的静态文件，或者其他静态文件。
url-loader包含了file-loader.
	url-loader会把小于某个值的图片文件转换成base64去处理，大于某个值的文件使用file-loader去处理
如果只是使用file-loader则只用安装file-loader
如果使用url-loader,则必须安装file-loader和url-loader

* 安装

  ```js
  npm install -D file-loader url-loader 
  ```

* webpack.config.js配置

  ```js
  const path= require("path")
  const HtmlWebpackPlugin = require('html-webpack-plugin')
  const MiniCssExtractPlugin = require('mini-css-extract-plugin');
  module.exports={
      mode:"development",
      devServer: {
          contentBase: path.join(__dirname, 'dist'),
          compress: true,
          port: 8080
        },
      entry:{
          main:"./src/main.js",
          test:"./src/index.js"
      },
      output:{
          path:path.resolve(__dirname,"dist"),
          filename:'js/[name].[chunkHash:8].js'
      },
      module: {
          rules: [
              //  loader的第一种写法
              {
                  test: /\.css$/,
                  use: [MiniCssExtractPlugin.loader, 'css-loader'],
              },
  
              // loader的第二种写法，这种可以加上一些options参数。
              {
                  test: /\.less$/,
                  use:[
                  {loader: MiniCssExtractPlugin.loader},
                  {loader:'css-loader' }, 
                  {loader:'less-loader'}, 
              
                  ]
              },
              // file loader配置 ，主要是使用文件的copy的方式
              // {
              //     test: /\.(png|jpg|gif)$/i,
              //     use:[
              //         {
              //             loader:"file-loader",
              //             options:{
              //                 name:"images/[name].[ext]",
              //                 publicPath:"/"
              //             }
              //         }
              //     ]
              // }
              //  url loader 配置
              {
                  test: /\.(png|jpg|gif)$/i,
                  use:[
                      {
                          loader:"url-loader",
                          options:{
                              limit:80, // 小于80的用bash64处理到js中，大于80的用文件copy的方式
                              name:"images/[name].[ext]",
                              publicPath:"/"
                          }
                      }
                  ]
              }
          ],
        },
      plugins: [
          new HtmlWebpackPlugin({
              filename:"index.html",
              title:"webpack",
              template:"plugin/index.html"
          }),
          new MiniCssExtractPlugin({
              filename: 'css/[name].css',
            }),
        ]
  }
  ```

* 测试

  新建一个assets目录里面存放一个1.png的图片

  修改index.css文件

  ```css
  body {
      color: #ff0088;
      background: url(./assets/1.jpg);
  }   
  ```

* build启动

  执行 npm run build 可以发现在dist目录中由images目录，里面存放了1.png，而且css中引用的路径也是相对于dist的相对路径。

#### 静态文件处理

有些文件是不需要经过webpack处理直接copy就可以，这时候需要copy-webpack-plugin

* 安装

```shel
npm install copy-webpack-plugin --save-dev
```

* 配置webpack-config.js

```js
const path= require("path")
const HtmlWebpackPlugin = require('html-webpack-plugin')
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const CopyPlugin = require('copy-webpack-plugin');

module.exports={
    mode:"development",
    devServer: {
        contentBase: path.join(__dirname, 'dist'),
        compress: true,
        port: 8080
      },
    entry:{
        main:"./src/main.js",
        test:"./src/index.js"
    },
    output:{
        path:path.resolve(__dirname,"dist"),
        filename:'js/[name].[chunkHash:8].js'
    },
    module: {
        rules: [
            {
                test: /\.css$/,
                use: [MiniCssExtractPlugin.loader, 'css-loader'],
            },

            {
                test: /\.less$/,
                use:[
                {loader: MiniCssExtractPlugin.loader},
                {loader:'css-loader' }, 
                {loader:'less-loader'}, 
            
                ]
            },
            {
                test: /\.(png|jpg|gif)$/i,
                use:[
                    {
                        loader:"url-loader",
                        options:{
                            limit:80, 
                            name:"/images/[name].[ext]",
                            publicPath:"/"
                        }
                    }
                ]
            }
        ],
      },
    plugins: [
        new HtmlWebpackPlugin({
            filename:"index.html",
            title:"webpack",
            template:"plugin/index.html"
        }),
        new MiniCssExtractPlugin({
            filename: 'css/[name].css',
          }),
        new CopyPlugin([
            {
                // 从那个目录复制到那个目录
                from: path.resolve(process.cwd(),"src/static"),
                to :path.resolve(process.cwd(),"dist/static")
            }
        ])
      ]
}
```


#### 代码转换

babel-loader  把es6的代码可以转换成es5等待浏览器适应的代码	

安装

```shell
npm install -D babel-loader @babel/core @babel/preset-env
```

@babel/preset-env 这个里面包含了一些默认的预设转换。

配置webpack.config.js

```js
const path= require("path")
const HtmlWebpackPlugin = require('html-webpack-plugin')
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const CopyPlugin = require('copy-webpack-plugin');

module.exports={
    mode:"development",
    devServer: {
        contentBase: path.join(__dirname, 'dist'),
        compress: true,
        port: 8080
      },
    entry:{
        main:"./src/main.js",
        test:"./src/index.js"
    },
    output:{
        path:path.resolve(__dirname,"dist"),
        filename:'js/[name].[chunkHash:8].js'
    },
    module: {
        rules: [
            {
                test: /\.css$/,
                use: [MiniCssExtractPlugin.loader, 'css-loader'],
            },

            {
                test: /\.less$/,
                use:[
                {loader: MiniCssExtractPlugin.loader},
                {loader:'css-loader' }, 
                {loader:'less-loader'}, 
            
                ]
            },
            {
                test: /\.(png|jpg|gif)$/i,
                use:[
                    {
                        loader:"url-loader",
                        options:{
                            limit:80, 
                            name:"/images/[name].[ext]",
                            publicPath:"/"
                        }
                    }
                ]
            },
            {
                test: /\.m?js$/,
                //  排除一些文件表示，哪些目录的文件不需要经过处理。可以直接使用 
                // include 表示哪些文件需要被处理
                exclude: /(node_modules|bower_components)/,
                use: {
                  loader: 'babel-loader',
                  options: {
                    presets: ['@babel/preset-env']
                  }
                }
            }
        ],
      },
    plugins: [
        new HtmlWebpackPlugin({
            filename:"index.html",
            title:"webpack",
            template:"plugin/index.html"
        }),
        new MiniCssExtractPlugin({
            filename: 'css/[name].css',
          }),
        new CopyPlugin([
            {
                from: path.resolve(process.cwd(),"src/static"),
                to :path.resolve(process.cwd(),"dist/static")
            }
        ])
      ]
}
```

可以编写进行测试js文件，

index.js

```js
import "./index.css"
import "./index.less"
import {add} from "./add"
console.log("index.js")
add(1,2)
```

add.js

```js
export const add = (x,y)=>{
    console.log(`${x}+${y}=${x+y}`)
}
```

而后npm run build 进行测试。