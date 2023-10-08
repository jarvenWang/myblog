---
author: "wangjinbao"
title: "Vue的debug调试"
date: 2023-06-01
description: "Vue.js是一种流行的JavaScript前端框架，用于构建用户界面。它是一种渐进式框架，可以逐步应用到项目中，也可以与其他库或现有项目进行整合。"
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: wangjinbao
authorEmoji: 👻
tags:
- javascript
- categories:

---
## 常用方式如下三种：
#### 1.源代码中增加debugger 或 console.log
#### 2.在Chrome浏览器Source中加断点
#### 3.vscode中直接调试，对源码定位准确直观
## Vscode的Debug配置

#### 1.安装拓展插件
Debugger for Chrome（停更）
所以安的 `JavaScript Debugger`
生成模板debug配置，后面会再重新配置：
点击debug按钮 -> `Run and Debug` -> Chrome
#### 2.在配置文件中添加：devtool: 'source-map'
找到项目的配置文件，把devtool改为'source-map'配置
vue2:
```vue
module.exports = {
  dev: {
        devtool: 'source-map',
   }
}
```
vue3
```vue
module.exports = {
  chainWebpack: (config) => {
    if (isDev) {
      config.devtool('source-map')
    }
  }
}
```
也可以通过 configureWebpack: { devtool: 'source-map' }进行配置，方式多种

#### 3.配置vscode\launch.json文件
在项目根目录下配置.vscode/launch.json 文件，具体配置 vscode-chrome-debug 插件有详细描述，我的配置如下：vscode 
默认生成的 launch.json 是没有 ："webpack:///./src/*": "${webRoot}/*"，我的打断点是灰色就是这里导致的。
通过修改配置让vscode 知道 webpack 调试的文件对应项目的本地文件。问题解决。
```vue
{
  // 使用 IntelliSense 了解相关属性。 
  // 悬停以查看现有属性的描述。
  // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
  "version": "0.2.0",
  "configurations": [
    {
      "type": "chrome",
      "request": "launch",
      "name": "vuejs: chrome",
      "url": "http://localhost:8028",
      "webRoot": "${workspaceFolder}/src",
      "sourceMapPathOverrides": {
        "webpack:///src/*": "${webRoot}/*",
        "webpack:///./src/*": "${webRoot}/*"
      }
    }
  ]
}
```
#### 4.添加断点后，启动项目，然后开启debug模式
