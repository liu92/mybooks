## 1、lambda 表达式的基本结构：

 （param1,param2,param3）->{

}

如下基本示例

```java
package com.learn.jdk;

import javax.swing.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

/**
 * @ClassName: SwingTest
 * @Description:
 * @Author: lin
 * @Date: 2019/12/2 21:20
 * History:
 * @<version> 1.0
 */
public class SwingTest {
    public static void main(String[] args) {
        JFrame jframe = new JFrame("My JFrame");
        JButton jButton = new JButton("My JButton");
        // 给button增加一个事件处理器，就是点击一个按钮的时候执行特定的代码
        // 在jdk1.8 之前会这样使用匿名类，
        // 其实这里我们只需要 打印语句其他的都不需要去管。
//        jButton.addActionListener(new ActionListener() {
//            @Override
//            public void actionPerformed(ActionEvent e) {
//                System.out.println("Button ");
//            }
//        });
        // 而使用jdk1.8的lambda表达式 的写法很简洁
        // 左边是参数，中间 ->是箭头符号，右边是lambda表达式的执行体,
        jButton.addActionListener(e -> System.out.println("Button Hello"));
//        jButton.addActionListener(e -> {
//            System.out.println("Button Hello");
//            System.out.println("Button Hello");
//            System.out.println("Button Hello");
//        });
        // 在没有定义参数类型时 也可以执行是因为 java编译系统 有称为类型推断一种机制，
        // 他就是能推断 event类型一定是ActionEvent，不过一般不去定义因为写出来感觉有点多余
        // 注意：java编译系统不是都能推断处理类型，有些时候根据上下文它是推断不出来的，这个时候需要你显示的去定义
        
        jButton.addActionListener((ActionEvent event) -> System.out.println("Button Hello"));
        // 将按钮添加到JFrame上
        jframe.add(jButton);
        jframe.pack();
        // 事件的可行性
        jframe.setVisible(true);
        // 默认的 ，当关闭的时候整个程序退出
        jframe.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
    }
}

```



不过不要局限于这个结构还有其他很多的结构。

## 2、遍历集合



```java
package com.learn.jdk;

import java.util.Arrays;
import java.util.List;
import java.util.function.Consumer;

/**
 * jdk1.8 遍历集合
 * @ClassName: ListTest
 * @Description:
 * @Author: lin
 * @Date: 2019/12/2 22:20
 * History:
 * @<version> 1.0
 */
public class ListTest {
    public static void main(String[] args) {
        List<Integer> lists = Arrays.asList(1, 2, 3 , 4, 5, 6, 7, 8);
        // 以前的写法
        // 第一种
        for (int i = 0; i < lists.size(); i++) {
            System.out.println(lists.get(i));
        }
        System.out.println("---------------------");
        // 第二种
        for (Integer list : lists) {
            System.out.println(list);
        }

        System.out.println("---------------------");

        // 第三种, 这里
        lists.forEach(new Consumer<Integer>() {
            @Override
            public void accept(Integer integer) {
                System.out.println(integer);
            }
        });
        System.out.println("---------------------");
    }

}

```

第三种方式是jdk1.8 中的写法，这个方法中 使用了匿名类的方式来编写,  lists.forEach 会针对每一个元素去调用对应的Consumer中的 accept方法，然后将每一个被迭代的元素打印出来。

那么这个Consumer接口又是怎么定义的呢？ 这个接口上有注解是 @FunctionalInterface 函数式接口。

```java
@FunctionalInterface
public interface Consumer<T>
```

凡是一个接口它上面加了@FunctionalInterface 注解，我们称这个接口是函数式接口。

```java
// 凡是一个接口它上面加了@FunctionalInterface 注解，我们称这个接口是函数式接口。
* An informative annotation type used to indicate that an interface
 * type declaration is intended to be a <i>functional interface</i> as
 * defined by the Java Language Specification.
    
* Conceptually, a functional interface has exactly one abstract method    
    // 他有个一默认的实现，所以他不是抽象的 
* Since {@linkplain java.lang.reflect.Method#isDefault()
 * default methods} have an implementation, they are not abstract. If
    // 如果一个接口声明了一个抽象的方法，并且这个抽象方法覆盖了java.lang.Object中的public方法
 * an interface declares an abstract method overriding one of the
    // 那么这种也不能称为一个函数式接口，因为接口的任何实 都会有来自Object类 以及其他的实现。
 * public methods of {@code java.lang.Object}, that also does
 * <em>not</em> count toward the interface's abstract method count
 * since any implementation of the interface will have an
 * implementation from {@code java.lang.Object} or elsewhere.  
    //函数式的接口实例可以通过 lambda表达式来创建，或者方法引用来创建，或者构造方法引用来创建
 * <p>Note that instances of functional interfaces can be created with
 * lambda expressions, method references, or constructor references.   
 * // 如果某个类型被加上了 FunctionalInterface 注解的话，编译器就会被强制生成一个错误信息
 * <p>If a type is annotated with this annotation type, compilers are
 * required to generate an error message unless:
 *  // 什么错误信息呢？如果不能满足下面两个条件，编译器就会生成错误信息
 * <ul> 
      // 如果类型 但是不是一个注解类型、枚举或者类 那编译器就会报错
 * <li> The type is an interface type and not an annotation type, enum, or class.
     // 被注解的类型满足了函数式接口的这种要求
 * <li> The annotated type satisfies the requirements of a functional interface.
 * </ul>   
     // 然而 ,编译器会对应满足函数式接口定义的任意的接口都会把它当做一个函数式接口，而不管是不是咋这个接口上加上FunctionalInterface注解
  * <p>However, the compiler will treat any interface meeting the
 * definition of a functional interface as a functional interface
 * regardless of whether or not a {@code FunctionalInterface}
 * annotation is present on the interface declaration.    
    
 public @interface FunctionalInterface {}    
```

那么到底什么是函数接口 ，如下注释说明，

**从概念上讲，一个接口它只有一个抽象方法** .

```java
* Conceptually, a functional interface has exactly one abstract method    
```

一个接口只有一个抽象方法？在java8中 接口不仅可以包含抽象方法，还可以包含有具体实现的方法，这种方法称为默认方法。

所以 这里我们对函数式接口就有个基本的概念，一个接口里面只有唯一的一个抽象方法。那么这个就是一个抽象接口。 	



关于函数式接口：

1)、一个接口它只有一个抽象方法，那么该接口是一个函数式接口

2)、如果我们在某个接口上声明一个FunctionalInterface注解，那么编译器就会安装函数式接口的方式来要求改接口

3)、如果某个接口只有一个抽象方法，但是我们并没有给这个接口声明FunctionalInterface注解，那么编译器依旧会将该接口看作是函数式接口。



## 3、函数式接口 的使用

```java
package com.learn.jdk;

/**
 * @ClassName: MyInterfaceDemo
 * @Description:
 * @Author: lin
 * @Date: 2019/12/3 22:48
 * @History:
 * @<version> 1.0
 */
@FunctionalInterface
 interface MyInterfaceDemo {
    /**
     * te
     */
    void  test();

    /**
     * to
     * @return
     */
    @Override
    String toString();
}

/**
 * test2类
 * @return
 * @exception
 * @author lin
 * @Date 22:52 2019/12/3
 **/
public class Test2{
    public  void  myTest(MyInterfaceDemo myInterfaceDemo){
       System.out.println(1);
       myInterfaceDemo.test();
       System.out.println(2);
    }

   public static void main(String[] args) {
      Test2 t = new Test2();
      // 函数式编程，以前要写匿名函数就是下面 的写法,
      // 所以使用函数式编程方式来编写匿名类就比较方便
//      t.myTest(new MyInterfaceDemo() {
//         @Override
//         public void test() {
//            System.out.println("mytest");
//         }
//      });

      // 这里使用() 是因为函数式接口中的方法是没有参数的。
      t.myTest(()->{
         System.out.println("mytest");
      });

      MyInterfaceDemo demo = ()->{
         System.out.println("hello");
      };
      System.out.println("MyInterfaceDemo的类型==="+demo.getClass());
      System.out.println("父类型===="+demo.getClass().getSuperclass());
      System.out.println("实现了那些接口========="+demo.getClass().getInterfaces()[0]);
   }
}
```



## 4、接口中默认方法

接口中默认方法实现一方面保证了jdk8新特性的加入比如lambda表达式，函数式接口，另一方面由保证了和老版本的兼容性。

```java
// Iterble接口类中 默认方法实现 
 /**   针对于Iterble的每一个元素，去执行给定的动作，是一种动作，把行为传递给了方法，而不是把数据作为参数传递给方法
     * Performs the given action for each element of the {@code Iterable}
     * until all elements have been processed or the action throws an
     // 如果没有被这个实现类所指定的话，那么动作就会按照迭代的顺序来去执行。
     * exception.  Unless otherwise specified by the implementing class,
     * actions are performed in the order of iteration (if an iteration order
     // 是否抛出异常取决于调用者
     * is specified).  Exceptions thrown by the action are relayed to the
     * caller.
     *
     * @implSpec
     // 这是默认的行为
     * <p>The default implementation behaves as if:
     * <pre>{@code
     *     for (T t : this)
     *         action.accept(t);
     * }</pre>
     *
     * @param action The action to be performed for each element
     * @throws NullPointerException if the specified action is null
     * @since 1.8
     */
default void forEach(Consumer<? super T> action) {
        Objects.requireNonNull(action);
        for (T t : this) {
            action.accept(t);
        }
    }
```



## 5、Lambda的作用：

  ● Lambda表达式为Java添加了缺失的函数式编程特性，使我们能将函数当做一等公民看待。

  ● 在将函数作为一等公民的语言中，Lambda表达式的类型是函数。<font color=#FF0000 >但是在java中，Lambad表达式是对象</font> ，他们必须依附于一类特别的对象类型----函数式接口（Functional Interface）

为什么在java中 Lambda表示是对象呢？ 

```	java
# 如下面的示例 我们将lambda表达式 赋一个对象引用。 并且这个lambda的类型是demo.getClass(), 父类型是demo.getClass().getSuperclass(),实现的接口是 MyInterfaceDemo. 所以这一点 和其他函数式编程语言的非常大的区别。
    
      MyInterfaceDemo demo = ()->{
         System.out.println("hello");
      };
      System.out.println("MyInterfaceDemo的类型==="+demo.getClass());
      System.out.println("父类型===="+demo.getClass().getSuperclass());
      System.out.println("实现了那些接口========="+demo.getClass().getInterfaces()[0]);
```



## 6.1外部迭代

```
# 使用迭代器 移动位置来进行迭代
List<Integer> lists = Arrays.asList(1, 2, 3 , 4, 5, 6, 7, 8);
for(int li:lists){
  System.out.println(li);
}
```

### 6.2 内部迭代

```
# 内部迭代 完全是通过lambda表达式将 元素取出来进行迭代
lists.forEach(new Consumer<Integer>() {    
 @Override    
 public void accept(Integer integer) {        
  System.out.println(integer);    
 }
});
```

### 6.3 通过方法引用方式

```
// 通过方法引用来 创建函数接口的实例
// method reference , 这个其实就是上面的另一种形式
lists.forEach(System.out::println);
```



## 7、lambda表达式 深入与流初步

```java
package com.learn.jdk.chapter5;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

/**
 * chapter5
 * lambda表达式 深入与流初步
 * @ClassName: Demo5
 * @Description:
 * @Author: lin
 * @Date: 2019/12/24 22:32
 * History:
 * @<version> 1.0
 */
public class Demo5 {
    public static void main(String[] args) {
         // 如果没有 MyInterfaceDemo1 这些上下文，那么编译器无法去推断类型
         // 并且MyInterfaceDemo1 函数式接口中的方法，不接受参数，没有返回值  () -> {}.
//         MyInterfaceDemo1 interfaceDemo1 = () -> {};
//         System.out.println(interfaceDemo1.getClass().getInterfaces()[0]);
//
//         MyInterfaceDemo2 myInterfaceDemo2 = () -> {};
//         System.out.println(myInterfaceDemo2.getClass().getInterfaces()[0]);

         // 变量集合的中元素，然后转换成大写 输出，这里使用lambda表达式来处理
         List<String> list = Arrays.asList("hello", "world", "hello world");
         // 1、将结合中元素 转换成大写 输出
//         list.forEach(item -> System.out.println(item.toUpperCase()));
         // 2、构造一个新的集合然后 将转换之后的这些元素 放到新的的集合中去
         //List<String> list2 = new ArrayList<>();
         // 第一种写法
         //list.forEach(item -> list2.add(item.toUpperCase()));
         //list2.forEach(item -> System.out.println(item));
         // 第二种写法 ，使用流(stream)的方式来编写。 stream() 是串行 也就是单线程，
         // parallelStream()是并行流，也就是流是多线程来操作的。
         // map() ,中function就是一个函数，给定一个输入然后得到一个输出 (Function 函数式接口方法方法定义)
         // 结束，list 把它转换成一个流，这个流调用map方法，然后将流 它中的源(就是这里的集合 list)
         // 遍历集合中每一个元素 然后做一个映射 将其转换成大写， 转换成大写之后还是返回stream.
         // 所以调用foreach方法 把每个元素打印出来
         list.stream().map(item -> item.toUpperCase()).forEach(item ->            System.out.println(item));
         // 第三 通过方法引用方式来写, 一个输入参数，得到一个返回结果
         // 这里的输入是指调用了toUpperCase 这个方法的“对象”作为输入参数。
        list.stream().map(String::toUpperCase).forEach(item-> System.out.println(item));

    }
}

/**
 * 函数式接口
 */
@FunctionalInterface
interface MyInterfaceDemo1{
    /**
     * 抽象方法
     * method
     */
    void method();
}

/**
 *
 */
@FunctionalInterface
interface MyInterfaceDemo2{
    /**
     * 抽象方法
     * method2
     */
    void method2();
}
```

## 8、Function接口详解

```java
package com.learn.jdk.chapter6;

import java.util.Arrays;
import java.util.List;
import java.util.function.Function;

/**
 * chapter6
 * Function接口详解
 * @ClassName: Demo6
 * @Description:
 * @Author: lin
 * @Date: 2019/12/24 22:32
 * History:
 * @<version> 1.0
 */
public class Demo6 {
    public static void main(String[] args) {

        List<String> list = Arrays.asList("hello", "world", "hello world");
        // String::toUpperCase 这个 双冒号是方法引用
        // 通过一个String对象去调用 toUpperCase这个方法，这里String是个类，应该toUpperCase不是静态方法
        // 那么为什么通过方法引用就可以呢？ 这里要注意 对于这种通过 String 类等这种类型 然后 :: 跟着实例方法
        // 情况下 它的第一个输入参数 一定就是调用了这个toUpperCase方法的对象，
        // 换句话说就是在真正调用toUpperCase的时候 一定会存在一个字符串类型的对象，然后通过这个对象去调用toUpperCase方法
        //
        // Function 有两个参数，一个是输入参数，一个是返回参数。
        // 下面定义输入和输出都是string类型
        Function<String, String> function = String::toUpperCase;
        System.out.println(function.getClass().getInterfaces()[0]);
        // 结果interface java.util.function.Function
    }
}

/**
 * 函数式接口
 */
@FunctionalInterface
interface MyInterfaceDemo1{
    /**
     * 抽象方法
     * method
     */
    void method();
}

/**
 *
 */
@FunctionalInterface
interface MyInterfaceDemo2{
    /**
     * 抽象方法
     * method2
     */
    void method2();
}
```

### 8.1、function接口使用示例

```java
package com.learn.jdk.chapter6;

import java.util.Arrays;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;

/**
 * chapter6 function接口详解
 * @ClassName: StringComparator
 * @Description: 字符串比较
 * @Author: lin
 * @Date: 2019/12/26 23:32
 * History:
 * @<version> 1.0
 */
public class StringComparator {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("zhangsan", "lisi", "wangwu", "zhaoliu");
        // 第一种、对集合排序
        // Collections.sort(list, new Comparator<String>() {
        // @Override
        // public int compare(String o1, String o2) {
        //   // 倒序排序
        //   return o2.compareTo(o1);
        //  }
        //  });
        //第二种排序, 这种排序方式 比较冗余，
        // 2 它会提示 你使用Comparator.reverseOrder() 方法来进行替换
        Collections.sort(list, (String o1, String o2)-> {
            return o2.compareTo(o1);
        });
        // 2.1 上面的还可以修改为, 因为这list本身是字符串类型的
        // 所以可以省略 o1,o2 类型声明
        Collections.sort(list , (o1, o2)->{
           return o2.compareTo(o1);
        });
        //2.2 还可以修改为下面的
        // expression o2.compareTo(o1)
        // statement {return o2.compareTo(o1);} 这是一个完整的语句 必须有分号
        Collections.sort(list, (o1,o2)-> o2.compareTo(o1));

        // Comparator.reverseOrder() 方法来替换上面的排序,
        // 这一行代码就可以代替上面的排序，但是这种方法脱离了lambda表达
        //Collections.sort(list, Comparator.reverseOrder());
         System.out.println(list);
    }
}
```

Tips: java lambda 表达是一种匿名函数；他是没有声明的方法，既没有访问修饰符，返回值声明和名字。

lambda表达式 作用

 ● 传递行为，而不仅仅是值 

 ● 提升抽象层次  

 ● API重用性更好 

 ● 更加灵活



Java Lambda结构

```
● 一个Lambda表达式可以有零个或者多个参数
● 参数的类型可以明确声明，也可以根据上下文推断。例如：(int a) 和 (a) 效果相同
● 所有参数需要包括在圆括号内,参数之间用逗号相隔。 例如：(a,b) 或 (int a, int b) 或
 (String a, int b, float c)
● 空圆括号代表参数集为空。 例如：()->42  参数为空返回42
● 当只有一个参数,且其类型可推导时，圆括号()可省略. 例如：a->return a*a
● Lambda表达式的主体可包含零条或多条语句
● 如果Lambda表达式的主体只有一条语句，花括号{} 可以省略. 匿名函数的返回类型与该主体表达式一致
● 如果Lambda表达的主体包含一条以上语句,则表达式必须包含在花括号{}中(形成代码块)。 匿名函数的返回类型与代码的返回类型一致，若没有返回则为空
```

### 8.2、function接口传递

```java
package com.learn.jdk.chapter6;

import java.util.function.Function;

/**
 * function接口详解
 * @ClassName: FunctionDemo
 * @Description: function接口详解
 * @Author: lin
 * @Date: 2019/12/27 10:12
 * History:
 * @<version> 1.0
 */
public class FunctionDemo {
    public static void main(String[] args) {
       FunctionDemo functionDemo = new FunctionDemo();
        // 参数1 是传递值，而Function是传递方法
        // 将 va->{return  2 * va ;} 作为一个动作或者行为传递给了下面的compute方法
        // 这种传递方式 和以前定义好 方法不同，这种方法事先不知道行为，
        // 而以前的方法 比如method1，method2 方法 这种是已经定义好的 行为方式
        System.out.println(functionDemo.compute(1, va->{return  2 * va ;}));
        System.out.println(functionDemo.compute(2, va -> 5 + va));
        System.out.println(functionDemo.compute(3 ,va->va* va));

        System.out.println(functionDemo.convert(5, va ->String.valueOf(va+ "hello")));
    }



    private int compute(int a , Function<Integer, Integer> function){
        // 将a 做为参数传入 ,
        // apply 执行什么样的动作或者行为 提前是不知道的，
        // 而是通过 方法的传递来告知的
        int result = function.apply(a);
        return  result;
    }

    private String convert(int b, Function<Integer, String> function){
        return  function.apply(b);
    }

    private int method1(int a){
        return  2 * a;
    }

    private int method2(int a){
        return  5 + a;
    }
}
```

Tips:高阶函数

```
如果一个函数接收一个函数作为参数，或者返回一个函数作为返回值，那么该函数就叫高阶函数
```

### 8.3 Function中 函数组合

```java
// compose 和将多个function串联在一起,
// 这个function函数式接口有一个输入, apply之后会得出一个返回结果，
// 然后这个输出结果又传递给了当前对象的apply方法. 这样做就形成了两个
// Function的串联，比如有function1 和function2, 那么把function2作为
// function1的 compose方法的参数传入进去,那么他的执行结果是先对输入参数
// 执行function2的apply, 
// 执行完之后 再将返回结果 再去作为当前的 funcion的输入参数
default <V> Function<V, R> compose(Function<? super V, ? extends T> before) {
        Objects.requireNonNull(before);
        return (V v) -> apply(before.apply(v));
    }

// andThen 方法
// 首先应用的是当前的Function,然后对应用完当前的function所得到的结果,
default <V> Function<T, V> andThen(Function<? super R, ? extends V> after) {
        Objects.requireNonNull(after);
        return (T t) -> after.apply(apply(t));
    }
```

### 8.4 BiFunction :接收两个参数并且得到一个结果

```
/**
  * Represents a function that accepts two arguments and produces a result.
  * This is the two-arity specialization of {@link Function}.
  *
  * <p>This is a <a href="package-summary.html">functional interface</a>
  * whose functional method is {@link #apply(Object, Object)}.
  *
  * @param <T> the type of the first argument to the function
  * @param <U> the type of the second argument to the function
  * @param <R> the type of the result of the function
  *
  * @see Function
  * @since 1.8
  */
 @FunctionalInterface
 public interface BiFunction<T, U, R>{}ccc
```

### 8.5 Function和BiFunction比较

