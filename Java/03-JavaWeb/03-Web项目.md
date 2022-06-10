项目名称为`content root`

创建一个空目录`WEB-INF`即可



## 客户端-服务器-数据库交互流程

1. 客户端发送url, 请求页面`add.html`
2. 服务器端有一个组件`addServlet`来接收请求中包含的数据
3. 服务器端的`addServlet`组件去调用`addDAOImpl`进行数据库操作
4. `addDAOImpl`执行完sql语句后返回数据给`addServlet`组件
5. `addServlet`返回数据给客户端

## Servlet的映射关系

通过配置文件`web.xml`中的`<servlet></servlet>`标签和`<servlet-mapping></servlet-mapping>`

```xml
<servlet>
    <servlet-name>servlet名称</servlet-name>
    <servlet-class>servletJava全类名</servlet-class>
</servlet>

<servlet-mapping>
	<servlet-name>servlet名称</servlet-name>
    <url-pattern>url对应路径</url-pattern>
</servlet-mapping>
```

发送post请求, 则会执行`XXXServlet`类中的`doPost()`方法

还可以通过注解来实现url到Servlet的映射

`@WebServlet(name = "helloServlet", value = "/hello-servlet")`



`doPost()`方式需要设置编码, 否则会中文乱码