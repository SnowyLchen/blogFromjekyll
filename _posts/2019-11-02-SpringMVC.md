---
layout: post
title: SpringMVC注解版
date: 2019-11-02
---

# SpringMVC

## 1、注解版开发步骤：

​	在web.xml中写

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                      http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1"
>

  <servlet>
    <servlet-name>springmvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <!--上下文位置的指定-->
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:springmvc.xml</param-value>
    </init-param>
  </servlet>

  <servlet-mapping>
    <servlet-name>springmvc</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>


</web-app>
```

​	在resources/Springmvc.xml中写

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
	
    
    <!--扫描包-->
    <context:component-scan base-package="com.tj.Controller"/>
    
    
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <!--前缀-->
        <property name="prefix" value="/jsp/"/>
        <!--后缀-->
        <property name="suffix" value=".jsp"/>
    </bean>

</beans>
```

​	在controller包下写

```java
package com.tj.Controller;

import org.springframework.stereotype.Controller;

import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
@RequestMapping("/Bye")
public class ByeController {

    @RequestMapping("/mvc")
    public String  mvc(Model model){
        model.addAttribute("model","modeler");

        //return的是viewName;  //实际是/jsp/mvc.jsp
        return "mvc";
    }
}
```

​	在webapp/jsp/mvc.jsp中写

```xml
<%--
  Created by IntelliJ IDEA.
  User: Administrator
  Date: 2019/6/1
  Time: 17:18
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
        model:${model}
</body>
</html>

```

​	地址栏中写==localhost:8080/Bye/mvc==

## 2.转发和重定向

​	SpringMVC默认是转发

​	本质区别：发起了一次新的请求

- 可以转发到页面（默认）
- 可以重定向到页面(redirect:path)
- 转发到另一个控制器（forward:path）

## 3.关于SpringMVC访问web元素

- request
- session
- application

可以通过模拟的对象完成操作（webRequest），也可以使用源生的servletAPI完成，直接在方法中入参即可。

## 4.注解详情

### 1> @RequestMapping（）

- value 写的是路径，是一个数组的形式，可以匹配多个路径

```xml
@RequestMapping（value={"/n1","n2"}）
```

- path是value的别名，所以二者任选其一

```xml
@RequestMapping（path={"/n1","n2"}）
```

- method 限定请求的类型（get，head，post，put,patch,delete,option,trace）可以为数组

```xml
@RequestMapping（value={"/n1","n2"},method=RequestMethod.GET）
```

- Parmas 可以去指定参数，还可以去限定这个参数的特征，必须等于某个值，不等于某个值。

```xml
@RequestMapping（value={"/n1","n2"},parmas={"girl=a","boy=b"}）
```

- hearders 能够影响浏览器的行为。
- consumers 消费者媒体类型，可以限定必须为application/json;charset=UTF-8
- produces 产生的响应的类型。

### 2> 关于请求路径的问题

SpringMVC支持ant风格

- ？任意的一个字符，斜杠除外

```xml
@RequestMapping（"/m1?"）
```

- ==*== 0到n。多少个字符都行，斜杠除外

```xml
@RequestMapping（"/m1*"）
```



- ** 支持任意层路径

```xml
@RequestMapping（"/m1/**"）
```



### 3> @GetMapping 、@PostMapping.....

- @GetMapping 是将RequestMapping中的method改为Get类型。限定了只支持get请求。
- @PostMapping是将RequestMapping中的method改为Post类型。限定了只支持Post请求。

### 4> 对于非get post请求的支持

   对于非get post请求的支持需要有额外的内容添加，要增加一个过滤器来额外处理

- 过滤器
- 返回的不再是页面，而是数据

```xml
<!--注册一个支持所有HTTP请求类型的过滤器-->
  <filter>
    <filter-name>hiddenHttpMethodFilter\</filter-name>
    <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
  </filter>
  <filter-mapping>
    <filter-name>hiddenHttpMethodFilter\</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>
```

- 表单提交里面还要添加一个隐藏的参数

```java
type="hidden" name="_method" value="DELETE"
```

@Responsebody

返回数据的，一般情况返回的是json数据

### 5> 关于静态资源的访问问题(css.js)

由于servlet设置的URL匹配方式为/，所以，它将静态资源也当做一个后台请求来处理。要去尝试匹配Controller里面的RequestMapping的组合，因为没有，所以404.



解决方式：

​	1.让SpringMVC单独处理，将这些交给容器的默认servlet处理，就不让DispatchServlet处理。

```xml
    	 <!--默认的servlet处理者-->
        <mvc:default-servlet-handler/>

        <!--只加这一行会导致所有的请求都使用默认的servlet处理，不加会使得所有注解失效-->
        <mvc:annotation-driven/>
MIME类型

```

​	2.通过映射关系描述，一一对编写规则。 

```xml
 <mvc:resources mapping="/static/css/*" location="static/css/"/>
```

