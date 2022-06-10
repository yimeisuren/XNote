# Spring配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--xmlns="http://www.springframework.org/schema/beans"根据该值修改-->
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation=
               "http://www.springframework.org/schema/beans
                http://www.springframework.org/schema/beans/spring-beans.xsd
                http://www.springframework.org/schema/context
                http://www.springframework.org/schema/beans/spring-context.xsd
                http://www.springframework.org/schema/aop
                http://www.springframework.org/schema/beans/spring-aop.xsd">

    <!--开启注解的支持-->
    <context:annotation-config/>
    <!--开启注解的扫描 todo:指定扫描的包名-->
    <context:component-scan base-package=""/>
    <!--扫描@Aspect注解, 如果有该注解将该类设置为代理类, 生成的对象即为代理对象-->
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
    
    <!--下面是通过配置文件方式实现AOP操作-->
    <bean id="userDaoImpl" class="dao.UserDaoImpl"/>
    <bean id="userDaoProxy" class="dao.UserDaoProxy"/>

    <!--配置aop配置增强-->
    <aop:config>
        <!--切入点表达式-->
        <aop:pointcut id="p" expression="execution(* dao.UserDaoImpl.add(..))"/>

        <!--配置切面: 指定哪个代理类对哪个方法进行增强-->
        <aop:aspect ref="userDaoProxy">
            <!--增强作用在哪个具体的方法上-->
            <aop:before method="before" pointcut-ref="p"/>
            <aop:after method="after" pointcut-ref="p"/>
            <aop:after-returning method="afterReturning" pointcut-ref="p"/>
            <aop:around method="around" pointcut-ref="p"/>
            <aop:after-throwing method="afterThrowing" pointcut-ref="p"/>
        </aop:aspect>
    </aop:config>
```

