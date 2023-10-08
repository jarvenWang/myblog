---
author: "wangjinbao"
title: "vue3简单例子"
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
## 例子1
### 自定义属性、事件
src/App.vue
```vue
<script setup>
import Product from "./components/Product.vue";
import { ref } from "vue";

let shopCar = ref([
  {
    id: 89,
    title: "ROG幻16 2023 第13代英特尔酷睿i9 16英寸 星云屏",
    subtitle:
      "设计师轻薄高性能游戏本笔记本电脑(i9-13900H 16G 1T RTX4060 2.5K 240HzP3广色域)灰",
    image:
      "https://img10.360buyimg.com/n1/jfs/t1/191391/18/41888/113687/64e5711cF1c35de3a/d15c1c04d114b810.jpg",
    price: 1000,
    count: 1,
    selected: false,
  },
  {
    id: 102,
    title: "机械革命无界14Pro",
    subtitle:
      "(R7-7840HS 16G 1T 120Hz 2.8K 高色域)轻薄本办公商务本游戏本笔记本电脑",
    image:
      "https://img11.360buyimg.com/n7/jfs/t1/88945/40/31886/150051/64f68461Ffa336065/b4b16f0597a24d21.jpg",
    price: 2000,
    count: 1,
    selected: false,
  },
  {
    id: 108,
    title: "联想（Lenovo）拯救者Y7000P",
    subtitle:
      "13代酷睿i7 2023游戏笔记本电脑 16英寸(13代i7-13620H 16G 1T RTX4050 2.5K 165Hz高色域屏)灰",
    image:
      "https://img12.360buyimg.com/n1/s450x450_jfs/t1/180461/29/38082/115800/650188f9Fab57feb5/dba4651979f21c8a.jpg",
    price: 3000,
    count: 1,
    selected: true,
  },
]);

//产品的状态发生改变
function changeShopCarProductChecked(checked, id) {
  console.log(checked);
  console.log(id);
  shopCar.value.some((product) => {
    if (id === product.id) {
      console.log(product);
      product.selected = checked;
      return true;
    }
  });
}
</script>
<template>
  <Product
    v-for="product in shopCar"
    :key="product.id"
    :id="product.id"
    :pictrue="product.image"
    :title="product.title"
    :subtitle="product.subtitle"
    :price="product.price"
    :count="product.count"
    :is-checked="product.selected"
    @change-product-checked="changeShopCarProductChecked"
  />
</template>
<style>
body {
  margin: 0;
  padding: 0;
}
* {
  margin: 0;
  padding: 0;
}
</style>

```

components/Product.vue
```vue
<script setup>
//自定义属性
let propsData = defineProps({
  id: { type: Number, required: true },
  isChecked: Boolean, //是否被选中
  pictrue: { type: String, required: true },
  title: { type: String, required: true },
  subtitle: { type: String, required: true },
  price: { type: Number, default: 0 },
  count: { type: Number, default: 0 },
});

//自定义事件
let emits = defineEmits([
  "changeProductChecked", //改变复选框的状态事件
]);

//改变产品选中的状态
function changeCheckedState(e) {
  //   console.log(e.target.checked);
  let newCheckedState = e.target.checked;
  emits("changeProductChecked", newCheckedState, propsData.id);
}
</script>

<template>
  <!-- 产品容器 -->
  <div class="box">
    <!-- 选项框 -->
    <input
      type="checkbox"
      class="p_checkbox"
      :checked="isChecked"
      @change="changeCheckedState"
    />
    <!-- 产品图片 -->
    <img class="p_image" :src="pictrue" />
    <!-- 产品内容 -->
    <div class="p_content">
      <h3>{{ title }}</h3>
      <span class="p_title">{{ subtitle }}</span>
      <h2 style="color: red">￥ {{ price }}</h2>
      <div class="p_area">
        <button>-</button>
        <span>{{ count }}</span>
        <button>+</button>
      </div>
    </div>
  </div>
</template>

<style>
#app {
  width: 100%;
}
.box {
  box-shadow: 0 0 8px gray;
  /* width: 100%; */
  padding: 20px;
  margin: 15px;
  display: flex;
  align-items: center;
  position: relative;
}
.p_checkbox {
  width: 25px;
  height: 25px;
  margin-right: 20px;
}
.p_image {
  width: 120px;
  height: 120px;
  margin: 0 auto;
}
.p_content {
  align-self: start;
}
.p_title {
  margin: 10px 10px;
}
.p_area {
  position: absolute;
  bottom: 5px;
  right: 5px;
}
.p_area button {
  width: 20px;
  height: 20px;
}
</style>

```