​	3.自行在web.xml定义映射规则

### 6> 各类注解

#### @PathVariable

restful风格

![1559491974245](\assets\images\1559491974245.png)

![1559492027278](\assets\images\1559492027278.png)

#### @ModelAttribute

​    在当前controller里的任意方法之前执行

- 方式1：用在方法上

```java
    @ModelAttribute
    public People init(Model model){
        System.out.println("init...........");
        People p=new People();
        p.setName("aa");
        model.addAttribute("people",p);
        return p;
    }

    @RequestMapping("/login")
    public String login(Model model){
        System.out.println(model.containsAttribute("people"));//true
        return "People";
    }
```

- 方式2：作为方法上

``` java
@ModelAttribute("people")
public People init(){
    System.out.println("init...........");
    People p=new People();
    p.setName("aa");
    return p;
}


@RequestMapping("/login")
public String login(Model model){
    System.out.println(model.containsAttribute("people"));//true
    System.out.println(model.containsAttribute("people2"));//flase
    return "People";
}
```

- 方式3：用在方法参数签名

```java
@RequestMapping("/login")
public String login(@ModelAttribute("people")People people,Model model){
    System.out.println(model.containsAttribute("people"));//true
    System.out.println(model.containsAttribute("people2"));//flase
    System.out.println(people.getName());
    return "People";
}
```

- 用在方法签名上并且使用路径变量绑定

@SessionAttributes

用在类上面，他会将模型自动添加到会员里面去（存）

```java
package com.tj.Controller;

import com.tj.pojo.People;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.SessionAttributes;

@Controller
@RequestMapping("/People3")
@SessionAttributes("people")
public class People3Controller {

    @ModelAttribute
    public People init(Model model){
        System.out.println("init...........");
        People p=new People();
        p.setName("aa");
        model.addAttribute("people",p);
        return p;
    }

    @RequestMapping("/show3")
    public String show2(@ModelAttribute People people){
        System.out.println(people.getName());
        return "redirect:/jsp/SessionAttributes.jsp";
    }
}

```

#### @SessionAttribute

要求当前这次访问中必须要有某个对象（检查）

#### @Requestbody

json数据，ajax

### 7> 关于post请求中文乱码的问题解决

  ```xml
<filter>
    <filter-name>characterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
      <param-name>Encoding</param-name>
      <param-value>utf-8</param-value>
    </init-param>
    <init-param>
      <param-name>forceRequestEncoding</param-name>
      <param-value>true</param-value>
    </init-param>
  </filter>
<filter-mapping>
  <filter-name>characterEncodingFilter</filter-name>
  <url-pattern>/*</url-pattern>
</filter-mapping>
  ```

### 8> 关于form表单提交数据的方式

- 方式1
  - 通过属性名称进行绑定，可以完成数据传递（页面当中表单元素的name值要和后台的形参的名字保持一致，属性个数不限）

```xml
前台：
<form action="People/show">
    <input type="text" name="name" value="">
    <input type="submit" value="提交">

</form>
```

```java
后台：
package com.tj.Controller;

import com.tj.pojo.People;
import org.springframework.stereotype.Controller;

import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

import java.util.ArrayList;
import java.util.List;

@Controller
@RequestMapping("/People")
public class PeopleController {

    @RequestMapping("/show")
    public String show(String name){
        System.out.println(name);
        return "ok";
    }
}

```



- 方式2：利用@RequestParam(页面当中表单元素的name值要和后台的形参的名字可以不一致)

```java
后台：
package com.tj.Controller;

import com.tj.pojo.People;
import org.springframework.stereotype.Controller;

import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

import java.util.ArrayList;
import java.util.List;

@Controller
@RequestMapping("/People")
public class PeopleController {

    @RequestMapping("/show")
    public String show(@RequestParam("name") String s){
        System.out.println(s);
        return "ok";
    }
}    
```

- 方式3：直接使用pojo形式传递

```java
package com.tj.Controller;
import com.tj.pojo.People;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
@Controller
@RequestMapping("/People")
public class PeopleController {

    @RequestMapping("/show2")
    public String show2(People people){
        System.out.println(people.getName());
        return "ok";
    }
}

```

### 注：关于日期提交

- 方式一：InitBinder

```java
	    @InitBinder
    public void initbinder(WebDataBinder binder){
        SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        dateFormat.setLenient(false);
        binder.registerCustomEditor(Date.class, new CustomDateEditor(dateFormat, false));

    }
```

- 方式二：在pojo的Date类型的属性上面添加      @DateTimeFormat(pattern = "yyyy-MM-dd")

  ``` java
  @DateTimeFormat(pattern = "yyyy-MM-dd")
  private Date birthday;
  ```

## 5拦截器

SpringMVC 提供了拦截器，类似于过滤器，他将在请求距离处理之前先做检查，有权决定，接下来是否继续，还可以对请求进行加工。

