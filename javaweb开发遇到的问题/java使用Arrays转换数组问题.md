```java
 public static void testArrayList1(){
        Integer[] arr={1,2,3,4,5,6,7,8,9,10};
        //ArrayList<Integer> arrayList1= (ArrayList<Integer>) Arrays.asList(arr);
        List<Integer> list= (List<Integer>) Arrays.asList(arr);
        ArrayList<Integer> arrayList=new ArrayList<> (list);
        list.add(10);
        list.add(101);
        /**
         * java.lang.UnsupportedOperationException
         *
         * */
        System.out.println(arrayList.size());
        System.out.println(getCapacity(arrayList));
        arrayList.trimToSize();
        System.out.println(getCapacity(arrayList));
        Iterator<Integer> iterator=arrayList.iterator();

        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }

    }
```

将数组转换成ArrayList时，存在以下问题：

1、使用Array.asList(T... a)返回的的ArrayList是Array类中的一个静态内部类，不是java.util.ArrayList

```
public static <T> List<T> asList(T... a) {
    return new ArrayList<>(a);
}
```

2、该类继承自抽象类AbstractList，该类的add()、remove()方法默认抛出UnsupportedOperationException异常，

```
public void add(int index, E element) {
    throw new UnsupportedOperationException();
}
 public E remove(int index) {
        throw new UnsupportedOperationException();
    }
```

所以无法将

​	< font color="red">Arrays.asList(arr)返回值直接赋值给java.util.ArrayList；

​	不能对该类进行增删操作。</font>



正确的操作方法：

 List<Integer> list= (List<Integer>) Arrays.asList(arr);
  ArrayList arrz=new ArrayList(list);

```java
public static void testArrayList2(){
        Integer[] arr={1,2,3,4,5,6,7,8,9,10};
        List<Integer> list= (List<Integer>) Arrays.asList(arr);
        ArrayList arrz=new ArrayList(list);
        arrz.add(10);
        arrz.add(20);
        System.out.println(arrz.size());
        System.out.println(getCapacity(arrz));
        arrz.trimToSize();
        System.out.println(getCapacity(arrz));
    }
//返回ArrayList中容量的大小
public static int getCapacity(ArrayList list){
    try{
        Class cls=list.getClass();
        Field field=cls.getDeclaredField("elementData");
        field.setAccessible(true);
        Object[] obj = (Object[]) field.get(list);
        return obj.length;
    }catch (Exception e){
        e.printStackTrace();
        return  -1;
    }
}
```