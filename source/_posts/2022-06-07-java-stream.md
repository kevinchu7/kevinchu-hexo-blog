---
title: Java8 Stream API学习理解
index_img: https://static.kevinchu.top/blog/assets/img/cover_022.jpeg
date: 2022-06-07 08:29:54
tags:
    - Java
categories:
    - 学习记录
---
>

>Stream是Java 8提供的新功能，是对集合（Collection）对象功能的增强，能对集合对象进行各种非常便利、高效的聚合操作（aggregate operation），或者大批量数据操作 (bulk data operation)。 与Lambda 表达式结合，也可以提高编程效率、简洁性和程序可读性。同时它提供串行和并行两种模式进行汇聚操作，并发模式能够充分利用多核处理器的优势，使用 fork/join 并行方式来拆分任务和加速处理过程。通常编写并行代码很难而且容易出错, 但使用 Stream API 无需编写一行多线程的代码，就可以很方便地写出高性能的并发程序。 
>
>关于Stream的深入理解，发现 [CarpenterLee](https://github.com/CarpenterLee)这位大神写得非常透彻，附上他的四篇关于Stream的博文地址：
https://www.cnblogs.com/CarpenterLee/p/6545321.html
https://www.cnblogs.com/CarpenterLee/p/6550212.html
https://www.cnblogs.com/CarpenterLee/p/6637118.html
https://www.cnblogs.com/CarpenterLee/p/6675568.html
>
>本文参考、转载自博客：https://blog.csdn.net/weixin_41231928/article/details/105930941

## 1.Stream是什么
Stream流其实是一个集合元素的函数模型，它并不是集合，也不是数据结构，其本身并不存储任何 元素（或其地址值），Stream是一个来自数据源的元素队列。它是有关算法和计算的，它更像一个高级版本的 Iterator。原始版本的 Iterator，用户只能显式地一个一个遍历元素并对其执行某些操作；高级版本的 Stream，用户只要给出需要对其包含的元素执行什么操作，比如 “过滤掉长度大于 10 的字符串”、“获取每个字符串的首字母”等，Stream 会隐式地在内部进行遍历，做出相应的数据转换。

需要注意的是：**Stream 就如同一个迭代器（Iterator），单向，不可往复，数据只能遍历一次，遍历过一次后即用尽了，就好比流水从面前流过，一去不复返。**


## 2.Stream流的构造及转换
Stream流的构造如下： 
```Java
String [] stringArray = new String[]{"a", "b", "c"};
// 1. Stream.of()
Stream stream1 = Stream.of(stringArray);
// 2. Arrays.stream()
Stream stream2 = Arrays.stream(stringArray);
// 3. Collections.stream()
List<String> list = Arrays.asList(stringArray);
Stream stream3 = list.stream();
// 4. IntStream.range()
IntStream.range(1, 3).forEach(System.out::println);
// 5. IntStream.rangeClosed() 
IntStream.rangeClosed(1, 3).forEach(System.out::println);
 
System.out.println("1>> " + stream1.equals(stream2));
System.out.println("2>> " + stream3.equals(stream2));
System.out.println("3>> " + stream3.equals(stream1));
System.out.println("4>> " + (stream3 == stream1));
System.out.println("5>> " + (stream3 == stream2));
System.out.println("6>> " + (stream2 == stream1));
```
（ range和rangeClosed就是[1,3)和[1,3]的区别。）
结果输出：
```Java
1
2
1
2
3
1>> false
2>> false
3>> false
4>> false
5>> false
6>> false
```

Stream流的转换如下：
```Java
public class Demo4 {
    public static void main(String[] args) {
 
        String [] stringArray = new String[]{"a", "b", "c"};
        // 1. stream.toArray
        Stream stream1 = Stream.of(stringArray);
        String [] strArray = (String[]) stream1.toArray(String []::new);
        // 2. stream.collect()
        List<String> list = Arrays.asList(stringArray);
        Stream stream3 = list.stream();
        List<String> list1 = (List<String>) stream3.collect(Collectors.toList());
 
        System.out.println("1>> " + stringArray.equals(strArray));
        System.out.println("2>> " + list.equals(list1));
        System.out.println("4>> " + (stringArray == strArray));
        System.out.println("5>> " + (list == list1));
    }
}
```

## 3.Stream的操作类型
>Intermediate：一个流可以后面跟随零个或多个 intermediate 操作。其目的主要是打开流，做出某种程度的数据映射/过滤，然后返回一个新的流，交给下一个操作使用。这类操作都是惰性化的（lazy），就是说，仅仅调用到这类方法，并没有真正开始流的遍历。

 包含的操作有：map (mapToInt, flatMap 等)、 filter、 distinct、 sorted、 peek、 limit、 skip、 parallel、 sequential、 unordered 

 >Terminal：一个流只能有一个 terminal 操作，当这个操作执行后，流就被使用“光”了，无法再被操作。所以这必定是流的最后一个操作。Terminal 操作的执行，才会真正开始流的遍历，并且会生成一个结果，或者一个 side effect。

 包含的操作有：forEach、 forEachOrdered、 toArray、 reduce、 collect、 min、 max、 count、 anyMatch、 allMatch、 noneMatch、 findFirst、 findAny、 iterator

 ## 4.map/flatMap 操作
 它们的作用就是把 input Stream 的每一个元素，映射成 output Stream 的另外一个元素。
 ```Java    
public class Demo5 {
    public static void main(String[] args) {
        
        String [] stringArray = new String[]{"a", "b", "c"};
        String [] newArray = (String[]) Arrays.stream(stringArray).map(String::toUpperCase).toArray(String[]::new);
        Arrays.stream(newArray).forEach(System.out::println);
    }
}
 ```
 ```Java
A
B
C 
```
再比如：
```Java
public class Demo6 {
    public static void main(String[] args) {
 
        List<Integer> nums = Arrays.asList(1, 2, 3, 4);
        List<Integer> squareNums = nums.stream().
                map(n -> n * n).
                collect(Collectors.toList());
        squareNums.stream().forEach(System.out::println);
    }
}
```
```Java
1
4
9
16
```
从上面例子可以看出，map 生成的是个 1:1 映射，每个输入元素，都按照规则转换成为另外一个元素。还有一些场景，是一对多映射关系的，这时需要 flatMap：
```Java
public class Demo7 {
    public static void main(String[] args) {
 
        Stream<List<Integer>> inputStream = Stream.of(
                Arrays.asList(1),
                Arrays.asList(1, 3),
                Arrays.asList(4, 5, 6)
        );
        Stream<Integer> outputStream = inputStream.
                 flatMap((childList) -> childList.stream());
        outputStream.forEach(System.out::println);
    }
}
```
```Java
1
1
3
4
5
6
```
可以看出flatMap是把多个流集合在一起。


## 5.filter 操作
filter 对原始 Stream 进行某项测试，通过测试的元素被留下来生成一个新 Stream，就像我们的第一个例子。下面是提取出偶数：
```Java
public class Demo8 {
    public static void main(String[] args) {
 
        Integer[] sixNums = {1, 2, 3, 4, 5, 6};
        Integer[] evens = Stream.of(sixNums).filter(n -> n%2 == 0).toArray(Integer[]::new);
    }
}
```

## 6.forEach 操作
forEach 往往接收一个 Lambda 表达式，然后在 Stream 的每一个元素上执行该表达式，可以替代for循环。例子参考前面的代码。

## 7.peek 操作
peek 对每个元素执行操作并返回一个新的 Stream，可以在执行Terminal类型操作之前使用。比如上面的获取偶数的例子，可以在toArray（）前进行打印操作：
```Java
public class Demo8 {
    public static void main(String[] args) {
 
        Integer[] sixNums = {1, 2, 3, 4, 5, 6};
        Integer[] evens = Stream.of(sixNums).filter(n -> n%2 == 0).peek(System.out::println).toArray(Integer[]::new);
    }
}   
```
```Java
2
4
6
```

## 8.findFirst 操作
返回 Stream 的第一个元素，或者空。
```Java
public class Demo8 {
    public static void main(String[] args) {
 
        Integer[] sixNums = {1, 2, 3, 4, 5, 6};
        Optional<Integer> first = Stream.of(sixNums).findFirst();
        System.out.println(first);
        System.out.println(first.get());
    }
}
```
```Java
Optional[1]
1
```
findFirst 操作的返回值类型：Optional。这也是一个模仿 Scala 语言中的概念，作为一个容器，它可能含有某值，或者不包含。使用它的目的是尽可能避免 NullPointerException。


## 9.reduce 操作
主要作用是把 Stream 元素组合起来。它提供一个起始值（种子），然后依照运算规则（BinaryOperator），和前面 Stream 的第一个、第二个、第 n 个元素组合。从这个意义上说，字符串拼接、数值的 sum、min、max、average 都是特殊的 reduce。例如 Stream 的 sum 就相当于

Integer sum = integers.reduce(0, (a, b) -> a+b); 或

Integer sum = integers.reduce(0, Integer::sum);

也有没有起始值的情况，这时会把 Stream 的前面两个元素组合起来，返回的是 Optional。
```Java
public class Demo9 {
    public static void main(String[] args) {
 
        // 字符串连接，concat = "ABCD"
        String concat = Stream.of("A", "B", "C", "D").reduce("", String::concat);
        System.out.println("concat :" + concat);
        // 求最小值，minValue = -3.0
        double minValue = Stream.of(-1.5, 1.0, -3.0, -2.0).reduce(Double.MAX_VALUE, Double::min);
        System.out.println("minValue :" + minValue);
        // 求和，sumValue = 10, 有起始值
        int sumValue = Stream.of(1, 2, 3, 4).reduce(0, Integer::sum);
        System.out.println("sumValue :" + sumValue);
        // 求和，sumValue = 10, 无起始值
        sumValue = Stream.of(1, 2, 3, 4).reduce(Integer::sum).get();
        System.out.println("sumValue :" + sumValue);
        // 过滤，字符串连接，concat = "ace"
        concat = Stream.of("a", "B", "c", "D", "e", "F")
                .filter(x -> x.compareTo("Z") > 0)
                .peek(System.out::println)
                .reduce("", String::concat);
        System.out.println("concat :" + concat);
    }
}
```
```Java
concat :ABCD
minValue :-3.0
sumValue :10
sumValue :10
a
c
e
concat :ace
```
上面代码例如第一个示例的 reduce()，第一个参数（空白字符）即为起始值，第二个参数（String::concat）为 BinaryOperator。

## 10.limit/skip 操作
limit 返回 Stream 的前面 n 个元素；skip 则是扔掉前 n 个元素。
```Java
public class Person {
    public int no;
    private String name;
    public Person (int no, String name) {
        this.no = no;
        this.name = name;
    }
    public String getName() {
        System.out.println(name);
        return name;
    }
}
```
```Java
public class Demo10 {
    public static void main(String[] args) {
        testLimitAndSkip();
    }
 
    public static void testLimitAndSkip() {
        List<Person> persons = new ArrayList();
        for (int i = 1; i <= 10000; i++) {
            Person person = new Person(i, "name" + i);
            persons.add(person);
        }
        List<String> personList2 = persons.stream().
                map(Person::getName).limit(10).skip(3).collect(Collectors.toList());
        System.out.println(personList2);
    }
}
```
```Java
name1
name2
name3
name4
name5
name6
name7
name8
name9
name10
[name4, name5, name6, name7, name8, name9, name10]
```

## 11.sorted 操作
对 Stream 的排序通过 sorted 进行，它比数组的排序更强之处在于你可以首先对 Stream 进行各类 map、filter、limit、skip 甚至 distinct 来减少元素数量后，再排序，这能帮助程序明显缩短执行时间。
```Java
public class Demo11 {
    public static void main(String[] args) {
        testLimitAndSkip();
    }
 
    public static void testLimitAndSkip() {
        List<Person> persons = new ArrayList();
        for (int i = 1; i <= 5; i++) {
            Person person = new Person(i, "name" + i);
            persons.add(person);
        }
        List<Person> personList2 = persons.stream()
                .limit(2)
                .sorted((p1, p2) -> p1.getName().compareTo(p2.getName()))
                .peek(p -> System.out.println("====>" + p.getName()))
                .collect(Collectors.toList());
    }
}
```
```Java
name2
name1
name1
====>name1
name2
====>name2
```
或者
```Java
public class Demo12 {
    public static void main(String[] args) {
        testLimitAndSkip();
    }
 
    public static void testLimitAndSkip() {
        List<Integer> nums = new ArrayList();
        nums.add(10);
        nums.add(1);
        nums.add(6);
        nums.add(9);
        nums.add(3);
        List<Integer> personList2 = nums.stream()
                .sorted(Comparator.reverseOrder())
                .peek(p -> System.out.println("====>" + p))
                .collect(Collectors.toList());
    }
}
```
```Java
====>10
====>9
====>6
====>3
====>1
```
这里需要注意排序依据：
>下面代码以自然序排序一个list
>list.stream().sorted()
>
>自然序逆序元素，使用Comparator 提供的reverseOrder() 方法
>list.stream().sorted(Comparator.reverseOrder())
>
>使用Comparator 来排序一个list
>list.stream().sorted(Comparator.comparing(Student::getAge))
>
>把上面的元素逆序
>list.stream().sorted(Comparator.comparing(Student::getAge).reversed())


## 12.parallel操作
并行和并发有什么区别？

并发当有多个线程在操作时,如果系统只有一个CPU,则它根本不可能真正同时进行一个以上的线程，它只能把CPU运行时间划分成若干个时间段,再将时间 段分配给各个线程执行，在一个时间段的线程代码运行时，其它线程处于挂起状。这种方式我们称之为并发(Concurrent)。

并行：当系统有一个以上CPU时,则线程的操作有可能非并发。当一个CPU执行一个线程时，另一个CPU可以执行另一个线程，两个线程互不抢占CPU资源，可以同时进行，这种方式我们称之为并行(Parallel)。

 使用stream的parallel操作可以使得循环是并行的，而不是一个循环结束下一个才能开始，最终的总处理时间取决于时间最长的那次循环，可以大大提高效率。

## 13.补充

### 13.1 详细场景代码示例
```Java
/**
 * Lambda 示例代码
 * Feng, Ge 2020年01月16日14:42:43
 */
 
/**
 * （1）Intermediate（中间操作）：
 * 一个流可以后面跟随零个或多个 intermediate 操作。
 * 其目的主要是打开流，做出某种程度的数据映射/过滤，然后返回一个新的流，交给下一个操作使用。
 * 这类操作都是惰性化的（lazy），就是说，仅仅调用到这类方法，并没有真正开始流的遍历。
 * map (mapToInt, flatMap 等)、 filter、 distinct、 sorted、 peek、
 * limit、 skip、 parallel、 sequential、 unordered
 * <p>
 * （2）Terminal（终止操作）：
 * 一个流只能有一个 terminal 操作，当这个操作执行后，流就被使用“光”了，无法再被操作。
 * 所以这必定是流的最后一个操作。Terminal 操作的执行，才会真正开始流的遍历，并且会生成一个结果，或者一个 side effect。
 * forEach、 forEachOrdered、 toArray、 reduce、 collect、 min、 max、 count、 anyMatch、 allMatch、 noneMatch、 findFirst、 findAny、 iterator
 * <p>
 * （3）short-circuiting（短路操作）。
 * 用以指：
 * • 对于一个 intermediate 操作，如果它接受的是一个无限流，它可以返回一个有限的新 Stream。
 * • 对于一个 terminal 操作，如果它接受的是一个无限流，但能在有限的时间计算出结果。
 * anyMatch、 allMatch、 noneMatch、 findFirst、 findAny、 limit
 * <p>
 * Feng, Ge 2020-10-20 17:28
 */
public class MyLambdaTest {
 
    @Test
    public void test1() {
        List list = StreamAPI.getStringList();
        Stream distinct = list.stream().distinct();
        System.out.println("【1】:" + distinct);
        Object collect = distinct.collect(Collectors.toList());
        System.out.println("【2】:" + collect);
        long count = list.stream().distinct().count();
        System.out.println("【3】:" + count);
    }
 
    @Test
    public void test2() {
        List<Integer> intList = StreamAPI.getIntegerList();
        Stream<Integer> stream1 = intList.stream().filter(element -> element > 5);
        stream1.forEach(System.out::println);
        System.out.println("==============================================================");
        List<String> strList = StreamAPI.getStringList();
        Stream<String> stream2 = strList.stream().filter(element -> Integer.valueOf(element).intValue() > 5);
        stream2.forEach(System.out::println);
        System.out.println("==============================================================");
        List<String> list = StreamAPI.getStringList();
        Stream<String> stream = list.stream().filter(element -> !element.contains("8"));
        stream.forEach(System.out::println);
 
        boolean hasDiagInfo = false;
        if (!org.springframework.util.CollectionUtils.isEmpty(StreamAPI.getBNullList())) {
            List<B> diagnoseInfos = StreamAPI.getBNullList().stream().filter(dia -> StringUtils.isNotEmpty(dia.getName())).collect(Collectors.toList());
            if (diagnoseInfos != null && diagnoseInfos.size() > 0){
                hasDiagInfo = true;
            }
            System.out.println(diagnoseInfos);
            System.out.println(hasDiagInfo);
        }
    }
 
    @Test
    public void test3() {
        List<String> strList = StreamAPI.getStringList();
        Stream<Integer> stream = strList.stream().map(String::length);
        stream.forEach(System.out::println);
        System.out.println("==============================================================");
        List<Integer> numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5);
        List<Integer> collect = numbers.stream().map(num -> num * 2).collect(Collectors.toList());
        collect.stream().forEach(System.out::println);
        System.out.println("==============================================================");
        List<B> bList = StreamAPI.getBList();
        List<String> nameList = bList.stream().map(b -> b.getName()).collect(Collectors.toList());
        nameList.stream().forEach(System.out::println);
    }
 
    /**
     * anyMatch()，只要有一个元素匹配传入的条件，就返回 true。
     * allMatch()，只有有一个元素不匹配传入的条件，就返回 false；如果全部匹配，则返回 true。
     * noneMatch()，只要有一个元素匹配传入的条件，就返回 false；如果全部匹配，则返回 true。
     */
    @Test
    public void test4() {
        List<B> bList = StreamAPI.getBList();
        boolean anyMatchFlag = bList.stream().anyMatch(element -> element.getName().contains("小"));
        boolean allMatchFlag = bList.stream().allMatch(element -> element.getName().length() > 1);
        boolean noneMatchFlag = bList.stream().noneMatch(element -> element.getName().startsWith("小"));
        System.out.println(anyMatchFlag);
        System.out.println(allMatchFlag);
        System.out.println(noneMatchFlag);
    }
 
    /**
     * Stream.reduce()合并流的元素并产生单个值。
     */
    @Test
    public void test5() {
        int[] numbers = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
        int sum = 0;
        for (int i : numbers) {
            sum += i;
        }
        System.out.println("sum : " + sum); // 55
        System.out.println("==============================================================");
        int sum1 = Arrays.stream(numbers).reduce(0, (a, b) -> a + b);
        System.out.println("sum : " + sum1); // 55
        System.out.println("==============================================================");
        int sum2 = Arrays.stream(numbers).reduce(0, Integer::sum);
        System.out.println("sum : " + sum2); // 55
        System.out.println("==============================================================");
        int max = Arrays.stream(numbers).reduce(0, (a, b) -> a > b ? a : b);  // 10
        int max1 = Arrays.stream(numbers).reduce(0, Integer::max);            // 10
        System.out.println("max : " + max);
        System.out.println("max1 : " + max1);
        System.out.println("==============================================================");
        int min = Arrays.stream(numbers).reduce(0, (a, b) -> a < b ? a : b);  // 0
        int min1 = Arrays.stream(numbers).reduce(0, Integer::min);            // 0
        System.out.println("min : " + min);
        System.out.println("min1 : " + min1);
        System.out.println("==============================================================");
        String[] strings = {"a", "b", "c", "d", "e"};
        // |a|b|c|d|e , the initial | join is not what we want
        String reduce = Arrays.stream(strings).reduce("", (a, b) -> a + "|" + b);
        System.out.println("reduce : " + reduce);
        // a|b|c|d|e, filter the initial "" empty string
        String reduce2 = Arrays.stream(strings).reduce("", (a, b) -> {
            if (!"".equals(a)) {
                return a + "|" + b;
            } else {
                return b;
            }
        });
        System.out.println("reduce2 : " + reduce2);
        // a|b|c|d|e , better uses the Java 8 String.join :)  （最好使用 Java 8 的 String.join）
        String join = String.join("|", strings);
        System.out.println("join : " + join);
    }
 
    /**
     * 根据limit(n)，直接截断流，后续操作不继续，限制其流中元素个数为n，此操作称为短路操作，提高效率时可以考虑短路操作
     */
    @Test
    public void test6() {
        List<B> bList = StreamAPI.getBList();
        bList.stream().filter(b -> b.getAge() > 3).limit(2).forEach(System.out::println);
    }
 
    /**
     * averagingDouble-求平均值并转换成Double类型，同理还有averagingInt、averagingLong
     */
    @Test
    public void test7() {
        List<B> bList = StreamAPI.getBList();
        Double ageAve = bList.stream().collect(Collectors.averagingDouble(B::getAge));
        System.out.println(ageAve);
    }
 
    /**
     * summingDouble-求和转换成Double类型，同理还有summingInt、summingLong
     */
    @Test
    public void test8() {
        List<B> bList = StreamAPI.getBList();
        Double ageAve = bList.stream().collect(Collectors.summingDouble(B::getAge));
        System.out.println(ageAve);
    }
 
    /**
     * maxBy-根据函数条件求最大值 ，minBy-根据函数条件求最小值
     */
    @Test
    public void test9() {
        List<B> bList = StreamAPI.getBList();
        Optional<B> stuOptional = bList.stream().collect(Collectors.maxBy((a, b) -> Double.compare(a.getAge(), b.getAge())));
        System.out.println(stuOptional.get());
    }
 
    /**
     * groupingBy-分组，支持多级分组
     */
    @Test
    public void test10() {
        List<B> bList = StreamAPI.getBList();
        Map<Integer, List<B>> ageGroup = bList.stream().collect(Collectors.groupingBy(B::getAge));
        Object single = JSONObject.toJSON(ageGroup);
        System.out.println(ageGroup);
        System.out.println(single);
        System.out.println("===================多级分组（先姓名后年龄）====================");
        Map<String, Map<String, List<B>>> groupmap = bList.stream()
                .collect(Collectors.groupingBy(B::getName, Collectors.groupingBy((e) -> {
                    if (e.getAge() <= 20) {
                        return "年轻人";
                    } else if (e.getAge() <= 50) {
                        return "中年人";
                    } else {
                        return "老年人";
                    }
                })));
        System.out.println(groupmap);
        Object mul = JSONObject.toJSON(groupmap);
        System.out.println(mul);
        System.out.println("===================多级分组（先姓名后年龄）====================");
        Map<Integer, Map<String, List<B>>> groupmap1 = bList.stream()
                .collect(Collectors.groupingBy(B::getAge, Collectors.groupingBy(B::getName)));
        System.out.println(groupmap1);
        Object mul1 = JSONObject.toJSON(groupmap1);
        System.out.println(mul1);
 
        System.out.println("===================Map分组====================");
        List<Map<String, Object>> resultList = new ArrayList<>();
        Map<String, Object> map1 = new HashMap<>();
        map1.put("name", "ok");
        map1.put("age", 6);
        Map<String, Object> map2 = new HashMap<>();
        map2.put("name", "okok");
        map2.put("age", 66);
        Map<String, Object> map3 = new HashMap<>();
        map3.put("name", "okokok");
        map3.put("age", 666);
        Map<String, Object> map4 = new HashMap<>();
        map4.put("name", "ok");
        map4.put("age", 16);
        resultList.add(map1);
        resultList.add(map2);
        resultList.add(map3);
        resultList.add(map4);
        Map<String, List<Map<String, Object>>> result = resultList.stream().collect(Collectors.groupingBy(MyLambdaTest::getGroupKey));
        System.out.println(JSONUtils.toJSONString(result));
    }
 
    private static String getGroupKey (Map<String, Object> map) {
        return map.get("name").toString();
    }
 
    /**
     * partitioningBy-分区
     */
    @Test
    public void test11() {
        List<B> bList = StreamAPI.getBList();
        Map<Boolean, List<B>> booleanGroup = bList.stream()
                .collect(Collectors.partitioningBy((e) -> e.getAge() > 9));
        System.out.println(booleanGroup);
    }
 
    /**
     * joining-连接字符串
     */
    @Test
    public void test12() {
        List<B> bList = StreamAPI.getBList();
        String nameStr = bList.stream().map(B::getName)
                .collect(Collectors.joining());
        System.out.println(nameStr);
        System.out.println("=======================================");
        String nameSplitStr = bList.stream().map(B::getName)
                .collect(Collectors.joining(","));
        System.out.println(nameSplitStr);
    }
 
    /**
     * sorted-排序，和reversed结合使用实现正序、倒序
     */
    @Test
    public void test13() {
        List<B> bList = StreamAPI.getBList();
        bList.stream()
                .sorted(Comparator.comparing(B::getAge).reversed()).forEach(e -> {
            System.out.println(e);
        });
        System.out.println("=================Map比较排序======================");
        List<Map<String, Object>> resultList = new ArrayList<>();
        Map<String, Object> map1 = new HashMap<>();
        map1.put("name", "ok");
        map1.put("age", 6);
        Map<String, Object> map2 = new HashMap<>();
        map2.put("name", "okok");
        map2.put("age", 66);
        Map<String, Object> map3 = new HashMap<>();
        map3.put("name", "okokok");
        map3.put("age", 666);
        resultList.add(map1);
        resultList.add(map2);
        resultList.add(map3);
        Stream<Map<String, Object>> sorted = resultList.stream().sorted(Comparator.comparing(MyLambdaTest::comparedByAge).reversed());
        List<Map<String, Object>> collect = sorted.collect(Collectors.toList());
        System.out.println(JSONUtils.toJSONString(collect));
    }
 
    private static long comparedByAge(Map<String, Object> map) {
        return Long.parseLong(map.get("age").toString());
    }
 
    /**
     * Stream流中的常用方法skip：用于跳过元素
     * 如果希望跳过前几个元素，可以使用skip方法获取一个截取之后的新流
     * 如果流的当前长度大于n，则跳过前n个，否则会得到一个长度为0的空流
     */
    @Test
    public void test14() {
        List<String> strings = StreamAPI.getStringList();
        Stream stream = strings.stream();
        Stream streamLimit = stream.skip(2);
        streamLimit.forEach(s -> System.out.println(s));
    }
 
    /**
     * parallelStream() <===>  stream().parallel()
     * parallelStream提供了流的并行处理，它是Stream的另一重要特性，其底层使用Fork/Join框架实现。
     * 简单理解就是多线程异步任务的一种实现。
     * 我们可以通过虚拟机启动参数 -Djava.util.concurrent.ForkJoinPool.common.parallelism=N 来设置worker的数量。
     * <p>
     * 并行可能带来的问题：
     * 由于并行流使用多线程，则一切线程安全问题都应该是需要考虑的问题，如：资源竞争、死锁、事务、可见性等等。
     * 如果某个生产者生产了许多重量级的任务（耗时很长），那么其他任务毫无疑问将会没有工作线程可用；更可怕的事情是这些工作线程正在进行IO阻塞。
     * 串行流：适合存在线程安全问题、阻塞任务、重量级任务，以及需要使用同一事务的逻辑。
     * 并行流：适合没有线程安全问题、较单纯的数据处理任务。
     */
    @Test
    public void test15() {
        List<B> bList = StreamAPI.getBList();
        bList.parallelStream().forEach(num ->
                System.out.println(Thread.currentThread().getName() + ">>" + num.getName()));
        System.out.println("=======================================");
        bList.stream().parallel().forEach(num ->
                System.out.println(Thread.currentThread().getName() + ">>" + num.getName()));
    }
 
    /**
     * 加上.sequential(), 将并行流变成顺序流(串行流)
     * 串行流：适合存在线程安全问题、阻塞任务、重量级任务，以及需要使用同一事务的逻辑。
     */
    @Test
    public void test16() {
        List<B> bList = StreamAPI.getBList();
        bList.parallelStream().sequential().forEach(num ->
                System.out.println(Thread.currentThread().getName() + ">>" + num.getName()));
    }
 
    /**
     * forEach 和  forEachOrdered
     * 因为是并行处理
     * 第一种输出的情况不确定
     * 第二行输出的一直是确定的
     */
    @Test
    public void test17() {
        Stream.of("AAA", "BBB", "CCC").parallel().forEach(s -> System.out.println("Output:" + s));
        System.out.println("=======================================");
        Stream.of("AAA", "BBB", "CCC").parallel().forEachOrdered(s -> System.out.println("Output:" + s));
    }
 
    @Test
    public void test18() {
        int[] array = {1, 2, 5, 5, 5, 5, 6, 6, 7, 2, 9, 2};
        List<Integer> numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5);
        /*int[]转list*///java8及以上版本
        List<Integer> list1 = Arrays.stream(array).boxed().collect(Collectors.toList());
 
        /*list转int[]*/
        //方法一：
        Integer[] intArr = numbers.toArray(new Integer[numbers.size()]);
        //方法二：java8及以上版本
        int[] intArr1 = numbers.stream().mapToInt(Integer::valueOf).toArray();
    }
}
```

### 13.2 for、foreach、stream 效率比较
- 1万以内的数据，for循环的性能要高于foreach和stream；
- 数据量上去之后（1000万），三种遍历方式基本已经没有什么差距了，但是Stream提供并行处理，在数据量大了之后，效率会明显增强。（但是单核CPU，Stream并行处理可能会效率更慢）