# webpack初学指北

##### By [Flavio Copes](https://medium.freecodecamp.org/@flaviocopes) | Jun 14, 2018

[原文](https://medium.freecodecamp.org/a-beginners-introduction-to-webpack-2620415e46b3)

# 什么是webpack

webpack是一个打包js模块的工具。也被称为模块打包工具

它会把许多文件打包生成一个或多个文件来运行你的app

它能做许多事情

* 帮助你打包资源
* 热加载
* 能够跑Babel然后转成es5，允许你使用js最新的特性而无需担心兼容性问题
* 能把CoffeeScript转成js
* 能把内联图像转成数据URIs
* 允许你在css文件里使用require（）
* etc......

webpack不仅用于前端，而且也用于node.js后台

Webpack是一个更有针对性的工具。 只需要指定应用程序的入口点（它甚至可以是带有脚本标记的HTML文件），webpack会分析文件并将它们捆绑在一个JavaScript输出文件中，其中包含运行应用程序所需的所有内容。

# 安装webpack

## 全局安装

yarn全局安装：

	yarn global add webpack webpack-cli
	
npm：

	npm i -g webpack webpack-cli
	
安装完成后，只需要跑以下命令

	webpack-cli
	
![](https://github.com/shArpyYAo/markdown-photo/blob/master/webpack-cli.png?raw=true)

## 本地安装

推荐本地安装webpack，因为这样就能够针对每个项目的需要自行更新webpack。

yarn：

	yarn add webpack webpack-cli -D
	
npm：

	npm i webpack webpack-cli --save-dev
	
安装完成后，在package.js文件加入以下代码：

	{ 
	  //... 
	  "scripts": { 
	    "build": "webpack" 
	  } 
	}
	
然后你就可以在项目的根目录执行以下命令运行webpack

	yarn build
	
# webpack设置

从webpack4开始，如果你没有特殊要求，无需更改配置，webpack会按照默认配置进行打包：

* 你的app入口在./src/indec.js
* 输出在./dist/main.js
* webpack按照生产模式来打包

如果有需要，你可以自己配置。webpack的配置文件在项目的根目录，叫webpack.config.js

# 入口

默认app的入口是在./src/index.js，在以下位置更改入口配置：

	module.exports = {
	  /*...*/
	  entry: './index.js'
	  /*...*/
	}
	
# 输出

默认的输出是在./dist/main.js，下面这个例子输出改成app.js：

	module.exports = {
	  /*...*/
	  output: {
	    path: path.resolve(__dirname, 'dist'),
	    filename: 'app.js'
	  }
	  /*...*/
	}

使用webpack允许你在js使用import或require语句，不仅在js中使用，其他文件也可以使用（比如说css文件）

webpack的目的是帮我们管理好所有的依赖，不仅是js。使用loaders是一种方法

举个例子：

	import 'style.css'

loader的配置就是：

	module.exports = {
	  /*...*/
	  module: {
	    rules: [
	      { test: /\.css$/, use: 'css-loader' },
	    }]
	  }
	  /*...*/
	}
	
这个正则匹配所有的css文件

可以这样设置loader：
	
	module.exports = {
	  /*...*/
	  module: {
	    rules: [
	      {
	        test: /\.css$/,
	        use: [
	          {
	            loader: 'css-loader',
	            options: {
	              modules: true
	            }
	          }
	        ]
	      }
	    ]
	  }
	  /*...*/
	}
	
你可以加多几个loaders：

	module.exports = {
	  /*...*/
	  module: {
	    rules: [
	      {
	        test: /\.css$/,
	        use:
	          [
	            'style-loader',
	            'css-loader',
	          ]
	      }
	    ]
	  }
	  /*...*/
	}
	
在这个例子中，css-loader解释css中的import‘style.css’语句。style-loader则用

	<style>

在dom里面插入css

顺序很重要，而且它是相反的（最后一个是先执行）。

当然，还有很多其他的loader

常见的有加载Babel的loader，专门把代码转成es5:

	module.exports = {
	  /*...*/
	  module: {
	    rules: [
	      {
	        test: /\.js$/,
	        exclude: /(node_modules|bower_components)/,
	        use: {
	          loader: 'babel-loader',
	          options: {
	            presets: ['@babel/preset-env']
	          }
	        }
	      }
	    ]
	  }
	  /*...*/
	}
	
这个例子让Babel预处理我们所有的React / JSX文件：

	module.exports = {
	  /*...*/
	  module: {
	    rules: [
	      {
	        test: /\.(js|jsx)$/,
	        exclude: /node_modules/,
	        use: 'babel-loader'
	      }
	    ]
	  },
	  resolve: {
	    extensions: [
	      '.js',
	      '.jsx'
	    ]
	  }
	  /*...*/
	}
	
# Plugins

Plugins跟loaders类似，但有些不同。他们能做loaders做不到的事情，并且是webpack的主要构建块

	module.exports = {
	  /*...*/
	  plugins: [
	    new HTMLWebpackPlugin()
	  ]
	  /*...*/
	}

HTMLWebpackPlugin插件可以自动创建HTML文件并添加输出JS包路径，因此可以为JavaScript提供服务。

还有个有用的插件，CleanWebpackPlugin，在创建任何输出之前清空dist/文件夹，因此在更改输出文件的名称时不要留下文件：

	module.exports = {
	  /*...*/
	  plugins: [
	    new CleanWebpackPlugin(['dist']),
	  ]
	  /*...*/
	}
	
# webpack mode

mode（在Webpack 4中引入）设置Webpack工作的环境。它可以设置为开发或生产（默认为生产，因此只能在转移到开发时设置它）。

	module.exports = {
	  entry: './index.js',
	  mode: 'development',
	  output: {
	    path: path.resolve(__dirname, 'dist'),
	    filename: 'app.js'
	  }
	}

开发模式：

* 打包非常快
* 对比于生产模式更少配置项
* 不删注释
* 提供更加细节地报错信息
* 提供良好的debugging体验

生产模式构建较慢，因为它需要生成更好的打包。 生成的JavaScript文件的大小较小，因为它删除了许多生产中不需要的东西。

我做了一个示例应用程序，只打印一个console.log语句。

生产模式：

![](https://github.com/shArpyYAo/markdown-photo/blob/master/生产模式.png?raw=true)

开发模式：

![](https://github.com/shArpyYAo/markdown-photo/blob/master/开发模式.png?raw=true)

# Watching changes

当应用程序发生更改时，Webpack可以自动重建打包，并且它会一直监听更改。

只需加入以下代码

	"scripts": {
	  "watch": "webpack --watch"
	}
	
run

	npm run watch
	
或者

	yarn run watch
	
监视模式的一个很好的功能，只有在构建没有错误时才重新打包。如果有错误，watch将继续监听更改，并尝试重建打包，但当前的包不会受到这些有问题的构建的影响。

# Handling images

Webpack允许使用文件加载器加载图片。

简单地配置：

	module.exports = {
	  /*...*/
	  module: {
	    rules: [
	      {
	        test: /\.(png|svg|jpg|gif)$/,
	        use: [
	          'file-loader'
	        ]
	      }
	    ]
	  }
	  /*...*/
	}
	
允许你这样子导入你的图片：
	
	import Icon from './icon.png'
	
	const img = new Image()
	img.src = Icon
	element.appendChild(img)
	
file-loader能够处理其它类型的文件，比如：fonts、CSV、XML等等。

# 生成Source Maps

由于Webpack打包压缩了代码，因此必须使用Source Maps来获取引发错误的原始文件的引用。（译者：sourcemap是为了解决开发代码与实际运行代码不一致时帮助我们debug到原始开发代码的技术。）

使用devtool属性来生成Source Maps：

	module.exports = {
	  /*...*/
	  devtool: 'inline-source-map',
	  /*...*/
	}
	
devtool有许多其他的值，最常用的值如下：

* none：不加载Source Maps
* source-map：ideal for production, provides a separate source map that can be minimized, and adds a reference into the bundle, so development tools know that the source map is available. Of course you should configure the server to avoid shipping this, and just use it for debugging purposes
* inline-source-map: ideal for development, inlines the source map as a Data URL