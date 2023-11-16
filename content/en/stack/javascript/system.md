---
author: "wangjinbao"
title: "通用后台管理系统"
date: 2023-07-01
description: "通用后台管理系统"
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

## 初步初始化项目
### 生成依赖文件package.json
`npm init -y`
```vue
npm init -y
```
### 脚手架的安装
`cnpm i -D @vue/cli`
```vue
cnpm i -D @vue/cli
指定安装（cnpm i -D @vue/cli@4.5.15）
```
查看vue-cli版本号：
```vue
npx vue -V
```
## 项目创建
### 局部创建：
`npx vue create project-one` 或 `npx vue init webpack project-one`
```vue
npx vue create project-one
vue2
yarn
$ cd project-one
$ yarn serve
//运行项目yarn run serve

```
### 全局创建：
`npx vue ui`
界面：
```vue
npx vue ui
🚀  Starting GUI...
🌠  Ready on http://localhost:8000
```
#### 全局安装vue-cli 2.9.6 和项目创建：
+ 安装webpack： `npm i webpack -g`
+ 安装脚手架： `npm i vue-cli -g`
+ 创建项目： `vue init webpack demo`

#### 全局安装vue-cli 4.5.15 和项目创建：
+ 用npm安装脚手架 ： `npm install -g @vue/cli`
+ 用yarn安装脚手架： `yarn global add @vue/cli`
+ 创建项目： `vue create my-project` 或 `vue ui`

** ps: 修改端口
文件package.json 里修改：`--port 5173`
```vue
  "scripts": {
    "serve": "vue-cli-service serve --port 5173 --open",
    "build": "vue-cli-service build",
    "lint": "vue-cli-service lint"
  },
```

## 添加配置文件
新建vue.config.js
```vue
module.exports = {
    devServer: {
        open: true
    }
}
```

## elementUI
### 安装：
`npm i element-ui -S` 全局使用：-S

### 使用：
在main.js中引入：
```vue
import ElementUI from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'

Vue.use(ElementUI)
```

## 安装sass
官网：https://sass.hk/
指定版本： `cnpm i sass-loader@10 node-sass@6 -S`
或 `cnpm i sass-loader node-sass -S`

以下是部分版本号对应，具体可百度

|  sass-loader   |  node-sass   |
|-----|-----|
|   4.1.1  |  4.3.0   |
|   7.0.3  |  4.7.2   |
|   7.3.1  |  4.7.2   |
|   7.3.1  |   4.14.1  |
|   10.0.1  |   6.0.1  |



```vue
cnpm i sass-loader node-sass -S
...
dependencies:
+ sass-loader ^13.3.2
+ node-sass ^9.0.0
```
使用：
样式嵌套
```vue
<style lang="scss">
样式嵌套
</style>
```

>*** 报错：Syntax Error: TypeError: this.getOptions is not a function
>解决方法：
>这个报错是类型错误，this.getOptions 不是一个函数 。这个错误一般就是less-loader库里的错误。
>主要是less-loader版本太高，不兼容this.getOptions方法。
>1. 删除目录 node_modules
>2. 去年package.json中sass和sass-loader
    找到package.json文件中的“less”和“less-loader”然后删除这两行
    或 找到package.json文件中的“sass”和“sass-loader”然后删除这两行

>*** node-sass安装失败报错：
>```vue
>✖ Install fail! Error: run postinstall error, please remove node_modules before retry!
>Command failed with exit code 1: node scripts/build.js
>```
>解决方法：
>node版本太高，对应的node-sass的版本过低，对应如下表：

| NodeJS | Supported node-sass version | Node Module |
|--------|-----------------------------|-------------|
| Node17 | 7.0+                        | 102         |
| Node16 | 6.0+                        | 93          |
| Node15 | 5.0+,<7.0                   | 88          |
| Node14 | 4.14+                       | 83          |
| Node13 | 4.13+,<5.0                  | 79          |
| Node12 | 4.12+                       | 72          |
| Node11 | 4.10,<5.0                   | 67          |
| Node10 | 4.9+,<6.0                   | 64          |
| Node8  | 4.5.3,<5.0                  | 57          |
| Node<8 | <5.0                        | <57         |

## 安装less
`cnpm i less less-loader -S`

## 安装图标font-awesome
官网地址：https://fontawesome.com.cn/
`cnpm i -D font-awesome`
+ 使用：
main.js内容：
```vue
import 'font-awesome/css/font-awesome.min.css'

<i class="fa fa-bluetooth-b fa-2x fa-border"></i>
```