```java
package com.learn.jdk.chapter7;

import java.util.function.BiFunction;
import java.util.function.Function;

/**
 * chapter7  function与 biFunction函数式接口详解
 * @ClassName: FunctionDemo2
 * @Description:  function与 biFunction函数式接口详解
 * @Author: lin
 * @Date: 2019/12/27 15:01
 * History:
 * @<version> 1.0
 */
public class FunctionDemo2 {
    public static void main(String[] args) {
        FunctionDemo2 function  = new FunctionDemo2();
        // 调用compose方法
        // 先计算后面的 va*va, 然后将其计算的结果传递当前对象的apply方法, 再去计算
        // va * 3
        // 2 * 2 -> 4 然后 4 * 3 =12
        System.out.println(function.compute1(2, va->va * 3, va -> va * va));
        // 调用andThen方法
        // 先计算前面的 va * 3, 然后将其计算的结果传递当前对象的apply方法, 再去计算
        // va * va
        // 2 * 3 =6 --> 6*6 =36
        System.out.println(function.compute2(2, va->va * 3, va -> va * va));

        // 输入两个参数 返回一个结果
        System.out.println(function.compute3(1, 2 ,(va1, va2) -> va1 + va2));
        System.out.println(function.compute3(1, 2 ,(va1, va2) -> va1 - va2));
        System.out.println(function.compute3(1, 2 ,(va1, va2) -> va1 * va2));
        System.out.println(function.compute3(1, 2 ,(va1, va2) -> va1 / va2));
        //  BiFunction lambda表达式 (v1, v2)-> v1 + v2
        // Function lambda表达式 v3-> v3 * v3
        // 首先将输入应用到 当前的biFunction, 也就是(v1, v2)-> v1 + v2
        // (v1, v2)-> v1 + v2的结果作为 参数的function 传入, 也就是 v3-> v3 * v3的输入
        System.out.println(function.compute4(2, 3, (v1, v2)-> v1 + v2, v3-> v3 * v3));
        // 思考： 为什么BiFunction没有 compose方法?
        // 个人理解： 如果有compose方法，那么在接收参数后，首先会调用apply(a),
        // 但是其引用返回的值只有一个，这样就你不能传入到 BiFunction中apply(a,b)方法去
    }

    /**
     * compose使用
     * @param t
     * @param function1
     * @param function2
     * @return
     */
    public  int compute1(int t, Function<Integer, Integer> function1, Function<Integer, Integer> function2){
        // 先去对输入参数应用 function2的 apply,
        // 然后把function2的结果作为function1的apply输入
        return  function1.compose(function2).apply(t);
    }

    /**
     * andThen使用
     * @param t
     * @param function1
     * @param function2
     * @return
     */
    public  int compute2(int t, Function<Integer, Integer> function1, Function<Integer, Integer> function2){
        // 先去对输入参数应用 function1的 apply,
        // 然后把function1的结果作为function2的apply输入
        return  function1.andThen(function2).apply(t);
    }

    /**
     * BiFunction使用
     * @param a
     * @param b
     * @param biFunction
     * @return
     */
    public int compute3(int a, int b, BiFunction<Integer, Integer, Integer> biFunction){
        return  biFunction.apply(a, b);
    }

    /**
     *  使用BiFunction中 andThen方法，
     *  注意这里andThen方法中接收的是Function,因为apply的返回值只有一个,
     *  然后这个值作andThen(Function<? super R, ? extends V> after) 方法中after为参数
     *  的输入，所以只有一个输入值 所以是Function类型。
     * @param a
     * @param b
     * @param biFunction
     * @param function
     * @return
     */
    public int compute4(int a, int b, BiFunction<Integer, Integer, Integer> biFunction, Function<Integer, Integer> function ){
        // 将BiFunction 调用andThen 方法，然后将其内部方法中调用apply(a,b)方法
        // 这个方法返回值只有一个，然后将这个返回值作为after参数 ,通过after调用 apply方法
        return biFunction.andThen(function).apply(a, b);
    }
}

```

### 8.6 BiFunction函数式接口实例

```java
package com.learn.jdk.chapter8;

import java.util.Arrays;
import java.util.List;
import java.util.function.BiFunction;
import java.util.stream.Collectors;

/**
 * BiFunction函数式接口实例演示
 * @ClassName: PersonDemo
 * @Description: BiFunction函数式接口实例演示
 * @Author: lin
 * @Date: 2019/12/27 17:07
 * History:
 * @<version> 1.0
 */
public class PersonDemo {
    public static void main(String[] args) {
        Person person1 = new Person("zairian", 20);
        Person person2 = new Person("lisa", 30);
        Person person3 = new Person("wangle", 40);
        List<Person> persons = Arrays.asList(person1, person2, person3);

        PersonDemo test = new PersonDemo();
//        List<Person> personResult = test.getPersonByUserName("lisa", persons);
//        personResult.forEach(person -> System.out.println(person.getUsername()));

        List<Person> personResult2 = test.getPersonByAge(20, persons);
        personResult2.forEach(person -> System.out.println(person.getAge()));

        List<Person> personResult3 = test.getPersonAge2(20 , persons, (age, personList)->
            personList.stream().filter(person -> person.getAge() > age).collect(Collectors.toList()));

        personResult3.forEach(person -> System.out.println(person.getAge()));

        System.out.println("===================================================");

        List<Person> personResult4 = test.getPersonAge2(20 , persons, (age, personList)->
                personList.stream().filter(person -> person.getAge() <= age).collect(Collectors.toList()));

        personResult4.forEach(person -> System.out.println(person.getAge()));
    }

    /**
     * 根据参数 来判断符合条件的元素，然后返回集合
     * @param userName
     * @param persons
     * @return
     */
    public List<Person> getPersonByUserName(String userName, List<Person> persons){
        // 这行代码的意思就是 将persons转换成流,然后过滤流中每一个元素,
        // filter 也是返回stream 对象, 这是stream的中间操作,每次操作完成后又返回这个对象,
        // 最后想把stream转换成 集合 可通过collect(Collectors.toList())进行转换。
        // 每一个元素要符合这个判断条件, 最后把它转换成list进行返回
         return  persons.stream().filter(person -> person.getUsername().equals(userName)).
                 collect(Collectors.toList());
    }

    /**
     * 判断集合中大于传入的 age 参数。
     * @param age
     * @param persons
     * @return
     */
    public  List<Person> getPersonByAge(int age, List<Person> persons){
        // 首先定义了一个BiFunction 这样一个对象, 然后他有两个参数,
        // 对于这两个参数 直接使用stream(流)的方式 去找到里面的每一个对象,
        // 这个对象的年龄要大于 ageOfPerson, 然后.collect(Collectors.toList()) 转换成一个集合.
        // 如果是statement {return o2.compareTo(o1);} 这是一个完整的语句, 那么必须有return
        // 下面可以替换 expression lambda , 也就是去掉花括号 {} 和return
        BiFunction<Integer, List<Person>, List<Person>> biFunction =
//                (ageOfPerson, personList)->{
//           return   personList.stream().filter(person -> person.getAge()> ageOfPerson).
//                    collect(Collectors.toList());
//        };
        (ageOfPerson, personList)-> personList.stream().filter(person -> person.getAge()> ageOfPerson).
                    collect(Collectors.toList());


        // 上面只是定义好了BiFunction, 这里才是应用它
        return  biFunction.apply(age, persons);
    }


    public List<Person> getPersonAge2(int age,List<Person> persons, BiFunction<Integer, List<Person>, List<Person>> biFunction){
        return  biFunction.apply(age, persons);
    }
}
```



## 9、 predicate 函数式接口

```java
package com.learn.jdk.chapter9;

import java.util.function.Predicate;

/**
 * chapter predicate
 * @ClassName: PredicateDemo
 * @Description: predicate 函数式接口
 * @Author: lin
 * @Date: 2019/12/29 21:53
 * History:
 * @<version> 1.0
 */
public class PredicateDemo {
    public static void main(String[] args) {
        Predicate<String> predicate = p ->p.length() > 6;
        // test方法的实现是 p ->p.length() > 6;
        System.out.println(predicate.test("hello11"));
    }
}

```

### 9.1 predicate 深入剖析和 函数式编程本质

```java
package com.learn.jdk.chapter9;

import java.util.Arrays;
import java.util.List;
import java.util.function.Predicate;

/**chapter10
 * @ClassName: PredicateDemo2
 * @Description:  predicate 深入剖析和 函数式编程本质
 * @Author: lin
 * @Date: 2019/12/29 22:05
 * History:
 * @<version> 1.0
 */
public class PredicateDemo2 {
    public static void main(String[] args) {
        List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        PredicateDemo2 predicateDemo2 = new PredicateDemo2();
        predicateDemo2.conditionFilter(list, item -> item % 2 == 0);
        System.out.println("------------------------------");
        predicateDemo2.conditionFilter(list, item -> item % 2 != 0);
        System.out.println("------------------------------");
        predicateDemo2.conditionFilter(list, item -> item > 5 );
        System.out.println("------------------------------");
        predicateDemo2.conditionFilter(list, item -> item > 3 );

        //所有的判断都为真
        predicateDemo2.conditionFilter(list, item-> true );
        //所有的判断都为假
        predicateDemo2.conditionFilter(list, item-> false );

        System.out.println("------------------------------");
        predicateDemo2.conditionFilter2(list, item -> item > 5, item -> item % 2 ==0);

        //
        System.out.println("isEqual======" + predicateDemo2.isEqual("test").test("test"));
    }



    /**
     * 函数编程和一起面向对象编程的区别，
     * 面向对象编程以前只需要接收 list, 具体怎么对这个参数进行处理，是由方法里面的代码来决定的
     * 函数编程实际上是把怎么进行处理，在调用的时候动态传入进来，相比原来在方法体里面编写业务处理逻辑，
     * 现在是将业务处理逻辑 抽取出来，然后放在调用者这一端。通过参数给动态的传入进来
     * @param list
     * @param predicate
     */
    public void  conditionFilter(List<Integer> list, Predicate<Integer> predicate){
        for (Integer integer : list) {
            //函数式编程它提供了一种更高层次的抽象化
            if(predicate.test(integer)){
                System.out.println(integer);
            }
        }
    }

    public void  conditionFilter2(List<Integer> list, Predicate<Integer> predicate, Predicate<Integer> predicate2){
        for (Integer integer : list) {
            //既要符合predicate的要求，也要符合predicate2的要求。然后才打印，否则不打印
            // predicate.and(predicate2).test(integer) ====> 6 ,8,10
            // predicate.or(predicate2).test(integer) ====> 2, 4, 6, 7, 8 ,9, 10
            // predicate.and(predicate2).negate().test(integer) ====> 1, 2, 3, 4, 5,  7, 9
            if(predicate.and(predicate2).negate().test(integer)){
                System.out.println(integer);
            }
        }
    }

    public Predicate<String> isEqual(Object object){
        return  Predicate.isEqual(object);
    }
}
```



### 9.2 其他的默认方法

```
1、表示的逻辑与，也就是当前的判断为假，后面的判断就不去计算了
/**
     * Returns a composed predicate that represents a short-circuiting logical
     * AND of this predicate and another.  When evaluating the composed
     * predicate, if this predicate is {@code false}, then the {@code other}
     * predicate is not evaluated.
     */
    default Predicate<T> and(Predicate<? super T> other) {
        Objects.requireNonNull(other);
        return (t) -> test(t) && other.test(t);
    }
2、表示的逻辑非，也就是取反
    /**
     * Returns a predicate that represents the logical negation of this
     * predicate.
     *
     */
    default Predicate<T> negate() {
        return (t) -> !test(t);
    }
3、表示的逻辑或者的关系
    /**
     * Returns a composed predicate that represents a short-circuiting logical
     * OR of this predicate and another.  When evaluating the composed
     * predicate, if this predicate is {@code true}, then the {@code other}
     * predicate is not evaluated.
     *
     */
    default Predicate<T> or(Predicate<? super T> other) {
        Objects.requireNonNull(other);
        return (t) -> test(t) || other.test(t);
    }

```



## 10.supplier 与函数式接口总结



```java
package com.learn.jdk.chapter11;

import java.util.function.Supplier;

/**
 * 通过使用supplier来生成 student的实例
 * @ClassName: StudentDemo
 * @Description: 通过使用supplier来生成 student的实例
 * @Author: lin
 * @Date: 2019/12/30 9:19
 * History:
 * @<version> 1.0
 */
public class StudentDemo {
    public static void main(String[] args) {
        //第一种
       Supplier<Student> supplier = () -> new Student();
       System.out.println(supplier.get().getName());
       System.out.println("-------------------");
       //第二种：方法引用,            构造方法引用，将鼠标放在new关键字上，就可以看到其实调用的是构造方法
        // Supplier 接口中定义，不接受参数 ，返回一个对象。所以调用构造方法符合这个定义
        // 如果 student类中 有多个构造方法，编译器会自动去找 不传入参数的构造方法。
        Supplier<Student> supplier2 = Student::new;
        System.out.println(supplier2.get().getName());
    }
}
```

### 10.1 supplier 与函数式接口总结

```java
/**
 * Chapter11 supplier
 * @ClassName: SupplierDemo
 * @Description: supplier 与函数式接口总结
 * @Author: lin
 * @Date: 2019/12/30 9:12
 * History:
 * @<version> 1.0
 */
public class SupplierDemo {
    public static void main(String[] args) {
        // 不接受参数，然后返回结果
        Supplier<String> supplier = () -> "hello world";
        //得到一个结果
        System.out.println(supplier.get());
    }
}
```

## 11、optional学习

```java
package com.learn.jdk.chapter12;

import java.util.Optional;

/**
 * chapter 12
 * @ClassName: OptionalDemo
 * @Description: optional 使用
 * @Author: lin
 * @Date: 2019/12/30 14:31
 * History:
 * @<version> 1.0
 */
public class OptionalDemo {
    public static void main(String[] args) {
        //下面的是传统的面向对象方式
        // of方法表示传入的参数不能为空
        Optional<String> optional = Optional.of("hhh");
        // 判断是否存在，如果存在则打印
        // 这种方式不是函数式 编程方式
//        if(optional.isPresent()){
//            System.out.println(optional.get());
//        }

        // 这里使用函数式的风格来重写
        // 推荐的Optional使用方式
        optional.ifPresent(item -> System.out.println(item));

        System.out.println("----------------------");
        // 如果optional 中不存在，则打印 下面的world
        System.out.println(optional.orElse("world"));
        System.out.println("---------");
        // 不接收参数
        // 如果optional 没有值，则调用supplier函数式接口 获取一个值打印出来。
        // 如果optional里面的值存在， 那么就不打印
        System.out.println(optional.orElseGet(() -> "li"));

    }
}
```



### 11.1 optional 深入详解

```java
package com.learn.jdk.chapter12;
import java.util.Arrays;
import java.util.Collections;
import java.util.List;
import java.util.Optional;

/**
 * chapter12
 * @ClassName: OptionalDemo2
 * @Description: optional 使用
 * @Author: lin
 * @Date: 2019/12/30 15:23
 * History:
 * @<version> 1.0
 */
public class OptionalDemo2 {
    public static void main(String[] args) {
      Employee employee = new Employee();
      employee.setName("lisa");

      Employee employee1 = new Employee();
      employee1.setName("zairian");

      Company company = new Company();
      company.setName("company1");
      List<Employee> employees  = Arrays.asList(employee, employee1);
      company.setEmployees( employees);
      // 如果company中不包含 任何employee信息, 那么就返回 一个空的集合
      // 如果存在就返回一个列表
      List<Employee> list = company.getEmployees();

      Optional<Company> optionals = Optional.ofNullable(company);
      //
      System.out.println(optionals.map(Company::getEmployees).
                orElse(Collections.emptyList()));
    }


    //Tips: 不要将Optional试图作为方法参数，也不要在类中定义Optional成员变量，
    // Optional 通常只作为方法的返回值，用来规避空指针异常这样一个问题
    // 编写Optional 一定要使用函数式的方式来编写
}
```



## 12、方法引用(method reference)方法引用实际上是Lambda表达式的一种语法糖

```java
/**
可以将方法引用看作是一个【函数指针】，function pointer
方法引用共分为4类:
1. 类名::静态方法名（注意不能传入参数，编译器会自动去识别）
2. 引用名(对象名，其实就是对象的一个引用)::实例方法名
3. 类名::实例方法名
4. 构造方法引用: 类名::new (实际上就是调用这个类的构造方法来生成对象)
*/
```



## 12.默认方法1、类方法实现接口，并且接口中有相同的方法

```java
package com.learn.jdk.chapter13.defaultmethod;

/**
 * @ClassName: MyClass
 * @Description:
 * @Author: lin
 * @Date: 2019/12/31 11:10
 * History:
 * @<version> 1.0
 */
public class MyClass implements MyInterface1 ,MyInterface2 {
    public static void main(String[] args) {
        MyClass myClass = new MyClass();
        // 如果该类实现了 MyInterface1和MyInterface2,
        // 并且两个接口有相同的方法，这种情况下会发生什么呢？
        // 注意: 在这种情况下 实现接口的类会去重写这个 默认方法。
        //  因为myclass 有一个defaultMethod实现，但是不知取那个，
        //  编译器是无从得知，所以我们显示的告诉编译， defaultMethod使用的哪一个
        myClass.defaultMethod();
    }

    @Override
    public void defaultMethod() {
        // 如果要使用MyInterface2中的方法那么
        // 使用这种方式,这样就可以使用MyInterface2中的方法了
        MyInterface2.super.defaultMethod();
//        System.out.println("hhhhh");
    }
}
```



### 12.2 默认方法:一个类继承实现类，并实现接口

```java
package com.learn.jdk.chapter13.defaultmethod;

import com.learn.jdk.chapter13.defaultmethod.impl.MyInterface1Impl;

/**
 * 继承实现类，实现接口
 * @ClassName: MyClass2
 * @Description:
 * @Author: lin
 * @Date: 2019/12/31 13:41
 * History:
 * @<version> 1.0
 */
public class MyClass2 extends MyInterface1Impl implements MyInterface2{
    public static void main(String[] args) {
        // 如果一个类继承了一个类和实现了一个接口,
        // 而继承的这个类重写了接口的默认的方法, 那么MyClass2就会使用
        // 重写了方法的 方法，而不会使用MyInterface2接口中默认方法
        MyClass2 myClass2 = new MyClass2();
        myClass2.defaultMethod();
        // 这里有一个java的设定: 实现类优先级要比接口的优先级高,
        // 这是因为实现类更为具体
    }
}
```



### 12.3 为什么接口可以有默认方法的实现? 这样设计的意义在于什么地方?

```java
1. 在新的特性要保证向后兼容, 保证既有接口中的方法不被破坏。
比如: 我们有一个类实现一个接口，而这个类要实现接口中相应方法，
如果在接口中有默认方法实现,那么实现接口的时候,
这个实现类就继承了默认方法。并这个类什么都不用做
```



### 12.4 方法引用的使用

```java
package com.learn.jdk.chapter13;

import java.util.Arrays;
import java.util.List;

/**
 * chapter13 方法引用
 * @ClassName: MethodReferenceDemo
 * @Description: 方法引用的使用
 * @Author: lin
 * @Date: 2019/12/30 16:09
 * History:
 * @<version> 1.0
 */
public class MethodReferenceDemo {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("hello", "world", "hello world");
        // 使用一般lambda表达式
        //list.forEach(item -> System.out.println(item));
        // 可以将方法引用看作是一个【函数指针】，function pointer
        // 也就是说指向一个函数的指针
        list.forEach(System.out::println);
    }
}
```



### 12.5 方法引用4中使用方式

