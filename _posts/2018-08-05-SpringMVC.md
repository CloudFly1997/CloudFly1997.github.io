---
layout: article
title: SpringMVC
key: 10008
tags: SpringMVC
category: blog
date: 2018-08-05 21:15:00 +08:00
---

## 简介

- SpringMVC是一种基于java实现的MVC设计模型的请求驱动类型的轻量级WEB框架
- SpringMVC属于SpringFrameWork的后续产品，已经融合在SpringWebFlow里面。Spring框架提供了构建Web应用程序的全功能MVC模块
- 使用Spring可插入的MVC架构，从而在使用Spring进行WEB开发时，可以选择使用Spring的SpringMVC框架或集成其他MVC开发框架，如Struts1、Struts2等

<!--more-->

### 角色划分

- 前端控制器(DispatcherServlet)
- 请求处理器映射(HandlerMapping)
- 处理器适配器(HandlerAdapter)
- 视图解析器(ViewResolver)
- 处理器或页面控制器(Controller)
- 验证器(Validator)
- 命令对象(Command 请求参数绑定到的对象)
- 表单对象(Form Object 提供给表单展示和提交到的对象)

### SpringMVC和Struts2对别

- 共同点：
  - 都是表现层框架，都是基于MVC模型写的
  - 底层都离不开原始ServletAPI
  - 处理请求的机制都是一个核心控制器(SpringMVC的核心控制器是Servlet、Struts2的核心控制器是Filter)
- 区别：
  - SpringMVC的入口是Servlet，而Struts2的入口是Filter
  - SpringMVC是基于方法设计的(单例的)，而Struts2是基于类(多例的，每一个请求创建一个Struts2框架)。Struts2每次执行都会创建一个动作类，所以SpringMVC会比Struts2快一点
  - SpringMVC使用更加简洁，同时支持JSR303，处理Ajax的请求更加方便(JSR303是一套JavaBean参数校验的标准，它定义了很多常用的校验注解，我们可以直接将这些注解加在JavaBean的属性上面，就可以在需要校验的时候进行校验)
  - Struts2的OGNL表达式使页面的开发效率相比SpringMVC更高些，当执行效率并没有JSTL高，尤其是Struts2的表单标签，远没有html执行效率高

### HelloWorld

#### 配置流程

- pom.xml

  ```xml
  <!--版本锁定-->
  <properties>
    <spring.version>5.0.2.RELEASE</spring.version>
  </properties>
  
  <dependencies>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-web</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>servlet-api</artifactId>
      <version>2.5</version>
      <scope>provided</scope>
    </dependency>
    <dependency>
      <groupId>javax.servlet.jsp</groupId>
      <artifactId>jsp-api</artifactId>
      <version>2.0</version>
      <scope>provided</scope>
    </dependency>
  </dependencies>
  ```

-  web.xml配置文件中配置核心控制器DispatcherServlet 

  ```xml
  <!--SpringMVC的核心控制器-->
  <servlet>
    <servlet-name>dispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!--配置Servlet的初始化参数，读取SpringMVC的配置文件，创建Spring容器-->
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:springmvc.xml</param-value>
    </init-param>
    <!--配置servlet启动时加载对象-->
    <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
    <servlet-name>dispatcherServlet</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>
  ```