## 常用正则插件
vscode里搜索 `any-rule`

## 安装axios
`cnpm i axios -S`
使用：
在main.js中：
```vue
import axios from 'axios'

Vue.prototype.axios = axios //挂载到原型，可以在全局使用
```

## 安装router
`cnpm i vue-router@3.5.3 -S`
使用：
1. 步骤一：新建router/index.js
```vue
import Vue from "vue";
import Router from 'vue-router'
import Home from '../components/Home.vue'

Vue.use(Router)

export default new Router({
    routes: [
        {
            path: '/home',
            //component: () => import('@/components/Home') //路由懒加载
            //component: resolve => require(['@/components/Home'],resolve) //异步加载
            component: Home
        }
    ],
    mode: 'history'
})
```
2. 步骤二：main.js中加载
main.js中的内容：
```vue
import router from './router'

new Vue({
router,
render: h => h(App),
}).$mount('#app')
```
3. 步骤三：router-view引用
```vue

<router-view></router-view>
```

## mock模拟数据
使用Apifox 的 "高级Mock" 功能 定义接口及反回值

## axios二次封装
### 步骤一：新建service.js
src/service.js内容如下：
```vue
import axios from "axios";
import { getToken } from "./utils/setToken";
import { Message } from "element-ui";


const service = axios.create({
baseURL: '/api',    //baseURL会自动加在请求地址上
timeout: 3000
})

//添加请求拦截器
service.interceptors.request.use((config) => {
//在请求之前做些什么（获取并设置token）
config.headers['token'] = getToken('token')
return config
}, (error) => {
//捕捉异常
return Promise.reject(error)
})

//添加响应拦截器
service.interceptors.response.use((response) => {
//对响应数据做些什么 （状态码提示）
let { status, message } = response.data
if (status !== 200) {
Message({ message: message || 'error', type: 'warning' })
}
return response  //注意return回去
}, (error) => {
//捕捉异常
return Promise.reject(error)
})

export default service
```
### 步骤二：vue.config.js配置代理
src/vue.config.js配置代理内容如下：
```vue
module.exports = {
    devServer: {
        open: true,
        proxy: {
            '/api': {
                target: 'http://i.system_admin.com/api',
                changeOrigin: true,  //允许跨域
                pathRewrite: {
                    '^/api': ''
                }
            }
        }
    },
    configureWebpack: {
        devtool: 'source-map'
    }
}
```
### 步骤三：main.js中全局挂载service
src/main.js修改内容如下：
```vue
import service from './service'
...
Vue.prototype.service = service //挂载到原型，可以在全局使用
```
### 步骤四：this.service.post直接使用
```vue
this.service.post("/login", this.form)
            .then((res) => {
              console.log(res.data);
              if (res.data.status == 200) {
                removeToken("username");
                setToken("username", res.data.data);
                setToken("token", res.data.data.token);
                this.$message({ message: res.data.message, type: "success" });
                this.$router.push("/home");
              } else {
                this.$message.error("登录失败");
              }
            })
```

> PS:报错：Proxy error: Could not proxy request /m1/3477035-0-default/api/login from 127.0.0.1:5173 to http://dev.kg_wbgl.com:4523.
> 解决方法:
> 原因：容器网络与本地postman接口mock请求不通过，解决：开一个后台接口容器即可

> PS:报错：[Vue warn]: Error in v-on handler: "TypeError: Cannot read properties of undefined (reading 'post')"
> 解决方法:
> 在把service.js中的变量 暴露出去 `export default service`

## body样式调整
在App.vue中添加：
```vue
body {
  margin: 0;
  padding: 0;
}
```


## 配置路由
1. 步骤一：router/indexjs添加
2. 步骤二：数据挂载
```vue
created() {
    this.menus = [...this.$router.options.routes];
},
```
3. 步骤三：开启路由
```vue
<el-menu router ...> //菜单开启

```
4. 步骤四：添加router-view
Home.vue中添加：
```vue
 <router-view></router-view>
```

## 面包屑
1. 步骤一：制作面包屑组件
新建 BreadCrumb.vue:
```vue
<el-card>
<el-breadcrumb separator-class="el-icon-arrow-right">
  <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
  <el-breadcrumb-item>活动管理</el-breadcrumb-item>
  <el-breadcrumb-item>活动列表</el-breadcrumb-item>
  <el-breadcrumb-item>活动详情</el-breadcrumb-item>
</el-breadcrumb>
</el-card>
```
2. 步骤二：主页引入面包屑
```vue
import BreadCrumb from "./common/BreadCrumb.vue";
<BreadCrumb />
```
3. 步骤三：$route.matched循环
```vue
<el-breadcrumb-item v-for="(item, index) in $route.matched" :key="index">{{ item.name }}
</el-breadcrumb-item>
```

