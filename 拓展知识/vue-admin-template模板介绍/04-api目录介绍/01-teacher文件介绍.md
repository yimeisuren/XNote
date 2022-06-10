```js
/**
 * 引入utils/request, request中封装了axios
 * 
 * request就相当于一个方法
 */
import request from '@/utils/request'


// es6模块化写法, 可以避免每个函数前面都要写export function
export default {

    /**
     * 按条件分页查询讲师列表(后端三参数)
     * @param {int} current 当前页
     * @param {int} limit 每页最多显示个数
     * @param {JSON} teacherQuery 查询条件
     * @returns 查询教师列表
     */
    pageTeacherCondition(current, limit, teacherQuery) {

        // 字符串拼接,需要和后端注解中的d
        let controllerUrl = "/edu/teacher"
        let functionName = "pageTeacherCondition"
        let url = `${controllerUrl}/${functionName}/${current}/${limit}`
        
        // 该方法的请求方式为post 
        let method = "post"

        // 构造对象
        let object = {
            url: url,
            method: method,
            /**
             * 后端使用RequestBody时, 需要使用data来获取数据
             * 
             * RequestBody表示通过json格式来传递数据
             * 
             * 使用data: teacherQuery表示将teacherQuery封装成json传递到接口里面去, 之后都采用这种写法
             */
            data: teacherQuery
        }

        // 调用request方法, 传入参数
        return request(object)
    }
}

```