-  springmvc.xml配置文件

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:mvc="http://www.springframework.org/schema/mvc"
           xmlns:context="http://www.springframework.org/schema/context"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="
            http://www.springframework.org/schema/beans
            http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/mvc
            http://www.springframework.org/schema/mvc/spring-mvc.xsd
            http://www.springframework.org/schema/context
            http://www.springframework.org/schema/context/spring-context.xsd">
  
        <!--开启注解扫描-->
        <context:component-scan base-package="com.wyf"></context:component-scan>
    
        <!--视图解析器对象-->
        <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
            <property name="prefix" value="/WEB-INF/pages/"></property>
            <property name="suffix" value=".jsp"/>
        </bean>
    
        <!--开启SpringMVC框架注解的支持-->
        <mvc:annotation-driven></mvc:annotation-driven>
    </beans>
  ```

-  编写index.jsp

  ```jsp
  <body>
    <h3>入门程序</h3>
    <a href="hello">入门程序</a>
  </body>
  ```

-  编写HelloController控制器类 

  ```java
  package com.wyf.controller;
  
  import org.springframework.stereotype.Controller;
  import org.springframework.web.bind.annotation.RequestMapping;
  
  //控制器类
  @Controller
  public class HelloController {
  
    @RequestMapping(path = "/hello")
    public String sayHello(){
      System.out.println("hello SpringMVC");
      return "success";
    }
  }
  ```

-  在WEB-INF目录下创建pages文件夹，编写success.jsp的成功页面 

  ```jsp
  <body>
      <h3>成功</h3>
  </body>
  ```

#### 执行流程

- 当启动Tomcat服务器的时候，因为配置了load-on-startup标签，所以会创建DispatcherServlet对象，就会加载springmvc.xml配置文件
- 开启了注解扫描，HelloController控制器类对象就会被创建
- 从index.jsp发送请求，请求会先到达DispatcherServlet核心控制器，根据配置@RequestMapping注解找到执行的具体方法
- 根据执行方法的返回值，在根据springmvc.xml配置的视图解析器，去指定的目录下查找指定名称的jsp文件
- Tomcat服务器渲染页面，做出响应



### SpringMVC组件之间的执行流程

![1571542578832](https://cloudfly1997.github.io/blog/SpringMVC.assets/1571542578832.png)



## 注解

### @RequestMapping

#### 作用

- 建立请求URL和处理请求方法之间的对应联系

#### 属性

- value：指定请求的URL，和path属性作用一样
- method：指定请求的方式
- params：指定限制请求的参数的条件
- headers：指定限制请求消息头的条件

#### 案例

- HelloController类

  ```java
  package com.wyf.controller;
  
  import org.springframework.stereotype.Controller;
  import org.springframework.web.bind.annotation.RequestMapping;
  import org.springframework.web.bind.annotation.RequestMethod;
  
  //控制器类
  @Controller
  @RequestMapping(value = "/user")
  public class HelloController {
  
      @RequestMapping(path = "/testRequestMapping",method = RequestMethod.GET,params = {"username=hehe"})
      //请求路径是/user/testRequestMapping，请求方式是get方式，请求参数有username为hehe的才允许执行
      public String testRequestMapping(){
          System.out.println("hello RequestMapping注解");
          return "success";
      }
  }
  ```



### @PathVariable

#### 作用

- 将URL路径中占位符参数绑定到控制器处理方法的形参上

#### 属性

- value：用于指定url中占位符名称
- required：是否必须提供占位符

#### 案例

```java
@RequestMapping("delete/{id}")
public Strng delete(@PathVariable("id") Integer id){
  UserDao.delete(id);
  return "redirect:/user/list.action";
}
```



### @RequestParam

#### 作用

-   把请求中指定名称的参数给控制器中的形参赋值 

#### 属性

- value：请求参数中的名称
- required：请求参数中是否必须提供此参数，默认值：true，表示必须提供，如果不提供将报错
- defaultValue：请求参数的默认值

#### 案例

- anno.jsp

  ```html
  <a href="anno/testRequestParam?name=wyf">PequestParam</a>
  ```

- AnnoController类

  ```java
  @Controller
  @RequestMapping("/anno")
  public class AnnoController {
  
  	@RequestMapping("testRequestParam")
  	public String testRequestRaram(@RequestParam(name = "name") String username){
  		System.out.println(username);
  		return "success";
  	}
  }
  ```



### @RequestBody

#### 作用

- 获取请求体的内容，Json数据，get请求不适用

#### 属性

- required：是否必须有请求体。默认值是true，当取值为true时，get请求方式会报错，如果取值为false，get请求得到的是null

#### 案例

- anno.jsp

  ```html
  <%--post请求方式--%>
  <form action="anno/testRequestBody" method="post">
    姓名：<input type="text" name="name"><br>
    年龄：<input type="text" name="age"><br>
    <input type="submit" value="提交">
  </form>
  <%--get请求方式--%>
  <a href="anno/testRequestBody?body=wyf">RequestBody</a>
  ```

- AnnoController类

  ```java
  @Controller
  @RequestMapping("/anno")
  public class AnnoController {
    
    @RequestMapping("testRequestBody")
    public String testRequestBody(@RequestBody String body){
      System.out.println(body);
      return "success";
    }
  }
  ```



### @RequestHeader（不常用）

#### 作用

- 获取请求消息头

####  属性

- value：提供消息头名称
- required：时候必须有此消息头

#### 案例

- anno.jsp

  ```html
  <a href="anno/testRequestHeader">RequestHeader</a>
  ```

- AnnoController类

  ```java
  @Controller
  @RequestMapping("/anno")
  public class AnnoController {
    
    @RequestMapping("testRequestHeader")
    public String testRequestHeader(@RequestHeader(value = "Accept") String header){
      System.out.println(header);
      return "success";
    }
  }
  ```

  

### @CookieValue（不常用）

#### 作用

- 把指定cookie名称的值传入控制器方法参数

#### 属性

- value：指定cookie的名称
- required：是否必须有此cookie

#### 案例

- anno.jsp

  ```html
  <a href="anno/testCookieValue">CookieValue</a>
  ```

- AnnoController类

  ```java
  @Controller
  @RequestMapping("/anno")
  public class AnnoController {
    
    @RequestMapping("testCookieValue")
    public String testCookieValue(@CookieValue(value = "JSESSIONID") String cookieValue){
      System.out.println(cookieValue);
      return "success";
    }
  }
  ```



### @ModelAttribute

#### 作用

- 当表单提交数据不是完整的实体类数据时，保证没有提交数据的字段使用数据库对象原来的数据

- 出现在方法上，表示当前方法会在控制器的方法执行之前先执行。可以修饰没有返回值的方法，也可以修饰有返回值的方法
- 出现在参数上，获取指定的数据给参数赋值

####  属性

- value：获取数据的key。key可以使POJO的属性名称，也可以是map结构的key

#### 案例

- anno.jsp

  ```html
  <form action="anno/ModelAttribute" method="post">
    姓名：<input type="text" name="uname"><br>
    年龄：<input type="text" name="age"><br>
    <input type="submit" value="提交">
  </form>
  ```

- AnnoController类

  ```java
  //有返回值
  @ModelAttribute
  public User showUser(String uname){
    System.out.println("加了ModelAttribute注解的showUser方法执行了");
    //通过用户名查询数据库(模拟)
    User user = new User();
    user.setUname(uname);
    user.setAge(20);
    user.setDate(new Date());
  
    return user;
  }
  
  @RequestMapping("ModelAttribute")
  public String ModelAttribute(User user){
    System.out.println(user);
    System.out.println("ModelAttribute执行了");
    return "success";
  }
    
    
    
  //无返回值
  @ModelAttribute
  public void showUser(String uname, Map<String,User> map){
    System.out.println("加了ModelAttribute注解的showUser方法执行了");
    //通过用户名查询数据库(模拟)
    User user = new User();
    user.setUname(uname);
    user.setAge(20);
    user.setDate(new Date());
  
    map.put("abc",user);
  }
  
  @RequestMapping("testModelAttribute")
  public String testModelAttribute(@ModelAttribute("abc") User user){
    System.out.println(user);
    System.out.println("testModelAttribute执行了");
    return "success";
  }
  ```

  

### @SessionAttribute

#### 作用

- 用于多次执行控制器方法间的参数共享
- 会将模型中对应的属性暂存到HttpSession中

#### 属性

- value：指定存入的属性名称
- type：指定存入的数据类型

#### 案例

- anno.jsp

  ```html
  <a href="anno/addSessionAttribute">addSessionAttribute</a><br>
  <a href="anno/getSessionAttribute">getSessionAttribute</a><br>
  <a href="anno/deleteSessionAttribute">deleteSessionAttribute</a><br>
  ```

- AnnoController类

  ```java
  @Controller
  @RequestMapping("/anno")
  @SessionAttributes(value = {"msg"})
  public class AnnoController {
    @RequestMapping("/addSessionAttribute")
    public String addSessionAttribute(Model model){
      System.out.println("添加SessionAttribute！！！");
      model.addAttribute("msg","wyf");
      return "success";
    }
  
    @RequestMapping("/getSessionAttribute")
    public String getSessionAttribute(ModelMap modelMap){
      System.out.println("得到SessionAttribute！！！");
      String msg = (String) modelMap.get("msg");
      System.out.println(msg);
      return "success";
    }
  
    @RequestMapping("/deleteSessionAttribute")
    public String deleteSessionAttribute(SessionStatus status){
      System.out.println("清除SessionAttribute！！！");
      status.setComplete();
      return "success";
    }
  }
  ```



## 响应返回值类型

### String类型

- response.jsp

  ```html
  <a href="user/ReturnString">ReturnString</a>
  ```

- UserController类

  ```java
  @Controller
  @RequestMapping("/user")
  public class UserController {
  
      @RequestMapping("ReturnString")
      public String ReturnString(Model model){
          System.out.println("ReturnString方法执行了");
          //模拟从数据库中查询出User对象
          User user = new User();
          user.setUsername("username");
          user.setPassword("password");
          user.setAge(20);
          //model对象
          model.addAttribute("user",user);
          return "success";
      }
  }
  ```

- success.jsp

  ```html
  <body>
      <h2>成功</h2>
      ${user.username}
      ${user.password}
      ${user.age}
  </body>
  ```



### Void类型

- response.jsp

  ```html
  <a href="user/ReturnVoid">ReturnVoid</a>
  ```

- UserController类

  ```java
  @Controller
  @RequestMapping("/user")
  public class UserController {
      @RequestMapping("ReturnVoid")
      public void testReturnVoid(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
          System.out.println("ReturnVoid方法执行了");
          //1.请求转发
          //request.getRequestDispatcher("/WEB-INF/pages/success.jsp").forward(request,response);
  
          //2.重定向
          //response.sendRedirect(request.getContextPath()+"/index.jsp");
  
          //3.直接进行响应
          response.setContentType("text/html;charset=utf-8");
          response.getWriter().print("你好");
  
          return ;
      }
  }
  ```

  

### ModelAndView类型

- response.jsp

  ```html
  <a href="user/ModelAndView">ModelAndView</a>
  ```

- UserController类

  ```java
  @Controller
  @RequestMapping("/user")
  public class UserController {
      @RequestMapping("ModelAndView")
      public ModelAndView ModelAndView(){
          System.out.println("ModelAndView方法执行了");
          //创建一个ModelAndView对象
          ModelAndView modelAndView = new ModelAndView();
          //模拟从数据库中查询出User对象
          User user = new User();
          user.setUsername("username");
          user.setPassword("password");
          user.setAge(20);
  
          //把user对象存储到modelandview对象中，也会把user对象存到request对象
          modelAndView.addObject("user",user);
  
          //跳转到那个页面
          modelAndView.setViewName("success");
  
          return modelAndView;
      }
  }
  ```

- success.jsp

  ```html
  <body>
      <h2>成功</h2>
      ${user.username}
      ${user.password}
      ${user.age}
  </body>
  ```



## 发送PUT和DELETE请求

- web.xml 中配置 HiddenHttpMethodFilter

  ```xml
  <filter>
    <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
  	<filter-name>HiddenHttpMethodFilter</filter-name>
  </filter>
  ```

- 页面创建一个post表单

- 创建一个hidden类型的input项，name=“_method”，value=“请求方式”

  ```html
  <form action="" meethod="post">
  	<input type="hidden" name="_method" value="DELETE"/>
  </form>
  ```



## 请求转发和重定向

### 请求转发（forward）

#### 注意

- 使用forward，路径必须写成实际 url ，不能使用视图解析器（逻辑视图）

#### 案例

- response.jsp

  ```html
  <a href="user/Forward">Forward</a>
  ```

- UserController类

  ```java
  @Controller
  @RequestMapping("/user")
  public class UserController {
      @RequestMapping("Forward")
      public String Forward(){
          System.out.println("Forward方法执行了");
  
          //请求转发
          return "forward:/WEB-INF/pages/success.jsp";
      }
  }
  ```



### 重定向（redirect）

#### 注意

- 如果重定向到 jsp 页面，则 jsp 页面不能写在WEB-INF目录中，否则无法找到

#### 案例

- response.jsp

  ```html
  <a href="user/Redirect">Redirect</a>
  ```

- UserController类

  ```java
  @Controller
  @RequestMapping("/user")
  public class UserController {
      @RequestMapping("Redirect")
      public String Redirect(){
          System.out.println("Redirect方法执行了");
  
          //重定向
          return "redirect:/index.jsp";
      }
  }
  ```



## 中文乱码过滤器

```xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
  <!--配置解决中文乱码的过滤器-->
  <filter>
    <filter-name>characterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
      <param-name>encoding</param-name>
      <param-value>UTF-8</param-value>
    </init-param>
  </filter>
  <filter-mapping>
    <filter-name>characterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>