```java
package com.learn.jdk.chapter13;

import java.util.Arrays;
import java.util.Collections;
import java.util.List;
import java.util.function.Function;
import java.util.function.Supplier;

/**
 *
 * @ClassName: MethodReferenceDemo2
 * @Description: 方法引用
 * @Author: lin
 * @Date: 2019/12/30 16:37
 * History:
 * @<version> 1.0
 */
public class MethodReferenceDemo2 {
    public static void main(String[] args) {
        /**
         * 第一种、类名::静态方法名
         */
        StudentDe student1 = new StudentDe("zairian", 20);
        StudentDe student2 = new StudentDe("lisp", 80);
        StudentDe student3 = new StudentDe("wangle", 50);
        StudentDe student4 = new StudentDe("zambia", 40);

        List<StudentDe> studentDes = Arrays.asList(student1, student2, student3, student4);

        // 这种方式是使用传统的lambda 方式来进行排序
        // 本质上就是接受两个参数，然后它调用了一个方法，而这个方法本身是客观存在的，
        // 所以下面方法引用通过类名来进行调用
//        studentDes.sort((studentParam1, studentParam2) ->
//                StudentDe.compareStudent1ByScore(studentParam1, studentParam2));
//        studentDes.forEach(studentDe ->
//        System.out.println("使用传统的lambda方式进行排序==="+studentDe.getScore()));

        System.out.println("----------------");
        // 使用方法引用的方式进行调用
        // 方法引用使用的要求，你使用的lambda表达式的这个实现也就是方法体
        // 它恰好有一个对应的方法跟他完成相同功能的 这么一个对应方法是客观存在的，
        // 那么这个时候就可以将lambda 替换为方法引用
//        studentDes.sort(StudentDe::compareStudent1ByScore);
//        studentDes.forEach(studentDe ->
//                System.out.println("使用方法引用的方式进行排序==="+studentDe.getScore()));

        // 根据字母排序
//        studentDes.sort((studentParam1, studentParam2) ->
//                StudentDe.compareStudent1ByName(studentParam1, studentParam2));
//        studentDes.forEach(studentDe ->
//        System.out.println("使用传统的lambda方式进行字母排序==="+studentDe.getName()));
//        studentDes.sort(StudentDe::compareStudent1ByName);
//        studentDes.forEach(studentDe ->
//        System.out.println("使用方法引用的方式根据字母排序==="+studentDe.getName()));


        /**
         * 第二种方式: 引用名(对象名，其实就是对象的一个引用)::实例方法名
         */
        // 先使用传统lambda方式
        StudentComparator studentComparator = new StudentComparator();
//        studentDes.sort((st1, st2) -> studentComparator.compareStudent1ByScore(st1, st2));
//        studentDes.forEach(studentDe ->
//        System.out.println("使用lambda方式排序"+studentDe.getScore()));
        // 使用方法引用方式

//        studentDes.sort(studentComparator::compareStudent1ByScore);
//        studentDes.forEach(studentDe ->
//               System.out.println("使用方法引用方式排序"+studentDe.getScore()));

        // 根据字母排序
//        studentDes.sort((st1, st2) -> studentComparator.compareStudent1ByName(st1, st2));
//        studentDes.forEach(studentDe ->
//               System.out.println("使用lambda方式排序"+studentDe.getName()));

//        studentDes.sort(studentComparator::compareStudent1ByName);
//        studentDes.forEach(studentDe ->
//                System.out.println("使用方法引用方式排序" + studentDe.getName()));

         // 第三种: 类名::实例方法名
         // 这里和上面两种的区别就是，上面的调用都很明确
         // 而下面这种就不是那么明确, 实例方法一定是对象来调用的,
         // 但是这里使用的是类名来调用实例方法。这是不可能直接调用实例方法的。
         // 那么这个实例方法一定是有那么一个对象来进行调用。
         // 那么这个对象就是sort 方法里面接收的 lambda表达式的"第一个参数" 来去调用的compareByScore
         // 如果接收多个参数 除了第一个参数之外，后续所有参数都做为这个方法的参数 传递进去
         // 就是类似 这样 (st1, st2)-> st1.compareByScore( st2),
         // 第一个参数 st1, 第二个参数st2 作为方法传进去的参数
//         studentDes.sort(StudentDe::compareByScore);
//         studentDes.forEach(studentDe ->
//                System.out.println("第三种使用类名::实例方法引用方式进行分数排序=="
//                        + studentDe.getScore()));

//         studentDes.sort(StudentDe::compareByName);
//         studentDes.forEach(studentDe ->
//                System.out.println("第三种使用类名::实例方法引用方式进行名字排序=="
//                        + studentDe.getName()));

         //例子
//         List<String> cis = Arrays.asList("sichuang","beijing","anhui","guangdong");
//         Collections.sort(cis, (cis1, cis2)-> cis1.compareToIgnoreCase(cis2));
//         cis.forEach(ci -> System.out.println(ci));
         // 这两种形式就是等价的
//         Collections.sort(cis, String::compareToIgnoreCase);
//         cis.forEach(System.out::println);


         MethodReferenceDemo2 methodDemo2 = new MethodReferenceDemo2();
         System.out.println(methodDemo2.getString(String::new));

         System.out.println(methodDemo2.getString2("hello", String::new));
    }

    public String getString(Supplier<String> supplier){
       return supplier.get() + "test";
    }

    public String getString2(String str, Function<String, String> function){
        return function.apply(str);
    }
}
```



### 12.6 方法引用中定义的比较方法

```java
package com.learn.jdk.chapter13;

/**
 * 方法引用2： 引用名(对象名，其实就是对象的一个引用)::实例方法名
 * 将方法放在一个新的类中，并且方法不是静态方法，是实例方法
 * @ClassName: StudentComparator
 * @Description:
 * @Author: lin
 * @Date: 2019/12/30 17:28
 * History:
 * @<version> 1.0
 */
public class StudentComparator {
    /**
     * 根据分数进行比较
     * @param student1
     * @param student2
     * @return
     */
    public  int compareStudent1ByScore(StudentDe student1, StudentDe student2){
        return  student1.getScore() - student2.getScore();
    }

    /**
     * 根据字母进行比较
     * @param student1
     * @param student2
     * @return
     */
    public  int compareStudent1ByName(StudentDe student1, StudentDe student2){
        return student1.getName().compareToIgnoreCase(student2.getName());
    }
}
```



### 12.7 方法引用中使用的实体

```java
ackage com.learn.jdk.chapter13;

import com.learn.jdk.chapter11.Student;

/**
 *
 * @ClassName: Student
 * @Description:
 * @Author: lin
 * @Date: 2019/12/30 9:17
 * History:
 * @<version> 1.0
 */
public class StudentDe {
    private String name;
    private int score;

    public StudentDe(){}

    public StudentDe(String name, int score){
        this.name = name;
        this.score = score;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getScore() {
        return score;
    }

    public void setScore(int score) {
        this.score = score;
    }

    /**
     * 下面的这个两个方法设计是有意而为之的，
     * 因为这两个方法在调用时和这类没有任何关系，
     * 也就是说将这两个方法发到其他的类中也是完全可以运行的 使用的。
     * 下面的可以编译运行，但是从设计上来说是错误的。
     * 根据分数排序
     * 对集合中的若干个学生进行排序，所以是两两比较
     * @param student1
     * @param student2
     * @return
     */
    public static int compareStudent1ByScore(StudentDe student1, StudentDe student2){
        return  student1.getScore() - student2.getScore();
    }

    /**
     * 根据名字排序
     * @param student1
     * @param student2
     * @return
     */
    public static int compareStudent1ByName(StudentDe student1, StudentDe student2){
        return student1.getName().compareToIgnoreCase(student2.getName());
    }


    /**
     * 这才是正确的设计
     * 当前的对象和 传入的对象比较，
     *
     * @param student
     * @return
     */
    public int compareByScore(StudentDe student){
        return  this.getScore() - student.getScore();
    }


    public int compareByName(StudentDe student){
        return  this.getName().compareToIgnoreCase(student.getName());
    }
}
```



## 13.stream 流

```java
流由3部分构成:
1.源
2.零个或者多个中间操作。(操作的就是这个源)
3.终止操作

流操作的分类:
1.惰性求值
  stream.xx().yyy().count(), 中间的xx().yyy()操作就是惰性操作,
  只有真正去调用count()计算是,中间操作才会被执行。
  如果没有count(),那么stream.xx().yyy() 就不会被执行,因为这里面没有终止操作
2.及早求值
  就是stream.xx().yyy().count()中的count() 立马要得到值

.Collection提供了新的stream()方法
.流不存储值，通过管道的方式获取值
.本质函数式的,对流的操作会生成一个结果，
 不过并不会修改底层的数据源,集合可以作为流的底层数据源
.延迟查找，很多流操作(过滤，映射，排序等)都可以延迟实现
```



### 13.1 stream 简单使用

```java
package com.learn.jdk.chapter15;

import java.util.Arrays;
import java.util.List;
import java.util.stream.Stream;

/**
 * chapter15
 * @ClassName: StreamDemo
 * @Description: stream 操作方式示例
 * @Author: lin
 * @Date: 2019/12/31 15:12
 * History:
 * @<version> 1.0
 */
public class StreamDemo {
    public static void main(String[] args) {
        // 创建stream 对象
        Stream stream = Stream.of("hello", "world");
        String[] str = new String[]{"hello", "li"};
        Stream stream1 = Stream.of(str);
        Stream stream2 = Arrays.stream(str);
        
        // 通过list创建
        List<String> list = Arrays.asList(str);
        Stream<String> stream3 = list.stream();
    }
}
```



### 13.2 Stream 使用1

```java
ackage com.learn.jdk.chapter15;

import java.util.stream.IntStream;

/**
 * @ClassName: StreamDemo1
 * @Description: IntStream 操作方式示例
 * @Author: lin
 * @Date: 2019/12/31 15:21
 * History:
 * @<version> 1.0
 */
public class StreamDemo1 {
    public static void main(String[] args) {
        int[] ints = new int[]{1, 3, 5, 7, 9};
        IntStream.of(ints).forEach(System.out::println);
        System.out.println("-----------------");
        // 在3,8 范围内的包含3,不包含8
        IntStream.range(3, 8).forEach(System.out::println);
        System.out.println("-----------------");
        // 包含3和8
        IntStream.rangeClosed(3, 8).forEach(System.out::println);
    }

}
```



### 13.3 IntStream使用

```java
package com.learn.jdk.chapter15;

import java.util.Arrays;
import java.util.List;

/**
 * @ClassName: StreamDemo2
 * @Description: IntStream 应用
 * @Author: lin
 * @Date: 2019/12/31 15:35
 * History:
 * @<version> 1.0
 */
public class StreamDemo2 {
    public static void main(String[] args) {
        //示例:将整型list中元素都乘以2，然后计算乘以2以后集合中元素和
        List<Integer> list = Arrays.asList(2, 4, 3, 5, 7);
//        int sum = 0;
//        int temp = 0;
//        // 原来的for循环方法处理
//        for (Integer integer : list) {
//             sum += integer*2;
//        }
//        System.out.println("集合元素相加===="+sum);

        // 现在使用stream来进行元素求和.
        // 这里我们就可以知道流的构成
        // list 就是 源
        // map(itm -> itm * 2): 中间操作
        // reduce() 就是终止操作
        // reduce(0, Integer::sum) 这个reduce代码的意思就是0 + itm*2,
        System.out.println("元素求和==="+list.stream().map(itm -> itm * 2).
                reduce(0, Integer::sum));
    }
}
```



### 13.4 Stream 的深度解析和源码实践

```java
package com.learn.jdk.chapter15;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.LinkedList;
import java.util.List;
import java.util.stream.Collectors;
import java.util.stream.Stream;

/**
 * chapter16
 * @ClassName: StreamDemo4
 * @Description: Stream 的深度解析和源码实践
 * @Author: lin
 * @Date: 2019/12/31 17:00
 * History:
 * @<version> 1.0
 */
public class StreamDemo4 {
    public static void main(String[] args) {
        //1
//        Stream<String> stream = Stream.of("hello", "world", "lin");
        //将一个stream对象 转换成 字符串数组
        //String[] stringArray1 = stream.toArray(itm -> new String[itm]);
        //方法引用, 这个正好符合IntFunction<A[]> 的声明
        //
//        String[] stringArray = stream.toArray(String[]::new);
//        Arrays.asList(stringArray).forEach(System.out::println);

        //2:将这个流转换成list
        Stream<String> stream1 = Stream.of("hello", "world", "lin");
//        List<String> collect = stream1.collect(Collectors.toList());
        // 使用collect的下面方法进行处理,
        // 第一个参数:不接受参数返回一个结果,
        // 第二个参数:BiConsumer接收两个参数，accumulator是累加器
        // 就是对集合中每一个元素进行遍历, 然后把这个集合的每一个元素应用到这个结果上面
        // 将每一个元素添加到这个返回的集合中， 其中第一个参数theList就是要返回的集合，
        //  第二个item是遍历的stream的当中的元素，将每一个元素都添加到这个theList当中
        // 第三个参数:也是BiConsumer, 不过这个combiner是合并器，
        //  也就是将上一次的操作结果和下一次的操作结果合并在一起
        // theList1.addAll(theList2) 就是讲上一次遍历得到的每一个list都添加到最终的theList1中去,
        // 最后返回的就theList1, 这个theList1就是最终要返回的对象,实际上就是ArrayList这个对象
        //<R> R collect(Supplier<R> supplier,
        //                  BiConsumer<R, ? super T> accumulator,
        //                  BiConsumer<R, R> combiner);
//        List<String> collect = stream1.collect(()-> new ArrayList<>(), (theList, item) ->
//                theList.add(item), (theList1, theList2) -> theList1.addAll(theList2));

        //使用方法引用来实现
        List<String> collect = stream1.collect(LinkedList::new,
                LinkedList::add, LinkedList::addAll);
        collect.forEach(System.out::println);

    }
}
```



### 13.5 Stream 实例剖析1

```java
package com.learn.jdk.chapter17;

import java.util.ArrayList;
import java.util.List;
import java.util.Set;
import java.util.TreeSet;
import java.util.stream.Collectors;
import java.util.stream.Stream;

/**
 * chapter17
 * @ClassName: StreamDemo5
 * @Description: Stream 实例剖析
 * @Author: lin
 * @Date: 2020/1/2 8:45
 * History:
 * @<version> 1.0
 */
public class StreamDemo5 {
    public static void main(String[] args) {
        Stream<String> stream = Stream.of("hello", "world", "lin");
        // 这里使用Collectors.toCollection(Supplier<C> collectionFactory)
        // 这里可以自定义转换成相应的集合
//        List<String> list = stream.collect(Collectors.toCollection(ArrayList::new));
//        list.forEach(System.out::println);

        // 转换成TreeSet
//        Set<String> set = stream.collect(Collectors.toCollection(TreeSet::new));
//        System.out.println(set.getClass());
//        set.forEach(System.out::println);

        //3.将stream中字符串，拼接成一个字符输出出来,
        // Collectors.joining() 一个接着一个拼接
        String str = stream.collect(Collectors.joining()).toString();
        System.out.println("拼接字符串======"+str);
    }
}
```



### 13.6 Stream 实例剖析2

```java
package com.learn.jdk.chapter17;

import java.util.Arrays;
import java.util.Collection;
import java.util.List;
import java.util.stream.Collectors;
import java.util.stream.Stream;

/**
 * @ClassName: StreamDemo6
 * @Description: Stream 实例剖析
 * @Author: lin
 * @Date: 2020/1/2 9:13
 * History:
 * @<version> 1.0
 */
public class StreamDemo6 {
    public static void main(String[] args) {
        // 将集合中元素转换成大写
//        List<String> list = Arrays.asList("hello", "world", "lin", "stream");
//        System.out.println(list.stream()
//                .map(String::toUpperCase)
//                .collect(Collectors.toList()));
//        System.out.println("-------------");

        // 2.每个数字的平方，然后在打印出来
//        List<Integer> list1 = Arrays.asList(2, 3, 4, 6, 7);
//        System.out.println("每个数的平方========="+list1.stream().map(item -> item * item)
//                .collect(Collectors.toList()));

        System.out.println("--------------------");

        //3.使用flatMap, 将下面的集合乘方 之后，作为整体输入出来 ，
        // 也就是形成一个list
        Stream<List<Integer>> stream = Stream.of(Arrays.asList(1, 2, 3),
                 Arrays.asList(4, 5, 6));
        stream.flatMap(Collection::stream)
                .map(item -> item * item).forEach(System.out::println);


    }
}
```



### 13.7  Stream.generate() 方法使用，Stream.iterate() 使用,如果不加限制那么就会产生无限流

```java
package com.learn.jdk.chapter17;

import java.util.IntSummaryStatistics;
import java.util.UUID;
import java.util.stream.Stream;

/**
 * @ClassName: StreamDemo7
 * @Description: 1. Stream.generate() 方法使用
 * @Author: lin
 * @Date: 2020/1/2 10:02
 * History:
 * @<version> 1.0
 */
public class StreamDemo7 {
    public static void main(String[] args) {
        //1. Stream.generate() 方法使用
//        Stream<String> stream = Stream.generate(UUID.randomUUID()::toString);
        // stream.findFirst() 返回流里面的第一个元素。
        // 为什么返回Optional，是为了避免NPE(空指针)
        // System.out.println(stream.findFirst().get());
//        stream.findFirst().ifPresent(System.out::println);

        // 2. Stream.iterate() 使用,如果不加限制那么就会产生无限流
        // 加上面limit就表示限制产生6个
        // 结果 1,3,5,7,9, 11
//        Stream.iterate(1, item -> item + 2)
//                .limit(6).forEach(System.out::println);
        // 找出该流中大于2的元素，然后将每个元素乘以2, 然后忽略掉流中的前两个元素,
        // 然后再取出流中的前两个元素，最后求出流中元素的总和。
//        System.out.println("求和==========" + Stream.iterate(1, item -> item + 2).limit(6)
//                .filter(ite -> ite > 2)
//                .map(ite -> ite * 2)
//                .skip(2)
//                .limit(2).reduce(0, Integer::sum));
        //第二种方式 使用mapToInt(), 将其转换为int, 如果使用map()那么里面会有一个自动拆箱和装箱的操作，
        // 这个转换会有一点点的性能损耗，所以jdk它会去避免这些损耗，因此提供了针对特定的原生类型 mapToInt()
        // 所以这里使用mapToInt()

        Stream<Integer> stream1 = Stream.iterate(1, item -> item + 2).limit(6);
//        System.out.println(stream1.filter(item -> item > 2)
//                .mapToInt(ite -> ite * 2)
//                .skip(2)
//                .limit(2).sum());

        // 这种方法是推荐使用的写法
        // 找出流中最小的元素
        // min() 方法返回的是Optional,因为这样更好的去规避空指针，
        // 而sum()返回的是int,就算stream中没有元素，他也只是返回 0.
        // 所以这就是这两种的区别
//         stream1.filter(item -> item > 2)
//                .mapToInt(ite -> ite * 2)
//                .skip(2)
//                .limit(2).min().ifPresent(System.out::println);
         
         //3.使用summaryStatistics()方法
//         IntSummaryStatistics intSummaryStatistics = stream1.filter(item -> item > 2)
//                .mapToInt(ite -> ite * 2)
//                .skip(2)
//                .limit(2).summaryStatistics();
//         System.out.println(intSummaryStatistics.getMin());
//         System.out.println(intSummaryStatistics.getCount());
//         System.out.println(intSummaryStatistics.getMax());

         //4.stream不能重复使用, 下面的的代码会报错,因为stream1.filter()会产生一个新的stream,
         //而 strem1.distinct 又重复使用了原来的stream1, 所以会报错
//           System.out.println(stream1);
//           System.out.println(stream1.filter(item -> item > 2));
//           System.out.println(stream1.distinct());

         //5.下面的方式可以 避免上面重复使用stream问题
         System.out.println(stream1);
         Stream<Integer> stream2 = stream1.filter(item -> item > 2);
         System.out.println(stream2);
         Stream<Integer> stream3 = stream2.distinct();
         System.out.println(stream3);

    }
}
```



### 13.8

```java
package com.learn.jdk.chapter17;

import java.util.Arrays;
import java.util.List;

/**
 * @ClassName: StreamDemo8
 * @Description:
 * @Author: lin
 * @Date: 2020/1/2 13:59
 * History:
 * @<version> 1.0
 */
public class StreamDemo8 {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("hello", "world", "lin");
        // 将集合中元素的首字母转换成大写，其他的字母不变
        //1.打印
//        list.stream().map(item ->
//                item.substring(0,1).toUpperCase() + item.substring(1))
//                .forEach(System.out::println);

        //2.下面的方式 不会打印出东西，因为没有终止操作
        //如果在程序的下面 加上forEach 就可以有数据打印出了
         list.stream().map(item -> {
               String result = item.substring(0,1).toUpperCase() + item.substring(1);
               System.out.println("t");
               return result;
         });
    }
}
```



### 13.8 IntStream和 distinct

```java
package com.learn.jdk.chapter17;

import java.util.stream.IntStream;

/**
 * @ClassName: StreamDemo9
 * @Description:
 * @Author: lin
 * @Date: 2020/1/2 14:19
 * History:
 * @<version> 1.0
 */
public class StreamDemo9 {
    public static void main(String[] args) {
        // 下面操作是不会结束的。是因为distinct 只是去重，而前面的iterate 一直返回0,1,
        // 没有一个限定范围
//        IntStream.iterate(0, i -> (i + 1) % 2)
//                .distinct()
//                .limit(6)
//                .forEach(System.out::println);

        // 该为这种方式就可以了, 限定流产生6个元素，再去重
        IntStream.iterate(0, i -> (i + 1) % 2)
                .limit(6).distinct().forEach(System.out::println);
    }
}
```



### 13.9 stream的短路和并发流

```java
package com.learn.jdk.chapter20;

import java.util.ArrayList;
import java.util.List;
import java.util.UUID;
import java.util.concurrent.TimeUnit;

/**
 * chapter 20
 * @ClassName: SteamDemo10
 * @Description: stream的短路和并发流
 * @Author: lin
 * @Date: 2020/1/2 16:29
 * History:
 * @<version> 1.0
 */
public class SteamDemo10 {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>(5000000);
        int count = 5000000;
        for (int i = 0; i < count; i++) {
             list.add(UUID.randomUUID().toString());
        }
        System.out.println("开始排序");
        // 纳秒
        long startTime = System.nanoTime();
        // 串行流
//        list.stream().sorted().count();
        // 开始排序
        //排序耗时:6787

        // 并行流
        list.parallelStream().sorted().count();
        //开始排序
        //排序耗时:3782

        long endTime = System.nanoTime();

        long millis = TimeUnit.NANOSECONDS.toMillis(endTime - startTime);

        System.out.println("排序耗时:" + millis);
    }
}
```



### 13.10 stream().mapToInt()

