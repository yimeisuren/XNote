```vue
<template>
  <div class="app-container">
    <!-- template和div标签是固定写法, 框架对其进行了封装 -->
    讲师列表
  </div>
</template>


<script>
// 引入对应的teacher.js文件, 其中有各种接口方法
import teacher from "@/api/edu/teacher";

export default {
  // 调用api都写在这里, 属于核心结构,页面的主要内容
  data() {
    // 这是data:{}的另一种写法,更常用

    return {
      // 定义变量及其初始值,名称可以任意定义
      list: null, //保存结果
      total: 0, //总记录数
      page: 1, //对应后端的current
      limit: 10, //对应后端的limit
      teacherQuery: {}, //对应后端的teacherQuery
    };
  },
  //   页面渲染前执行,一般调用methods中定义的方法
  created() {
    this.getList();
  },
  //   创建具体的方法,一般调用teacher.js中定义的方法
  methods: {
    /**
     * 调用pageTeacherCondition方法
     */
    getList() {
      //原始方式: 引入axios, 使用下面语句
      //   axios.post("").then().catch()

      //request.js对axios封装, teacher.js对具体实现要求进行封装
      teacher
        .pageTeacherCondition(this.page, this.limit, this.teacherQuery)
        .then((response) => {
          // 请求成功, response中携带数据
          console.log(response);
        })
        .catch((error) => {
          // 请求失败
          console.log(error);
        });
    },
  },
};
</script>


```

