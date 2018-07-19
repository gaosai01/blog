# lamdba-jdk8特性

### FunctionInterface

### Runable

### Consumer

消费者

### BiConsumer

两个物件的消费者

### Predicate

断言， 比较，判断，作为stream的filter过滤，true才能返回

### 用法1 ()->{}

### 用法2 ::

类的静态方法   （都做为函数参数）

对象的普通方法 （都做为函数参数）

类的普通方法 （两个参数，一个作为对象，一个作为函数参数）  String::compareTo  === x.compareTo(b)