```java
package com.learn.jdk.chapter20;

import java.util.Arrays;
import java.util.List;

/**
 * @ClassName: StreamDemo11
 * @Description:
 * @Author: lin
 * @Date: 2020/1/2 16:56
 * History:
 * @<version> 1.0
 */
public class StreamDemo11 {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("hello", "world", "hello world");
        //1、打印这个列表中 长度为5的第一个单词, 并且把长度打印出来

//        list.stream().mapToInt(item -> item.length()).filter(length -> length == 5)
//                .findFirst().ifPresent(System.out::println);
        // 如果长度没有5的，那么打印出来的是空的

        //2、这种方式也可以
//        list.stream().mapToInt(item -> item.length()).filter(length -> length == 5)
//                .limit(1).forEach(it -> System.out.println(it));

        //3、
        // Stream 可以看作为一个容器，这个容器存放的是对每个元素的操作
        // mapToInt是一种操作, filter是一种操作。
        // 比如说对流的处理 如迭代，判断、过滤等它会拿着容器中操作 逐个应用到stream中的每一个元素上，
        // 并且将这些操作串行化，一个元素应用了一个操作之后 接着第二个操作等。
        // "而不是" 对于一个元素应用完第一个操作，第二个元素应用完第二个操作。

        //注意: stream是存在短路运算的，只要找到符合条件的 后面的就不会再去执行
        list.stream().mapToInt(item -> {
          int length = item.length();
          System.out.println(item);
          return  length;
        }).filter(length -> length == 5)
                .findFirst().ifPresent(System.out::println);
    }
}
```



### 13.11 stream 和 flatMap

```java
package com.learn.jdk.chapter20;

import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

/**
 * @ClassName: StreamDemo12
 * @Description:
 * @Author: lin
 * @Date: 2020/1/2 17:41
 * History:
 * @<version> 1.0
 */
public class StreamDemo12 {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("hello welcome", "world hello",
                "hello world hello", "hello welcome");
        // 找出列表中所有的单词 并进行去重

        // 1、这种方式不对
//        List<String[]> collect = list.stream().map(item -> item.split(" "))
//                .distinct().collect(Collectors.toList());
//        collect.forEach(item -> Arrays.asList(item).forEach(System.out::println));


        // 2、下面使用flatMap
        //  Arrays::stream(T[] array) 接收一个数组的 返回一个stream对象。
        // flatMap 将多个中间的stream合并成一个stream.
        List<String> result = list.stream().map(item -> item.split(" "))
                .flatMap(Arrays::stream).distinct()
                .collect(Collectors.toList());
        result.forEach(System.out::println);
    }
}
```



### 13.12 stream分组与分区详解

```java
package com.learn.jdk.chapter21;

import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

/**
 * chapter21
 * @ClassName: StreamDemo12
 * @Description: stream分组与分区详解
 * @Author: lin
 * @Date: 2020/1/2 21:44
 * History:
 * @<version> 1.0
 */
public class StreamDemo13 {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("Hi", "Hello", "你好,哈哈哈");
        List<String> list2 = Arrays.asList("zairian", "lisa",
                "wangle", "zarla");
        // 将两个集合组合起来，形成各自 人名打招呼。

        List<String> result = list.stream().flatMap(item -> list2.stream().map(item2 -> item + " " + item2)).
                collect(Collectors.toList());
        result.forEach(System.out::println);
    }
}
```



## 14、 stream 和 分组：group by  分区：partition by 

```java
package com.learn.jdk.chapter21;

import java.util.Arrays;
import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;

/**
 * @ClassName: StreamDemo14
 * @Description:
 * @Author: lin
 * @Date: 2020/1/2 22:17
 * History:
 * @<version> 1.0
 */
public class StreamDemo14 {
    public static void main(String[] args) {
        StudentTest test1 = new StudentTest("zaneta", 100, 20);
        StudentTest test2 = new StudentTest("lise", 80, 20);
        StudentTest test3 = new StudentTest("wadge", 90, 30);
        StudentTest test4 = new StudentTest("zeal", 70, 40);

        List<StudentTest> list = Arrays.asList(test1, test2, test3, test4);

        // 根据名称分组
//        Map<String, List<StudentTest>> map = list.stream().
//                collect(Collectors.groupingBy(StudentTest::getName));
//        System.out.println(map);
        //根据分数分组
//        Map<Integer, List<StudentTest>> collect = list.stream().
//                collect(Collectors.groupingBy(StudentTest::getScore));
//        System.out.println(collect);

        //根据名字分组，然后 count
//        Map<String, Long> collect2 = list.stream().
//                collect(Collectors.groupingBy(StudentTest::getName, Collectors.counting()));
//        System.out.println(collect2);

        //根据名称分组，再求分数的平均
        Map<String, Double> collect3 = list.stream().
                collect(Collectors.groupingBy(StudentTest::getName,
                        Collectors.averagingLong(StudentTest::getScore)));
        System.out.println(collect3);

        // 分区partition, 就是Boolean值，要么true, 要么false
        Map<Boolean, List<StudentTest>> listMap = list.stream().
                collect(Collectors.partitioningBy(studentTest -> studentTest.getScore() >= 90));
        System.out.println(listMap);


        // 分组：group by
        // 分区：partition by (结果只会有两个分区，在程序语言中表达就是true 和false. 分区是分组的一种特例)
    }
}
```



### 14.1 通过源码分析 ,深入理解stream, collect等

```java
package com.learn.jdk.chapter22;

import java.util.Arrays;
import java.util.List;
import static java.util.stream.Collectors.*;

/**
 * chapter22
 * @ClassName: StreamTest1
 * @Description: 通过源码分析 ,深入理解stream, collect等
 * @Author: lin
 * @Date: 2020/1/3 8:53
 * History:
 * @<version> 1.0
 */
public class StreamTest1 {
    public static void main(String[] args) {
        //
        StudentTe t1 = new StudentTe("zings", 70);
        StudentTe t2 = new StudentTe("lisa", 90);
        StudentTe t3 = new StudentTe("wang", 100);
        StudentTe t4 = new StudentTe("zarla", 90);

        List<StudentTe> studentTes = Arrays.asList(t1, t2, t3, t4);

        // 静态导入 Collectors类
        // 所以这里显示Collectors.toList()
//        List<StudentTe> studentTeList = studentTes.stream().
//                collect(toList());
//        studentTeList.forEach(System.out::println);

//        System.out.println("---------------------");
//
//        System.out.println("count: " + studentTes.stream().collect(counting()));
//        System.out.println("count: " + studentTes.stream().count());
    }

}
```



### 14.2、 收集器用法详解与多分组和分区

```java
package com.learn.jdk.chapter25;

import com.learn.jdk.chapter22.StudentTe;

import java.util.*;

import static java.util.stream.Collectors.*;

/**
 * chapter25
 * @ClassName: StreamTest2
 * @Description: 收集器用法详解与多分组和分区
 * @Author: lin
 * @Date: 2020/1/3 16:21
 * History:
 * @<version> 1.0
 */
public class StreamTest2 {
    public static void main(String[] args) {

        // 测试
        StudentTe  te1 = new StudentTe("zings", 74);
        StudentTe  te2 = new StudentTe("lisa", 74);
        StudentTe  te3 = new StudentTe("wang", 100);
        StudentTe  te4 = new StudentTe("zarla", 80);
        StudentTe  te5 = new StudentTe("zarla", 80);

        List<StudentTe> studentTes = Arrays.asList(te1, te2, te3, te4, te5);
        //求出分数最低的学生

        studentTes.stream().collect(minBy(Comparator.comparingInt(StudentTe::getScore))).
                ifPresent(System.out::println);
        studentTes.stream().collect(maxBy(Comparator.comparingInt(StudentTe::getScore))).
                ifPresent(System.out::println);
        System.out.println(studentTes.stream().collect(averagingInt(StudentTe::getScore)));

        System.out.println(studentTes.stream().collect(summingInt(StudentTe::getScore)));
        // 求出摘要信息
        IntSummaryStatistics intSummaryStatistics = studentTes.stream().collect(summarizingInt(StudentTe::getScore));
        System.out.println(intSummaryStatistics);

        System.out.println("-----------------------");
        //拼接名字
        System.out.println(studentTes.stream().
                map(StudentTe::getName).collect(joining()));
        //joining(", ")
        System.out.println(studentTes.stream().
                map(StudentTe::getName).collect(joining(", ")));
        //加入前缀和后缀
        System.out.println(studentTes.stream().
                map(StudentTe::getName).collect(joining(", ", "<begin> ", " <end>")));

        System.out.println("-----------------------");
        // 分组， 第一次分组外层key 是一个integer类型，然后在对第一次分组的进行第二次分组
        // 第二次分组key是String类型 ,value是 List<StudentTe>.
        Map<Integer, Map<String, List<StudentTe>>> collect = studentTes.stream().
                collect(groupingBy(StudentTe::getScore, groupingBy(StudentTe::getName)));
        System.out.println("分组= " + collect);

        System.out.println("----------------------------");
        // 分区
        Map<Boolean, List<StudentTe>> collect1 = studentTes.stream().
                collect(partitioningBy(studentTe -> studentTe.getScore() > 80));
        System.out.println("分区: "+ collect1);
        // 在根据分数大于80，进行再次分区。

        Map<Boolean, Map<Boolean, List<StudentTe>>> mapMap =
        studentTes.stream().
                collect(partitioningBy(studentTe -> studentTe.getScore() > 80,
                        partitioningBy(studentTe -> studentTe.getScore() > 90)));
        //返回的结果就是嵌套的了
        System.out.println("再分区: " + mapMap);

        System.out.println("---------------------");
        // 求出每个分区中，学生的个数
        Map<Boolean, Long> longMap = studentTes.stream().
                collect(partitioningBy(studentTe -> studentTe.getScore() > 80, counting()));
        System.out.println("求出每个分区中，学生的个数 " + longMap);

        // 根据名字分组，在得到学生的分数
        // 使用collectingAndThen()去收集最小值，收集完之后 然后在从里面把它包含的值取出来，
        // 这个get一定是有值的，这是因为groupingBy()分组. 每一组中是一定有数据的。
        Map<String, StudentTe> collect2 = studentTes.stream().
                collect(groupingBy(StudentTe::getName,
                        collectingAndThen(minBy(Comparator.comparingInt(StudentTe::getScore)),
                                Optional::get)));
        System.out.println("分组: " + collect2);
    }
}
```



### 14.3 Comparator 详解与类型推断

```java
package com.learn.jdk.chapter26;

import java.util.Arrays;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;
import java.util.stream.Collectors;

/**
 * chapter26
 * @ClassName: ComparatorDemo
 * @Description: Comparator 详解与类型推断
 * @Author: lin
 * @Date: 2020/1/5 10:58
 * History:
 * @<version> 1.0
 */
public class ComparatorDemo {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("nihao", "hello", "world", "welcome");
        //根据字母进行升序的排序
//        Collections.sort(list);
//        System.out.println(list);
        // 字符串长度
//        Collections.sort(list, (item1 , item2) -> item1.length() - item2.length());
//        Collections.sort(list, (item1 , item2) -> item2.length() - item1.length());
//        System.out.println(list);

        // 使用方法引用, 降序
//        Collections.sort(list, Comparator.comparingInt(String::length).reversed());
//        System.out.println(list);

        //这里使用item -> item.length()报错，
        // lambda表达式的参数推断, 如果编译器不能推断类型是，必须要明确指定参数类型
        // 这里推断 item 的类型 是根据 comparingInt(ToIntFunction<? super T> keyExtractor)方法来进行的，
        // 而这个方法里面的泛型T 是字符串类型，而? 是 T的父类，或者父类的父类。
        // 所以这里无法去推断类型，只能是给一个同样的类型Object。
        // 加上reversed()方法会影响类型的推断。
//        Collections.sort(list, Comparator.comparingInt((String item) -> item.length()).reversed());
//        System.out.println(list);

        //注意： 【这个编译报错】，
        // Collections.sort(list, Comparator.comparingInt((Boolean item) -> 1).reversed());


        //先按照字符串的长度，然后在按照字母的升序顺序排序(这里使用thenComparing)，使用jdk的不区分大小写
        //Collections.sort(list, Comparator.comparingInt(String::length).
        // thenComparing(String.CASE_INSENSITIVE_ORDER));


//        Collections.sort(list, Comparator.comparingInt(String::length).
//                thenComparing((item1, item2) -> item1.toLowerCase().compareTo(item2.toLowerCase())));

        // 方法引用的方式
//        Collections.sort(list, Comparator.comparingInt(String::length).
//                thenComparing(Comparator.comparing(String::toLowerCase)));

        //Comparator.comparing()的重载方法， Comparator.reverseOrder 将顺序逆转过来
//        Collections.sort(list, Comparator.comparingInt(String::length).
//                thenComparing(Comparator.comparing(String::toLowerCase, Comparator.reverseOrder())));

        // 首先根据字符串长度排序升序，再reversed进行降序排序，然后thenComparing是对于前面结果等于0的才去引用thenComparing，
        // 如果不等于0就不会应用thenComparing，
        // 将字母转换为小写，再逆序排序
//        Collections.sort(list, Comparator.comparingInt(String::length).reversed().
//                thenComparing(Comparator.comparing(String::toLowerCase, Comparator.reverseOrder())));
        // 输出结果 [welcome, world, nihao, hello]


        //thenComparing 比较器的串联
        // 再加一个thenComparing, 结果还是和上面的一样，为什么呢？
        // 这是因为在Comparator.comparingInt(String::length).reversed()，
        // thenComparing 第一次的应用的时候 其中的元素[welcome, nihao, hello, world].这个时候对其后面的三个元素进行比较
        // 也就是两两比较不会为零， 返回的  res = compare(c1, c2); res 不为零。 所以第二个thenComparing 不会引用到。
        // 因此结果还是一样的
        Collections.sort(list, Comparator.comparingInt(String::length).reversed().
                thenComparing(Comparator.comparing(String::toLowerCase, Comparator.reverseOrder())).
                thenComparing(Comparator.reverseOrder()));
        //

        System.out.println(list);
    }
}
```



## 15、扩展BinaryOperator

```java
package com.learn.jdk.extension;

import java.util.Comparator;
import java.util.function.BinaryOperator;

/**
 * BinaryOperator：
 * 对两个相同类型的操作数进行的运算, 产生与该操作数相同的类型的结果
 * 这个是BiFunction的一种具体或者特例, BiFunction接收三个参数,前两个参数是输入参数类型,第三个参数是返回参数类型
 * @ClassName: BinaryOperatorDemo
 * @Description:
 * @Author: lin
 * @Date: 2019/12/30 10:34
 * History:
 * @<version> 1.0
 */
public class BinaryOperatorDemo {
    public static void main(String[] args) {
       BinaryOperatorDemo binaryOperatorDemo = new BinaryOperatorDemo();
        //操作本身要符合BinaryOperator 中的apply的定义
        System.out.println("两数相加====" +binaryOperatorDemo.calculation(2, 3, (v1, v2) -> v1 + v2));

        System.out.println("两数相减====" +binaryOperatorDemo.calculation(2, 3, (v1, v2) -> v1 - v2));
        System.out.println("两数相乘====" +binaryOperatorDemo.calculation(2, 3, (v1, v2) -> v1 * v2));
        System.out.println("两数相除====" +binaryOperatorDemo.calculation(2, 3, (v1, v2) -> v1 / v2));

        // 取出长度小的
        System.out.println(binaryOperatorDemo.getSort("hello123", "world", (a, b) -> a.length() - b.length()));
        //上面的可以替换为 Comparator.comparingInt 方式 ,方法引用
        // binaryOperatorDemo.getSort("hello", "world", Comparator.comparingInt(String::length));

        // 首字母在ascii码 上排前的就是小的
        System.out.println(binaryOperatorDemo.getSort("hello", "world", (a, b) -> a.charAt(0) - b.charAt(0)));
    }

    /**
     * 操作本身是一种抽象
     * @param a
     * @param b
     * @param binaryOperator
     * @return
     */
    public int calculation(int a, int b ,  BinaryOperator<Integer> binaryOperator){
        return binaryOperator.apply(a, b);
    }

    public String getSort(String a, String b, Comparator<String> comparator){
        return BinaryOperator.minBy(comparator).apply(a, b);
    }
}

```



## 16、内部迭代和外部迭代本质

```
stream 和sql 语句很类似
select name from student where age > 23 and address ="shanghai" order by age desc;

内部迭代
描述性的语言
students.stream().filter(student ->student.getAge()-23)
 .filter(student -> student.getAddress().equals("shanghai"))
 .sorted(...).forEach(student -> System.out.println(student.getName()));

中间操作都会返回一个stream对象，比如说返回Stream<Student>, Stream<Integer>, Stream<String>
终止操作则不会返回Stream类型，可能不返会值， 也有可能返回其他类型的单个值。

外部迭代
jdk8 之前的写法，这种写法描述性不强
List<Student> list = new ArrayList<>();
for(int i=0; i< students.size(); i++){
 Student student = students.get(i);
  if(student.getAge() > 20 && student.getAddress().equals("shanghai")){
   list.add(student);
 }
}
Collections.sort(list, Comparator(...));
for(Student student : list){
  System.out.println(student.getName());
}

fork join
集合关注是数据与数据存储本身
流关注的则是对数据的计算

流与迭代器类似的一点是: 流是无法重复使用或消费的。

```



## 17、stream分组和分区

```
分组：group by
分区：partition by (结果只会有两个分区，在程序语言中表达就是true 和false)
```

## 18、深入源码分析 collect

```
1、collect 收集器
2、Collector 作为collect方法的参数
3、Collector 是一个接口, 她是一个可变的汇聚(归约)操作，将输入元素积累到一个可变的结果容器中；
  它会在所有元素都处理完毕后，将积累的结果转换为一个最终的表示(这是一个可选操作), 
  还原操作可以顺序或并行执行
有对应的实现类供我们去使用.

4、Collectors 本身提供了关于Collector的常见汇聚实现, Collectors本身实际上是一个工厂.
5、为了确保串行与并行操作结果的等价性，Collector函数需要满足两个条件: identity(同一性) 与associativity(关联性)
6、a(某一个线程它的执行分支所得到的一个部分结果) == combiner.apply(a, supplier.get())
7、函数式编程最大的特点: 表示做什么, 而不是如何做。 

注意：
串行
 A a1 = supplier.get();
 *     accumulator.accept(a1, t1); 第一个参数是每一次累积之后的中间结果
, 第二个参数是stream中要处理的下一个元素 , 所以开始调用supplier.get() 得到a1, 这是一个结果容器。
  这个结果容器是空的，每次要往这个容器中累积类容。所以第一次调用时这个容器是空的。
t1是流中的下一个元素
 *     accumulator.accept(a1, t2);
 *      

并行
       A a2 = supplier.get();
 *     accumulator.accept(a2, t1); 
       一个线程将结果累积到a2中，
 *     A a3 = supplier.get();
       先得到一个新的容器，然后另外一个线程将遇到的第一个元素t2累积到a3中。 这个a2和a3是两个不同的对象
 *     accumulator.accept(a3, t2);
       前面的代码执行完成后产生两个部分结果a2,a3, 
       然后调用combiner.apply(a2, a3)将两个部分结果合并起来 形成一个结果。
       将这个结果传递给finisher.apply(). 最后会把这个合并后的结果转换成 最终的r2结果. 
       对于结合性上来说 r1 必须等价于r2. 因为r1和r2 本身就是最终执行的结果。
 *     R r2 = finisher.apply(combiner.apply(a2, a3)); 
     
对于单线程和多线程执行过程不一样，但是执行结果必须是一样的。

reduce 和 collector 区别：
reduce: 不可变性，里面处理的对象都是不可变的的。对于reduce操作 它本来是不可变的，但是通过一种可变的操作去实现它。
 在单线程情况下是可以正常运行的，但是在并行流情况下就错乱了
collector(mutable container): 一种可变的行为,像LinkList, ArrayList就是可变结果容器

==========================================
一、
* @see Stream#collect(Collector)
 * @see Collectors
 *
 * @param <T> the type of input elements to the reduction operation
 * @param <A> the mutable accumulation type of the reduction operation (often
 *            hidden as an implementation detail)
 * @param <R> the result type of the reduction operation
 * @since 1.8
 */
public interface Collector<T, A, R> 
接口中,
1、参数T是流中每一个元素的类型， 
2、A 是可变的容器的类型，比如第一个元素累积到集合当中，
接着把第二个元素累积到集合当中....所以这个A实际上就是集合的类型。
可以认为这个A就是中间操作生成结果的类型
3、R表示汇聚结果的操作类型

二、
/**
 * A function that folds a value into a mutable result container.
 *
 * @return a function which folds a value into a mutable result container
 */
 BiConsumer<A, T> accumulator();
BiConsumer 类中:
public interface BiConsumer<T, U>
 第一个参数T表示每一次操作的中间结果的那个类型, 而后面的U表示后面流中待处理的下一个元素类。
所有根据BiConsumer类中的描述, 
这里collector接口中 BiConsumer<A, T>  的参数A 就表示结果类型，每一次都要对结果做处理，
并处理完之后这个结果作为下一次调用的第一个参数传进去。所以这个A就表示每一次处理的结果的类型
而T就表示流中待处理的下一个元素的类型
```

## 19、收集器用法详解和多级分组合分区

```
Collectors 类是一个辅助类，可以认为是一个工厂。它的作用就是向开发者提供常见的Collector实现。
并且Collectors的构造方法被设计为私有的，从而杜绝你创建对象的可能性. 
 它既然是工厂那么里面的方法大多都是静态方法。既然Collectors是工厂类 提供了常见的Collector接口的实现。
我们知道Collector是接口 既然要实现那么一定要返回实现类xxxImpl 的实例。 
CollectorImpl类和Collectors类的结合性是非常密切的，
所以jdk的设计者就直接将CollectorImpl实现类放在了Collectors里面了 因为其他的地方是不会用到的。所以就直接放到Collectors类里面

我们看Collectors 中实现的一些常用的方法，它返回的都是CollectorImpl实例。
public static <T>
    Collector<T, ?, List<T>> toList() {
        return new CollectorImpl<>((Supplier<List<T>>) ArrayList::new, List::add,
                                   (left, right) -> { left.addAll(right); return left; },
                                   CH_ID);
    }
```



