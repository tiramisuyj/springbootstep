---------------- 集成jdbc----------------------------------------
1.引入依赖：
mysql-connector-java:MySql连接Java的驱动程序
spring-boot-starter-jdbc:支持通过JDBC连接数据库
<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
</dependency>
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
2.配置信息：
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/springbootstep?characterEncoding=utf-8&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=yj123!@#
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
3.建表语句和sql：
-- ----------------------------
-- Table structure for ay_user
-- ----------------------------
DROP TABLE IF EXISTS `ay_user`;
CREATE TABLE `ay_user`  (
  `id` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL,
  `name` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL,
  `password` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8mb4 COLLATE = utf8mb4_0900_ai_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of ay_user
-- ----------------------------
INSERT INTO `ay_user` VALUES ('1', '阿毅', '123456');
INSERT INTO `ay_user` VALUES ('2', '阿兰', '123456');



----------------------------集成Druid----------------------------------------------
1.Druid概述：Druid是阿里巴巴开源项目中的一个数据库连接池。Druid是一个JDBC组件，包括三个部分：
a.DruidDriver代理Driver，能够提供基于Filter-Chain模式的插件体系；
b.DruidDataSource高效可管理的数据库连接池；
c.SQLParser，支持所有JDBC兼容的数据库，包括Oracle，MySql，SqlServer等。
Druid在监控，可扩展，稳定性和性能方面具有明显的优势，通过其提供的监控功能可以观察数据库连接池和SQL查询的情况，使用Druid连接池
可以提高数据库的访问性能。
2.引入依赖：
<dependency>
	<groupId>com.alibaba</groupId>
	<artifactId>druid</artifactId>
	<version>1.1.4</version>
</dependency>



---------------------------集成Spring Data Jpa-------------------------------------
Jpa(java persistence api)是sun 官方提出的java持久化规范，所谓规范，即只定义标准规则，不提供实现。而jpa的主要实现有
hibernate/EclipseLink/OpenJPA等，jpa是一套规范，不是一套产品，hibernate是一套产品，如果这些产品实现了jpa规范，
那么我们就可以称其为jpa的实现产品。
Spring Data Jpa是Spring Data的一个子项目，通过提供基于JPA的Respository极大地减少了JPA作为数据访问方案的代码量。
通过Spring Data JPA框架，开发者可以省略实现持久层业务逻辑的工作，唯一要做的就是声明持久层的接口，其它都交给Spring Data
Jpa来完成。
Spring Data JPA最顶层的接口是Repository，该接口是所有Repository类的父类

---------------------------集成thymeleaf--------------------------------------------
thymeleaf是一个优秀的，面向java的xml/xhtml/html5页面模板，具有丰富的标签语言和函数。关于thymeleaf表达式/标签/函数等更多的
内容，大家可以到官网http://www.thymeleaf.org参考学习
1.引入依赖
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
注意：使用springboot 2.1.2.RELEASE会有问题，界面显示不出来，原因待排查，改用1.5.17.RELEASE



---------------------------集成filter和listener--------------------------------------------------
1.Filter：过滤器，是处于客户端与服务器资源文件之间的一道过滤网，它是Servlet技术中最激动人心的技术之一，web开发人员通过Filter技术管理
web服务器的所有资源，例如对jsp/servlet/静态图片文件或静态html文件等进行拦截，从而实现一些特殊的功能，如实现url级别的权限访问控制，
过滤敏感词汇，压缩响应信息等一些高级功能。
Filter接口源代码如下：
public interface Filter{
	void init(FilterConfig var1) throws ServletException;
	void doFilter(ServletRequest var1,ServletResponse var2,FilterChain var3) throws IOException,ServletException;
	void destroy();
}
2.Filter的创建和销毁由web服务器负责。web应用程序启动时，web服务器将创建Filter的实例对象，并调用其init方法，读取web.xml配置，完成对象
的初始化功能，从而为后续的用户请求做好拦截的准备工作（Filter对象只会创建一次，init方法只会执行一次）。开发人员通过init方法的参数可获得代表
当前filter配置信息的FilterConfig对象。
3.当客户请求访问与过滤器关联的URL时，过滤器将先执行doFilter方法， FilterChain参数用于访问后续过滤器。filter对象创建后会驻留在内存中，当
web应用移除或服务器停止时才销毁。在web容器卸载Filter对象之前，destroy被调用。该方法在Filter的生命周期中仅执行一次，在这个方法中，可以释放
过滤器使用的资源。
4.Filter可以有多个，一个个Filter组合起来就形成一个FilterChain，也就是我们所说的过滤链，FilterChain执行遵循先进后出的原则；当客户端
发送一个Request请求时，这个Request请求会先经过FilterChain，由它利用dofilter()方法调用各个子Filter，至于子filter的执行顺序如何，
则看客户端是如何制定规则的。当Request请求被第一个Filter处理后，又通过dofilter()往下传送，被第二个，第三个。。。。。。Filter截获处理。
当request请求被所有的filter处理之后，返回的顺序是从最后一个开始返回，直接返回给客户端。
5.使用：
新建Filter类：AyUserFilter(添加注解：@WebFilter(filterName = "ayUserFilter",urlPatterns = "/*")，implements Filter)
启动类添加注解：@ServletComponentScan(@ServletComponentScan：使用该注解后，servlet/filter/listener可以直接通过
@WebServlet，@WebFilter，@WebListener注解自动注册，无须其它代码)


1.listener，也叫监听器，是servlet的监听器，可以用于监听web应用中某些对象，信息的创建，销毁，增加，修改，删除等动作的发生，然后做出相应的
响应处理。当范围对象的状态发生变化时，服务器自动调用监听对象中的方法，常用于统计在线人数和在线用户，系统加载时进行信息初始化，统计网站的访问量等。。
2.根据监听对象可以把监听器分为3类，ServletContext（对应application）/HttpSession（对应session）/ServletRequest（对应
request）。Application在整个web服务中只有一个，在web服务关闭时销毁。session对应每个会话，在会话起始时创建，一端关闭会话时销毁。
request对象是客户发送请求时创建的（一同创建的还有response），用于封装请求数据，在一次请求处理完毕时销毁。
3.根据监听的事件，可以把监听器分为以下3类
监听对象创建与销毁，如ServletContextListener。
监听对象域中属性的增加和删除，如HttpSessionListener和ServletRequestListener。
监听绑定到Session上的某个对象的状态，如ServletContextAttributeListener/HttpSessioinAttributeListener/ServletRequestAttributeListener等
4.使用
新建Listener类：AyUserListener(添加注解：@WebListener， implements ServletContextListener)


-----------------------------集成redis------------------------------------------
1.windows redis安装：
a.解压：Redis-x64-3.2.100.rar
b.用cmd进入解压的目录，运行命令redis-server redis.windows.conf，启动redis服务，此时当关闭命令窗口redis服务也回关闭
c.运行redis-cli.exe
d.可以将redis服务安装成windows服务，在命令窗口输入：redis-server --service-install redis.windows.conf即可
f.Redis可视化管理工具：RedisStudio，百度云连接：http://pan.baidu.com/s/1gfIbLar  密码：mpne

ps：listener中也实现redis：AyUserListener
当我们的数据存放到redis的时候，建和值都是通过spring提供的Serializer序列化到数据库的,redisTemplate使用的时JdkSerializationRedisSerializer，
而StringRedisTemplate默认使用StringRedisSerializer，所以，我们需要让用户ayUser实现序列化接口：Serializable，具体实现如下：
public class AyUser implements Serializable{

使用：
引入依赖：
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
application.properties添加属性值：
# redis配置
## 默认redis数据库为db0
spring.redis.database=0
## 服务器地址，默认为localhost
spring.redis.host=localhost
## 链接端口，默认为6379
spring.redis.port=6379
## redis默认密码为空
spring.redis.password=


------------------------------集成Log4j2--------------------------
介绍：
1.Log4j组成：记录器（ALL,DEBUG,INFO,WARN,ERROR,FATAL,OFF）,输出源（console,files）,布局（格式化输出内容）
2.log4j支持两种配置文件格式，一种是xml格式的文件，一种是java特性文件log4j2.properties。properties文件简单易读，而
xml文件可以配置更多的功能（比如过滤），没有好坏，能够融汇贯通就好
使用说明：
1.引入依赖
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-log4j2</artifactId>
</dependency>
springboot默认使用Logback日志框架来记录日志，并用INFO级别输出到控制台。所以我们在引入Log4j2之前，
需要先排除该包的依赖，再引入Log4j2的依赖，具体方法：
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
	<exclusions>
		<!-- 排查springboot默认日志 -->
		<exclusion>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-logging</artifactId>
		</exclusion>
	</exclusions>
</dependency>
2.application.properties添加配置：
### log4j配置
logging.config=classpath:log4j2.xml
3.创建log4j2.xml文件
打印到控制台(<Console/> :指定控制台输出，<PatternLayout/>:控制日志的输出格式)
<?xml version="1.0" encoding="UTF-8" ?>
<Configuration status="WARN">
	<appenders>
		<Console name="Console" target="SYSTEM_OUT">
			<!-- 指定日志的输出格式 -->
			<PatternLayout pattern="[%d{HH:mm:ss:SSS}] [%p] - %l - %m%n" />
		</Console>
	</appenders>
	<loggers>
		<root level="info">
			<!-- 控制台输出 -->
			<appender-ref ref="Console" />
		</root>
	</loggers>
</Configuration>


-------------------------集成Quartz定时器和发送Email---------------------------
使用
1.引入依赖
<dependency>
	<groupId>org.quartz-scheduler</groupId>
	<artifactId>quartz</artifactId>
	<version>2.2.3</version>
</dependency>
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-mail</artifactId>
</dependency>
2.添加mail配置
### Mail邮件配置
### 邮箱主机
spring.mail.host=smtp.163.com
### 用户名
spring.mail.username=ay_test@163.com
### 设置授权码
spring.mail.password=ay123456
### 默认编码
spring.mail.default-encoding=UTF-8
spring.mail.properties.mail.smtp.auth=true
spring.mail.properties.mail.smtp.starttls.anable=true
spring.mail.properties.mail.smtp.starttls.required=true

ps：注意设置授权码

介绍：
邮件发送和接收的过程如下：
a.发件人使用SMTP协议传输邮件到邮件服务器A
b.邮件服务器A根据邮件中指定的接收者投送邮件至相应的邮件服务器B
c.收件人使用POP3协议从邮件服务器B接收邮件
SMTP（Simple Mail Transfer Protocol）是电子邮件（Email）传输的互联网标准，定义在RFC5321，默认使用25端口。
POP3（Post Office Protocol-Version 3）主要用于支持使用客户端远程管理在服务器上的电子邮件，定义在RFC1939，为
POP协议的第三版（最新版）
这两个协议都属于TCP/IP协议组的应用层协议，运行在TCP层



--------------------------集成MyBatis----------------------------------
介绍：MyBatis是一款优秀的持久层框架，支持定制化Sql/存储过程/高级映射。MyBatis避免了几乎所有的JDBC代码和手动设置参数以及
获取结果集。MyBatis可以使用简单的xml或者注解来配置和映射原生信息，将接口和java的POJOS（Plain Old Java Objects，
普通的java对象）映射成数据库中的记录
@Mapper：重要注解，注解在dao上
使用：
1.引入依赖：
<dependency>
	<groupId>org.mybatis.spring.boot</groupId>
	<artifactId>mybatis-spring-boot-starter</artifactId>
	<version>1.3.1</version>
</dependency>
### mybatis配置
mybatis.mapper-locations=classpath:/mappers/*Mapper.xml
mybatis.type-aliases-package=com.cnc.springbootstep.mybatis.dao


----------------------------集成activeMQ------------------------------------
使用：
1.引入依赖：
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-activemq</artifactId>
</dependency>
2.添加配置：
### activeMq配置
spring.activemq.broker-url=tcp://localhost:61616
spring.activemq.in-memory=true
spring.activemq.pool.enabled=false
spring.activemq.packages.trust-all=true
3.下载：apache-activemq-5.15.8-bin.zip包，运行bin里面的32/64位，
默认启动端口8161，访问http://localhost:8161/admin

介绍：
操作：
创建表ay_mood：
DROP TABLE IF EXISTS `ay_mood`;
CREATE TABLE `ay_mood`  (
  `id` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL,
  `content` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL,
  `user_id` varchar(32) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL,
  `praise_num` int(11) NULL DEFAULT NULL,
  `publish_time` datetime(0) NULL DEFAULT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8mb4 COLLATE = utf8mb4_0900_ai_ci ROW_FORMAT = Dynamic;



--------------------------异步调用--------------------------------
使用：
1.在要进行异步调用的方法上添加注解：@Async
2.入口类上添加注解@EnableAsync开启异步调用


-----------------------全局异常处理---------------------------------------
使用：
1.在resources/static/目录下创建404.html
<!DOCTYPE HTML>
<html lang="en">
<head>
	<meta charset="utf-8" />
	<title>title</title>
</head>
<body>
	<div class="text" style="text-align:center">
		主人，我累了，让我休息一会
	</div>
</body>
</html>
2.新建error包，并创建ErrorPageConfig配置类：
@Configuration
public class ErrorPageConfig {
	
	@Bean
	public EmbeddedServletContainerCustomizer containerCustomizer() {
		return new EmbeddedServletContainerCustomizer() {
			
			@Override
			public void customize(ConfigurableEmbeddedServletContainer arg0) {
				ErrorPage error404Page = new ErrorPage(HttpStatus.NOT_FOUND,"/404.html");
				arg0.addErrorPages(error404Page);
			}
		};
	}

}
3.全局异常类的开发：BusinessException
4.异常信息类：ErrorInfo
5.异常处理类：
@ControllerAdvice//定义统一的异常处理类
public class GlobalDefaultExceptionHandler


----------------------集成mongodb-----------------------
1.mongodb下载地址：https://www.mongodb.com/download-center/community
2.mongodb客户端下载地址：https://www.mongodbmanager.com/download
3.mongodb安装：配置环境变量path即可（配置到bin那个目录）
4.mongodb启动：双击bsondump.exe
5.mongodb常用命令：
//显示所有数据库
>show dbs;
//使用test数据库
>use test;
//创建一个名为user的集合
>db.user.insert({"id":"1","name":"ay","age":"26"});
//查询user的集合
>db.user.find();
//将集合user名称为ay的记录更新为名称al
>db.user.update({"id":"1"},{"id":"1","name":"al","age":"26"});
//查询user的集合
>db.user.find();
//显示所有集合
>show collections;
//删除集合user名称为al的记录
>db.user.remove({"name":"al"});
//查询user集合
>db.user.find();

spring集合mongodb步骤：
1.引入依赖
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
2.添加属性配置：
### mongodb配置
### host地址
spring.data.mongodb.host=localhost
### 默认数据库端口号27017
spring.data.mongodb.port=27017
### 链接数据库名test
spring.data.mongodb.database=test


--------------------------------------集成Spring Security----------------------------
使用：
1.引入依赖：
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-security</artifactId>
</dependency>