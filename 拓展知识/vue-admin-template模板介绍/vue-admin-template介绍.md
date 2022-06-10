# vue-admin-template模板介绍

## vue.config.js

配置文件, 修改访问端口地址

## src目录

### ==api目录==

自定义方法

### asset目录

静态资源

### components目录

存放组件

### icons目录

存放图标

### ==router目录==

路由

### style目录

样式文件

### utils目录

### request文件

### ==views目录==

项目中的具体页面





@RestController表示交给Spring管理, 并返回数据

# 操作流程

1. 修改vue.config.js中的端口号为8001







# 添加路由

1. 在router/index.js中添加
2. title属性决定页面的标题
3. component表示点击路由跳转到指定页面
4. 

# 讲师列表的前端实现

路由-->页面

## 添加路由

1. 在`router/index.js`中模仿样例改造一级路由`讲师管理`和二级路由

2. 创建路由对应的页面并修改`router/index.js`中对应的路由映射路径

   1. 讲师列表`views/edu/teacher/list.vue`
   2. 添加讲师`views/edu/teacher/save.vue`

3. `.vue`页面的基本写法

   ```vue
   <template>
     <div class="app-container">
       <!-- template和div标签是固定写法, 框架对其进行了封装 -->
       添加讲师
     </div>
   </template>
   ```

4. 在`api`文件夹中创建`teacher.js`, 在其中定义接口的访问地址

   ```js
   // 引入utils/request, request中封装了axios
   import request from '@/utils/request'
   
   export function getList(params) {
     return request({
       url: '/vue-admin-template/table/list',
       method: 'get',
       params
     })
   }
   ```

   

5. 