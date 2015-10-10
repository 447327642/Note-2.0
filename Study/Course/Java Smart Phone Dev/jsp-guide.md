# JSP 学习指南

For Project 1 Unit 5 & 6

## 开发环境搭建

Mac 10.11 El Capitan

### Java

系统已经不自带 Java，所以需要自己安装。这里比较简单，略。

### Tomcat

1. 下载 [Tomcat](http://tomcat.apache.org/)
2. 解压，并将文件夹命名为 Tomcat 并将文件夹移动到根目录/Library中，安装过程便完成了
3. `sudo chmod 755 /Library/Tomcat/bin/*.sh`
4. 进入 `/Library/Tomcat/bin/` 文件夹
	+ 开启服务器 `./startup.sh`
	+ 关闭服务器 `./shutdown.sh`
5. 开启服务器后访问 `localhost:8080` 应该就可以看到 Tomcat 的欢迎页面

### Eclipse

1. 首先确保已经做好之前的 Tomcat 安装和配置工作，Library 中 link 好了 Tomcat。
2. 确保 Eclipse 处于 Java EE 模式下，选中我们的项目，点选底部框中的 Servers 标签页，创建一个新 Server。
3. Apache -> Tomcat (v7.0) Server -> Next -> 设置 Tomcat 安装路径 -> 如有需要，设置 JRE 版本 -> Finish
4. 选择 new => other => Dynamic Web Project ,按要求填写项目信息，假如工程名字为Servlet，一直next，知道最后勾选添加web.xml,finish。

实现第一个servlet实例，New => Servlet ,输入如下代码

```java
 package servlet;
 
 import javax.servlet.http.HttpServlet;

 public class Hello extends HttpServlet {
 
	private static final long serialVersionUID = 1L;
	public void doGet(HttpServletRequest request, HttpServletResponse response)
	 		throws IOException, ServletException {
		response.setContentType("text/html");
		PrintWriter writer = response.getWriter();
		writer.println("Hello");
	}
 }
```

打开 WebContent -> WEB-INF -> web.xml, 增加servlet

```xml
<servlet>
  <servlet-name>HelloWorld</servlet-name>
  <servlet-class>servlet.HelloWorld</servlet-class>
</servlet>
<servlet-mapping>
	<servlet-name>HelloWorld</servlet-name>
	<url-pattern>/Hello</url-pattern>
</servlet-mapping>
```

其中 servlet-class 是确定的，而servlet-name则可以自己命名。

接下来可以运行了，不过要怎么做呢？很简单，选中工程，run as 选择server，然后打开浏览器输入 `127.0.0.1：8080/HelloWorld` or `127.0.0.1：8080/Hello`

这里注意 mapping 中的 servlet name 务必要和 servlet 中的 servlet name 一致

如果 server 无法启动，那么可以用以下方法解决

1. Clean project & server OR
2. Remove .snap file from this directory <workspace-directory>\.metadata\.plugins\org.eclipse.core.resources OR
3. Remove temp file from this directory <workspace-directory>\.metadata\.plugins\org.eclipse.wst.server.core

当然更多是要注意 console 中给出的错误信息来进行改正

