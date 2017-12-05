## java中的异常

### 继承关系
Exception和Error都继承自Throwable

### throw和catch
throw出Throwable类型的对象（包括子类）

catch到Throwable类型的对象（包括子类）

```
try {
	throw new Error();
} catch (Exception e) {
	e.printStackTrace();
}catch ( Error  e) {
	System.out.println( "q" );
	e.printStackTrace();
}
```

### finally

不管 try 语句块正常结束还是异常结束，finally语句块都是要被执行的

在try语句块或catch语句块中执行到System.exit(0)直接退出程序

finally块中的return语句会覆盖try块中的return返回

finally 语句块在 catch语句块中的return语句之前执行
