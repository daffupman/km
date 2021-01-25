# Java Web

## 一、Servlet

Servlet是Java Web的一个组件，也是一个规范，还是一个程序（类：javax.servlet.Servlet）。Tomcat服务器是对Servlet的一个实现。

### Servlet生命周期

Tomcat管理Servlet的生命周期：创建，初始化，运行，销毁。java.servlet.Servlet接口中的方法：

- String getServletInfo() ：获取Servlet的信息，作者，版权，版本
- ServletConfig getServletConfig()：获取Servlet的配置信息对象
- void init(ServletConfig config)：初始化对象
- void service(ServletRequest req,ServletResponse resp)：服务方法，网页操作
- void destroy()：销毁Servlet对象方法

首次启动Tomcat，会执行构造器，并且只会执行一次。接着调用init()方法，也是只调用一次。然后是调用service()，每次请求都会调用service()方法。当正常关闭Tomcat会调用destroy()方法。

> 保证Servlet类必须有一个公共的无参数构造器，方便底层创建Servlet对象：Class.newInstance()，否则500异常

### Servlet的请求流程

![image-20210124215103204](C:/Users/daffupman/AppData/Roaming/Typora/typora-user-images/image-20210124215103204.png)

### Servlet初始化参数

ServletConfig接口：表示Servlet的信息配置对象（web.xml中当前Servlet的配置对象）

- String getServletName()：获取当前Servlet的名字，就是servlet-name的文本内容；
- ServletContext getServletContext()：获取当前的Servlet上下文，就是当前应用对象，重要；
- String getInitParameter(String name)：根据指定的参数名获取初始化参数值；
- Enumeration\<String\> getInitParameterNames()：获取当前Servlet的所有初始化参数的名字

### HttpServletRequest

ServletRequest接口：请求对象，封装了获取所有请求信息的方法。HttpServletRequest接口：是ServletRequest的子接口，处理http协议请求的方法。

常用方法

- String getMethod()：返回请求方式：如GET/POST
-  String getRequestURI()：返回请求行中的资源名字部分:如/test/index.html
- StringBuffer getRequestURL()：返回浏览器地址栏中所有的信息
- String getContextPath()：返回当前项目的上下文路径(\<Context\>元素的path属性值.)
- String getRemoteAddr()：回发出请求的客户机的IP地址
- String getHeader(String name)：返回指定名称的请求头的值。
- String getParameter(String name):返回指定名字参数的值。
- String[] getParameterValues(String name):返回指定名字参数的多个参数值。
- Enumeration\<String\> getParameterNames():返回所有参数名的Enumeration对象。
- Map<String,String[]> getParameterMap():返回所有的参数和值所组成的Map对象。

### 字符编码

在Tomacat中，对应POST和GET请求，默认编码格式是ISO-8859-1，对于中文会乱码。此时要么先解码再编码

```java
Byte[] data = username.getBytes("ISO-8859-1");
String username = new String(data,"UTF-8");
```

要么统一修改编码，对于POST请求方式

```java
 request.setCharacterEncoding("UTF-8");
```

GET请求方式：

```xml
<!-- 修改Tomcat的server.xml文件中的默认端口的URIEncoding为UTF-8 -->
```

### HttpServletResponse

ServletResponse接口：响应对象，封装了获取响应信息的方法。 HttpServletResponse接口：处理Http响应。常用方法：

- OutputStreaem getOutputStream()：获取字节输出流对象

- PrintWriter getWriter()：获取字符输出流对象

  ```java
  response.setContentType("text/html;charset=utf-8"); // 设置输出的MIME类型（内容的类型）和输出数据的编码方式
  ```

## 二、Filter

程序中的过滤器就是一个特殊的类（javax.servlet.Filter），是JavaWeb的一个组件，是双向的。过滤器可以对所有的请求或者响应做拦截操作。通常可以对请求中的字符编码，登录验证，敏感字过滤，MVC的前端控制器。

FilterChain(过滤器链)：多个过滤器按照一定的顺序,排列起来。

## 三、Listerner

监听器是Web的另一个组件，主要用于监听作用域对象的创建,监听作用域对象属性的添加/删除/替换。