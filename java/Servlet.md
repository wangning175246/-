## Servlet

#### 概念：

​	运行在服务器端的小程序

* Servlet就是一个接口，定义了Java类被浏览器访问到(tomcat识别)的规则。
* 将来我们自定义一个类，实现Servlet接口，复写方法。

#### 快速入门：

 1.  创建JavaEE项目

     file-->newProject-->选择javaEnterprise-->Web Application(Java EE Version-->JavaEE8 )--->填写项目名称

     Java EE Version JavaEE6 也就是3.0 以上可以使用不用创建web.xml ,可以使用WebServlet注解的方式配置

     src 中写代码的

     src 中的代码 编译后会生成到 `F:\code\java_code\java_web\out\artifacts\java_web_login_war_exploded\WEB-INF`目录下。

 2.  定义一个类，实现Servlet接口

     public class ServletDemo1 implements Servlet

 3.  实现接口中的抽象方法,

     ```java
     package cn.itcost.web.servlet;
     
     import javax.servlet.*;
     import java.io.IOException;
     
     public class ServletDemo1 implements Servlet {
     
         @Override
         public void init(ServletConfig servletConfig) throws ServletException {
     
         }
     
         @Override
         public ServletConfig getServletConfig() {
             return null;
         }
     
         @Override
         public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
             System.out.println("hello word");
         }
     
         @Override
         public String getServletInfo() {
             return null;
         }
     
         @Override
         public void destroy() {
     
         }
     }
     ```

 4.  配置Servlet,在web.xml中配置：

     ```xml
     <?xml version="1.0" encoding="UTF-8"?>
     <web-app xmlns="http://java.sun.com/xml/ns/javaee"
                xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
     		  http://java.sun.com/xml/ns/javaee/web-app_4_0.xsd"
                version="4.0">
         <!--配置Servlet-->
         <servlet>
             <servlet-name>demo1</servlet-name>
             <servlet-class>cn.itcost.web.servlet.ServletDemo1</servlet-class>
         </servlet>
         <!--配置路径-->
         <servlet-mapping>
             <servlet-name>demo1</servlet-name>
             <url-pattern>/demo1/</url-pattern>
         </servlet-mapping>
     
     </web-app>
     ```

#### 执行过程

 	1. 当服务器接受到客户端浏览器的请求后，会解析请求URL路径，获取访问的Servlet的资源路径
 	2. 查找web.xml文件，是否有对应的`<url-pattern>`标签体内容。
 	3. 如果有，则在找到对应的`<servlet-class>`全类名
 	4. tomcat会将字节码文件加载进内存，并且创建其对象
 	5. 调用其方法

#### Servlet中的生命周期方法：

 1.  被创建：执行init方法，只执行一次
       默认情况下，第一次被访问时，Servlet被创建
       可以配置执行Servlet的创建时机。
        		第一次被访问时被创建，在`<servlet>`标签下配置`<load-on-startup>`为负数 
        		在tomcat启动时被创建，在`<servlet>`标签下配置`<load-on-startup>`的值为0或正整数
        	Servlet的init方法，只执行一次，说明一个Servlet在内存中只存在一个对象，Servlet是单例的
        		多个用户同时访问时，可能存在线程安全问题。
        		尽量不要在Servlet中定义成员变量。即使定义了成员变量，也不要对修改值

 2.  提供服务：执行service方法，执行多次
    每次访问Servlet时，Service方法都会被调用一次。

 3.  被销毁：执行destroy方法，只执行一次

    Servlet被销毁时执行。服务器关闭时，Servlet被销毁

    只有服务器正常关闭时，才会执行destroy方法。

    destroy方法在Servlet被销毁之前执行，一般用于释放资源

#### Servlet3.0：

支持注解配置。可以不需要web.xml

1. 创建JavaEE项目，选择Servlet的版本3.0以上，可以不创建web.xml

2. 定义一个类，实现Servlet接口

3. 复写方法

4. 在类上使用@WebServlet注解，进行配置

  @WebServlet("资源路径")

注解的源码

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface WebServlet {
    String name() default "";//相当于<Servlet-name>
    String[] value() default {};//代表urlPatterns()属性配置
    String[] urlPatterns() default {};//相当于<url-pattern>
    int loadOnStartup() default -1;//相当于<load-on-startup>
    WebInitParam[] initParams() default {};
    boolean asyncSupported() default false;
    String smallIcon() default "";
    String largeIcon() default "";
    String description() default "";
    String displayName() default "";
}
```

