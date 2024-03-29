# Java简介

java之父：James Goslin, java是一种编译型语言，需要通过java虚拟机（jvm）编译成计算机语言使得计算机能够运行！

编译型语言：C、C++ 、java

解释型语言：python,ruby,javascript 解释器直接加载源码然后运行，代价是运行效率太低。



# Java安装jdk和jre

自行百度



# Java基本语法

## 数据类型

### 基本数据类型

1. 整形 ： byte short  int  long    1 ,2 , 4 , 8

| 类型  | 占位 | 最大值                     | 最小值                    | 默认值 |
| ----- | ---- | -------------------------- | ------------------------- | ------ |
| byte  | 8    | -128                       | 127                       | 0      |
| short | 16   | -32768                     | 32767                     | 0      |
| int   | 32   | -2,147,483,648             | 2,147,483,647             | 0      |
| long  | 64   | -9,223,372,036,854,775,808 | 9,223,372,036,854,775,807 | 0L     |

2. 浮点数

| 类型   | 占位 | 默认值 |
| ------ | ---- | ------ |
| float  | 32   | 0.0f   |
| double | 64   | 0.0d   |

两种浮点类型都不能表示精确的值

3. 布尔值

| 类型    | 占位 | 默认值 |
| ------- | ---- | ------ |
| boolean | 1    | false  |

4. 字符

| 类型 | 占位           | 最小值              | 最大值                   |
| ---- | -------------- | ------------------- | ------------------------ |
| char | 16 unicode字符 | **\u0000**（即为0） | **\uffff**（即为65,535） |

由于字符是一张unicode 编码，所有理论上所有字符都有对应的数字，如下：

```java
char c = 65;
System.out.println(c); //A
int a = (int) c;
System.out.println(a);// 65
```



### 基本类型的运算

#### 基本运算

**需要注意的是 相同类型的数据进行运算只能得到这个类型的数据**，比如；

```java
int a = 1;
int b = 2;
System.out.println(a/b) //0
```

**但是整型、实型（常量）、字符型数据可以混合运算。运算中，不同类型的数据先转化为同一类型，然后进行运算。**

规则：

```java
低  ------------------------------------>  高

byte,short,char—> int —> long—> float —> double 
```

以上的计算为自动类型转换，而要使得高位的数据向低位转换，则需要强制类型转换，过程中可能会损失一些精度

```java
int a = 12;
byte b = (byte)a; //在赋值时用括号包起来时即可！
```

#### 短路运算

布尔运算的一个重要特点是短路运算。如果一个布尔运算的表达式能提前确定结果，则后续的计算不再执行，直接返回结果。

因为`false && x`的结果总是`false`，无论`x`是`true`还是`false`，因此，与运算在确定第一个值为`false`后，不再继续计算，而是直接返回`false`。

```java
public class Main {
    public static void main(String[] args) {
        boolean b = 5 < 3;
        boolean result = b && (5 / 0 > 0); //不会报错
        System.out.println(result);
    }
}
```



### 引用数据类型

java中所有的类都是一种引用数据类型

- 在Java中，引用类型的变量非常类似于C/C++的指针。**引用类型指向一个对象，指向对象的变量是引用变量**，**指向这个对象的内存地址**。这些变量在声明时被指定为一个特定的类型，比如 Employee、Puppy 等。变量一旦声明后，类型就不能被改变了。
- 对象、数组都是引用数据类型。
- 所有引用类型的默认值都是null。指该变量不指向任何变量！
- 一个引用变量可以用来引用任何与之兼容的类型。



### final关键字

1. final关键字用在基本数据类型中，表示，这个指向这个基本数据类型的变量不可被重新赋值，也就是所谓的常量

2. 而在应用数据类型中，如果变量指向一个引用数据类型，则表示这个变量不能指向新的对象，也就是此变量指向的内存地址是固定的，不变的，但是这个对象里的构造函数是可变的，属性值也是可变的，只是不能指向新的对象。

3. 修饰一个方法时，表示这个方法不可以被@override,覆写

4. 修饰一整个类时，表示这个类不能被继承

5. 修饰一个类中的属性时，表示这个属性（其实也是变量）不能被重新赋值。

6. 可以先声明后赋值，以下两种方式都是可以的：

   ```java
   final int a = 10;
   final int b;
   b = 10;
   ```

### 字符和字符串

char 字符 ，是一种基本数据类型，而字符串 String 是一种java内置的引用类型数据。

**基本类型的变量是“持有”某个数值，引用类型的变量是“指向”某个对象；**

而字符串有这几种特性：

连接的便捷性：+ 和js一样，java也可以简单的用`+`进行连接，这是java编译时的优化。与其他数据类型连接时也会将其他数据类型转换为字符串