拦截器可以设计多个。

​	通过实现Handlerinterceptor接口

定义了三个非常重要的方法：

- 前置处理
- 后置处理
- 完成处理

### eg1

​	拦截器实现方法耗时与警告

interceptor

```java
package com.utils.interCeptors;

import org.apache.log4j.Logger;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;
import sun.rmi.runtime.Log;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * 方法耗时统计拦截器
 */
public class  MethodTimerInterCeptor implements HandlerInterceptor {

    private static  final Logger LOGGER=Logger.getLogger(MethodTimerInterCeptor.class);

    //前置处理  开始到结束，两个点减法
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //1.定义开始时间
        long startTime=System.currentTimeMillis();
        //2.将其放到请求域中
        request.setAttribute("startTime",startTime);
        //3.返回值为true，才会找下一个拦截器，如果没有下一个拦截器，则去Controller
        //记录请求日志
        LOGGER.info(request.getRequestURI()+"请求到达！");
        return true;
    }
    //后置处理
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        //1.取出startTime
        long startTime= (long) request.getAttribute("startTime");
        //2.得到end
        long endTime=System.currentTimeMillis();
        //3.记录一下耗时
        long spendTime=startTime-endTime;
        if(spendTime>=1000){
            LOGGER.warn("方法耗时严重，请及时处理，耗时："+spendTime+"毫秒");

        }else {
            LOGGER.info("方法耗时"+spendTime+"毫秒。耗时正常!");
        }

    }
    //完成处理
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {

    }
}
```

springMVC.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <context:component-scan base-package="com.web.controller"/>


    <!--默认的servlet处理者-->
    <mvc:default-servlet-handler/>
    <!--只加这一行会导致所有的请求都使用默认的servlet处理-->
    <mvc:annotation-driven/>

    <!--拦截器的配置-->
    <mvc:interceptors>
        <mvc:interceptor>
            <!--/*只能拦截/name一层，而/**可以拦截多层-->
            <!--/**/*可以拦截任意包任意层-->
            <mvc:mapping path="/**"/>
            <bean class="com.utils.interCeptors.MethodTimerInterCeptor">

            </bean>
        </mvc:interceptor>
    </mvc:interceptors>
</beans>
```

### eg2

会话拦截器，做用户检查

后台

```java
package com.utils.interCeptors;

import com.pojo.User;
import com.utils.DBCon.DBConnection;
import org.apache.log4j.Logger;
import org.springframework.web.servlet.HandlerInterceptor;
import sun.rmi.runtime.Log;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;

public class SessionInterceptor implements HandlerInterceptor {

    private static final Logger LOGGER= Logger.getLogger(SessionInterceptor.class);

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

        Object user = request.getSession().getAttribute("SESSION_USER");
        if(user==null){
            LOGGER.info("您不具备权限，请先登录！");
            return false;
        }
        if (user instanceof User){
            User u= (User) user;
            System.out.println("密码"+u.getUpassword());
            u.setUpassword(null);
            request.getSession().setAttribute("SESSION_USER",u);
            LOGGER.info(u.getUname()+"处于登录状态，可以进行操作");
            return true;
        }else {
            LOGGER.info("你在搞事情！");
            return false;
        }
    }
}

```

springMVC.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">


    <context:component-scan base-package="com.web.controller"/>

    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <!--前缀-->
        <property name="prefix" value="/jsp/"/>
        <!--后缀-->
        <property name="suffix" value=".jsp"/>
    </bean>

    <!--默认的servlet处理者-->
    <mvc:default-servlet-handler/>
    <!--只加这一行会导致所有的请求都使用默认的servlet处理-->
    <mvc:annotation-driven/>

    <!--拦截器的配置-->
    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/user/**/*"/>
            //只让login同行。拦截其它
            <mvc:exclude-mapping path="/user/login"/>
            <bean class="com.utils.interCeptors.SessionInterceptor"></bean>
        </mvc:interceptor>
 
    </mvc:interceptors>
</beans>
```

### 拦截器执行顺序的问题

如果有N个拦截器，并且都能拦截到某个URI的时候，执行顺序问题，

在SpringMVC当中拦截器定义的顺序是有关系的，配在前面的优先拦截，按照上下顺序拦截

- 前置处理是i1 i2 i3
- 后置处理是i3 i2 i1

### 拦截器和过滤器的比较

- 相似点
  - 都有优先处理请求的权利，可以决定是否将请求转移到实际处理的控制器处。都可以对请求或会话当中的数据进行加工改造。
- 不同
  - 拦截器可以做前置处理，也可以做后置处理，还可以完成后处理，控制的较细。过滤器只负责过滤的行为。（前置.后置）。
  - 过滤器优先执行还是拦截器先执行（过滤器）。
  - 过滤器是servlet规范里面的组件。
  - 拦截器是框架自己额外添加的组件。