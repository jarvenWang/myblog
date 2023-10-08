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