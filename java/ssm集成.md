# spring + spring mvc + mybatis + log4j 项目集成

### 不要适合新手阅读，只做自己日常回顾
### 首先声明项目采用maven做项目jar包管理

### 1. 集成 spring 和 spring mvc

先加入jar包依赖

```
	<properties>
		<!-- spring版本号 -->
		<spring.version>4.0.2.RELEASE</spring.version>
		<!-- mybatis版本号 -->
		<mybatis.version>3.2.6</mybatis.version>
		<mybatis.spring.version>1.2.2</mybatis.spring.version>
		<!-- log4j日志文件管理包版本 -->
		<slf4j.version>1.7.7</slf4j.version>
		<log4j.version>1.2.17</log4j.version>
		<mysql.version>5.1.30</mysql.version>
		<druid.version>1.1.2</druid.version>

	</properties>
	
		<!-- spring核心包 -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-core</artifactId>
			<version>${spring.version}</version>
		</dependency>

		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-web</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-oxm</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-tx</artifactId>
			<version>${spring.version}</version>
		</dependency>

		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-jdbc</artifactId>
			<version>${spring.version}</version>
		</dependency>

		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-webmvc</artifactId>
			<version>${spring.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-aop</artifactId>
			<version>${spring.version}</version>
		</dependency>

		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context-support</artifactId>
			<version>${spring.version}</version>
		</dependency>

		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-test</artifactId>
			<version>${spring.version}</version>
		</dependency>
```

然后在web.xml上配置spring 和 spring mvc的配置文件

```
	<!-- 配址spring的bean锁在的位置 -->
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:conf/spring-*.xml</param-value>
	</context-param>
	<!-- spring通过监听tomcat启动进行bean配置 -->
	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>
	<!-- 配置spring mvc有关信息 -->
	<servlet>
		<servlet-name>SpringMVC</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<!-- spring mvc有关bean的配置路径 -->
			<param-value>classpath:springmvc-servlet.xml</param-value>
		</init-param>
		<load-on-startup>1</load-on-startup>
	</servlet>
	<servlet-mapping>
		<servlet-name>SpringMVC</servlet-name>
		<url-pattern>/</url-pattern>
	</servlet-mapping>
	
	<!-- 编码过滤器 -->
	<filter>
		<filter-name>encodingFilter</filter-name>
		<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
		<!-- 3.0下的注解 -->
		<async-supported>true</async-supported>
		<init-param>
			<param-name>encoding</param-name>
			<param-value>UTF-8</param-value>
		</init-param>
	</filter>
	<filter-mapping>
		<filter-name>encodingFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
	
```

然后各个bean的配置文件,先列举和spring mvc有关的吧

springmvc-servlet.xml文件配置

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:mvc="http://www.springframework.org/schema/mvc"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
	xsi:schemaLocation="http://www.springframework.org/schema/beans 
                        http://www.springframework.org/schema/beans/spring-beans-4.0.xsd 
                        http://www.springframework.org/schema/mvc 
                        http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd 
                        http://www.springframework.org/schema/context 
                        http://www.springframework.org/schema/context/spring-context-4.0.xsd 
                        http://www.springframework.org/schema/aop 
                        http://www.springframework.org/schema/aop/spring-aop-4.0.xsd 
                        http://www.springframework.org/schema/tx 
                        http://www.springframework.org/schema/tx/spring-tx-4.0.xsd">
	<!-- 设置使用spring mvc有关注解（@controller等）的类所在的包 -->
	<context:component-scan base-package="com.gaosai" />
	<mvc:annotation-driven />
	
	<!-- <mvc:resources location="/assets/" mapping="/assets/*"></mvc:resources> -->
</beans>
```

### mybatis配置

首先加入jar包依赖

```
		<!-- mybatis核心包 -->
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis</artifactId>
			<version>${mybatis.version}</version>
		</dependency>
		<!-- mybatis/spring包 -->
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis-spring</artifactId>
			<version>${mybatis.spring.version}</version>
		</dependency>

		<!-- 导入Mysql数据库链接jar包 -->
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<version>${mysql.version}</version>
		</dependency>
		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>druid</artifactId>
			<version>${druid.version}</version>
		</dependency>