</web-app>
```



## 处理静态资源

- DispatcherServlet会拦截到所有的资源，导致一个问题就是静态资源（img、css、js）也会被拦截到，从而不能被使用。解决问题就是需要配置静态资源不进行拦截

- 在springmvc.cml配置文件添加mvc:resources标签配置不过滤
  - location属性：表示webapp目录下的包下的所有文件
  - mapping属性：表示以/static开头的所有请求路径，如/static/a或者/static/a/b

```xml
<!-- 设置静态资源不过滤 -->
<mvc:resources location="/css/" mapping="/css/**"/>
<mvc:resources location="/images/" mapping="/images/**"/>
<mvc:resources location="/js/" mapping="/js/**"/>
```



## 自定义类型转换器

### 流程

- 定义一个类，实现Convarter接口，该接口有两个泛型
- 在Spring配置文件中配置类型转换器
- 在annotation-driven标签中引用配置的类型转换服务

### 案例

- params.jsp

  ```html
  <%@ page contentType="text/html;charset=UTF-8" language="java" %>
  <html>
  <head>
      <title>自定义类型转换器</title>
  </head>
  <body>
      <p>自定义类型转换器</p>
      <form action="param/saveUser" method="post">
          用户姓名：<input type="text" name="uname"/><br>
          用户年龄：<input type="text" name="age"/><br>
          用户出生日期：<input type="text" name="date"/><br>
          <input type="submit" value="提交">
      </form>
  </body>
  </html>
  ```

- springmvc.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:mvc="http://www.springframework.org/schema/mvc"
         xmlns:context="http://www.springframework.org/schema/context"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="
          http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd
          http://www.springframework.org/schema/mvc
          http://www.springframework.org/schema/mvc/spring-mvc.xsd
          http://www.springframework.org/schema/context
          http://www.springframework.org/schema/context/spring-context.xsd">
  
      <!--开启注解扫描-->
      <context:component-scan base-package="com.wyf"></context:component-scan>
  
      <!--视图解析器对象-->
      <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
          <property name="prefix" value="/WEB-INF/pages/"></property>
          <property name="suffix" value=".jsp"/>
      </bean>
  
      <!--配置自定义类型转换器-->
      <bean id="conversionServiceFactory" class="org.springframework.context.support.ConversionServiceFactoryBean">
          <property name="converters">
              <set>
                  <bean class="com.wyf.utils.StringToDateConverter"></bean>
              </set>
          </property>
      </bean>
  
      <!--开启SpringMVC框架注解的支持-->
      <mvc:annotation-driven conversion-service="conversionServiceFactory"></mvc:annotation-driven>
  </beans>
  ```