不可变：字符串是一种引用数据类型，该变量指向的只是该实例化字符串的内存地址，所以重新赋值后，实际上这个变量指向的是一个新的内存地址，一个新的实例化字符串！

```java
String a = "123";
System.out.println(a.hashCode());  //48690
a = "456";
System.out.println(a.hashCode());  //51669
```

### 定长数组

1. 定义形式 

   `数据类型(基本|引用)+[]+变量名 =  new +数据类型[长度] | newnew +数据类型[]{初始化数据} |{初始化数据} `

   ```java
   public class Main {
       public static void main(String[] args) {
           String[] names = {"ABC", "XYZ", "zoo"};
           String s = names[1];
           names[1] = "cat";
           System.out.println(s); // s是"XYZ"还是"cat"?
       }
   }
   ```

   以上代码的结果是“xyz”,原因如下：

   s变量指向的对象是字符串 “xyz”,name[1] 重新指向了另一个对象“cat”,此时s指向的对象“xyz”并没有发生改变，所以无妨!

2. 多维数组

   例子：

   ```java
   int[][] ns = {
       { 1, 2, 3, 4 },
       { 5, 6, 7, 8 },
       { 9, 10, 11, 12 }
   };
   System.out.println(ns.length); // 3
   System.out.println(ns[0][1]); //2
   
   for(int[] a : ns){
       for(int b : ns){
           System.out.pringln(b)
       }
   }
   ```

   以上的数组就是一个二维数组，就是数组里套数组，有几维，定义时就写几个`[]`,访问方式也是如此！

   要想获取其中的所有元素，就用两层循环！

## 命令行参数

在java的入口方法main方法中，String[] args就是我们所谓的命令行参数，那它既然是参数，我们如何在调用时赋值的，答案就是在命令行执行时赋值：

```
public class Main {
    public static void main(String[] args) {
        for (String arg : args) {
            if ("-version".equals(arg)) {
                System.out.println("v 1.0");
                break;
            }
        }
    }
}
```

编译好后

```
$ java Main -version
v 1.0 
```

在编辑器中也能实现

Edit Confugration -->Program arguments输入参数即可！

## 流程控制

### 条件语句

1. if else
2. switch 使用`switch`时，注意`case`语句并没有花括号`{}`，而且，`case`语句具有“*穿透性*”，漏写`break`将导致意想不到的结果，所以`defaul`默认值和`break`建议一定带上！

### 循环

for

while

do while

数组或集合遍历循环

```java
for(数据类型+变量 : 数组变量名/数组 ){
	System.out.println(变量)
}
```



# java面向对象初识

java 是一门面向对象的语言，所有的代码都写在class(类)里。

而且类的名字和java文件名必须保持一致，建议每个类都首字母大写，以便区分！

**一个文件一个类！**

入口(Entry of Program)：指程序运行的起点。

只有main方法可以作为程序的入口。

这是一个public static修饰的方法，所以他是一个静态的方法。返回值是void类型，也就是无返回值。方法名是main。参数是String数组。

当运行程序的时候，通常都是以main方法作为起点，以main方法中的第一条语句作为程序的第一条语句。由于main方法是一个静态的方法，所以该方法放在任何一个类中都是可以执行的(剪切粘贴到哪里都可以。。)

而各个类，通过功能的不同，可以分为不同的功能包，Model，View，Control，Model是模型，也就是存放各种数据类型，封装成各个类，cat,dog。View视图层，Control控制层，也就是来控制各种逻辑代码！

## 作用域

### public

定义为`public`的`class`、`interface`可以被其他任何类访问：

```java
package abc;

public class Hello {
    public void hi() {
    }
}
```

定义为`public`的`field`、`method`可以被其他类访问，前提是首先有访问`class`的权限：

```java
package abc;

public class Hello {
    public void hi() {
    }
}
```

上面的`hi()`方法是`public`，可以被其他类调用，前提是首先要能访问`Hello`类：

```java
package xyz;

class Main {
    void foo() {
        Hello h = new Hello();
        h.hi();
    }
}
```

定义为`private`的`field`、`method`不能被其他类访问，只能是内部私有的。

实际上，确切地说，`private`访问权限被限定在`class`的内部，而且与方法声明顺序*无关*。推荐把`private`方法放到后面，因为`public`方法定义了类对外提供的功能，阅读代码的时候，应该先关注`public`方法：

```
package abc;

public class Hello {
    public void hello() {
        this.hi();
    }

    private void hi() {
    }
}
```

由于Java支持嵌套类，如果一个类内部还定义了嵌套类，那么，嵌套类拥有访问`private`的权限：（只有在这一种情况下，private才能修饰内部类）