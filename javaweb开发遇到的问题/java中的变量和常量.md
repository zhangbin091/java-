### 一、变量

可以变化的量，一个空间中存储的值可以变化。

```java
int a;//a可以随意赋值
String str;
```

类型 变量名；进行声明。

变量可以分为：局部变量，成员变量，类变量

```java
public class Demo01 {
    int a=10;//实例变量，成员变量，属于实例
    static int b=20;//类变量，属于类
    final int d=30;//常量，属于实例的常量
    final static int c=200;//常量，属于类的常量
    public static void main(String[] args) {
        Demo01 demo01=new Demo01();
        demo01.show();
    }
    public void show(){
        int a=100;//局部变量
        System.out.println(a);
    }
}
```

##### 1、局部变量

在方法中声明的变量

##### 2、成员变量（实例变量）

类的成员，属于对象

##### 3、类变量，静态变量

被static修饰的变量，属于类

### 二、常量

##### 1、区分常量值与常量

常量值是常量的具体和直观的变现形式，常量是形式化的变现。

常量值通过数据直接表示的：

int类型常量值： 1、2、3、

实型常量值：126.25、225.45

布尔类型常量值：true（真）、false（假）

字符型与字符串常量值： 'e'、'E' 、"22"

转义字符： \'

##### 2、常量的声明

常量不同于常量值，他可以在程序中用符号来代替常量值使用，必须先定义、初始化，不然无法使用，常量一旦被初始化就不可以被修改。

java中通过final来定义一个常量。





### 三、final修饰符的使用

##### 1、在类上使用

表示该类是一个最终类，无法被继承，比如String类型。

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
   ......
    }
```

##### 2、在方法上使用

表示该方法是一个最终方法，无法被子类重写

```java
public class Demo {
    public static void main(String[] args) {
        Son son=new Son();
        son.show();
    }
}
class Father{
     final void show(){
        System.out.println("father show");
    }
}
class Son extends Father{
//    public final void show() {
// 编译不通过，final修饰的方法不能被重写，但此方法仍然被继承
//    }
}
```

##### 3、在变量上使用

修饰基本类型，表示声明一个常量，值无法改变。

修饰一个引用数据类型，比如对象、数组，对象、数组存储的内容可以改变，但是该常量无法被重新赋值，无法改变地址的引用。

在方法中用final修饰参数，同上

```java
public class Test {
    public static void main(String[] args) {
        Test test=new Test();
        test.showInt(6);//显示6
        A a=new A(8);
        a.showA();//显示 5
        
    }
    public void showInt(final int a){
        //a=20;对于基本类型，基本类型的值在方法内部是不能够改变的，编译不通过,final修饰的变量a无法被修改
        System.out.println(a);
    }
    public void showA(final A a){
        //a=new A(5);引用类型，无法对a进行重新赋值，引用无法改变
        a.setA(5);//a的属性可以修改，引用类型执行的地址不能修改
        a.showA();
    }
}
class A{
    private int a;
    public A() {
    }
    public A(int a) {
        this.a = a;
    }
    public void showA(){
        System.out.println(a);
    }
    public int getA() {
        return a;
    }
    public void setA(int a) {
        this.a = a;
    }
}
```





