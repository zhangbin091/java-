### 一、枚举类的声明

```java
public enum Size {
    //定义了常量
    SMALL,MEDIUM,LARGE,EXTRA_LARGE
}
```

枚举类声明以关键字enum创建，可以包含构造器、方法以及域

```java
public enum Size2 {
    SMALL("S", 50),MEDIUM("M", 60),LARGE("L", 70),EXTRA_LARGE("XL", 80);
    private String scale;
    private int size;
    Size2(String scale, int size) {
        this.scale=scale;
        this.size = size;
    }
    public String getScale() {
        return scale;
    }
    public int getSize() {
        return size;
    }
}

```

所有的枚举类型都是Enum类的子类，toString()返回枚举常量名

```java
public abstract class Enum<E extends Enum<E>>
        implements Comparable<E>, Serializable {
    private final String name;//枚举常量的名字
    public final String name() {
        return name;
    }
     private final int ordinal;//枚举常量在枚举中声明的位置
     public final int ordinal() {
        return ordinal;
    }
    public String toString() {
        return name;
    }
}
```



枚举可以有构造器、方法和域，构造器只有在构造枚举常量的时候被调用
比较两个枚举类型的值时，不需要调用equals，直接使用“==“就可以了；

注意：当枚举中添加新方法时，需要在enum实例序列的最后添加一个分号，且必须定义enum实例

### 二、枚举的使用

```java
 public static void main(String[] args) {
        Size2 size2 = Size2.SMALL;
        System.out.println(size2.toString());//返回枚举常量的名字
        System.out.println(size2.name());//返回枚举常量的名字
        Size2 size21=Enum.valueOf(Size2.class, "MEDIUM");
        Size2 size22=Size2.valueOf("MEDIUM");
        System.out.println(size22);//返回枚举常量的名字
        System.out.println(size21.toString());
        System.out.println(size21.getScale());
        Size2[] values = Size2.values();//返回全部枚举值
        for (Size2 size22: values) {
            System.out.println(size22.toString());
        }
        System.out.println(Size2.EXTRA_LARGE.ordinal());
        System.out.println(Size2.SMALL.ordinal());//ordinal()返回枚举常量值在枚举变量声明中的位置位置从0开始
    }
```



```
获取枚举值有三种方式：
1、枚举类.枚举实例
2、Enum.valueOf(枚举类.class,"枚举常量名")
3、枚举类.valueOf("枚举常量名")
打印枚举常量名有三种方式
1、枚举常量名.toString()
2、枚举常量名.name()
3、枚举常量名
```