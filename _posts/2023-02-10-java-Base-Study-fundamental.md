---
layout: post
title: "Java Base Study——Fundamentals"
date: 2023-02-10
description: "Java学习笔记"
tag: Java
---

万万没想到，丢了这么多年的Java要重新捡起来

# Java简介

Java介于编译型语言和解释型语言之间。编译型语言如C、C++，代码是直接编译成机器码执行，但是不同的平台（x86、ARM等）CPU的指令集不同，因此，需要编译出每一种平台的对应机器码。解释型语言如Python、Ruby没有这个问题，可以由解释器直接加载源码然后运行，代价是运行效率太低。而Java是将代码编译成一种“字节码”，它类似于抽象的CPU指令，然后，针对不同平台编写虚拟机JVM，不同平台的虚拟机负责加载字节码并执行，这样就实现了“一次编写，到处运行”的效果。

JDK：Java Development Kit

JRE：Java Runtime Environment

简单地说，JRE就是运行Java字节码的虚拟机。但是，如果只有Java源码，要编译成Java字节码，就需要JDK，因为JDK除了包含JRE，还提供了编译器、调试器等开发工具。

![JDK与JRE的关系](\assets\java notes\2023-02-10-java-Base-Study-fundamental\1.png)

在JDK里面有许多可执行文件：

- java：这个可执行程序其实就是JVM，运行Java程序，就是启动JVM，然后让JVM执行指定的编译后的代码；
- javac：这是Java的编译器，它用于把Java源码文件（以`.java`后缀结尾）编译为Java字节码文件（以`.class`后缀结尾）；
- jar：用于把一组`.class`文件打包成一个`.jar`文件，便于发布；
- javadoc：用于从Java源码中自动提取注释并生成文档；
- jdb：Java调试器，用于开发阶段的运行调试。

# Java数据类型

1.基本数据类型：

- 整数类型：byte（1个字节），short（2个字节），int（4），long（8，long型结尾需要加L）

- 浮点数：float（4，需要加 f 后缀），double（8）

  浮点数在计算机中常常无法精确表示，并且计算可能出现误差，因此，判断浮点数相等用`==`判断不靠谱，正确的方法是利用差值小于某个临界值来判断：

  ```java
  public class Main {
      public static void main(String[] args) {
          double x = 1 - 9.0 / 10;
          if (Math.abs(x - 0.1) < 0.00001) {
              System.out.println("x is 0.1");
          } else {
              System.out.println("x is NOT 0.1");
          }
      }
  }
  ```


- 字符类型：char（1，使用单引号）

- 布尔类型：boolean

  短路运算：如果一个布尔运算的表达式能提前确定结果，则后续的计算不再执行，直接返回结果。因为`false && x`的结果总是`false`，无论`x`是`true`还是`false`，因此，与运算在确定第一个值为`false`后，不再继续计算，而是直接返回`false`。

2.常量：

定义变量前加上`final`，初始化后不可再赋值；

3.var关键字：

```java
StringBuilder sb = new StringBuilder();
var sb = new StringBuilder();
```

4.引用类型：

1）字符串String：引用类型的变量类似于C语言的指针，它内部存储一个“地址”，指向某个对象在内存的位置。字符串具有不可变特性。Java的字符串除了是一个引用类型外，还有个重要特点，就是字符串不可变。

```java
public class Main {
    public static void main(String[] args) {
        String s = "hello";
        String t = s;
        s = "world";
        System.out.println(t); // t是"hello"还是"world"?
    }
}
```

上述例子，t还是hello。执行`String s = "hello";` JVM虚拟机先创建字符串`"hello"`，然后，把字符串变量`s`指向它，`String t = s;` t的指针指向`"hello"`，紧接着，执行`s = "world";`时，JVM虚拟机先创建字符串`"world"`，然后，把字符串变量`s`指向它，原来的字符串`"hello"`还在，只是我们无法通过变量`s`访问它而已，可以通过`t`去访问。

**判断引用类型相等**

