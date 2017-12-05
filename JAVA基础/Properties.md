## Properties 配置文件使用

#### 问：为何而生
答：因为有时候当项目发布到正式环境中时，java生成的生成class文件不方便修改配置。比如常见的log4j的日志输出文件目录，开发环境和正式环境不一样，通过修改.porperties文件就可以改变java程序的执行效果，有类似于ioc控制反转的意思。这样方便了运维人员的工作。

#### System.getProperties();

#### jvm的配置

```java
//可以输出所有的jvm配置,比如java.version，java.home等
Properties pps = System.getProperties();
pps.list(System.out);
```

#### 获取jvm的一些版本信息，系统信息

```java

private static String sysProp(String property) {
    try {
         return System.getProperty(property);
    }
    catch (SecurityException ex) {
         System.err.println("SecurityException on try find system property '"
         + property + "'");
         return "";
    }
}

public static final String OS_NAME = sysProp("os.name").toLowerCase();
public static final String JAVA_SPECIFICATION_VERSION = sysProp("java.specification.version");
public static final String JAVA_VERSION = sysProp("java.version");

// ------------------------- java版本
private static boolean isJavaVersion(String versionPrefix) {
     return JAVA_SPECIFICATION_VERSION.startsWith(versionPrefix);
}

public static boolean isJava1_1 = isJavaVersion("1.1");
public static boolean isJava1_2 = isJavaVersion("1.2");
public static boolean isJava1_3 = isJavaVersion("1.3");
public static boolean isJava1_4 = isJavaVersion("1.4");
public static boolean isJava1_5 = isJavaVersion("1.5");
public static boolean isJava1_6 = isJavaVersion("1.6");
public static boolean isJava1_7 = isJavaVersion("1.7");
public static boolean isJava1_8 = isJavaVersion("1.8");
public static boolean isJava1_9 = isJavaVersion("9");

```

#### 加载自己的配置文件
有教程存在6种加载方式，此处列举一种吧。

```java
Properties p = new Properties();
p.load(new FileInputStream(new File("ketvalue.porperties")));
String value = p.getProperty("mykey");
System.out.println(value);
```