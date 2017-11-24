## java.lang.Runtime类和Process

#### 简介

Runtime类封装了运行时的环境。每个 Java 应用程序都有一个 Runtime 类实例，使应用程序能够与其运行的环境相连接。

一般不能实例化一个Runtime对象，应用程序也不能创建自己的 Runtime 类实例，但可以通过 getRuntime 方法获取当前Runtime运行时对象的引用。

一旦得到了一个当前的Runtime对象的引用，就可以调用Runtime对象的方法去控制Java虚拟机的状态和行为。 

当Applet和其他不被信任的代码调用任何Runtime方法时，常常会引起SecurityException异常。

### 获取jvm的全部内存，空闲内存，最大内存

```java

Runtime r = Runtime.getRuntime();
System.out.println(r.totalMemory());
System.out.println(r.freeMemory());
System.out.println(r.maxMemory());
```

### 运行子进程

```java
Runtime r = Runtime.getRuntime();
Process p = r.exec("java -version");
InputStream is = p.getErrorStream();
BufferedReader reader = new BufferedReader(new InputStreamReader(is));

String line;
while ((line = reader.readLine()) != null) {
	System.out.println(line);
}
//等待子进程退出,因此会造成阻塞
p.waitFor();
//获取子进程退出的返回值，0为正常退出。
int code = p.exitValue();
System.out.println(code);
```
### gc和exit
垃圾回收和程序退出