在Java中，判断值类型的变量是否相等，可以使用`==`运算符。但是，判断引用类型的变量是否相等，`==`表示“引用是否相等”，或者说，是否指向同一个对象。要判断引用类型的变量内容是否相等，必须使用`equals()`方法：

```java
public class Main {
    public static void main(String[] args) {
        String s1 = "hello";
        String s2 = "HELLO".toLowerCase();
        System.out.println(s1);
        System.out.println(s2);
        if (s1.equals(s2)) { // 如果是s1 == s2 则是false
            System.out.println("s1 equals s2");
        } else {
            System.out.println("s1 not equals s2");
        }
    }
}
```

要忽略大小写比较，使用`equalsIgnoreCase()`方法。

```java
// 是否包含子串:
"Hello".contains("ll"); // true
"Hello".indexOf("l"); // 2
"Hello".lastIndexOf("l"); // 3
"Hello".startsWith("He"); // true
"Hello".endsWith("lo"); // true
"Hello".substring(2); // "llo"
"Hello".substring(2, 4); //"ll"
"  \tHello\r\n ".trim(); // "Hello"
"\u3000Hello\u3000".strip(); // "Hello"
" Hello ".stripLeading(); // "Hello "
" Hello ".stripTrailing(); // " Hello"
"".isEmpty(); // true，因为字符串长度为0
"  ".isEmpty(); // false，因为字符串长度不为0
"  \n".isBlank(); // true，因为只包含空白字符
" Hello ".isBlank(); // false，因为包含非空白字符

// 替换
String s = "hello";
s.replace('l', 'w'); // "hewwo"，所有字符'l'被替换为'w'
s.replace("ll", "~~"); // "he~~o"，所有子串"ll"被替换为"~~"

// 正则
String s = "A,,B;C ,D";
s.replaceAll("[\\,\\;\\s]+", ","); // "A,B,C,D"

// 拼接
String[] arr = {"A", "B", "C"};
String s = String.join("***", arr); // "A***B***C"

// 格式化字符串
public class Main {
    public static void main(String[] args) {
        String s = "Hi %s, your score is %d!";
        System.out.println(s.formatted("Alice", 80));
        System.out.println(String.format("Hi %s, your score is %.2f!", "Bob", 59.5));
    }
}

// 类型转换
String.valueOf(123); // "123"
String.valueOf(45.67); // "45.67"
String.valueOf(true); // "true"
int n1 = Integer.parseInt("123"); // 123

```

# Java流程控制

## 条件判断

```java
if(...){
    ...
} else {
    ...
}

if(...){
    ...
} else if(...){
    ...
} else {
    ...
}
```

## switch多重选择

类似于sql里面的case when语句：

```java
public class Main {
    public static void main(String[] args) {
        int option = 99;
        switch (option) {
        case 1:
            System.out.println("Selected 1");
            break;
        case 2:
            System.out.println("Selected 2");
            break;
        case 3:
            System.out.println("Selected 3");
            break;
        default:
            System.out.println("Not selected");
            break;
        }
    }
}
```

每个`case`里面一定要写`break`,因为`case`具有穿透性，不写break，后面的语句都会继续执行；

使用`switch`时，如果遗漏了`break`，就会造成严重的逻辑错误，而且不易在源代码中发现错误。从Java 12开始，`switch`语句升级为更简洁的表达式语法，使用类似模式匹配（Pattern Matching）的方法，保证只有一种路径会被执行，并且不需要`break`语句：

```java
public class Main {
    public static void main(String[] args) {
        String fruit = "apple";
        switch (fruit) {
        case "apple" -> System.out.println("Selected apple");
        case "pear" -> System.out.println("Selected pear");
        case "mango" -> {
            System.out.println("Selected mango");
            System.out.println("Good choice!");
        }
        default -> System.out.println("No fruit selected");
        }
    }
}
```

## while 循环

```java
while (条件表达式) {
    循环语句
}
// 继续执行后续代码
```

## do while 循环

先执行循环，再判断条件：

```java
public class Main {
    public static void main(String[] args) {
        int sum = 0;
        int n = 1;
        do {
            sum = sum + n;
            n ++;
        } while (n <= 100);
        System.out.println(sum);
    }
}
```

