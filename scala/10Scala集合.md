## 十 Scala 集合

在使用 Scala 集合类方法时有几个重要的概念必须知道：

- 谓词：谓词就是一个方法，一个函数或者一个匿名函数，接受一个或多个参数，返回一个 Boolean 值。
- 匿名函数：

```
(i: Int) => i % 2 == 0
_ % 2 == 0
```

- 隐式循环: 在 Scala 中像 filter, foreach, map, reduceLeft 和许多类似集合的方法都有自带算法的循环。



#### 1. Scala 中的集合的层级结构

![image-20200913155456290](C:%5Cdata%5Csoftware%5CTypora%5Cnote%5CScala%E7%AC%94%E8%AE%B0%5Cimage-20200913155456290.png)