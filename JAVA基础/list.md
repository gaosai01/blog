## java基础知识之 list

本文主要讲解 Vector ， ArrayList， LinkedList 和 CopyOnWriteList

### ArrayList 和 LinkedList

这两个数据结构线程不安全，一个是数组，一个是链表

### Vector

线程安全的数据结构，数组

遍历不安全

遍历时对vector对象加锁就可以实现线程安全。

### CopyOnWriteArrayList

线程安全