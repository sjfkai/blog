---
title: 构建 React 组件并发布到 NPM
date: 2019-03-13 19:14:41
tags: 
  - 前端
  - react
  - webpack
  - npm
---

平时我们开发前端项目，大多都是直接构建一个可以访问的前端项目。如果我们需要开发一个 `React 组件`供别人引用，应该怎样做呢？

本文将为你讲解如何使用 `webpack` 打包一个 `React 组件`并上传到 `npm`

你也可以直接 clone [最终代码](https://github.com/sjfkai/build-react-component)，在其基础上完成自己的组件。

## 初始化项目

```bash
mkdir your-component \
&& cd your-component \
&& npm init -y
```

安装依赖的模块。

```bash
npm i --save-dev \
webpack \
webpack-cli \
webpack-dev-server \
clean-webpack-plugin \
html-webpack-plugin \
style-loader \
css-loader \
babel-loader \
@babel/core \
@babel/preset-env @babel/preset-react \
react \
react-dom 
```

先编写一个临时的组件

`./src/index.js`

```js
import React from 'react'
import './style.css'

export default class YourComponent extends React.Component {
  render() {
    return <div className="red">YourComponent</div>
  }
}
```

`./src/style.css`

```css
.red {
  color: red;
}
```

## 配置开发环境

虽然说，我们最终构建的是一个供引用的模块，但是开发过程中，查看实时效果也是很有必要的。我们这里增加一个引用 `YourComponent` 的 `demo`。并使用 `webpack-dev-server` 实现动态重载。

新建文件 `./demo/app.js`

```js
import React from 'react'
import ReactDOM from 'react-dom'
import YourComponent from '../src/index'

const root = document.createElement('div');
window.document.body.appendChild(root);
ReactDOM.render(
  <YourComponent/>,
  root
)
```

webpack配置 `webpack.dev.js`

```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const CleanWebpackPlugin = require('clean-webpack-plugin');

module.exports = {
  mode: "development",
  entry: './demo/app.js',
  output: {
    path: path.resolve(__dirname, 'demo_dist'),
    filename: 'demo.js',
  },
  devtool: 'inline-source-map',
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      },
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      }
    ]
  },
  plugins: [
    new CleanWebpackPlugin(),
    new HtmlWebpackPlugin({
      title: 'your component',
    })
  ]
};
```

增加 `.babelrc`

```json
{
  "presets": ["@babel/preset-env", "@babel/preset-react"]
}
```

`package.json` 中增加 `scripts`:

    "start": "webpack-dev-server --config webpack.dev.js"


运行 `npm start`

访问 [localhost:8080](http://localhost:8080) 查看效果


## 正式构建

组件开发完成后，我们就可以把组件打包成可供他人 `import` 的格式了。

新增 webpack 配置 `webpack.prod.js`

```js
const path = require('path');
const CleanWebpackPlugin = require('clean-webpack-plugin');

module.exports = {
  mode: "production",
  entry: './src/index.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'YourComponent.js',
    libraryTarget: 'commonjs2',
  },
  externals: {
    react: 'react', // 避免打包react源码
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader"
        }
      },
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      }
    ]
  },
  plugins: [
    new CleanWebpackPlugin(),
  ]
};
```

你可能会发现，这里的配置与 `webpack.dev.js` 几乎是一样的，你可以使用 [webpack-merge](https://www.npmjs.com/package/webpack-merge) 来减少耦合。

`package.json` 中增加 `scripts`:

    "build": "webpack --config webpack.prod.js"

运行 `npm run build`

打包后的文件为 `./dist/YourComponent.js`

## 上传到 NPM

上传到 npm 之前需要修改 `package.json` 中的内容。

增加 `peerDependencies` (与 `webpack.prod.js` 的 externals 相对应):

```json
"peerDependencies": {
    "react": "16.x"
}
```

修改`main` 为 `dist/YourComponent.js`

其他内容请根据自己的项目修改，可以参考[package.json的文档](https://docs.npmjs.com/files/package.json.html)

然后记得增加 `README.md` 文件来介绍你的组件

最后上传到 npm （没有注册的话需要先[注册](https://www.npmjs.com/signup)）

```bash
npm login  # 填写账号密码

npm publish
```

发布成功后，就可以去`npm`查找自己刚刚上传的组件了。


## 源码

[https://github.com/sjfkai/build-react-component](https://github.com/sjfkai/build-react-component)