## 数据列表
1. 步骤一:引入el-table
```vue
<template>
  <el-table
    :data="tableData"
    stripe
    style="width: 100%">
    <el-table-column
      prop="date"
      label="日期"
      width="180">
    </el-table-column>
    <el-table-column
      prop="name"
      label="姓名"
      width="180">
    </el-table-column>
    <el-table-column
      prop="address"
      label="地址">
    </el-table-column>
  </el-table>
</template>

<script>
  export default {
    data() {
      return {
        tableData: [{
          date: '2016-05-02',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1518 弄'
        }, {
          date: '2016-05-04',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1517 弄'
        }, {
          date: '2016-05-01',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1519 弄'
        }, {
          date: '2016-05-03',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1516 弄'
        }]
      }
    }
  }
</script>
```
2. 步骤二：封装接口
```vue
export function student(params) {
    return service({
        method: 'get',
        url: '/student',
        params
    })
}
```
3. 步骤三：请求处理数据
```vue
<script>
import { student } from "@/api/api";
export default {
  data() {
    return {
      tableData: [],
    };
  },
  created() {
    this.getData();
  },
  methods: {
    getData(params) {
      student(params).then((res) => {
        console.log(res);
        if (res.data.status === 200) {
          this.tableData = res.data.data;
          this.tableData.forEach((item) => {
            //尽量不要修改原数据
            item.sex === 1 ? (item.sex_text = "男") : (item.sex_text = "女");
            item.state === "1"
              ? (item.state_text = "已入学")
              : item.state === "2"
              ? (item.state_text = "未入学")
              : (item.state_text = "休学中");
          });
        }

      });
    },
  },
};
</script>
```

**PS报错： error  The template root requires exactly one element
Vue只允许模板里存在一个根节点。在 `<template>` 中添加一个 `<div>`标签，之后所有的组件全部加在 `<div> `里即可解决。

## 分页
1. 步骤一：复制分页样式
```vue
<div class="block">
    <span class="demonstration">完整功能</span>
    <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="currentPage4"
      :page-sizes="[100, 200, 300, 400]"
      :page-size="100"
      layout="total, sizes, prev, pager, next, jumper"
      :total="400">
    </el-pagination>
  </div>
```
2. 步骤二：修改currentPage、pageSize、total等变量和方法
```vue
:current-page="currentPage"
:page-size="pageSize"
:total="total"
```
3. 步骤三：计算computed每页数据
```vue
computed: {
    compData() {
      return this.tableData.slice(
        (this.currentPage - 1) * this.pageSize,
        this.currentPage * this.pageSize
      );
    },
  },
```
4. 步骤四：handleSizeChange和handleCurrentChange修改变量
```vue
    handleSizeChange(val) {
      this.pageSize = val;
      this.currentPage = 1;
      console.log(`每页 ${val} 条`);
    },
    handleCurrentChange(val) {
      this.currentPage = val;
      console.log(`当前页: ${val}`);
    },
```

## 新增/编辑form提交
1. 步骤一：验证form
提交方法 sure('form') 传参数
```vue
<el-button type="primary" @click="sure('form')">确 定</el-button>
```
2. 步骤二：请求添加/编辑组合接口
```vue
 sure(form) {
this.$refs[form].validate((valid) => {
    if (valid) {
    //增加
        if (this.status) {
            info(this.form).then((res) => {
            console.log("新增");
                if (res.data.status === 200) {
                    this.getData();
                    this.$message({ type: "success", message: res.data.message });
                    this.dialogFormVisible = false;
                    this.$refs["form"].resetFields();
                }
            });
        } else {
            updateInfo(this.form).then((res) => {
            console.log("更新");
                if (res.data.status === 200) {
                    this.getData();
                    this.$message({ type: "success", message: res.data.message });
                    this.dialogFormVisible = false;
                    this.$refs["form"].resetFields();
                }
            });
        }
    }
});
},
```

## 清空之前的form表单避免验证
1. 方法一：@close="close" 和 destroy-on-close	
`close` Dialog关闭的回调--清空表单
`destroy-on-close`	关闭时销毁 Dialog 中的元素	boolean	—	false
```vue
<template>
  <el-dialog :destroy-on-close="true" @close="close">
  </el-dialog>
</template>
<script>
close() {
  this.form = {
    name: "",
    sex: "1",
    age: "",
    father: "",
    mather: "",
    address: "",
    time: "",
    phone: "",
  };
},
</script>
```

