# Java方法传值的那些故事

### 基本类型和引用类型的区别

当你声明一个东西的时候，你需要先搞清楚它是基本类型还是引用类型。

```
int num = 10;
String str = "hello";
```

![1](https://pic3.zhimg.com/80/166032bc90958c21604110441ad03f45_hd.jpg)

如图所示，num是基本类型，值就直接保存在变量中。而str是引用类型，变量中保存的只是实际对象的地址。一般称这种变量为"引用"，引用指向实际对象，实际对象中保存着内容。

### 赋值运算符的作用

```
num = 20;
str = "java";
```

对于基本类型 num ，赋值运算符会直接改变变量的值，原来的值被覆盖掉。对于引用类型 str，赋值运算符会改变引用中所保存的地址，原来的地址被覆盖掉。

**但是原来的对象不会被改变（非常重要！！！）**。

![1](https://pic4.zhimg.com/80/287c0efbb179638cf4cf27cbfdf3e746_hd.jpg)

如上图所示，"hello" 字符串对象没有被改变。（没有被任何引用所指向的对象是垃圾，会被垃圾回收器回收）

### 调用方法时发生了什么？

参数传递本质上是创建参数的副本开始方法调用。如果参数是基本数据类型，则复制一份；如果参数是引用数据类型，则复制一份「引用」，即对象的地址。现在，在方法中操作的参数和外界传递的参数是**两份不同的数据**。这也是为什么下面的代码中，对应的参数没有被改变的原因。因为方法里面操作的根本就是一个数据的副本啊！

```
// 第一个例子：基本类型
void foo(int value) {
    value = 100;
}
foo(num); // num 没有被改变

// 第二个例子：引用类型
void foo(String text) {
    text = "windows";
}
foo(str); // str 也没有被改变
```

因此，在这个代码中，

```
public class Home {
    public static Cat cat;

    public static void main(String[] args) {
        newCat(cat); // cat只是一个地址（一个数字），例如123456，意思是在内存的123456处你可以找到这个对象。
        System.out.println(cat);
    }

    public static void newCat(Cat cat) {
        // 现在开始方法调用，传递进来的这个cat是地址的拷贝（副本）
        // 注意看，IDEA会用黑色提示你
        // 现在的这个操作是在让一个地址重新指向别的地方
        // 例如原先cat是123456，现在我让它指向654321处的另外一个对象
        // 注意，因为这个数字是外面传进来的数字的拷贝，所以外面的地址不会受影响
        cat = new Cat("Tom");
    }
}
```

一旦把代码改成这样：

```
public class Home {
    public static Cat cat;

    public static void main(String[] args) {
        newCat();
        System.out.println(cat);
    }

    public static void newCat() {
        // 注意看，cat变红了。为什么，因为它不再是参数了，而是静态成员变量的地址了。
        // 修改它会立刻反馈到静态成员变量中。
        cat = new Cat("Tom");
    }
}
```
