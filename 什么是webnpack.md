# 什么是webpack
###### Webpack 是一个前端资源加载/打包工具。它将根据模块的依赖关系进行静态分析，然后将这些模块按照指定的规则生成对应的静态资源。
![image](https://www.runoob.com/wp-content/uploads/2017/01/what-is-webpack.png)
###### 从图中我们可以看出，Webpack 可以将多种静态资源 js、css、less 转换成一个静态文件，减少了页面的请求。

---

###### 接下来我们简单为大家介绍 Webpack4 的安装与使用。
# 安装 Webpack4
###### 在安装Webpack前，你本地环境需要支持 node.js
###### 由于npm安装速度慢，使用淘宝镜像及其命令cnpm，安装使用介绍参照：[使用淘宝NPM镜像](https://www.runoob.com/nodejs/nodejs-npm.html)
###### 使用cnpm安装webpack：

```
cnpm install webpack webpack-cli -g
```
> *webpack4中webpack依托于webpack-cli所以除了安装webpack还要安装webpack-cli模块

###### 然后在项目文件夹安装webpack

```
cnpm install -D webpack webpack-cli
```
> 这时候可以webpack已经可以在项目中运行了，但还是会报错，因为还没有设置webpack.config.js文件，没有entry和output出入口webpack不知道你要出来哪里的文件
###### 同目录下创建webpack.config.js文件如下：

```
var webpack = require('webpack');
var commonsPlugin = new webpack.optimize.CommonsChunkPlugin('common.js');

module.exports = {
    //插件项
    plugins: [commonsPlugin],
    //页面入口文件配置
    entry: {
        index : './src/js/page/index.js'
    },
    //入口文件输出配置
    output: {
        path: 'dist/js/page',
        filename: '[name].js'
    },
    module: {
        //加载器配置
        loaders: [
            { test: /\.css$/, loader: 'style-loader!css-loader' },
            { test: /\.js$/, loader: 'jsx-loader?harmony' },
            { test: /\.scss$/, loader: 'style!css!sass?sourceMap'},
            { test: /\.(png|jpg)$/, loader: 'url-loader?limit=8192'}
        ]
    },
    //其它解决方案配置
    resolve: {
        root: 'E:/github/flux-example/src', //绝对路径
        extensions: ['', '.js', '.json', '.scss'],
        alias: {
            AppStore : 'js/stores/AppStores.js',
            ActionType : 'js/actions/ActionType.js',
            AppAction : 'js/actions/AppAction.js'
        }
    }
};
```
###### 现在运行webpack就会输出runoob1.js到dist/bundle.js了
### 参数介绍
1. plugins 是插件项，这里我们使用了一个 CommonsChunkPlugin 的插件，它用于提取多个入口文件的公共脚本部分，然后生成一个 common.js 来方便多页面之间进行复用。
2. entry 是页面入口文件配置，output 是对应输出项配置（即入口文件最终要生成什么名字的文件、存放到哪里），其语法大致为：
```
{
    entry: {
        page1: "./page1",
        //支持数组形式，将加载数组中的所有模块，但以最后一个模块作为输出
        page2: ["./entry1", "./entry2"]
    },
    output: {
        path: "dist/js/page",
        filename: "[name].bundle.js"
    }
}
```
> 该段代码最终会生成一个 page1.bundle.js 和 page2.bundle.js，并存放到 ./dist/js/page 文件夹下。

3. module.loaders 是最关键的一块配置。它告知 webpack 每一种文件都需要使用什么加载器来处理：

```
module: {
    //加载器配置
    loaders: [
        //.css 文件使用 style-loader 和 css-loader 来处理
        { test: /\.css$/, loader: 'style-loader!css-loader' },
        //.js 文件使用 jsx-loader 来编译处理
        { test: /\.js$/, loader: 'jsx-loader?harmony' },
        //.scss 文件使用 style-loader、css-loader 和 sass-loader 来编译处理
        { test: /\.scss$/, loader: 'style!css!sass?sourceMap'},
        //图片文件使用 url-loader 来处理，小于8kb的直接转为base64
        { test: /\.(png|jpg)$/, loader: 'url-loader?limit=8192'}
    ]
}
```
> 如上，"-loader"其实是可以省略不写的，多个loader之间用“!”连接起来。
注意所有的加载器都需要通过 npm 来加载，并建议查阅它们对应的 readme 来看看如何使用。
拿最后一个 [url-loader](https://github.com/webpack/url-loader) 来说，它会将样式中引用到的图片转为模块来处理，使用该加载器需要先进行安装：

```
npm install url-loader -save-dev
```
配置信息的参数“?limit=8192”表示将所有小于8kb的图片都转为base64形式（其实应该说超过8kb的才使用 url-loader 来映射到文件，否则转为data url形式）。

你可以[点这里](http://webpack.github.io/docs/list-of-loaders.html)查阅全部的 loader 列表。

4. 最后是 resolve 配置，这块很好理解，直接写注释了：

```
resolve: {
    //查找module的话从这里开始查找
    root: 'E:/github/flux-example/src', //绝对路径
    //自动扩展文件后缀名，意味着我们require模块可以省略不写后缀名
    extensions: ['', '.js', '.json', '.scss'],
    //模块别名定义，方便后续直接引用别名，无须多写长长的地址
    alias: {
        AppStore : 'js/stores/AppStores.js',//后续直接 require('AppStore') 即可
        ActionType : 'js/actions/ActionType.js',
        AppAction : 'js/actions/AppAction.js'
    }
}
```

---
关于 webpack.config.js 更详尽的配置可以参考[这里](http://webpack.github.io/docs/configuration.html)。


---