>PS:报错：[Vue warn]: Invalid prop: type check failed for prop “xxx“.Expected Boolean, got String with value
> 解决方案：
> Element UI官方文档中，只需要在 `destroy-on-close` 前面加冒号 `:` 就能解决标红。

2. 方法二：
>在el-form标签中添加  :validate-on-rule-change="false"   属性
>使用  this.$refs[formName].clearValidate(); 
```vue
<el-form :model="form" :rules="rules" ref="form" :validate-on-rule-change="false">
</el-form>

<script>
addStudent() {
  this.form = {
    name: "",
    sex: "1",
    age: "",
    father: "",
    mather: "",
    address: "",
    time: "",
    phone: "",
  };

  if (this.$refs["form"]) {
    this.$nextTick(() => {
      this.$refs["form"].clearValidate();
    });
  }

}
</script>
```

## 避免编辑双向绑定
`{ ...row }`
```vue
 edit(row) {
      this.dialogFormVisible = true;
      console.log(row);
      this.form = { ...row };
      this.status = false;
    },
```

## 删除确定弹窗
this.$confirm().then().catch()
```vue
<script>
del(row) {
  // console.log(row);
  this.$confirm("确定要删除？", "提示", {
    confirmButtonText: "确定",
    cancelButtonText: "取消",
    type: "warning",
    // showCancelButton: false, //是否显示取消按钮
    // showClose: false, //是否显示右上角的x
    closeOnClickModal: true, //是否可以点击空白处关闭弹窗
  })
      .then(() => {
        delInfo(row.id).then((res) => {
          if (res.data.status === 200) {
            this.getData();
            this.$message({ message: res.data.message, type: "success" });
          }
        });
      })
      .catch(() => {
        this.$message({ message: "已取消删除", type: "info" });
      });
},
</script>
```

## 表格数据获取封装
在utils/table.js中内容：
```vue
<script>
//作业列表获取表格数据
export function getTableData(root, url, params, arr) {
  root.service.get(url, { params: params || {} }).then((res) => {
    if (res.data.status === 200) {
      root.tableData = res.data.data
      root.total = res.data.total
      root.tableData.map(item => {
        arr.map(aItem => {
          item[aItem] ? item[aItem + '_text'] = '是' : item[aItem + '_text'] = '否'
        })
      })
    }
  }).catch((err) => {
    throw err
  })
}
</script>
```

## 分页组件的封装和使用
1. 步骤一：封装
新增 /components/common/Pageing.vue:
```vue
<template>
  <div>
    <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="page"
        :page-sizes="[5, 10, 15, 20]"
        :page-size="size"
        layout="total, sizes, prev, pager, next, jumper"
        :total="total"
        :url="url"
    >
    </el-pagination>
  </div>
</template>
<script>
import { getTableData } from "@/utils/table";
export default {
  props: {
    total: Number,
    url: String,
  },
  data() {
    return {
      page: 1,
      size: 5,
    };
  },
  created() {
    getTableData(this.$parent, "/works", { page: this.page, size: this.size }, [
      "completed",
    ]);
  },
  methods: {
    handleSizeChange(val) {
      this.size = val;
      this.page = 1;
      getTableData(this.$parent, "/works", { page: this.page, size: val }, [
        "completed",
      ]);
      // console.log(`每页 ${val} 条`);
    },
    handleCurrentChange(val) {
      this.page = val;
      getTableData(this.$parent, "/works", { page: this.page, size: val }, [
        "completed",
      ]);
      // console.log(`当前页: ${val}`);
    },
  },
};
</script>
```
2. 步骤二：定义传参
```vue
<template>
  :total="total"
  :url="url"
</template>
<script>
props: {
  total: Number,
      url: String,
}
</script>
```
3. 步骤三：使用
```vue
1.引入组件：
import Page from "@/components/common/Pageing.vue";
2.注册组件：
components: {
Page,
},
3.使用组件：
<Page :total="total" :ur="url" />
```

## echarts 使用
### 安装
`cnpm i -D echarts@4`
### 挂载使用
在main.js中：
```vue
import echarts from 'echarts'
Vue.prototype.$echarts = echarts
```
之后全局中就可以使用 `this.$echarts` 了

