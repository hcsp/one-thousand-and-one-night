# 每日一题

2019.12.10 【数据类型】【装箱/拆箱】【中等】

为什么下列代码能够实现`1+1=4`？

```
Field valueField = Integer.class.getDeclaredField("value");
valueField.setAccessible(true);
valueField.setInt(1, 2);

Integer a = 1, b = 1;
System.out.println("1+1=" + (a + b));
```