- web.xml

  ```xml
  <!DOCTYPE web-app PUBLIC
   "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
   "http://java.sun.com/dtd/web-app_2_3.dtd" >
  
  <web-app>
    <display-name>Archetype Created Web Application</display-name>
  
    <!--SpringMVC的核心控制器-->
    <servlet>
      <servlet-name>dispatcherServlet</servlet-name>
      <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
      <!--配置Servlet的初始化参数，读取SpringMVC的配置文件，创建Spring容器-->
      <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:springmvc.xml</param-value>
      </init-param>
      <!--配置servlet启动时加载对象-->
      <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
      <servlet-name>dispatcherServlet</servlet-name>
      <url-pattern>/</url-pattern>
    </servlet-mapping>
  
    <!--配置解决中文乱码的过滤器-->
    <filter>
      <filter-name>characterEncodingFilter</filter-name>
      <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
      <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
      </init-param>
    </filter>
    <filter-mapping>
      <filter-name>characterEncodingFilter</filter-name>
      <url-pattern>/*</url-pattern>
    </filter-mapping>
  </web-app>
  ```

- ParamController类

  ```java
  @Controller
  @RequestMapping("/param")
  public class ParamController {
      //自定义类型转换器：
      @RequestMapping("saveUser")
      public String saveUser(User user){
          System.out.println("执行了...");
          System.out.println(user.toString());
          return "success";
      }
  }
  ```