```

然后通过spring进行mybatis相关bean的配置

```

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xsi:schemaLocation="http://www.springframework.org/schema/beans    
                        http://www.springframework.org/schema/beans/spring-beans-3.1.xsd    
                        http://www.springframework.org/schema/context    
                        http://www.springframework.org/schema/context/spring-context-3.1.xsd    
                        http://www.springframework.org/schema/mvc    
                        http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd">

	<!-- 引入properties配置文件 -->
	<bean id="propertyConfigurer"
		class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
		<property name="locations">
			<list>
				<value>classpath:custom/jdbc.properties</value>
			</list>
		</property>
	</bean>

	<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource"
		init-method="init" destroy-method="close">
		<property name="driverClassName" value="${jdbc.driver}" />
		<property name="url" value="${jdbc.url}" />
		<property name="username" value="${jdbc.username}" />
		<property name="password" value="${jdbc.password}" />
		<!-- 初始化连接大小 -->
		<property name="initialSize" value="${jdbc.initialSize}"></property>
		<!-- 连接池最大数量 -->
		<property name="maxActive" value="${jdbc.maxActive}"></property>
		<!-- 连接池最大空闲 -->
		<property name="maxIdle" value="${jdbc.maxIdle}"></property>
		<!-- 连接池最小空闲 -->
		<property name="minIdle" value="${jdbc.minIdle}"></property>
		<!-- 获取连接最大等待时间 -->
		<property name="maxWait" value="${jdbc.maxWait}"></property>
	</bean>

	<!-- spring和MyBatis完美整合，不需要mybatis的配置映射文件 -->
	<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<property name="dataSource" ref="dataSource" />
		<!-- 自动扫描mapping.xml文件 -->
		<property name="mapperLocations" value="classpath:mapping/*.xml"></property>
	</bean>

	<!-- DAO接口所在包名，Spring会自动查找其下的类 -->
	<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
		<property name="basePackage" value="com.gaosai.idp.mapping" />
		<property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"></property>
	</bean>

	<!-- (事务管理)transaction manager, use JtaTransactionManager for global tx -->
	<bean id="transactionManager"
		class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="dataSource" />
	</bean>

</beans>

```

其中数据库连接池使用了druid，有关jdbc的properties文件配置

```
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/demo?useUnicode=true&characterEncoding=utf8&autoReconnect=true&rewriteBatchedStatements=TRUE
jdbc.username=root
jdbc.password=123456
jdbc.initialSize=0
jdbc.maxActive=20
jdbc.maxIdle=20
jdbc.minIdle=1
jdbc.maxWait=60000
jdbc.minEvictableIdleTimeMillis=300000
jdbc.timeBetweenEvictionRunsMillis=60000
```

有关mybatis的mapping文件设置，xml文件 可以使用 （https://github.com/mybatis/generator/） 这个工具自动生成xml，实体，还有 class


### log4j配置

首先加入jar包依赖

```
		<!-- log start -->
		<dependency>
			<groupId>log4j</groupId>
			<artifactId>log4j</artifactId>
			<version>${log4j.version}</version>
		</dependency>
		<!-- 格式化对象，方便输出日志 -->
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-api</artifactId>
			<version>${slf4j.version}</version>
		</dependency>
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-log4j12</artifactId>
			<version>${slf4j.version}</version>
		</dependency>
		<!-- log end -->
```

然后在web.xml里面进行配置

```
	<!-- 加载log4j的配置文件log4j.properties -->
    <context-param>
        <param-name>log4jConfigLocation</param-name>
        <param-value>classpath:log4j.properties</param-value>
    </context-param>

    <!-- 设定刷新日志配置文件的时间间隔，这里设置为10s -->
    <context-param>
        <param-name>log4jRefreshInterval</param-name>
        <param-value>10000</param-value>
    </context-param>

    <!-- 加载Spring框架中的log4j监听器Log4jConfigListener -->
    <listener>
        <listener-class>org.springframework.web.util.Log4jConfigListener</listener-class>
    </listener>

```


然后配置log4j.properties文件

```
# Root logger option
log4j.rootLogger=DEBUG, stdout, file

# Redirect log messages to console
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target=System.out
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} %-5p %c{1}:%L - %m%n

# Redirect log messages to a log file
log4j.appender.file=org.apache.log4j.RollingFileAppender
#outputs to Tomcat home
log4j.appender.file.File=/Users/mac/Desktop/xx.log
log4j.appender.file.MaxFileSize=1MB
log4j.appender.file.MaxBackupIndex=10
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} %-5p %c{1}:%L - %m%n

```

然后在class中使用log4j

```

import org.apache.log4j.Logger;

public class  Test{

	private static Logger logger = Logger.getLogger(Test.class);
	
	public void log(){
	
		logger.info("info ");
		logger.error("error");
		logger.warn("warn");
		
	}
	
}
```

log4j中等级

FATAL       0  
ERROR      3  
WARN       4  
INFO         6  
DEBUG      7 


 