## 20、自定义收集器

```java
package com.learn.jdk.chapter28;

import java.util.*;
import java.util.function.BiConsumer;
import java.util.function.BinaryOperator;
import java.util.function.Function;
import java.util.function.Supplier;
import java.util.stream.Collector;

import static java.util.stream.Collector.Characteristics.IDENTITY_FINISH;
import static java.util.stream.Collector.Characteristics.UNORDERED;

/**
 * chapter28
 * @ClassName: CustomizeCollector
 * @Description: 自定义 收集器
 * 这里Collector<T, Set<T>, Set<T>>
 *      中 第一个去遍历的流当中每一个元素类型设置为T类型
 *      第二个 中间的结果类型Set<T>,
 *      第三个返回的最终结果类型 这里设定和第二个类型一样
 * @Author: lin
 * @Date: 2020/1/8 13:48
 * History:
 *
 * @<version> 1.0
 */
public class CustomizeCollector<T> implements Collector<T, Set<T>, Set<T>> {
    /**
     * 用于返回中间收集的结果容器
     *
     * @return
     */
    @Override
    public Supplier<Set<T>> supplier() {
        System.out.println("supplier invoked!");
        // ()-> new HashSet<>()
        // 这个返回的是中间结果容器的一个初始化对象，
        // 后面是不断的往这个结果容器中追加内容
        return HashSet<T>::new;
    }

    /**
     * 累加器，不断的从流中遍历元素，将这个元素从<A,T>  T加A当中
     * @return
     */
    @Override
    public BiConsumer<Set<T>, T> accumulator() {
        System.out.println("accumulator invoked!");
        // :: 指定到BiConsumer, add实际上对应的BiConsumer中的accept(T t, U u)。
        return Set<T>::add;
        // 这个和上面的等价
        //return (item1, item2) -> item1.add(item2);

        // 这种写法不可以，不能编译,
        // Set<T>本身是接口，HashSet是Set的一个实现，当然也有add.
        // 但是为什么不能使用HashSet<T>::add
        // 这是因为上面的Supplier<<Set<T>> 返回的是一个中间结果容器
        // 并且这个方法是往这个中间结果容器中添加内容，
        // 当这个返回的类型和中间结果返回的容器类型不一致那么就不允许这么做
        // 但是如果改为Set<T>这个就符合Set<T>接口的要求，
        // 因此这里就不能使用一个具体类型的set. 要使用给的泛型类型。
//        return  HashSet<T>::add;
    }

    /**
     * 表示对并行流，多个线程所执行的部分结果合并到一起
     * @return
     */
    @Override
    public BinaryOperator<Set<T>> combiner() {
        System.out.println("combiner invoked!");
        return (set1, set2) -> {
            set1.addAll(set2);
            return  set1;
        };
    }

    /**
     * 是一个可选的操作，根据实际情况提供一个实现。
     * finisher是一个完成器，将结果Function<A, R> , 结果A转换成R ，
     * 在有些时候这个A就是R
     * 1、执行是在combiner 后面执行，
     * 将所有的结果都合并到一起，如果是单线程就不用合并，
     * 合并完之后去返回一个最终类型，
     * 在这个程序中最终的结果类型是一样的，
     * 就是我们只需把中间的累加之后的结果容器返回给用户就可以了.
     *
     * 2、除非这个累加完之后得到的结果容器跟你想要得到的对象类型是不一致的，
     * 那么finisher函数你必须要显示的正确的把它实现出来
     * @return
     */
    @Override
    public Function<Set<T>, Set<T>> finisher() {
        System.out.println("finisher invoked!");
//        return ite -> ite;
        // identity返回的就是一个函数，这个函数总是把它的输入参数给原封不动的返回
        return  Function.identity();
    }

    /**
     * 返回一个set对象，Characteristics表示当收集器的一个特性，这个特性就是三个枚举值
     * @return
     */
    @Override
    public Set<Characteristics> characteristics() {
        System.out.println("characteristics invoked!");
        //返回一个集合，这个集合标识着当前收集器的很多特性
        // 所以返回一个不可变的集合
        return Collections.unmodifiableSet(
                EnumSet.of(IDENTITY_FINISH,UNORDERED));
    }


    public static void main(String[] args) {
        List<String> list = Arrays.asList("hello", "world", "welcome");
        Set<String> set = list.stream().collect(new CustomizeCollector<>());
        System.out.println(set);
    }
//运行结果
//supplier invoked!
//accumulator invoked!
//combiner invoked!
//characteristics invoked!
//characteristics invoked!
//[world, hello, welcome]
    // 运行结果可以从代码的实现类中了解到，在ReferencePipeline中的collect的方法实现中知道
    // 如果有IDENTITY_FINISH，就会调用finisher,如果没有就不会调用，
    // 并且在实现类的方法中我们可以知道 在进行强制类型转换时一定要确保成功因为在源码中根本没有去进行验证，
//    return collector.characteristics().contains(Collector.Characteristics.IDENTITY_FINISH)
//               ? (R) container
//               : collector.finisher().apply(container);
    // 如果 A和R类型不一样那么转换就会抛出异常。所以这里要注意


}
```



### 20.1 stream 中collect的实现在ReferencePipeline中

```
@Override
       @SuppressWarnings("unchecked")
       public final <R, A> R collect(Collector<? super P_OUT, A, R> collector) {
           A container;
           if (isParallel()
                   && (collector.characteristics().contains(Collector.Characteristics.CONCURRENT))
                   && (!isOrdered() || collector.characteristics().contains(Collector.Characteristics.UNORDERED))) {
               container = collector.supplier().get();
               BiConsumer<A, ? super P_OUT> accumulator = collector.accumulator();
               forEach(u -> accumulator.accept(container, u));
           }
           else {
               container = evaluate(ReduceOps.makeRef(collector));
           }
           return collector.characteristics().contains(Collector.Characteristics.IDENTITY_FINISH)
                  ? (R) container
                  : collector.finisher().apply(container);
       }

==========================================================================
  public static <T, I> TerminalOp<T, I>
    makeRef(Collector<? super T, I, ?> collector) {
        Supplier<I> supplier = Objects.requireNonNull(collector).supplier();
        BiConsumer<I, ? super T> accumulator = collector.accumulator();
        BinaryOperator<I> combiner = collector.combiner();
        class ReducingSink extends Box<I>
                implements AccumulatingSink<T, I, ReducingSink> {
            @Override
            public void begin(long size) {
                state = supplier.get();
            }

            @Override
            public void accept(T t) {
                accumulator.accept(state, t);
            }

            @Override
            public void combine(ReducingSink other) {
                state = combiner.apply(state, other.state);
            }
        }
        return new ReduceOp<T, I, ReducingSink>(StreamShape.REFERENCE) {
            @Override
            public ReducingSink makeSink() {
                return new ReducingSink();
            }

            @Override
            public int getOpFlags() {
                return collector.characteristics().contains(Collector.Characteristics.UNORDERED)
                       ? StreamOpFlag.NOT_ORDERED
                       : 0;
            }
        };
    }
```



### 20.2 castingIdentity的说明，给定一个输入直接就把他强制转换成结果

```java
 @SuppressWarnings("unchecked")
    private static <I, R> Function<I, R> castingIdentity() {
        return i -> (R) i;
    }
```



### 20.3 Collector中IDENTITY_FINISH的解释

```java
/**
     * Indicates that the finisher function is the identity function and
     * can be elided.  If set, it must be the case that an unchecked cast
     * from A to R will succeed.
表示finisher函数就是identity函数并且可以被省略掉,如果设置了,
那么必须是未检出的转换从A到R的类型转换一定是成功的. 
如果标注为这个特性,那么他会将中间结果直接进行一个强制类型转换,转换成结果的R类型 然后返回。
不会调用finisher这个方法
IDENTITY_FINISH
     */
        

/**
 * Indicates that this collector is <em>concurrent</em>, meaning that
 * the result container can support the accumulator function being
 * called concurrently with the same result container from multiple
 * threads.
 *
 * <p>If a {@code CONCURRENT} collector is not also {@code UNORDERED},
 * then it should only be evaluated concurrently if applied to an
 * unordered data source.
表示这个收集器是并行的，这意味着结果容器支持 accumulator函数 并行的被调用, 在相同的容器下使用多个线程。
也就是多个线程同时的去操作一个相同的结果容器。
如果 一个并行收集器它不是UNORDERED,那么它只能被并行的用于无序的数据源。
CONCURRENT
 */
       
```



## 21、自定义收集器深度剖析与并行流陷阱  和收集器枚举值特性分析和并行流原理

```java
package com.learn.jdk.chapter29;

import java.util.*;
import java.util.function.BiConsumer;
import java.util.function.BinaryOperator;
import java.util.function.Function;
import java.util.function.Supplier;
import java.util.stream.Collector;

import static java.util.stream.Collector.Characteristics.*;
import static java.util.stream.Collectors.groupingBy;

/**
 * chapter29
 * @ClassName: CustomizeCollector2
 * @Description: 自定义收集器深度剖析与并行看流陷阱
 * 定义类的条件：
 * 输入：Set<String>
 * 输出：Map<String,String>
 * @Author: lin
 * @Date: 2020/1/8 16:25
 * History:
 * @<version> 1.0
 */
public class CustomizeCollector2<T> implements Collector<T, Set<T>, Map<T,T>> {
    /**
     * 用于返回中间收集的结果容器
     * @return
     */
    @Override
    public Supplier<Set<T>> supplier() {
        System.out.println("supplier invoked!");
//        return HashSet::new;
        return () ->{
            // 如果是串行流,那么这里只打印一次,也就是说产生是结果容器只有一个。
            // 如果是并行流,那么这里会打印多次, 这里会打印多个结果容器
            System.out.println("-------------");
            return new HashSet<>();
        };
    }

    @Override
    public BiConsumer<Set<T>, T> accumulator() {
        System.out.println("accumulator invoked!");
        // set 表示中间结果容器类型
        // item 表示stream中下一个元素的类型
        return (set, item) -> {
            // 一共10元素，按照顺序执行,要往中间容器中累加10个,那么这个方法就会被调用10次
            // 这里打印出 set 就会有异常。
            // 如果是并行的话,那么绝对不要再accumulator中 对目标或者中间的结果容器执行任何的额外操作,
            // 比如 一边执行打印,一边添加。 只单纯的执行本身该执行的操作
            // 否则在并行的情况下会抛出异常。

            // 因此在没有CONCURRENT的情况下 就变成多个线程会有多个中间结果容器,
            // 比如说有3个线程那么就有3个中间结果容器, 每一次的set是属于线程本身的set。3个线程会有3个set。他们之间是互补干扰的
            System.out.println("accumulator: " +set+", " + Thread.currentThread().getName());
            set.add(item);
        };
    }

    /**
     * combiner只有在并行stream时才会被调用，
     * 如果是串行stream是不会被调用的，因为串行流只有一个线程，
     * 这个一个线程会执行所以的任务，所以不需要合并的操作，这个时候combiner是不会被调用的
     * @return
     */
    @Override
    public BinaryOperator<Set<T>> combiner() {
        // 这里打印只是表示combiner被调用,但不表示lambda表达式被调用
        System.out.println("combiner invoked!");
        // 在并行情况下并且收集器本身没有CONCURRENT这个特性的情况下,combiner才会被调用,
        // 如果是并行流并且存在CONCURRENT这个特性,那么combiner不会被调用
        return (set1, set2) -> {
            System.out.println("set1: " + set1);
            System.out.println("set2: " + set2);
            set1.addAll(set2);
            return  set1;
        };
    }

    /**
     * finisher的调用是, 在没有IDENTITY_FINISH时才会真正调用
     * @return
     */
    @Override
    public Function<Set<T>, Map<T, T>> finisher() {
        System.out.println("finisher invoked!");
        //转换，如果类型不一致那么就是抛出异常
        return set -> {
//            Map<T,T> map = new HashMap<>(16);
            //排序
            Map<T,T> map = new TreeMap<>();
            set.stream().forEach(item -> map.put(item, item));
            return map;
        };
    }

    /**
     * 收集器有什么特性，实际上就是你赋予的。
     * @return
     */
    @Override
    public Set<Characteristics> characteristics() {
        System.out.println("characteristics invoked!");
        return Collections.unmodifiableSet(
                // IDENTITY_FINISH, CONCURRENT
                EnumSet.of(UNORDERED));

//        return Collections.unmodifiableSet(
//                // IDENTITY_FINISH
//                EnumSet.of(UNORDERED,IDENTITY_FINISH));
        // 这里先不加IDENTITY_FINISH, 其运行结果可以得到结果，
        // 如果这里加上了IDENTITY_FINISH,那么在finisher进行转换的时候 会怎样呢？
        // 加上之后 就会报错，Exception in thread "main" java.lang.ClassCastException: java.util.HashSet cannot be cast to java.util.Map,
        // 也就说在有IDENTITY_FINISH是 类型转换的时候报错了,因为中间容器类型和返回类型不一致


        //  return Collections.unmodifiableSet(
        //                // IDENTITY_FINISH
        //                EnumSet.of(UNORDERED,CONCURRENT));
        //思考:这里如果加上了CONCURRENT,那么在并行流情况下执行会出现错误。这个出现错误的原因是什么？
        //错误: Exception in thread "main" java.util.ConcurrentModificationException:
        // java.util.ConcurrentModificationException
        // 原因: 这里猜测是因为在并行流的情况下,多线程执行调用accumulator进行计算,
        // 但是并发这种情况下如果没有 锁的情况下,可能造成了并发异常
    }

    public static void main(String[] args) {
        System.out.println("超线程数: "+Runtime.getRuntime().availableProcessors());

        int count = 1;
        for (int i = 0; i <count; i++) {
            List<String> list = Arrays.asList("hello", "world", "welcome","zairian","lisa",
                    "wadge","zambia","cc","b","d");
            Set<String> set = new HashSet<>();
            set.addAll(list);

            System.out.println("set" + set);
//        Map<String, String> map = set.stream().collect(new CustomizeCollector2<>());
            //并行流
            Map<String, String> map = set.parallelStream().collect(new CustomizeCollector2<>());
            // 再上面的characteristics中如果不加上了CONCURRENT,
            // 还是可以使用并行流只不过这个并行流操作的就不是一个结果容器了,而是多个结果容器了,
            // 也就是说有多少个线程就有多少个结果容器被操作。
            // 如果加上CONCURRENT,就表示多个线程操作一个结果容器，那么最终的结果容器只有一个。
            // 如果结果容器只有一个，那么多个线程操作一个结果容器，combiner就无需合并了
//            System.out.println(map);

//            Map<String, String> map = set.stream().
//                    parallel().sequential().parallel().collect(new CustomizeCollector2<>());
            // 这里的这种写法,看最后一个串行的还是并行的,
            // 如果最后一个parallel就是并行,如果是sequential那么就是串行的
        }

    }
}
```

## 22、Collectors工厂类源码分析与实战

