# 每日一题

## 2019.12.10 【数据类型】【装箱/拆箱】【中等】

Q:为什么下列代码能够实现`1+1=4`？

```
Field valueField = Integer.class.getDeclaredField("value");
valueField.setAccessible(true);
valueField.setInt(1, 2);

Integer a = 1, b = 1;
System.out.println("1+1=" + (a + b));
```

A: 因为自动装/拆箱和缓存的存在。

**必须强调，这个例子是出于学习的目的，绝对绝对不要在工作中写这样的代码！**

如果你有兴趣，你可以观察一下上述代码的字节码，它等价于：

```
Field valueField = Integer.class.getDeclaredField("value");
valueField.setAccessible(true);
valueField.setInt(Integer.valueOf(1), 2);

Integer a = Integer.valueOf(1), b = Integer.valueOf(1);
System.out.println("1+1=" + (a.intValue() + b.intValue()));
```

其中编译器偷偷摸摸帮你插入的`Integer.valueOf`/`intValue()`就是我们耳熟能详的“自动装箱/拆箱机制”。

如果你对字节码不熟悉，也没关系，只要在对应的方法里打一个断点调试即可。

因此，你看到的`Integer a = 1`，实际上是`Integer.valueOf(1)`对象。那么，请你打开这个方法，阅读一下它的代码，会发现，它对-128~127范围内的整数，内部维护了一个缓存，这是[Java语言规范](https://docs.oracle.com/javase/specs/jls/se8/html/jls-5.html#jls-5.1.7)所要求的：

> If the value p being boxed is an integer literal of type int between -128 and 127 inclusive (§3.10.1), or the boolean literal true or false (§3.10.3), or a character literal between '\u0000' and '\u007f' inclusive (§3.10.4), then let a and b be the results of any two boxing conversions of p. It is always the case that a == b.

所以，看起来我们在操作数字1，实际上我们只是在操作`Integer.valueOf(1)`返回的不知道是什么鬼的对象。这个对象内部的数据，被反射篡改了：

```
Field valueField = Integer.class.getDeclaredField("value");
valueField.setAccessible(true);
valueField.setInt(Integer.valueOf(1), 2);
```

因此，你看到的`a+b`实际上是`a.intValue()+b.intValue()`，对这两个对象调用方法获取一个（被篡改后的）值，那么自然也就会输出令人惊讶的结果了。
