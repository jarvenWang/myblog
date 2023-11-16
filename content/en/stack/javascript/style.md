---
author: "wangjinbao"
title: "样式技巧"
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
## 常用样式
+ ### vue中默认样式位置
```vue
src/assets/main.css
src/assets/base.css
如不需要可以注释
```

+ ### 清除所有的外边距和内边距
```vue
*{
  margin:0;
  padding:0;
}
```
+ ### 并排排列
```vue
.box{
  display:flex;
}
```
+ ### 垂直排部
```vue
.box{
  align-items:center;
}
```
+ ### div 竖 排列
设置div的父元素的display为flex，再设置flex-direction为column，即可将div竖排列
```vue
<style>
.container {
  display: flex;
  flex-direction: column;
}
<br>
 .box {
   width: 200px;
   height: 100px;
   margin-bottom: 10px;
   background-color: #f1f1f1;
 }
</style>
<br>
<div class="container">
<div class="box"></div>
<div class="box"></div>
<div class="box"></div>
</div>
```
+ ### 自已顶端对齐
```vue
.box{
  align-self:start;
}
```
+ ### 左右对称对齐
```vue
.container{
justify-content: space-between;
}
```
+ ### div中flex文字左右上下居中
```vue
.container{
justify-content: center;
align-items: center;
}
```

+ ### div圆角
```vue
border-radius:8px;
```
+ ### div阴影
```vue
box-shadow:0 0 5px red;
```

+ ### vscode中添加自动样式
```vue
插件搜索 AutoScssStruct4Vue
```

+ ### 走马灯
```vue
<!-- 轮播图 -->
<template>
    <div class="block text-center">
      <el-carousel height="500px">
        <el-carousel-item
          v-for="item in 4"
          :key="item"
          style="position: absolute; top: 0px; z-index: 9"
        >
          <img
            src="https://m.360buyimg.com/babel/jfs/t20260925/7569/37/22520/40263/65122aa8F7db4b1cc/4690718474f0e4b7.jpg"
          />
        </el-carousel-item>
      </el-carousel>
    </div>
</template>
<style lang="scss">
.c-left {
  z-index: 8;
  position: relative;
  width: 1480px;
  height: 500px;
  background: #000;
  margin: auto;
  .block {
    img {
      width: 100%;
      height: 500px;
    }
  }
  .text-center {
  }

  h3 {
  }
  img {
    width: 100%;
    height: 500px;
  }
}
.demonstration {
  color: var(--el-text-color-secondary);
}

.el-carousel__item h3 {
  color: #475669;
  opacity: 0.75;
  line-height: 150px;
  margin: 0;
  text-align: center;
}

.el-carousel__item:nth-child(2n) {
  background-color: #99a9bf;
}

.el-carousel__item:nth-child(2n + 1) {
  background-color: #d3dce6;
}
</style>
```

+ ### 多div层叠
```vue
父类div
position: relative;
子类div1
position: absolute; top: 0px; z-index: 10;
子类div2
position: absolute; top: 0px; z-index: 9;
```
+ ### div居中最简单方法
```vue
<template>
  <div class="pro-container">
    <div class="pro-middle"></div>
  </div>
</template>
<style lang="scss">
.pro-container {
  padding: 2vmin;
  display: flex;
  .pro-middle {
    width: 1480px;
    height: 500px;
    background: rgb(213, 216, 248);
    margin: auto;
  }
}
</style>
```
+ ### 图片懒加载
自定义命令：v-img-lazy
```vue
v-img-lazy
```

+ ### ul li横向排列
```vue
<style>
ul {
  list-style: none;
}
li {
  display: inline-block;
  margin-right: 20px;
}
</style>
```

+ ### div鼠标悬停阴影
```vue
.div {
  width: 240px;
  height: 240px;
  /* 设置相对移动 */
  position: relative;
}
.div:hover {
  /* 移动 */
  top: -10px;
  left: -10px;
  /* 图片变大 */
  transform: scale(1.1);
  /* 阴影 */
  box-shadow: 10px 5px 5px #888888;
  /* 鼠标变化 */
  cursor: pointer;
}
```

+ ### 动态 div鼠标悬停阴影
```vue
<style>
.goods-item-img {
  width: 240px;
  height: 240px;
  position: relative;
  box-shadow: 0 8px 12px rgba(0, 0, 0, 0.5);
  transition: all 0.6s cubic-bezier(0.165, 0.84, 0.44, 1);
  transform: scale(1);
}
.goods-item-img::after {
  border-radius: 5px;
  position: absolute;
  z-index: -1;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  box-shadow: 0 16px 24px rgba(0, 0, 0, 0.7);
  opacity: 0;
  transition: all 0.3s cubic-bezier(0.165, 0.94, 0.94, 1);
}
.goods-item-img:hover {
  transform: scale(1.05, 1.05);
}
.goods-item-img:hover::after {
  opacity: 1;
}
</style>
```
+ ### 背景图片充满div
```vue
<style>
background: url("../assets/bg3.webp") center no-repeat;
background-attachment: fixed;
background-size: cover;
</style>
```

+ ### element插件中的label样式
```vue
<el-form-item label="用户名" prop="username">
</el-form-item>

<style>
.el-form-item__label {
  color: white;
}
</style>
```

+ ### element插件中的左对齐
```vue
<style>
.demo-form-inline {
  text-align: left;
}
</style>
```