```
收集器:
对于Collectors静态工厂类来说,其实现一共分为两种情况:
1、通过CollectorImpl来实现。
2、通过reducing方法来实现, reducing方法本身有通过CollectorImpl实现的。


static class CollectorImpl<T, A, R> implements Collector<T, A, R> {
        private final Supplier<A> supplier;
        private final BiConsumer<A, T> accumulator;
        private final BinaryOperator<A> combiner;
        private final Function<A, R> finisher;
        private final Set<Characteristics> characteristics;

        CollectorImpl(Supplier<A> supplier,
                      BiConsumer<A, T> accumulator,
                      BinaryOperator<A> combiner,
                      Function<A,R> finisher,
                      Set<Characteristics> characteristics) {
            this.supplier = supplier;
            this.accumulator = accumulator;
            this.combiner = combiner;
            this.finisher = finisher;
            this.characteristics = characteristics;
        }

        CollectorImpl(Supplier<A> supplier,
                      BiConsumer<A, T> accumulator,
                      BinaryOperator<A> combiner,
                      Set<Characteristics> characteristics) {
            this(supplier, accumulator, combiner, castingIdentity(), characteristics);
        }

        @Override
        public BiConsumer<A, T> accumulator() {
            return accumulator;
        }

        @Override
        public Supplier<A> supplier() {
            return supplier;
        }

        @Override
        public BinaryOperator<A> combiner() {
            return combiner;
        }

        @Override
        public Function<A, R> finisher() {
            return finisher;
        }

        @Override
        public Set<Characteristics> characteristics() {
            return characteristics;
        }
    }



/**
     * Returns a {@code Collector} that accumulates the input elements into a
     * new {@code Collection}, in encounter order.  The {@code Collection} is
     * created by the provided factory.
     *
     * @param <T> the type of the input elements
     * @param <C> the type of the resulting {@code Collection}
     * @param collectionFactory a {@code Supplier} which returns a new, empty
     * {@code Collection} of the appropriate type
     * @return a {@code Collector} which collects all the input elements into a
     * {@code Collection}, in encounter order
     */
    这个方法更为广泛和通用
    public static <T, C extends Collection<T>>
    Collector<T, ?, C> toCollection(Supplier<C> collectionFactory) {
        对于这个方法必须要接收一个参数collectionFactory, 因为这个参数用于指定中间结果容器以及最终所返回的结果的集合类型,所以需要自己提供
        如果要返回一个LinkList那么就传入一个LinkList::new 那么就可以用这个方法
        return new CollectorImpl<>(collectionFactory, Collection<T>::add,
                                   (r1, r2) -> { r1.addAll(r2); return r1; },
                                   CH_ID);
    }

    /**
     * Returns a {@code Collector} that accumulates the input elements into a
     * new {@code List}. There are no guarantees on the type, mutability,
     * serializability, or thread-safety of the {@code List} returned; if more
     * control over the returned {@code List} is required, use {@link #toCollection(Supplier)}.
     *
     * @param <T> the type of the input elements
     * @return a {@code Collector} which collects all the input elements into a
     * {@code List}, in encounter order
     */
    public static <T>
    Collector<T, ?, List<T>> toList() {
        //对toList进行分析 
        // 首先CollectorImpl 实现类中有几个参数,
         supplier用于生产中间结果容器的 、
         accumulator累加器、
         combiner合并器、
         finisher完成器、
         characteristics 特性。
        // 它的实现如下,
        第一个参数是supplier,然后它用 ArrayList::new 强制的转换为(Supplier<List<T>>)。实际上它也可以这样写ArrayList<T>::new。
        第二个参数是accumulator累加器, 累积器是不断往中间结果中添加,这里调用List::add方法引用,传进来的第一次参数作为add方法的调用者,第二个参数作为add的参数
        第三个参数combiner合并器,这里使用lambda表达式(left, right) -> { left.addAll(right); return left; } 这里是将后面的部分结果right添加到第一个left中 然后返回left
        第四个参数CH_ID是个常量, 这个常量里面定义的是IDENTITY_FINISH,也就是说中间结果类型和最终的结果类型是同一个类型,
        也就是说toList()方法是不需要finisher的。
        return new CollectorImpl<>((Supplier<List<T>>) ArrayList::new, List::add,
                                   (left, right) -> { left.addAll(right); return left; },
                                   CH_ID);
    }


  /**
     * Returns a {@code Collector} that accumulates the input elements into a
     * new {@code Set}. There are no guarantees on the type, mutability,
     * serializability, or thread-safety of the {@code Set} returned; if more
     * control over the returned {@code Set} is required, use
     * {@link #toCollection(Supplier)}.
     *
     * <p>This is an {@link Collector.Characteristics#UNORDERED unordered}
     * Collector.
     *
     * @param <T> the type of the input elements
     * @return a {@code Collector} which collects all the input elements into a
     * {@code Set}
     */
     // toSet()也是一种特化版本
    public static <T>
    Collector<T, ?, Set<T>> toSet() {
        // 首先中间结果容器是 set类型, 累加器是 Set::add, 
        然后合并器(left, right) -> { left.addAll(right); return left; }
        characteristics 特性是UNORDERED和IDENTITY_FINISH, 这说明是无序的同时 中间结果类型和最终结果类型是一致的
      
        return new CollectorImpl<>((Supplier<Set<T>>) HashSet::new, Set::add,
                                   (left, right) -> { left.addAll(right); return left; },
                                   CH_UNORDERED_ID);
    }


/**
     * Returns a {@code Collector} that concatenates the input elements into a
     * {@code String}, in encounter order.
     *
     * @return a {@code Collector} that concatenates the input elements into a
     * {@code String}, in encounter order
     */
      将多个字符串拼接,不用任何的分割符 分割
    public static Collector<CharSequence, ?, String> joining() {
        //首先new CollectorImpl(), 三个参数
         第一个是流中元素类是CharSequence
         第二个是中间结果容器 StringBuilder
         第三个是结果String, 这里可以知道StringBuilder和String类型不一样,中间结果类型和最终结果类型是不一致的。
         所以要使用finisher
        实现 首先 
         第一个参数 supplier 调用StringBuilder::new,
         第二个参数 accumulator累加器 StringBuilder::append 使用append不断往StringBuilder追加
         第三个参数 combiner合并器 (r1, r2) -> { r1.append(r2); return r1; } r2不断的append到r1中 然后返回r1,
         第四个参数 finisher完成器 StringBuilder::toString 这里要使用的原因是类型不一致 所以相当于把StringBuilder转换成字符串,
         第五个参数 characteristics 特性CH_NOID 表示三个特性都不具备。
        return new CollectorImpl<CharSequence, StringBuilder, String>(
                StringBuilder::new, StringBuilder::append,
                (r1, r2) -> { r1.append(r2); return r1; },
                StringBuilder::toString, CH_NOID);
    }


/**
     * Returns a {@code Collector} that concatenates the input elements,
     * separated by the specified delimiter, in encounter order.
     *
     * @param delimiter the delimiter to be used between each element
     * @return A {@code Collector} which concatenates CharSequence elements,
     * separated by the specified delimiter, in encounter order
     */
    中间有分割符
    public static Collector<CharSequence, ?, String> joining(CharSequence delimiter) {
        return joining(delimiter, "", "");
    }



  /**
     * Adapts a {@code Collector} accepting elements of type {@code U} to one
     * accepting elements of type {@code T} by applying a mapping function to
     * each input element before accumulation.
     *
     * @apiNote
     * The {@code mapping()} collectors are most useful when used in a
     * multi-level reduction, such as downstream of a {@code groupingBy} or
     * {@code partitioningBy}.  For example, given a stream of
     * {@code Person}, to accumulate the set of last names in each city:
     * <pre>{@code
     *     Map<City, Set<String>> lastNamesByCity
     *         = people.stream().collect(groupingBy(Person::getCity,
     *                                              mapping(Person::getLastName, toSet())));
     * }</pre>
     *
     * @param <T> the type of the input elements
     * @param <U> type of elements accepted by downstream collector
     * @param <A> intermediate accumulation type of the downstream collector
     * @param <R> result type of collector
     * @param mapper a function to be applied to the input elements
     * @param downstream a collector which will accept mapped values
     * @return a collector which applies the mapping function to the input
     * elements and provides the mapped results to the downstream collector
     */
   // 这个方法就是映射, 适配一个Collector接收一个U类型的把它适配到一个T类型的, 也就是说这个收集器接收一个类型的你把它映射成另外的一个类型，
        在累积之前都应用一个映射函数。
    public static <T, U, A, R>
    Collector<T, ?, R> mapping(Function<? super T, ? extends U> mapper,
                               Collector<? super U, A, R> downstream) {
        BiConsumer<A, ? super U> downstreamAccumulator = downstream.accumulator();
        return new CollectorImpl<>(downstream.supplier(),
                                   (r, t) -> downstreamAccumulator.accept(r, mapper.apply(t)),
                                   downstream.combiner(), downstream.finisher(),
                                   downstream.characteristics());
    }



/**
     * Adapts a {@code Collector} to perform an additional finishing
     * transformation.  For example, one could adapt the {@link #toList()}
     * collector to always produce an immutable list with:
     * <pre>{@code
     *     List<String> people
     *         = people.stream().collect(collectingAndThen(toList(), Collections::unmodifiableList));
          首先收集并处理，先收集一个list,然后再去处理 把它转换成Collections::unmodifiableList 不可变列表
     * }</pre>
     *
     * @param <T> the type of the input elements
     * @param <A> intermediate accumulation type of the downstream collector
     * @param <R> result type of the downstream collector
     * @param <RR> result type of the resulting collector
     * @param downstream a collector
     * @param finisher a function to be applied to the final result of the downstream collector
     * @return a collector which performs the action of the downstream collector,
     * followed by an additional finishing step
     */
   接收一个Collector去执行额外的转换。
    接收两个参数一个downstream, 一个finisher。 先去应用downstream 等应用完了之后再去应用finisher
    public static<T,A,R,RR> Collector<T,A,RR> collectingAndThen(Collector<T,A,R> downstream,
                                                                Function<R,RR> finisher) {
        获取特性集合
        Set<Collector.Characteristics> characteristics = downstream.characteristics();
        if (characteristics.contains(Collector.Characteristics.IDENTITY_FINISH)) {
            if (characteristics.size() == 1)
                characteristics = Collectors.CH_NOID;
            else {
                characteristics = EnumSet.copyOf(characteristics);
                // 这里将IDENTITY_FINISH去掉的
                // 其含义是 如果强制进行转换那么就不会执行finisher, 但是这里需要执行finisher所以将。
                // IDENTITY_FINISH移除
                characteristics.remove(Collector.Characteristics.IDENTITY_FINISH);
                characteristics = Collections.unmodifiableSet(characteristics);
            }
        }
        return new CollectorImpl<>(downstream.supplier(),
                                   downstream.accumulator(),
                                   downstream.combiner(),
                                   downstream.finisher().andThen(finisher),
                                   characteristics);
    }


 /**
     * Returns a {@code Collector} accepting elements of type {@code T} that
     * counts the number of input elements.  If no elements are present, the
     * result is 0.
     *
     * @implSpec
     * This produces a result equivalent to:
     * <pre>{@code
     *     reducing(0L, e -> 1L, Long::sum)
     * }</pre>
     *
     * @param <T> the type of the input elements
     * @return a {@code Collector} that counts the input elements
     */
    // 计算
    public static <T> Collector<T, ?, Long>
    counting() {
        // 调用reducing 方法 , 第一个初始值是0L, 第二个映射是 e -> 1L 给定一个元素都返回1, 
         第三个 将所有的1加起来。
        return reducing(0L, e -> 1L, Long::sum);
    }


   /**
     * Returns a {@code Collector} that produces the sum of a integer-valued
     * function applied to the input elements.  If no elements are present,
     * the result is 0.
     *
     * @param <T> the type of the input elements
     * @param mapper a function extracting the property to be summed
     * @return a {@code Collector} that produces the sum of a derived property
     */
   将ToIntFunction应用到stream中的每一个元素上 得到一个整型的结果，然后把这个整型的结果求和，
   如果没有元素存在那么结果就是0
    public static <T> Collector<T, ?, Integer>
    summingInt(ToIntFunction<? super T> mapper) {
        return new CollectorImpl<>(
                // 这里为什么定义一个整型数组,为什么不直接定义一个整型数字? 
                // 如果这里使用的是一个整型数字,但是数字是无法传递的,数字本身是一个值类型的,
                // 而数组是一个引用类型的。 如果这里返回一个数字的话那么这样就返回一个固定的不可变的,
                // 那么怎么将这个数字传递到下面去喃,是传递不了的。 
                // 如果使用数组就可以,因为数组本身是一个引用,把这个数组传递过去是可以的。
                // 同时对于收集器来说中间收集器应该是一个容器就是它可以容纳东西的容器。
                // 而数组本身是容器,但是呢并不是说往数组里面添加新的东西而是从数组中取出它唯一的元素也就a[0],
                // 然后不断的给a[0]的值往上累加使得这个a[0]的值不断变化。
                () -> new int[1],
                (a, t) -> { a[0] += mapper.applyAsInt(t); },
                (a, b) -> { a[0] += b[0]; return a; },
                a -> a[0], CH_NOID);
    }


 /**
     * Returns a {@code Collector} that produces the arithmetic mean of an integer-valued
     * function applied to the input elements.  If no elements are present,
     * the result is 0.
     *
     * @param <T> the type of the input elements
     * @param mapper a function extracting the property to be summed
     * @return a {@code Collector} that produces the sum of a derived property
     */
    public static <T> Collector<T, ?, Double>
    averagingInt(ToIntFunction<? super T> mapper) {
        return new CollectorImpl<>(
                () -> new long[2],
                  // a[0]是总数, a[1]是个数
                (a, t) -> { a[0] += mapper.applyAsInt(t); a[1]++; },
                (a, b) -> { a[0] += b[0]; a[1] += b[1]; return a; },
                // 如果a[1]个数为零,那么就等于0,如果不等于零,那么就用a[0]/a[1] = 总数/个数
                a -> (a[1] == 0) ? 0.0d : (double) a[0] / a[1], CH_NOID);
    }
```

### 22.1、Collectors工厂类源码之 _reducing,_groupingBy,_partitioningBy分析

```
/**
     * Returns a {@code Collector} implementing a "group by" operation on
     * input elements of type {@code T}, grouping elements according to a
     * classification function, and returning the results in a {@code Map}.
     *
     * <p>The classification function maps elements to some key type {@code K}.
     * The collector produces a {@code Map<K, List<T>>} whose keys are the
     * values resulting from applying the classification function to the input
     * elements, and whose corresponding values are {@code List}s containing the
     * input elements which map to the associated key under the classification
     * function.
     *
     * <p>There are no guarantees on the type, mutability, serializability, or
     * thread-safety of the {@code Map} or {@code List} objects returned.
     * @implSpec
     * This produces a result similar to:
     * <pre>{@code
     *     groupingBy(classifier, toList());
     * }</pre>
     *
     * @implNote
     * The returned {@code Collector} is not concurrent.  For parallel stream
     * pipelines, the {@code combiner} function operates by merging the keys
     * from one map into another, which can be an expensive operation.  If
     * preservation of the order in which elements appear in the resulting {@code Map}
     * collector is not required, using {@link #groupingByConcurrent(Function)}
     * may offer better parallel performance.
     *
     * @param <T> the type of the input elements
     * @param <K> the type of the keys
     * @param classifier the classifier function mapping input elements to keys
     * @return a {@code Collector} implementing the group-by operation
     *
     * @see #groupingBy(Function, Collector)
     * @see #groupingBy(Function, Supplier, Collector)
     * @see #groupingByConcurrent(Function)
     */
    public static <T, K> Collector<T, ?, Map<K, List<T>>>
    // 输入类型是T以及T之上的类型, 返回类型是k以及k之下的类型。
    groupingBy(Function<? super T, ? extends K> classifier) {
        // 第一个参数是一个function对象,第二个是一个Collector对象
        return groupingBy(classifier, toList());
    }

 /**
     * Returns a {@code Collector} implementing a cascaded "group by" operation
     * on input elements of type {@code T}, grouping elements according to a
     * classification function, and then performing a reduction operation on
     * the values associated with a given key using the specified downstream
     * {@code Collector}.
     *
     * <p>The classification function maps elements to some key type {@code K}.
     * The downstream collector operates on elements of type {@code T} and
     * produces a result of type {@code D}. The resulting collector produces a
     * {@code Map<K, D>}.
     *
     * <p>There are no guarantees on the type, mutability,
     * serializability, or thread-safety of the {@code Map} returned.
     *
     * <p>For example, to compute the set of last names of people in each city:
     * <pre>{@code
     *     Map<City, Set<String>> namesByCity
     *         = people.stream().collect(groupingBy(Person::getCity,
     *                                              mapping(Person::getLastName, toSet())));
     * }</pre>
     *
     * @implNote
     * The returned {@code Collector} is not concurrent.  For parallel stream
     * pipelines, the {@code combiner} function operates by merging the keys
     * from one map into another, which can be an expensive operation.  If
     * preservation of the order in which elements are presented to the downstream
     * collector is not required, using {@link #groupingByConcurrent(Function, Collector)}
     * may offer better parallel performance.
     *
     * @param <T> the type of the input elements
     * @param <K> the type of the keys
     * @param <A> the intermediate accumulation type of the downstream collector
     * @param <D> the result type of the downstream reduction
     * @param classifier a classifier function mapping input elements to keys
     * @param downstream a {@code Collector} implementing the downstream reduction
     * @return a {@code Collector} implementing the cascaded group-by operation
     * @see #groupingBy(Function)
     *
     * @see #groupingBy(Function, Supplier, Collector)
     * @see #groupingByConcurrent(Function, Collector)
     */
    //这个方法本身是返回一个收集器Collector, 而且也会传入一个收集器downstream来使用,
      //那么使用的这个收集器可以认为是一个下游,返回的可以认为是一个上游
     // 构造思路是downstream本身是一个收集器,
     // 既然是一收集器所以它有Supplier、accumulator、combiner和一个可以选的finisher,
     //既然具备了收集器所具有的全部元素,那么它又提供了Function分类器这样一个函数,
     它实际上就是将这个分类器函数给应用到收集器中,使得对收集器的中间收集过程进行了一系列的转换,
      那么最终转换成的一个收集器就是这个方法返回的结果Collector<T, ?, Map<K, D>>.
     T类型表示stream流中元素类型,
     K类型表示分类器所返回的那个结果的类型,换句话说k就是所返回的那个map的key.
     D类型表示返回map的值类型。
 public static <T, K, A, D>
    Collector<T, ?, Map<K, D>> groupingBy(Function<? super T, ? extends K> classifier,
                                          Collector<? super T, A, D> downstream) {
          
        return groupingBy(classifier, HashMap::new, downstream);
    }



  /**
     * Returns a {@code Collector} implementing a cascaded "group by" operation
     * on input elements of type {@code T}, grouping elements according to a
     * classification function, and then performing a reduction operation on
     * the values associated with a given key using the specified downstream
     * {@code Collector}.  The {@code Map} produced by the Collector is created
     * with the supplied factory function.
     *
     * <p>The classification function maps elements to some key type {@code K}.
     * The downstream collector operates on elements of type {@code T} and
     * produces a result of type {@code D}. The resulting collector produces a
     * {@code Map<K, D>}.
     *
     * <p>For example, to compute the set of last names of people in each city,
     * where the city names are sorted:
     * <pre>{@code
     *     Map<City, Set<String>> namesByCity
                 // 第一个参数就是分类器,也就是这个例子中调用getCity的返回结果,实际上就是city对象
                 //第二个参数这里传入的是TreeMap::new, 这个实际上就传递给Supplier<M> mapFactory
                 //第三个是mapping,这个mapping是collector中提供的映射函数,
                   将一个类型映射成一个结果的,最终得到一个收集器toSet()。
     *         = people.stream().collect(groupingBy(Person::getCity, TreeMap::new,
     *                                              mapping(Person::getLastName, toSet())));
     * }</pre>
     *
     * @implNote
     * The returned {@code Collector} is not concurrent.  For parallel stream
     * pipelines, the {@code combiner} function operates by merging the keys
     * from one map into another, which can be an expensive operation.  If
     * preservation of the order in which elements are presented to the downstream
     * collector is not required, using {@link #groupingByConcurrent(Function, Supplier, Collector)}
     * may offer better parallel performance.
     *
     * @param <T> the type of the input elements
     * @param <K> the type of the keys
     * @param <A> the intermediate accumulation type of the downstream collector
     * @param <D> the result type of the downstream reduction
     * @param <M> the type of the resulting {@code Map}
     * @param classifier a classifier function mapping input elements to keys
     * @param downstream a {@code Collector} implementing the downstream reduction
     * @param mapFactory a function which, when called, produces a new empty
     *                   {@code Map} of the desired type
     * @return a {@code Collector} implementing the cascaded group-by operation
     *
     * @see #groupingBy(Function, Collector)
     * @see #groupingBy(Function)
     * @see #groupingByConcurrent(Function, Supplier, Collector)
     */
    public static <T, K, D, A, M extends Map<K, D>>
    Collector<T, ?, M> groupingBy(Function<? super T, ? extends K> classifier,
                                  Supplier<M> mapFactory,
                                  Collector<? super T, A, D> downstream) {
        // 获取下游流的Supplier
        Supplier<A> downstreamSupplier = downstream.supplier();
        // 得到一个累加器对象。 这样做的目的就是从downstream中的5个方法来最终得到新的一个collectorImpl对象
        // 那么collectorimpl对象就要提供那5个方法来构造 collectorimpl对象。
        // 所以先获取下游的两个对象来去构造这个方法本身应该返回的那个collector对象
        BiConsumer<A, ? super T> downstreamAccumulator = downstream.accumulator();
        //这里开始进行构造accumulator对象,这个对象是借助下游收集器的累加器对象来进行构造的
        BiConsumer<Map<K, A>, T> accumulator = (m, t) -> {
            K key = Objects.requireNonNull(classifier.apply(t), "element cannot be mapped to a null key");
            A container = m.computeIfAbsent(key, k -> downstreamSupplier.get());
            // container表示中间容器, t表示流中元素类型 
            downstreamAccumulator.accept(container, t);
        };
        //将部分结果合并到一起
        BinaryOperator<Map<K, A>> merger = Collectors.<K, A, Map<K, A>>mapMerger(downstream.combiner());
        @SuppressWarnings("unchecked")
        // mapFactory 是一个Supplier对象调用get方法返回中间结果类型,而中间结果类型是Map<K, A>类型,
        // 所以强制的转换成Map<K, A>类型是可以转换的
        Supplier<Map<K, A>> mangledFactory = (Supplier<Map<K, A>>) mapFactory;

        if (downstream.characteristics().contains(Collector.Characteristics.IDENTITY_FINISH)) {
            return new CollectorImpl<>(mangledFactory, accumulator, merger, CH_ID);
        }
        else {
            @SuppressWarnings("unchecked")
            Function<A, A> downstreamFinisher = (Function<A, A>) downstream.finisher();
            Function<Map<K, A>, M> finisher = intermediate -> {
                  //接收两个参数,返回一个结果
                intermediate.replaceAll((k, v) -> downstreamFinisher.apply(v));
                @SuppressWarnings("unchecked")
                M castResult = (M) intermediate;
                return castResult;
            };
            return new CollectorImpl<>(mangledFactory, accumulator, merger, finisher, CH_NOID);
        }
    }


    /**
     * Returns a {@code Collector} which partitions the input elements according
     * to a {@code Predicate}, reduces the values in each partition according to
     * another {@code Collector}, and organizes them into a
     * {@code Map<Boolean, D>} whose values are the result of the downstream
     * reduction.
     *
     * <p>There are no guarantees on the type, mutability,
     * serializability, or thread-safety of the {@code Map} returned.
     *
     * @param <T> the type of the input elements
     * @param <A> the intermediate accumulation type of the downstream collector
     * @param <D> the result type of the downstream reduction
     * @param predicate a predicate used for classifying input elements
     * @param downstream a {@code Collector} implementing the downstream
     *                   reduction
     * @return a {@code Collector} implementing the cascaded partitioning
     *         operation
     *
     * @see #partitioningBy(Predicate)
     */
    public static <T, D, A>
    Collector<T, ?, Map<Boolean, D>> partitioningBy(Predicate<? super T> predicate,
                                                    Collector<? super T, A, D> downstream) {
        BiConsumer<A, ? super T> downstreamAccumulator = downstream.accumulator();
        BiConsumer<Partition<A>, T> accumulator = (result, t) ->
                downstreamAccumulator.accept(predicate.test(t) ? result.forTrue : result.forFalse, t);
        BinaryOperator<A> op = downstream.combiner();
        BinaryOperator<Partition<A>> merger = (left, right) ->
                new Partition<>(op.apply(left.forTrue, right.forTrue),
                                op.apply(left.forFalse, right.forFalse));
        Supplier<Partition<A>> supplier = () ->
                  //分成两组,这里没有使用map是因为没法限制它有几组元素,
                  // 所以这里就没有使用map作为返回结果,而是自己定义了一个私有的静态类去描述这个结果。
                  // 而这个私有静态内部类把最终结果分为两部分,一部分是真的,一部分是假的。
                new Partition<>(downstream.supplier().get(),
                                downstream.supplier().get());
        if (downstream.characteristics().contains(Collector.Characteristics.IDENTITY_FINISH)) {
            return new CollectorImpl<>(supplier, accumulator, merger, CH_ID);
        }
        else {
            Function<Partition<A>, Map<Boolean, D>> finisher = par ->
                    new Partition<>(downstream.finisher().apply(par.forTrue),
                                    downstream.finisher().apply(par.forFalse));
            return new CollectorImpl<>(supplier, accumulator, merger, finisher, CH_NOID);
        }
    }
```



## 23、Stream 和 baseStream 源码分析

```
public interface Stream<T> extends BaseStream<T, Stream<T>>

S表示 BaseStream<T,S>类型的下级,从上面的Stream<T>可以知道到,这个实现符合BaseStream中S的规定,
Stream<T>中S的类型就是Stream<T>类型
public interface BaseStream<T, S extends BaseStream<T, S>
```



### 23.1 Spliterator 分割迭代器

