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


## 2019.12.11 【集合】【搜索技巧】【guava】【简单】

给定两个List，如`[黑桃, 红心, 梅花, 方块]`和`[A, 2, 3, 4, 5, 6, ..., J, Q, K]`

返回一个新的52个元素的List，包含各种花色和数字的组合，即`[[黑桃, A], [红心, A], [梅花, A], [方块, A], [黑桃, 2], [红心, 2], [梅花, 2], [方块, 2], ..., [黑桃, K], [红心, K], [梅花, K], [方块, K]]`。

我相信你能自己写出来。我的问题是，你能不能通过搜索，找到一个第三方库，完成上述功能？这样，你就不用自己实现一遍，直接调用别人写好的方法就行了。

锻炼一下自己的搜索技能吧！

A: 在数学上，这种操作叫做`笛卡尔积`，对应的英文是`Cartesian product`，用这些关键词去搜索，就可以很容易地找到Guava中有一个`Sets/Lists.cartesianProduct`方法可以直接拿来用。

下次，当想要自己实现一个东西时，先按捺一下自己激动的心，去搜索一下有没有成熟的、久经考验的第三方库可以使用！

## 2019.12.13 【Java基础】【方法调用】【值传递】【简单】

小明想要用下列代码找出一个字符串中，大写字母、小写字母和数字分别有多少个，但是结果很明显不对。请帮小明找到问题并修复程序中的bug。

```
    public static void main(String[] args) {
        int upperCase = 0, lowerCase = 0, digit = 0;
        count("AbC1DefG230", upperCase, lowerCase, digit);
        System.out.println("upperCase: " + upperCase + ", lowerCase: " + lowerCase + ", digit: " + digit);
    }

    // 统计字符串中的大写字符、小写字符、数字出现的个数
    public static void count(String s, int upperCase, int lowerCase, int digit) {
        for (char ch : s.toCharArray()) {
            if (Character.isUpperCase(ch)) {
                upperCase++;
            } else if (Character.isLowerCase(ch)) {
                lowerCase++;
            } else if (Character.isDigit(ch)) {
                digit++;
            }
        }
    }
```