- 自定义类型转换器：StringToDateConverter类

  ```java
  import org.springframework.core.convert.converter.Converter;
  
  import java.text.DateFormat;
  import java.text.SimpleDateFormat;
  import java.util.Date;
  
  //把字符串转为日期
  public class StringToDateConverter implements Converter<String, Date> {
      @Override
      public Date convert(String s) {
          if(s == null){
              throw new RuntimeException("字符串为空！");
          }else{
              DateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd");
              try {
                  return dateFormat.parse(s);
              } catch (Exception e) {
                  throw new RuntimeException("字符串类型转换失败！");
              }
          }
      }
  }
  ```



## 国际化

- web.xml

  ```xml
  <!-- 国际化配置start -->
  <!-- 主要用于获取请求中的locale信息，将其转为Locale对像，获取LocaleResolver对象-->
  <mvc:interceptors>
    <bean id="localeChangeInterceptor" class="org.springframework.web.servlet.i18n.LocaleChangeInterceptor"/>
  </mvc:interceptors>
  
  <bean id="messageSource" class="org.springframework.context.support.ResourceBundleMessageSource">
    <!-- 表示语言配置文件是以language开头的文件（language_zh_CN.properties）-->
    <property name="basename" value="language"/>
    <!-- 语言区域里没有找到对应的国际化文件时，默认使用language.properties文件-->
    <property name="useCodeAsDefaultMessage" value="true" />
  </bean>
  
  <!-- 配置SessionLocaleResolver用于将Locale对象存储于Session中供后续使用 -->
  <bean id="localeResolver" class="org.springframework.web.servlet.i18n.SessionLocaleResolver"/>
  <!-- 国际化配置end -->
  ```