```
/**
     * Creates a {@link Spliterator} over the elements in this collection.
     *
     * Implementations should document characteristic values reported by the
     * spliterator.  Such characteristic values are not required to be reported
     * if the spliterator reports {@link Spliterator#SIZED} and this collection
     * contains no elements.
     *
     * <p>The default implementation should be overridden by subclasses that
     * can return a more efficient spliterator.  In order to
     * preserve expected laziness behavior for the {@link #stream()} and
     * {@link #parallelStream()}} methods, spliterators should either have the
     * characteristic of {@code IMMUTABLE} or {@code CONCURRENT}, or be
     * <em><a href="Spliterator.html#binding">late-binding</a></em>.
     * If none of these is practical, the overriding class should describe the
     * spliterator's documented policy of binding and structural interference,
     * and should override the {@link #stream()} and {@link #parallelStream()}
     * methods to create streams using a {@code Supplier} of the spliterator,
     * as in:
     * <pre>{@code
     *     Stream<E> s = StreamSupport.stream(() -> spliterator(), spliteratorCharacteristics)
     * }</pre>
     * <p>These requirements ensure that streams produced by the
     * {@link #stream()} and {@link #parallelStream()} methods will reflect the
     * contents of the collection as of initiation of the terminal stream
     * operation.
     *
     * @implSpec
     * The default implementation creates a
        默认实现会创建一个 延迟绑定(late-binding) 的 spliterator,
        是从集合的Iterator(迭代器)创建的一个延迟绑定的分割迭代器。
       创建出来的分割迭代器会继承集合迭代器的 快速失败(遇到问题抛出异常不在往下走了)的属性。
     * <em><a href="Spliterator.html#binding">late-binding</a></em> spliterator
     * from the collections's {@code Iterator}.  The spliterator inherits the
     * <em>fail-fast</em> properties of the collection's iterator.
     * <p>
     * The created {@code Spliterator} reports {@link Spliterator#SIZED}.
     *
     * @implNote
     * The created {@code Spliterator} additionally reports
       创建出来的Spliterator会额外的增加一个SUBSIZED这样一个特性。Spliterator分割后会生成若干个块,
         那么每一个块的大小又是确定的这个就称为 SUBSIZED。
     * {@link Spliterator#SUBSIZED}.
     *
     * <p>If a spliterator covers no elements then the reporting of additional
     * characteristic values, beyond that of {@code SIZED} and {@code SUBSIZED},
     * does not aid clients to control, specialize or simplify computation.
     * However, this does enable shared use of an immutable and empty
     * spliterator instance (see {@link Spliterators#emptySpliterator()}) for
     * empty collections, and enables clients to determine if such a spliterator
     * covers no elements.
     *
     * @return a {@code Spliterator} over the elements in this collection
     * @since 1.8
     */
    @Override
    default Spliterator<E> spliterator() {
        return Spliterators.spliterator(this, 0);
    }



 /**
     专门为原生值设定的分割迭代器
     * A Spliterator specialized for primitive values.
         
     *  这个T类型必须是原生类型的包装类型,比如说针对于原生类型的int,对于的Integer
     * @param <T> the type of elements returned by this Spliterator.  The
     * type must be a wrapper type for a primitive type, such as {@code Integer}
     * for the primitive {@code int} type.
         
    针对原生类型特化的consumer
     * @param <T_CONS> the type of primitive consumer.  The type must be a
     * primitive specialization of {@link java.util.function.Consumer} for
     * {@code T}, such as {@link java.util.function.IntConsumer} for
     * {@code Integer}.
      原生的分割迭代器类型 
     * @param <T_SPLITR> the type of primitive Spliterator.  The type must be
     * a primitive specialization of Spliterator for {@code T}, such as
     * {@link Spliterator.OfInt} for {@code Integer}.
     *
     * @see Spliterator.OfInt
     * @see Spliterator.OfLong
     * @see Spliterator.OfDouble
     * @since 1.8
     */
    public interface OfPrimitive<T, T_CONS, T_SPLITR extends Spliterator.OfPrimitive<T, T_CONS, T_SPLITR>>
            extends Spliterator<T> {

}



/**
     * A Spliterator specialized for {@code int} values.
     * @since 1.8
     */
      // T就是Integer类型, T_CONS是 IntConsumer类型, T_SPLITR是ofInt类型
    public interface OfInt extends OfPrimitive<Integer, IntConsumer, OfInt> {
        @Override
        OfInt trySplit();

        //这个tryAdvance的顶层是OfPrimitive
        @Override
        boolean tryAdvance(IntConsumer action);

        @Override
        default void forEachRemaining(IntConsumer action) {
            do { } while (tryAdvance(action));
        }

        /**
         * {@inheritDoc}
         * @implSpec
         * If the action is an instance of {@code IntConsumer} then it is cast
         * to {@code IntConsumer} and passed to
           如果这个action是IntConsumer的实例的话那么他会强制的转换为IntConsumer然后传递个
           tryAdvance。
         * {@link #tryAdvance(java.util.function.IntConsumer)}; otherwise
         * the action is adapted to an instance of {@code IntConsumer}, by
         * boxing the argument of {@code IntConsumer}, and then passed to
         * {@link #tryAdvance(java.util.function.IntConsumer)}.
             否则这个action会被适配为IntConsumer实例,方式是通过对于IntConsumer的装箱
            然后再将它传递给上面的tryAdvance方法
         */
        @Override
        // 这个tryAdvance的顶层是Spliterator
        default boolean tryAdvance(Consumer<? super Integer> action) {
               // 这里为什么能判断 action是 IntConsumer呢？ 而且 IntConsumer和 Consumer
                  并没有继承关系。那么为什么能转换的呢？
               // 个人猜测这里是因为action中 ? 是Integer或者 Integer的上级,
               // 然后这里用action来判断是不是为IntConsumer。是为向上转型的操作吧 
               // 实际上 Consumer接口中接收的参数是accept(T t);
                 而IntConsumer中接收的餐是accept(int t)。
                因此Consumer和IntConsumer在参数T是Int/Integer是时候作用会重叠。
               为什么会重叠呢,这是因为JAVA中存在自动装箱和拆箱的操作。
                                               
            if (action instanceof IntConsumer) {
                return tryAdvance((IntConsumer) action);
            }
            else {
                if (Tripwire.ENABLED)
                    Tripwire.trip(getClass(),
                                  "{0} calling Spliterator.OfInt.tryAdvance((IntConsumer) action::accept)");
               // 对于lambda表达式的信息都是通过上下文推测出来的
                return tryAdvance((IntConsumer) action::accept);
            }
        }

        /**
         * {@inheritDoc}
         * @implSpec
         * If the action is an instance of {@code IntConsumer} then it is cast
         * to {@code IntConsumer} and passed to
         * {@link #forEachRemaining(java.util.function.IntConsumer)}; otherwise
         * the action is adapted to an instance of {@code IntConsumer}, by
         * boxing the argument of {@code IntConsumer}, and then passed to
         * {@link #forEachRemaining(java.util.function.IntConsumer)}.
         */
        //遍历后面所有的
        @Override
        default void forEachRemaining(Consumer<? super Integer> action) {
              //action传递的即可能是一个引用也可能传递的是一个lambda表达式,
              // 如果传递的是lambda表达式 那么符合Consumer接口的要求,也符合IntConsumer的要求
            if (action instanceof IntConsumer) {
                forEachRemaining((IntConsumer) action);
            }
            else {
                if (Tripwire.ENABLED)
                    Tripwire.trip(getClass(),
                                  "{0} calling Spliterator.OfInt.forEachRemaining((IntConsumer) action::accept)");
                 // 如果不是IntConsumer那么传递了一个 方法引用, 方法引用本身就是lambda表达式
                 // 当点击accept时会进入 Consumer接口中去,当点击:: 会进入IntConsumer中去
                 // 这是因为进行强制转换
                forEachRemaining((IntConsumer) action::accept);
            }
        }
}
```



### 23.2 、通过Consumer例子来 理解OfInt中 tryAdvance方法中的转换

```java
package com.learn.jdk.chapter40;

import java.util.function.Consumer;
import java.util.function.IntConsumer;

/**
 * chapter40
 * @ClassName: ConsumerTest
 * @Description: Consumer 和 IntConsumer
 * @Author: lin
 * @Date: 2020/1/17 15:02
 * History:
 * @<version> 1.0
 */
public class ConsumerTest {

    public void test(Consumer<Integer> consumer){
        consumer.accept(100);
    }

    public static void main(String[] args) {
        ConsumerTest consumerTest = new ConsumerTest();
         // 定义一个lambda表达式 既可以赋值给Consumer对象,也可以赋值给IntConsumer对象
        Consumer<Integer> consumer = i -> System.out.println(i);
        IntConsumer intConsumer = i -> System.out.println(i);

        System.out.println(consumer instanceof Consumer);
        System.out.println(intConsumer instanceof IntConsumer);

         // 面向对象的方式
         consumerTest.test(consumer);
          //运行报错,intConsumer是lambda表达式是不能转换为Consumer对象的
//         consumerTest.test((Consumer) intConsumer);

         //函数式方式
         consumerTest.test(consumer::accept);
         //函数式方式
         consumerTest.test(intConsumer::accept);
    }

}
```



## 24、ReferencePipeline和AbstractPipeline源码分析

```
/**
 一个抽象的基类是一个中间管道阶段或者是管道源阶段 里面的元素类型是U类型
 * Abstract base class for an intermediate pipeline stage or pipeline source
 * stage implementing whose elements are of type {@code U}.
 *
 * @param <P_IN> type of elements in the upstream source
 * @param <P_OUT> type of elements in produced by this stage
 *
 * @since 1.8
 */
abstract class ReferencePipeline<P_IN, P_OUT>
        extends AbstractPipeline<P_IN, P_OUT, Stream<P_OUT>>
        implements Stream<P_OUT>  {






    /**
       源阶段
     * Source stage of a ReferencePipeline.
     *
     * @param <E_IN> type of elements in the upstream source
               上游源元素类型
     * @param <E_OUT> type of elements in produced by this stage
               这个阶段源所 生成的元素类型。
     * @since 1.8
     */
    static class Head<E_IN, E_OUT> extends ReferencePipeline<E_IN, E_OUT> {

 /**
         * Constructor for the source stage of a Stream.
         *
         * @param source {@code Supplier<Spliterator>} describing the stream
         *               source
         * @param sourceFlags the source flags for the stream source, described
         *                    in {@link StreamOpFlag}
         */
        Head(Supplier<? extends Spliterator<?>> source,
             int sourceFlags, boolean parallel) {
            super(source, sourceFlags, parallel);
        }

        /**
         * Constructor for the source stage of a Stream.
         *
         * @param source {@code Spliterator} describing the stream source
         * @param sourceFlags the source flags for the stream source, described
         *                    in {@link StreamOpFlag}
         */
        Head(Spliterator<?> source,
             int sourceFlags, boolean parallel) {
            super(source, sourceFlags, parallel);
        }

        @Override
        final boolean opIsStateful() {
            throw new UnsupportedOperationException();
        }

        @Override
        final Sink<E_IN> opWrapSink(int flags, Sink<E_OUT> sink) {
            throw new UnsupportedOperationException();
        }

        // Optimized sequential terminal operations for the head of the pipeline
           针对于管道源的一个优化的串行终止操作
        // 也就是说在 被流的源对象所调用的时候,会直接调用这个forEach
        // list.stream().forEach()
         
        @Override
        public void forEach(Consumer<? super E_OUT> action) {
            if (!isParallel()) {
                sourceStageSpliterator().forEachRemaining(action);
            }
            else {
                super.forEach(action);
            }
        }
}



/**
 * Abstract base class for "pipeline" classes, which are the core
 * implementations of the Stream interface and its primitive specializations.
 * Manages construction and evaluation of stream pipelines.
 *
 * <p>An {@code AbstractPipeline} represents an initial portion of a stream
 * pipeline, encapsulating a stream source and zero or more intermediate
 * operations.  The individual {@code AbstractPipeline} objects are often
 * referred to as <em>stages</em>, where each stage describes either the stream
 * source or an intermediate operation.
 *
 * <p>A concrete intermediate stage is generally built from an
 * {@code AbstractPipeline}, a shape-specific pipeline class which extends it
 * (e.g., {@code IntPipeline}) which is also abstract, and an operation-specific
 * concrete class which extends that.  {@code AbstractPipeline} contains most of
 * the mechanics of evaluating the pipeline, and implements methods that will be
 * used by the operation; the shape-specific classes add helper methods for
 * dealing with collection of results into the appropriate shape-specific
 * containers.
 *
 * <p>After chaining a new intermediate operation, or executing a terminal
 * operation, the stream is considered to be consumed, and no more intermediate
 * or terminal operations are permitted on this stream instance.
 *
 * @implNote
 * <p>For sequential streams, and parallel streams without
 * <a href="package-summary.html#StreamOps">stateful intermediate
 * operations</a>, parallel streams, pipeline evaluation is done in a single
  这里注意: 函数式编程的执行方式 比如在一个集合中取出1元素执行完了一趟操作完后再执行下一个元素
  在24.1中的stream().map()...有解释 
 * pass that "jams" all the operations together.  For parallel streams with

 * stateful operations, execution is divided into segments, where each
 * stateful operations marks the end of a segment, and each segment is
 * evaluated separately and the result used as the input to the next
 * segment.  In all cases, the source data is not consumed until a terminal
     源数据直到终止操作开始的时候,源数据才开始被消费。
 * operation begins.
 *
 * @param <E_IN>  type of input elements
 * @param <E_OUT> type of output elements
 * @param <S> type of the subclass implementing {@code BaseStream}
 * @since 1.8
 */
abstract class AbstractPipeline<E_IN, E_OUT, S extends BaseStream<E_OUT, S>>
        extends PipelineHelper<E_OUT> implements BaseStream<E_OUT, S> {}
```

### 24.1、ReferencePipeline和AbstractPipeline源码分析

```
1.ReferencePipeline表示流的源阶段与中间阶段
2.AbstractPipeline.Head表示流的源阶段

两者大部分属性的设定都是类似的,但是存在一些属性是不同的,比如说Head是没有previousStage的,
而ReferencePipeline则是存在previousStage的等等。


stream().map(i -> i*2).filter(i > 10)....
将每一个元素拿出来,经历中间所有的操作 这个就是管道计算在一趟过程中就执行完了,
这一趟操作会将所有的操作都放置到一起去完成
1,2,3,4,5,6


list.stream().map(itm -> item).forEach( System.out::println);
这种方式会调用AbstractPipeline中的forEach方法
list.stream().forEach( System.out::println);
而这种方式会调用ReferencePipeline.Head中的forEach()
```



### 24.2、流调用机制和原理 如下例子

```
package com.learn.jdk.chapter36;

import java.util.Arrays;
import java.util.List;

/**
 * chapter36 stream源码分析
 * @ClassName: StreamTest10
 * @Description: Stream 源码分析 ,流调用机制和原理
 * @Author: lin
 * @Date: 2020/1/16 14:00
 * History:
 * @<version> 1.0
 */
public class StreamTest10 {
    public static void main(String[] args) {
        List<String> list = Arrays.asList( "hello", "world", "welcome");

       System.out.println(list.getClass());
        // 打印出来的class java.util.Arrays$ArrayList 这个是Arrays类中的一个内部类,
        // 只是这个名字和java.util.ArrayList类的名字相同,但是这两者是不同的类

        list.stream().forEach(System.out::println);
    }
}


list.stream执行的Collection中的Stream方法,
default Stream<E> stream() {
        return StreamSupport.stream(spliterator(), false);
    }
而默认方法Stream中又调用spliterator()方法

    default Spliterator<E> spliterator() {
        return Spliterators.spliterator(this, 0);
    }

这个spliterator()方法的有很多实现spliterator()方法并且重写了这个方法,
并且Arrays中的内部类ArrayList也实现了这个方法spliterator(),
所以在调用的时候应该是调用的这个静态内部类重写的spliterator()方法。
private static class ArrayList<E> extends AbstractList<E>{

  @Override
        public Spliterator<E> spliterator() {
            return Spliterators.spliterator(a, Spliterator.ORDERED);
        }
}

 public static <T> Spliterator<T> spliterator(Object[] array,
                                                 int additionalCharacteristics) {
        return new ArraySpliterator<>(Objects.requireNonNull(array),
                                      additionalCharacteristics);
    }


而在上面我们使用的是Arrays类中ArrayList静态内部类。
然后执行Spliterators类中的静态类 ArraySpliterator<T>的ArraySpliterator方法。
 public ArraySpliterator(Object[] array, int additionalCharacteristics) {
            this(array, 0, array.length, additionalCharacteristics);
        }
最后调用这个静态内部类中的forEachRemaining方法。
        @SuppressWarnings("unchecked")
        @Override
        public void forEachRemaining(Consumer<? super T> action) {
            Object[] a; int i, hi; // hoist accesses and checks from loop
            if (action == null)
                throw new NullPointerException();
            if ((a = array).length >= (hi = fence) &&
                (i = index) >= 0 && i < (index = hi)) {
                 这个循环才是真正的去遍历流当中的每一个元素的执行语句
                do { action.accept((T)a[i]); } while (++i < hi);
            }
        }




public class StreamTest10 {
    public static void main(String[] args) {
        List<String> list = Arrays.asList( "hello", "world", "welcome");
        list.stream().map(item -> item).forEach(System.out::println);
    }
}

对于map的执行过程:首先进入Stream接口类中 map方法
     /** 
         将给定的函数应用到这个流中每一个元素之后所返回的结果
       * Returns a stream consisting of the results of applying the given
       * function to the elements of this stream.
       *
       * <p>This is an <a href="package-summary.html#StreamOps">intermediate
       * operation</a>.
       *
       * @param <R> The element type of the new stream
       * @param mapper a <a href="package-summary.html#NonInterference">non-interfering</a>,
       *               <a href="package-summary.html#Statelessness">stateless</a>
       *               function to apply to each element
       * @return the new stream
       */
      <R> Stream<R> map(Function<? super T, ? extends R> mapper);
执行代码在 ReferencePipeline类中 map具体实现

    @Override
    @SuppressWarnings("unchecked")
    public final <R> Stream<R> map(Function<? super P_OUT, ? extends R> mapper) {
        Objects.requireNonNull(mapper);
         //返回对象是无状态的操作,StatelessOp是一个抽象类显然不能new,
         所以实现上返回的StatelessOp这个类的一个子类实例,这是匿名内部类的一个概念。
         这里new 出来是继承了StatelessOp类的子类对象。会把opWrapSink这个方法实现出来
        return new StatelessOp<P_OUT, R>(this, StreamShape.REFERENCE,
                                     StreamOpFlag.NOT_SORTED | StreamOpFlag.NOT_DISTINCT) {
            @Override
            Sink<P_OUT> opWrapSink(int flags, Sink<R> sink) {
                return new Sink.ChainedReference<P_OUT, R>(sink) {
                    @Override
                    public void accept(P_OUT u) {
                        downstream.accept(mapper.apply(u));
                    }
                };
            }
        };
    }

 对于StatelessOp类

    /**
       这个是一个中间无状态的流的一个基类
     * Base class for a stateless intermediate stage of a Stream.
     *
     * @param <E_IN> type of elements in the upstream source
     * @param <E_OUT> type of elements in produced by this stage
     * @since 1.8
     */
    abstract static class StatelessOp<E_IN, E_OUT>
            extends ReferencePipeline<E_IN, E_OUT> {
    /**
     * Construct a new Stream by appending a stateless intermediate
     * operation to an existing stream.
     *
      这个upstream上游表示 head, 如果有filter的话,那么对于filter的上游就是map
     * @param upstream The upstream pipeline stage
      上游管道特性
     * @param inputShape The stream shape for the upstream pipeline stage
      新的操作标识
     * @param opFlags Operation flags for the new stage
     */ 
       
        StatelessOp(AbstractPipeline<?, E_IN, ?> upstream,
                    StreamShape inputShape,
                    int opFlags) {
            super(upstream, opFlags);
            //上游得到他的输出shape一定等于当前输入的shape
            assert upstream.getOutputShape() == inputShape;
        }

注意:实际上流的串联就相当于一个双向链表

   AbstractPipeline(AbstractPipeline<?, E_IN, ?> previousStage, int opFlags) {
         前一个阶段已经被消费掉了,那么就抛出异常。对一个已经消费的流再去追加那么肯定是不可以的
        if (previousStage.linkedOrConsumed)
            throw new IllegalStateException(MSG_STREAM_LINKED);
        追加完之后立刻将前一个阶段linkedOrConsumed 设置为true
        previousStage.linkedOrConsumed = true;
        将下一个阶段设置为this
        previousStage.nextStage = this;

        this.previousStage = previousStage;
        this.sourceOrOpFlags = opFlags & StreamOpFlag.OP_MASK;
        this.combinedFlags = StreamOpFlag.combineOpFlags(opFlags, previousStage.combinedFlags);
        this.sourceStage = previousStage.sourceStage;
        if (opIsStateful())
            sourceStage.sourceAnyStateful = true;
        深度等于之前的深度加1
        this.depth = previousStage.depth + 1;
    }



===============================================================================================

/**
  sink是Consumer的扩展类用于在整个流管道的各个阶段去处理值
 * An extension of {@link Consumer} used to conduct values through the stages of
  提供一些额外的方法去管理这个大小的信息,控制的流程等
 * a stream pipeline, with additional methods to manage size information,
 在首次调用sink的accept()方法之前,
 * control flow, etc.  Before calling the {@code accept()} method on a
 * {@code Sink} for the first time, you must first call the {@code begin()}
 你必须要调用begin()方法去通知它 数据要过来了(可选还可以通知这个数据量是多少),
 * method to inform it that data is coming (optionally informing the sink how
  在所有数据都被发送过来之后 你必须要调用end()方法。 
 * much data is coming), and after all data has been sent, you must call the
 * {@code end()} method.  After calling {@code end()}, you should not call
  在调用完end()方法你就不应该再调用accept()方法，除非你再调用一次begin()方法，
  也就是说这个sink是可以重用的
 * {@code accept()} without again calling {@code begin()}.  {@code Sink} also
 Sink还提供了一种机制 sink可以协作的发出信号 它不希望接收任何数据了 
  通过cancellationRequested()方法来实现
 * offers a mechanism by which the sink can cooperatively signal that it does
 * not wish to receive any more data (the {@code cancellationRequested()}
  一个流就可以轮询发送更多的数据到sink之前 是否取消了。
 * method), which a source can poll before sending more data to the
 * {@code Sink}.
 *
 * <p>A sink may be in one of two states: an initial state and an active state.
  sink可以有两种状态，一种是初始状态另一种是激活状态。
 * It starts out in the initial state; the {@code begin()} method transitions
    首先它是从初始状态开始的，begin方法会将它转换成 激活状态
 * it to the active state, and the {@code end()} method transitions it back into
   而end()方法会将其转换会初始状态,那么这样调用end()方法就可以重用了。
 * the initial state, where it can be re-used.  Data-accepting methods (such as
   数据接收 比如accept()放只在激活状态下有效。
 * {@code accept()} are only valid in the active state.
 *
 * @apiNote
 * A stream pipeline consists of a source, zero or more intermediate stages
                                                         比如说汇聚或者for-ecah
 * (such as filtering or mapping), and a terminal stage, such as reduction or
 * for-each.  For concreteness, consider the pipeline:
 *
 * <pre>{@code
 *     int longestStringLengthStartingWithA
 *         = strings.stream()
 *                  .filter(s -> s.startsWith("A"))
 *                  .mapToInt(String::length)
 *                  .max();
 * }</pre>
 *
 * <p>Here, we have three stages, filtering, mapping, and reducing.  The
 * filtering stage consumes strings and emits a subset of those strings; the
                                                      
 * mapping stage consumes strings and emits ints; the reduction stage consumes
 * those ints and computes the maximal value.
 *
  Sink实例是用于表示管道当中的每一个阶段
 * <p>A {@code Sink} instance is used to represent each stage of this pipeline,
 无论这个阶段是接受的是对象，int,longs或者doubles等等
 * whether the stage accepts objects, ints, longs, or doubles.  Sink has entry
   Sink有一个入口点
 * points for {@code accept(Object)}, {@code accept(int)}, etc, so that we do
  我们不需要一个专门的接口针对原生的特化。
 * not need a specialized interface for each primitive specialization.  (It
 * might be called a "kitchen sink" for this omnivorous tendency.)  The entry
 * point to the pipeline is the {@code Sink} for the filtering stage, which
 * sends some elements "downstream" -- into the {@code Sink} for the mapping
 * stage, which in turn sends integral values downstream into the {@code Sink}
 * for the reduction stage. The {@code Sink} implementations associated with a
 * given stage is expected to know the data type for the next stage, and call
 * the correct {@code accept} method on its downstream {@code Sink}.  Similarly,
 * each stage must implement the correct {@code accept} method corresponding to
 * the data type it accepts.
 *
 * <p>The specialized subtypes such as {@link Sink.OfInt} override
 * {@code accept(Object)} to call the appropriate primitive specialization of
 * {@code accept}, implement the appropriate primitive specialization of
 * {@code Consumer}, and re-abstract the appropriate primitive specialization of
 * {@code accept}.
 *
 * <p>The chaining subtypes such as {@link ChainedInt} not only implement
 * {@code Sink.OfInt}, but also maintain a {@code downstream} field which
 * represents the downstream {@code Sink}, and implement the methods
 * {@code begin()}, {@code end()}, and {@code cancellationRequested()} to
 * delegate to the downstream {@code Sink}.  Most implementations of
 * intermediate operations will use these chaining wrappers.  For example, the
 * mapping stage in the above example would look like:
 *
 * <pre>{@code
 *     IntSink is = new Sink.ChainedReference<U>(sink) {
 *         public void accept(U u) {
 *             downstream.accept(mapper.applyAsInt(u));
 *         }
 *     };
 * }</pre>
 *
 * <p>Here, we implement {@code Sink.ChainedReference<U>}, meaning that we expect
 * to receive elements of type {@code U} as input, and pass the downstream sink
 * to the constructor.  Because the next stage expects to receive integers, we
 * must call the {@code accept(int)} method when emitting values to the downstream.
 * The {@code accept()} method applies the mapping function from {@code U} to
 * {@code int} and passes the resulting value to the downstream {@code Sink}.
 *
 * @param <T> type of elements for value streams
 * @since 1.8
 */
interface Sink<T> extends Consumer<T> {}
}

```