## for 循环

`for`循环的功能非常强大，它使用计数器实现循环。`for`循环会先初始化计数器，然后，在每次循环前检测循环条件，在每次循环后更新计数器。计数器变量通常命名为`i`。

`for`循环经常用来遍历数组，因为通过计数器可以根据索引来访问数组的每个元素：

```java
int[] ns = { 1, 4, 9, 16, 25 };
for (int i=0; i<ns.length; i++) {
    System.out.println(ns[i]);
}
```

for each可以更加简单的遍历数组：除了数组外，`for each`循环能够遍历所有“可迭代”的数据类型，包括`List`、`Map`等。

```java
public class Main {
    public static void main(String[] args) {
        int[] ns = { 1, 4, 9, 16, 25 };
        for (int n : ns) {
            System.out.println(n);
        }
    }
}
```

# Java数据结构

## 数组

数组是同一数据类型的集合，数组一旦创建后，大小就不可变

数组遍历

```java
// method 1
public class Main {
    public static void main(String[] args) {
        int[] ns = { 1, 4, 9, 16, 25 };
        for (int i=0; i<ns.length; i++) {
            int n = ns[i];
            System.out.println(n);
        }
    }
}
// method 2
public class Main {
    public static void main(String[] args) {
        int[] ns = { 1, 4, 9, 16, 25 };
        for (int n : ns) {
            System.out.println(n);
        }}}

```

打印数组内容

Java标准库提供了`Arrays.toString()`，可以快速打印数组内容：

```java
import java.util.Arrays;

public class Main {
    public static void main(String[] args) {
        int[] ns = { 1, 1, 2, 3, 5, 8 };
        System.out.println(Arrays.toString(ns));
    }
}
```

数组排序

调用JDK提供的`Arrays.sort()`就可以排序：

```
import java.util.Arrays;

public class Main {
    public static void main(String[] args) {
        int[] ns = { 28, 12, 89, 73, 65, 18, 96, 50, 8, 36 };
        Arrays.sort(ns);
        System.out.println(Arrays.toString(ns));
    }
}
```

对于基础属性数组，变量`ns`指向的数组的内容已经发生改变了，但是对于一个字符串数组进行排序：

```java
String[] ns = {'banana','apple','pear'}
```

调用`Arrays.sort(ns);`排序后，原来的3个字符串在内存中均没有任何变化，但是`ns`数组的每个元素指向变化了。

## List

有序列表：`List`的行为和数组几乎完全相同：`List`内部按照放入元素的先后顺序存放，每个元素都可以通过索引确定自己的位置，`List`的索引和数组一样，从`0`开始。

```java
// List创建
List<String> list = new ArrayList<>(); // 只能放入String类型
List<Integer> list = List.of(1, 2, 5); // 用of可以将值填进去

// 在末尾添加一个元素
list.add("a");

// 在指定索引添加一个元素
list.add(2, 'b');

// 删除指定索引的元素
list.remove(2);

// 删除某个元素
list.remove('a');

//语法使用 list.set(下标，修改后元素)
list.set(1,100); //将下标为1处的元素改为100

// 获取链表大小（包含元素的个数）
list.size();

//for  each循环
for (Object o1 : list) {
    System.out.println(o1);
}

list.contains("a");
list.indexOf('a') // 返回某个元素的索引
    
```

## Map

key-value 映射表

```java
// map的创建与遍历
import java.util.HashMap;
import java.util.Map;

public class Main {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();
        map.put("apple", 123);
        map.put("pear", 456);
        map.put("banana", 789);
        for (String key : map.keySet()) {
            Integer value = map.get(key);
            System.out.println(key + " = " + value);
        }
    }
}
```

`keySet()`返回包含不重复的key集合；

`entrySet()`返回包含每一个`key-value`映射；

```java
import java.util.HashMap;
import java.util.Map;

public class Main {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();
        map.put("apple", 123);
        map.put("pear", 456);
        map.put("banana", 789);
        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            String key = entry.getKey();
            Integer value = entry.getValue();
            System.out.println(key + " = " + value);
        }
    }
}

```

