## 请求

请求行General

请求头Request Headers: 客户端告诉服务器

请求体Payload/Form Data, 因为Content-Type不同

get方式没有请求体, 但是有一个queryString, 添加在url后面

post方式是Form Data

json格式是Request Payload

## 会话Session

Http无状态: 服务器无法判断两次请求是否是同一个客户端发送过来的

通过会话跟踪技术来解决http无状态的问题

使用setAttribute来保存数据到session作用域中, 可能需要执行重定向或内部转发的操作, 此时再从中作用域中将数据提取出来. 在某个页面中加载java内存中的数据称为**页面的渲染**. thymeleaf就是用来实现页面渲染的技术

## 服务器内部转发和客户端重定向

服务器内部转发: 服务器去联系服务器内的另一个Servlet, 然后另一个Servlet把数据返回给客户端, 此时客户端的url并不会发生变化

```java
        request.getRequestDispatcher("xxxxxx").forward(request, response);
```



客户端重定向: 服务器直接返回客户端新的Servlet地址, 然后客户端再次请求新的Servlet

```java
        response.sendRediret("xxxxx");

```