- 创建国际化文件

  ![1571573846215](https://cloudfly1997.github.io/blog/SpringMVC.assets/1571573846215.png)

  ![1571574036732](https://cloudfly1997.github.io/blog/SpringMVC.assets/1571574036732.png)

- index.jsp

  ```html
  <a href="?locale=zh_CN">中文</a>
  <a href="?locale=en_US">英文</a>
  ```



## 文件上传

### 配置

- pom.xml

  ```xml
  <!--文件上传-->
  <dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.3.1</version>
  </dependency>
  <dependency>
    <groupId>commons-io</groupId>
    <artifactId>commons-io</artifactId>
    <version>2.4</version>
  </dependency>
  ```

- web.xml 中配置文件解析器对象

  ```xml
  <!--配置文件解析器对象，要求id名称必须是multipartResolver-->
  <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
      <!--设置最大上传大小-->
      <property name="maxUploadSize" value="10485760"></property>
  </bean>
  ```

### 案例（本地）

- index.jsp

  ```html
  <form method="post" action="user/fileupload" enctype="multipart/form-data">
      选择文件：<input type="file" name="upload"/><br>
      <input type="submit" value="上传"/>
  </form>
  ```

- UserController类

  ```java
  @Controller
  @RequestMapping("/user")
  public class UserController {
  
      //SpringMVC方式文件上传方式：
      @RequestMapping("fileupload")
      public String fileupload(HttpServletRequest request,MultipartFile upload) throws Exception {
          System.out.println("SpringMVC方式文件上传....");
  
          //使用fileupload组件完成文件上传
          String path = request.getSession().getServletContext().getRealPath("/uploads/");
          File file = new File(path);
          if(!file.exists()){
              //创建该文件夹
              file.mkdirs();
          }
  
          //获取上传文件的名称
          String filename = upload.getOriginalFilename();
          //把文件的名称设置唯一值
          String uuid = UUID.randomUUID().toString().replace("_", "").toUpperCase();
          filename=uuid+"_"+filename;
  
          //完成文件上传
          upload.transferTo(new File(path,filename));
  
          return "success";
      }
  }
  ```

### 案例（跨服务器）

- pom.xml

  ```xml
  <dependency>
    <groupId>com.sun.jersey</groupId>
    <artifactId>jersey-core</artifactId>
    <version>1.18.1</version>
  </dependency>
  <dependency>
    <groupId>com.sun.jersey</groupId>
    <artifactId>jersey-client</artifactId>
    <version>1.18.1</version>
  </dependency>
  ```

- 配置Tomcat服务器：在Tomcat服务器的配置文件web.xml中设置允许读写操作

  ```xml
  <servlet>
      <servlet-name>default</servlet-name>
      <servlet-class>org.apache.catalina.servlets.DefaultServlet</servlet-class>
      <init-param>
          <param-name>debug</param-name>
          <param-value>0</param-value>
      </init-param>
      <!--允许读写操作 -->
      <init-param>
           <param-name>readonly</param-name>
           <param-value>false</param-value>
      </init-param>
      
      <init-param>
          <param-name>listings</param-name>
          <param-value>false</param-value>
      </init-param>
      <load-on-startup>1</load-on-startup>
  </servlet>
  ```

- index.jsp

  ```html
  <form method="post" action="user/fileupload" enctype="multipart/form-data">
      选择文件：<input type="file" name="upload"/><br>
      <input type="submit" value="上传"/>
  </form>
  ```

- UserController类

  ```java
  @Controller
  @RequestMapping("/user")
  public class UserController {
  
      @RequestMapping("fileupload")
      public String fileupload(MultipartFile upload) throws Exception {
          System.out.println("SpringMVC跨服务器方式文件上传....");
  
          //定义上传文件服务器路径
          String path="http://localhost:9090/FileUploadServer_war_exploded/uploads/";
  
          //获取上传文件的名称
          String filename = upload3.getOriginalFilename();
          //把文件的名称设置唯一值
          String uuid = UUID.randomUUID().toString().replace("_", "").toUpperCase();
          filename=uuid+"_"+filename;
  
          //完成文件上传：跨服务器上传
  
          //创建客户端对象
          Client client = Client.create();
          //和图片服务器进行连接
          WebResource webResource = client.resource(path + filename);
          //上传文件
          webResource.put(upload.getBytes());
  
          return "success";
      }
  }
  ```

### 案例（多文件上传）（未完成）





## 拦截器

### 概述

- SpringMVC框架中的拦截器用于对处理器进行预处理和后处理的技术
- 可以定义拦截器链，拦截器链就是将拦截器按一定的顺序结成一条链，在访问被拦截的方法时，拦截器链中的拦截器会按找定义的顺序执行
- 拦截器也是AOP思想的一种实现方式
- 想要自定义拦截器，需要实现HandlerInterceptor接口

### 拦截器和过滤器的异同

- 过滤器是Servlet规范的一部分，任何框架都可以使用过滤器技术
- 拦截器是SpringMVC框架独有的
- 过滤器配置了/*，可以拦截任何资源
- 拦截器只会对控制器中的方法进行拦截

### HandlerInterceptor接口中的方法

- preHandle方法是controller方法执行前拦截的方法
  - 可以使用request或者response跳转到指定页面
  - return true ：放行，执行下一个拦截器，如果没有，执行controller中的方法
  - return false：不放行
- postHandle是controller方法执行后执行的方法，在JSP视图执行前
  - 可以使用request或者response跳转到指定页面
  - 如果指定了跳转页面，那么controller方法跳转的页面将不会显示
- afterCompletion方法是在JSP执行之后执行
  - request或者response不能再跳转页面了

### 自定义拦截器

- springmvc.xml

  ```xml
  <!--配置拦截器-->
  <mvc:interceptors>
      <mvc:interceptor>
          <!--要拦截的具体方法-->
          <mvc:mapping path="/user/*"/>
          <!--不要拦截的方法
          <mvc:exclude-mapping path="">
          -->
          <!--配置拦截器对象-->
          <bean id="userInterceptor" class="com.wyf.interceptor.UserInterceptor1"></bean>
      </mvc:interceptor>
  
      <mvc:interceptor>
          <!--要拦截的具体方法-->
          <mvc:mapping path="/user/*"/>
          <!--不要拦截的方法
          <mvc:exclude-mapping path="">
          -->
          <!--配置拦截器对象-->
          <bean id="userInterceptor2" class="com.wyf.interceptor.UserInterceptor2"></bean>
      </mvc:interceptor>
  </mvc:interceptors>
  ```

-  UserInterceptor1类

  ```java
  //自定义拦截器
  public class UserInterceptor1 implements HandlerInterceptor {
      /**
       *预处理：controller方法执行前
       **/
      @Override
      public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
          System.out.println("UserInterceptor拦截器执行了...111");
          //request.getRequestDispatcher("/WEB-INF/pages/error.jsp").forward(request,response);
          return true;
      }
  
      /**
       * 后处理方法：controller方法执行后，Jsp页面执行之前
       * */
      @Override
      public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
          System.out.println("UserInterceptor执行.....之后111");
          //request.getRequestDispatcher("/WEB-INF/pages/error.jsp").forward(request,response);
      }
  
      /**
       * Jsp页面执行执行之后
       */
      @Override
      public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
          System.out.println("UserInterceptor执行....最后111");
      }
  }
  ```

-  UserInterceptor2类

  ```java
  //自定义拦截器
  public class UserInterceptor2 implements HandlerInterceptor {
      /**
       *预处理：controller方法执行前
       **/
      @Override
      public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
          System.out.println("UserInterceptor拦截器执行了...222");
          //request.getRequestDispatcher("/WEB-INF/pages/error.jsp").forward(request,response);
          return true;
      }
  
      /**
       * 后处理方法：controller方法执行后，Jsp页面执行之前
       * */
      @Override
      public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
          System.out.println("UserInterceptor执行.....之后222");
          //request.getRequestDispatcher("/WEB-INF/pages/error.jsp").forward(request,response);
      }
  
      /**
       * Jsp页面执行执行之后
       */
      @Override
      public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
          System.out.println("UserInterceptor执行....最后222");
      }
  }
  ```



## 异常处理

![SpringMVC异常处理流程分析图](https://cloudfly1997.github.io/blog/SpringMVC.assets/SpringMVC异常处理流程分析图.png)

### 实现HandlerExceptionResolver接口

- GlobalExceptionResolver类

  ```java
  public class GlobalExceptionResolver implements HandlerExceptionResolver {
          @Override
          public ModelAndView resolveException(HttpServletRequest request,
                       HttpServletResponse response, Object handler, Exception ex) {
                String exMsg = "";
                 if(null != ex) {
                       exMsg = ex.getMessage();
                }
                ModelAndView modelAndView = new ModelAndView();
                modelAndView.setViewName("exception");
                Map<String, String> map = new HashMap<String, String>();
                map.put( "key", "exception occured: " + exMsg);
                modelAndView.addAllObjects(map);
                return modelAndView;
         }
  }
  ```

-  springmvc.xml

  ```xml
  <!--配置异常处理器-->
  <bean id="GlobalExceptionResolver" class="com.wyf.exception.GlobalExceptionResolver"/>
  ```

###  使用@ExceptionHandler注解

- ExampleController类

  ```java
  @Controller
  class ExampleController {
      @ExceptionHandler(Exception.class)
      @ReponseBody
      public Object exceptionHandler(Exception e) {
          ...
          return new Object();
      }
      
      @RequestMapping(...)
      public Object doController() {
      }
  }
  ```