### 24.3 lambda表达式和匿名内部类的区别

```java
package com.learn.jdk.chapter44;

/**
 * chapter44
 * @ClassName: LambdaTest
 * @Description:
 * @Author: lin
 * @Date: 2020/1/19 14:05
 * History:
 * @<version> 1.0
 */
public class LambdaTest {
    /**
     * 这个this表示 当前类对象的实例。 lambda表达式和匿名内部类 完全不是同一个事
     *  匿名内部类是一个新的作用域，而lambda的作用域是外层作用域。
      */
    Runnable r1 = () -> System.out.println(this);

    /**
     * 这个 new Runnable(){} 表示实现里Runnable接口的一个实现类实例,
     * 这个类实际上是没有名字的 也就是匿名类
     */
    Runnable r2 = new Runnable() {
        @Override
        public void run() {
            // 这个this 表示匿名内部类 所对应的对象。
            System.out.println(this);
        }
    };

    public static void main(String[] args) {
        LambdaTest lambdaTest = new LambdaTest();
        Thread t1 = new Thread(lambdaTest.r1);
        t1.start();

        System.out.println("**************************");

        Thread t2 = new Thread(lambdaTest.r2);
        t2.start();

        // 问题:两个this的输出是什么,输出是一样的吗？ 如果不一样那么分别是什么？
        // 1、第一个r1打印的是 com.learn.jdk.chapter44.LambdaTest@5983b966
        // 2、第二个r2输出的是 com.learn.jdk.chapter44.LambdaTest$1@2cacedc9
        // 从第二个输出可以知道 LambdaTest$1其实是一个匿名内部类的特点, 这个匿名类本身没有名字所以是匿名类,
        // 那么在java中匿名类 表示是就是用外层的public类(文件名字)后面跟上一个$符合,
        // 然后按照顺序 如果第一个匿名类 就有$1来表示,如果在当前文件中出现第二个匿名类那么就是$2这样排下来
    }

}
```



### 24.4 ReferencePipeline类

```
 ReferencePipeline中 map 方法返回 Stream<T> ,而 返回的是new StatelessOp,
    这个StatelessOp是在ReferencePipeline类中的一个静态类并且 继承了ReferencePipeline类，
    而ReferencePipeline类又实现了Stream，所以这里可以返回StatelessOp的一个对象。
    StatelessOp 本身是一个抽象类显然是不能new的，所以这里return new StatelessOp()是匿名类，
    实际上是继承了StatelessOp的一个具体子类的对象，这个子类是没有名字的因此是一个匿名类。
  @Override
    @SuppressWarnings("unchecked")
    public final <R> Stream<R> map(Function<? super P_OUT, ? extends R> mapper) {
        Objects.requireNonNull(mapper);
           //this表示ReferencePipeline, ReferencePipelineb表示多个中间操作中的一个
        return new StatelessOp<P_OUT, R>(this, StreamShape.REFERENCE,
                                     StreamOpFlag.NOT_SORTED | StreamOpFlag.NOT_DISTINCT) {
            @Override
            // 操作包装 就是将操作合在一起。比如 stream-map-filter-forEach 
            //就是一个元素经历了这三个操作的所以阶段 map-filter-forEach，
            // 这就是opWrapSink的作用
            Sink<P_OUT> opWrapSink(int flags, Sink<R> sink) {
                return new Sink.ChainedReference<P_OUT, R>(sink) {
                    @Override
                    public void accept(P_OUT u) {
                        downstream.accept(mapper.apply(u));
                    }
                };
            }
        };
    }

第一个参数是AbstractPipeline，因为ReferencePipelineb是AbstractPipeline类的子类。
第二个参数是流的类别。
第三个是流的特性标志
这里调用的是父类的构造方法super(upstream, opFlags);
StatelessOp(AbstractPipeline<?, E_IN, ?> upstream,
                    StreamShape inputShape,
                    int opFlags) {
            super(upstream, opFlags);
            assert upstream.getOutputShape() == inputShape;
        }


    /**
     将一个中间操作给追加到存在的管道上
     * Constructor for appending an intermediate operation onto an existing
     * pipeline.
     *  上游元素 的源
     * @param upstream the upstream element source.
     */
    ReferencePipeline(AbstractPipeline<?, P_IN, ?> upstream, int opFlags) {
        super(upstream, opFlags);
    }

stream ------>map ----filter------forEach
upStream              downStream


如果父类中规定了一种执行顺序，然后每一个顺序当中每一个步骤该怎么做父类它是不管的，
而是需要又子类去实现 如果遇到了这种情况 一定是遇到了模板方法模式。
比如这里的Sink类中 begin(), accept(), end()方法



  // Terminal operations from Stream

    @Override
    public void forEach(Consumer<? super P_OUT> action) {
        evaluate(ForEachOps.makeRef(action, false));
    }

```



## 25、TerminalOp源码分析与终止操作层次

```
/**
  在流管道的一个操作它会接收一个流作为输入并且生成一个结果或者拥有副作用的。
  什么是副作用 就是调用一个方法，方法传递了一参数 最终在方法实现里面 修改了参数。
 比如传递了一个对象的引用 可能在这个方法执行代码里修改传进来的引用参数所指向的那个对象某些成员变量
 这就叫做副作用。
 * An operation in a stream pipeline that takes a stream as input and produces
 * a result or side-effect.  A {@code TerminalOp} has an input type and stream
     一个终止操作有一个输入类型和流的状态，和结果类型。TerminalOp还用于一组操作标识
 * shape, and a result type.  A {@code TerminalOp} also has a set of
 它描述了操作是如何处理流当中元素的 比如短路或者按照顺序的执行
 * <em>operation flags</em> that describes how the operation processes elements
 * of the stream (such as short-circuiting or respecting encounter order; see
 * {@link StreamOpFlag}).
 * 
  TerminalOp必须要提供一种串行和并行操作的实现，根据给定的流源和特定的中间操作。
 * <p>A {@code TerminalOp} must provide a sequential and parallel implementation
 * of the operation relative to a given stream source and set of intermediate
 * operations.
 *
 * @param <E_IN> the type of input elements
 * @param <R>    the type of the result
 * @since 1.8
 */
interface TerminalOp<E_IN, R> {

  /** 执行一种并行操作的计算使用给定的PipelineHelper，它描述了上游的中间操作
   * Performs a parallel evaluation of the operation using the specified
   * {@code PipelineHelper}, which describes the upstream intermediate
   * operations.
   *  
    默认的使用指定PipelineHelper来执行的串行的计算
   * @implSpec The default performs a sequential evaluation of the operation
   * using the specified {@code PipelineHelper}.
   *
   * @param helper the pipeline helper
   * @param spliterator the source spliterator
   * @return the result of the evaluation
   */
  //并行实现，会使用串行的方式来执行。
  default <P_IN> R evaluateParallel(PipelineHelper<E_IN> helper,
                                    Spliterator<P_IN> spliterator) {
      if (Tripwire.ENABLED)
          Tripwire.trip(getClass(), "{0} triggering TerminalOp.evaluateParallel serial default");
      return evaluateSequential(helper, spliterator);
  }

}
```



### 25.1、ForEachOps 类

```
/**
 * Factory for creating instances of {@code TerminalOp} that perform an
 * action for every element of a stream.  Supported variants include unordered
 * traversal (elements are provided to the {@code Consumer} as soon as they are
 * available), and ordered traversal (elements are provided to the
 * {@code Consumer} in encounter order.)
 *
 * <p>Elements are provided to the {@code Consumer} on whatever thread and
 * whatever order they become available.  For ordered traversals, it is
 * guaranteed that processing an element <em>happens-before</em> processing
 * subsequent elements in the encounter order.
 *
 * <p>Exceptions occurring as a result of sending an element to the
 * {@code Consumer} will be relayed to the caller and traversal will be
 * prematurely terminated.
 *
 * @since 1.8
 */
final class ForEachOps {
    /**  
     * Constructs a {@code TerminalOp} that perform an action for every element
     * of a stream.
     *
     * @param action the {@code Consumer} that receives all elements of a
     *        stream
     * @param ordered whether an ordered traversal is requested
     * @param <T> the type of the stream elements
     * @return the {@code TerminalOp} instance
     */
    public static <T> TerminalOp<T, Void> makeRef(Consumer<? super T> action,
                                                  boolean ordered) {
        Objects.requireNonNull(action);
        return new ForEachOp.OfRef<>(action, ordered);
    }


   static abstract class ForEachOp<T>
            implements TerminalOp<T, Void>, TerminalSink<T, Void> {

           @Override
           public <S> Void evaluateSequential(PipelineHelper<T> helper,
                                              Spliterator<S> spliterator) {
               return helper.wrapAndCopyInto(this, spliterator).get();
           }
  }

}
```



### 25.2、PipelineHelper类分析

```

/**
  Helper类 用于执行StreamOps流管道，
在一处捕获有关流管道的所以信息（输出形状，中间操作，流标志，并行性等）。
 * Helper class for executing <a href="package-summary.html#StreamOps">
 * stream pipelines</a>, capturing all of the information about a stream
 * pipeline (output shape, intermediate operations, stream flags, parallelism,
 * etc) in one place.
 *
 * <p>
PipelineHelper描述了一个流管道最初的分块，包含了它的源、中间操作和额外的附加的 合并信息
 * A {@code PipelineHelper} describes the initial segment of a stream pipeline,
 * including its source, intermediate operations, and may additionally
 * incorporate information about the terminal (or stateful) operation which

 * follows the last intermediate operation described by this
 * {@code PipelineHelper}. The {@code PipelineHelper} is passed to the
 * {@link TerminalOp#evaluateParallel(PipelineHelper, java.util.Spliterator)},
 * {@link TerminalOp#evaluateSequential(PipelineHelper, java.util.Spliterator)},
 * and {@link AbstractPipeline#opEvaluateParallel(PipelineHelper, java.util.Spliterator,
 * java.util.function.IntFunction)}, methods, which can use the
 * {@code PipelineHelper} to access information about the pipeline such as
 * head shape, stream flags, and size, and use the helper methods
 * such as {@link #wrapAndCopyInto(Sink, Spliterator)},
 * {@link #copyInto(Sink, Spliterator)}, and {@link #wrapSink(Sink)} to execute
 * pipeline operations.
 *
 * @param <P_OUT> type of output elements from the pipeline
 * @since 1.8
 */
abstract class PipelineHelper<P_OUT> {

  /**  应用由PipelineHelper所描述的管道阶段，把这个管道阶段应用给Spliterator，
        然后将结果发送给所提供的sink对象
       * Applies the pipeline stages described by this {@code PipelineHelper} to
       * the provided {@code Spliterator} and send the results to the provided
       * {@code Sink}.
       *
       * @implSpec
       * The implementation behaves as if:
       * <pre>{@code
       *     intoWrapped(wrapSink(sink), spliterator);
       * }</pre>
       *
       * @param sink the {@code Sink} to receive the results
       * @param spliterator the spliterator describing the source input to process
       */
      abstract<P_IN, S extends Sink<P_OUT>> S wrapAndCopyInto(S sink, Spliterator<P_IN> spliterator);


 /**
      这个包装会接收PipelineHelper的类型的输出元素，然后使用一个sink来对其进行包装
     * Takes a {@code Sink} that accepts elements of the output type of the
     * {@code PipelineHelper}, and wrap it with a {@code Sink} that accepts
      并且实现所有由PipelineHelper描述的的中间操作，并将结果传递给所提供的sink上
     * elements of the input type and implements all the intermediate operations
     * described by this {@code PipelineHelper}, delivering the result into the
     * provided {@code Sink}.
     *
     * @param sink the {@code Sink} to receive the results
     * @return a {@code Sink} that implements the pipeline stages and sends
     *         results to the provided {@code Sink}
     */
    // 这个方法本质上完成了对多个流的串联
    abstract<P_IN> Sink<P_IN> wrapSink(Sink<P_OUT> sink);
   

    /** 将Spliterator中获取到的元素推送到所提供的sink当中，
     * Pushes elements obtained from the {@code Spliterator} into the provided
       如果流管道已经知道有短路阶段，那么会进行短路的判断
     * {@code Sink}.  If the stream pipeline is known to have short-circuiting
     * stages in it (see {@link StreamOpFlag#SHORT_CIRCUIT}), the
     * {@link Sink#cancellationRequested()} is checked after each
     * element, stopping if cancellation is requested.
     *
     * @implSpec
       这个方法遵循sink的协议 先去调用 begin 在推元素之前。 然后调用Sink.accept,最后去调用
      Sink.end 当所有元素被推送后
     * This method conforms to the {@code Sink} protocol of calling
     * {@code Sink.begin} before pushing elements, via {@code Sink.accept}, and
     * calling {@code Sink.end} after all elements have been pushed.
     *
     * @param wrappedSink the destination {@code Sink}
     * @param spliterator the source {@code Spliterator}
     */
      将操作连接起来后还没处理完，要将元素逐个的推到所提供好的已经包装好的Sink对象当中。
    abstract<P_IN> void copyInto(Sink<P_IN> wrappedSink, Spliterator<P_IN> spliterator);
}
```



### 25.3 AbstractPipeline 类

```

/**
 * Abstract base class for "pipeline" classes, which are the core
 * implementations of the Stream interface and its primitive specializations.
 * Manages construction and evaluation of stream pipelines.
 *
 * <p>An {@code AbstractPipeline} represents an initial portion of a stream
 * pipeline, encapsulating a stream source and zero or more intermediate
 * operations.  The individual {@code AbstractPipeline} objects are often
 * referred to as <em>stages</em>, where each stage describes either the stream
 * source or an intermediate operation.
 *
 * <p>A concrete intermediate stage is generally built from an
 * {@code AbstractPipeline}, a shape-specific pipeline class which extends it
 * (e.g., {@code IntPipeline}) which is also abstract, and an operation-specific
 * concrete class which extends that.  {@code AbstractPipeline} contains most of
 * the mechanics of evaluating the pipeline, and implements methods that will be
 * used by the operation; the shape-specific classes add helper methods for
 * dealing with collection of results into the appropriate shape-specific
 * containers.
 *
 * <p>After chaining a new intermediate operation, or executing a terminal
 * operation, the stream is considered to be consumed, and no more intermediate
 * or terminal operations are permitted on this stream instance.
 *
 * @implNote
 * <p>For sequential streams, and parallel streams without
 * <a href="package-summary.html#StreamOps">stateful intermediate
 * operations</a>, parallel streams, pipeline evaluation is done in a single
 * pass that "jams" all the operations together.  For parallel streams with
 * stateful operations, execution is divided into segments, where each
 * stateful operations marks the end of a segment, and each segment is
 * evaluated separately and the result used as the input to the next
 * segment.  In all cases, the source data is not consumed until a terminal
 * operation begins.
 *
 * @param <E_IN>  type of input elements
 * @param <E_OUT> type of output elements
 * @param <S> type of the subclass implementing {@code BaseStream}
 * @since 1.8
 */
abstract class AbstractPipeline<E_IN, E_OUT, S extends BaseStream<E_OUT, S>>
        extends PipelineHelper<E_OUT> implements BaseStream<E_OUT, S> {
// Terminal evaluation methods
    
    /**
     * Evaluate the pipeline with a terminal operation to produce a result.
     *
     * @param <R> the type of result
     * @param terminalOp the terminal operation to be applied to the pipeline.
     * @return the result
     */
    final <R> R evaluate(TerminalOp<E_OUT, R> terminalOp) {
        assert getOutputShape() == terminalOp.inputShape();
        if (linkedOrConsumed)
            throw new IllegalStateException(MSG_STREAM_LINKED);
        linkedOrConsumed = true;

        return isParallel()
               ? terminalOp.evaluateParallel(this, sourceSpliterator(terminalOp.getOpFlags()))
               : terminalOp.evaluateSequential(this, sourceSpliterator(terminalOp.getOpFlags()));
    }

          // ForEachOps类中的实现
        @Override
        public <S> Void evaluateSequential(PipelineHelper<T> helper,
                                           Spliterator<S> spliterator) {
            return helper.wrapAndCopyInto(this, spliterator).get();
        }

     //实现
    @Override
    final <P_IN, S extends Sink<E_OUT>> S wrapAndCopyInto(S sink, Spliterator<P_IN> spliterator) {
        copyInto(wrapSink(Objects.requireNonNull(sink)), spliterator);
        return sink;
    }


   //具体实现
    @Override
    @SuppressWarnings("unchecked")
    final <P_IN> Sink<P_IN> wrapSink(Sink<E_OUT> sink) {
        Objects.requireNonNull(sink);

        for ( @SuppressWarnings("rawtypes") AbstractPipeline p=AbstractPipeline.this; p.depth > 0; p=p.previousStage) {
             //这里opWrapSink的实现在ReferencePipeline类中map方法可以找到其实现。
             ReferencePipeline类中map方法对opWrapSink方法的具体实现方法就是用来返回一个Sink.ChainedReference
            sink = p.opWrapSink(p.previousStage.combinedFlags, sink);
        }
        return (Sink<P_IN>) sink;
    }

    @Override
    final <P_IN> void copyInto(Sink<P_IN> wrappedSink, Spliterator<P_IN> spliterator) {
       Objects.requireNonNull(wrappedSink);

       if (!StreamOpFlag.SHORT_CIRCUIT.isKnown(getStreamAndOpFlags())) {
           //模板方法模式的一个标准实现
           
           //获取到一个精确的大小
           wrappedSink.begin(spliterator.getExactSizeIfKnown());
            //对于一个流来说 它有n个中间操作 和一个终止操作，最终这个操作就是通过这个方法来完成的
           // 这个方法把中间多个操作封装成wrappedSink对象，
           //每一个元素就经历这个wrappedSink所对应的n个中间操作和一个终止操作来完成整个的操作。
           spliterator.forEachRemaining(wrappedSink);
           wrappedSink.end();
       }
       else {
           copyIntoWithCancel(wrappedSink, spliterator);
       }
    }

}
```