### 图形渲染
```vue
<template>
  <div class="data-view">
    <el-card>
      <div id="main1"></div>
    </el-card>
    <el-card>
      <div id="main2"></div>
    </el-card>
  </div>
</template>

<script>
import { dataview } from "@/api/api";
export default {
  data() {
    return {};
  },
  created() {
    dataview()
      .then((res) => {
        console.log(res);
        if (res.data.status === 200) {
          let { legend, xAxis, series } = res.data.data;
          this.draw(legend, xAxis, series);
        }
      })
      .catch((err) => {
        throw err;
      });
  },
  mounted() {
    let myChart = this.$echarts.init(document.getElementById("main1"));
    myChart.setOption({
      title: {
        text: "大佬进阶班",
      },
      tooltip: {},
      xAxis: {
        data: ["一班", "二班", "三班", "四班", "五班", "六班"],
      },
      yAxis: {},
      series: [
        {
          name: "人数",
          type: "line",
          data: [32, 22, 11, 66, 65, 89],
        },
      ],
    });
  },
  methods: {
    draw(legend, xAxis, series) {
      let myChart1 = this.$echarts.init(document.getElementById("main2"));
      let option = {
        title: {
          text: "会话量",
        },
        tooltip: {
          trigger: "axis",
        },
        legend: {
          data: legend,
        },
        xAxis: {
          type: "category",
          data: xAxis,
        },
        yAxis: {
          type: "value",
        },
        series: series,
      };
      myChart1.setOption(option);
    },
  },
};
</script>
```
## 树形控件
```vue
<template>
  <div class="userList">
    <el-tree :data="menus" show-checkbox node-key="id" :props="defaultProps">
    </el-tree>
  </div>
</template>
<script>
export default {
  data() {
    return {
      menus: [],
      defaultProps: {
        children: "children",
        label: "name",
      },
    };
  },
  created() {
    this.menus = [...this.$router.options.routes];
    console.log(this.$router.options.routes);
  },
};
</script>
```

## 权限管理和动态路由
>思路：
>1.根据不同的用户登录上来，返回对应的路由权限菜单
>2.通过树形控件达到权限的精准控制，根据不同角色，勾选不同的菜单权限，将菜单数据提交给后端进行保存
>3.后端保存之后，在用户进行登录的时候就会查询该用户或该角色所拥有的菜单数据，最终进行动态的渲染展示
>4.动态添加路由使用 `router.addRoutes` (vue-router3.x版本方法，已废弃)方法，后续使用 `router.addRoute` 进行动态路由添加

### 权限节点获取
element组件tree的方法：`getCheckedNodes`
说明：若节点可被选择（即 show-checkbox 为 true），则返回目前被选中的节点所组成的数组
(leafOnly, includeHalfChecked) 接收两个 boolean 类型的参数，1. 是否只是叶子节点，默认值为 false 2. 是否包含半选节点，默认值为 false

实现：
1. 步骤一：el-tree组件中添加ref="tree"
```vue
<el-tree :data="menus" show-checkbox node-key="id" :props="defaultProps"
      ref="tree">
</el-tree>
```
2. 步骤二：绑定获取节点方法
```vue
<el-button @click="getCheckedNodesFunc">通过node获取</el-button>
```
3. 步骤三：this.$refs.tree.getCheckedNodes()
```vue
<script>
methods: {
  getCheckedNodesFunc() {
    let arr = this.$refs.tree.getCheckedNodes();
    console.log(arr);
  },
},
</script>
```

## 路由导航守卫
在main.js中：
```vue
import { getToken } from "@/utils/setToken.js";

//路由导航守卫
router.beforeEach((to, from, next) => {
    if (!getToken("username")) {
        if (to.path !== '/login') {
            console.log("from.path == ", from.path)
            console.log("to.path !== /login")
            next('/login')
        } else next()
    } next()
})

new Vue({
router,
render: h => h(App),
}).$mount('#app')
```
>ps:如果报错 Error: Navigation cancelled from “/...“ to “/...“ with a new navigation
>在src/router/index.js内添加：
```vue
import Vue from "vue";
import Router from 'vue-router'

Vue.use(Router)

//解决编程式路由往同一地址跳转时会报错的情况
const originalPush = Router.prototype.push;
const originalReplace = Router.prototype.replace;
//push
Router.prototype.push = function push(location, onResolve, onReject) {
if (onResolve || onReject)
return originalPush.call(this, location, onResolve, onReject);
return originalPush.call(this, location).catch(err => err);
};
//replace
Router.prototype.replace = function push(location, onResolve, onReject) {
if (onResolve || onReject)
return originalReplace.call(this, location, onResolve, onReject);
return originalReplace.call(this, location).catch(err => err);
};

export default new Router({...})
```