Java 流
---
1. 本文主要记录了关于Java8中的Stream的使用

# 1. 引入
```java
List<Integer> list = new ArrayList<>();
for (int i = 0; i < 100; i++) 
{
    list.add(rd.nextInt(101));
}

System.out.println(list.stream().filter(t -> t >= 30 && t <= 70).count());
```
1. 上面的程序中，我们统计了这个List中t大于30并且小于70的个数

# 2. API
| 方法名                                                                            | 功能                                                                                                                                                                             | 备注                                                                                                  |
| --------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| stream()                                                                          | 集合转换成流，后者是并行流方法。                                                                                                                                                 |
| parallelStream()                                                                  | 集合转换为并行流                                                                                                                                                                 | -                                                                                                     |
| filter(T -> boolean_exp)                                                          | 保留T中boolean_exp为true的元素                                                                                                                                                   | -                                                                                                     |
| collect(toList())                                                                 | 可以将流再转会List                                                                                                                                                               | -                                                                                                     |
| distinct()                                                                        | 去掉重复元素                                                                                                                                                                     | 记得提前重写类的equals方法                                                                            |
| sorted()/sorted((T,T)->int)                                                       | 如果流中的元素的类实现了 Comparable 接口，即有自己的排序规则，那么可以直接调用 sorted() 方法对元素进行排序，如 Stream。反之, 需要调用 sorted((T, T) -> int) 实现 Comparator 接口 | `list = list.stream().sorted((p1, p2) -> p1.getAge() - p2.getAge()).collect(toList());`               |
| limit(long n)/skip(long n)                                                        | 返回前n个元素和去掉前n个元素                                                                                                                                                     | -                                                                                                     |
| map(T -> R)                                                                       | 将流中的每一个元素 T 映射为 R（类似类型转换）                                                                                                                                    | -                                                                                                     |
| flatMap(T -> Stream)                                                              | 将流中的每一个元素T映射成一个流，然后再把每一个流连接成为一个流                                                                                                                  | -                                                                                                     |
| anyMatch(T -> boolean_exp)/allMatch(T -> boolean_exp)/noneMatch(T -> boolean_exp) | 流中是否有一个元素/全部元素/没有元素匹配给定的boolean_exp条件                                                                                                                    | -                                                                                                     |
| findAny()                                                                         | 找到其中一个元素 （使用 stream() 时找到的是第一个元素；使用 parallelStream() 并行时找到的是其中一个元素）                                                                        | -                                                                                                     |
| findFirst()                                                                       | 找到第一个元素                                                                                                                                                                   | -                                                                                                     |
| count()                                                                           | 返回个数                                                                                                                                                                         | long类型                                                                                              |
| collect()                                                                         | 收集方法，参数是一个收集器接口                                                                                                                                                   | -                                                                                                     |
| forEach()                                                                         | 对于之中的每一个进行处理                                                                                                                                                         | `list.stream().forEach(System.out::println);`<br>`list.stream().forEach(PersonMapper::insertPerson);` |

## 2.1. reduce方法
1. `reduce((T,T)->T)/reduce(T,(T,T)->T)`
2. 用于组合流中的元素，如求和，求积，求最大值等 

```java
int sum = list.stream().map(Person::getAge).reduce(0, (a, b) -> a + b);
int sum = list.stream().map(Person::getAge).reduce(0, Integer::sum);
```

3. reduce 第一个参数 0 代表起始值为 0，lambda (a, b) -> a + b 即将两值相加产生一个新值 也可以

```java
Optional<Integer> sum = list.stream().map(Person::getAge).reduce(Integer::sum);
```

4. 上述返回的是一个Optional对象，是一个容器类，表示一个值是否存在

## 2.2. sorted()方法

### 2.2.1. 自然排序

```java
public void natural() {
    List<String> list = Arrays.asList("q", "a","b");
    // 排序后输出
    list.stream().sorted().forEach(System.out::println);
    // a
    // b
    // q
}
```

### 2.2.2. 定制排序
```java
@Data
public class man{
    private Integer loc;
    private String dname;
    private String deptno;
}

public void modify() {
    List<String> deptList = new ArrayList<man>();
    // 排序
    deptList.stream().sorted((x,y) -> {
        if(x.getDeptno().equals(y.getDeptno())) {
            return x.getDname().compareTo(y.getDname());
        }else {
            return x.getLoc().compareTo(y.getLoc())
        }
    });
    // 打印
    deptList.stream().sorted((x,y) -> {
        if(x.getDeptno().equals(y.getDeptno())) {
            return x.getDname().compareTo(y.getDname());
        }else {
            return x.getLoc().compareTo(y.getLoc())
        }
    });
}
```

# 3. 具体例子实现

## 3.1. 合并两个List并且去掉重复项
1. Java8之前是通过Set完成的

```java
Set<String> set = new HashSet<>(listA);
set.addAll(listB);
List<String> list = new ArrayList<>(set);
System.out.println(list);
```

2. Java8之后是通过Stream相关的流操作完成

```java
List<String> collect = Stream.of(listA, listB)
                .flatMap(Collection::stream)
                .distinct()
                .collect(Collectors.toList());
```

3. 如果要合并的是对象，则重写equals和hashcode方法即可。

# 4. 参考
1. <a href = "https://jingyan.baidu.com/article/ac6a9a5e379c722b653eacbd.html">java8中Stream API如何排序数据</a>