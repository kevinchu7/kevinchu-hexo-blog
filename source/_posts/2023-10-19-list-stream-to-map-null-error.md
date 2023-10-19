---
title: list.stream()转map的空指针问题踩坑
index_img: https://static.kevinchu.top/blog/assets/img/cover_016.jpeg
archive: false
date: 2023-10-19 21:14:54
updated: 2023-10-19 21:14:54
tags:
    - Java
categories:
    - 踩坑指南
---

>这是Java中使用stream处理集合时一个很容易被忽视的问题，稍微不注意就很致命，最近正好遇到了，特此记录一下。

## 1.问题

众所周知，Java的HashMap的key和value值都可以为null的，但是，如果使用stream方法将List转成Map，`Collectors.toMap()`转换后的Map的value值是不能取null的，不做处理就会报空指针异常。

例如：

```Java
import lombok.Builder;
import lombok.Data;

import java.util.ArrayList;
import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;

public class StreamTest {

    @Data
    @Builder
    static class Student {
        String id;
        String name;
    }

    public static void main(String[] args) {
        List<Student> studentList = new ArrayList<Student>() {{
            add(Student.builder().id("1001").name("张三").build());
            add(Student.builder().id("1002").name("李四").build());
            add(Student.builder().id("1003").build());
        }};
        Map<String, String> studentMap = studentList.stream()
                .collect(Collectors.toMap(Student::getId, Student::getName));
        System.out.println(studentMap);
    }
}
```
以上代码中，`studentList`的第三个元素的`name`属性为null，导致转成Map时value为空报错。

从`Collectors.toMap()`底层代码也可以找到其使用到了Map的`merge`方法对value做了为空校验，当value为空时会抛空指针异常：

![](https://static.kevinchu.top/blog/public/20231020012401.png)


## 解决方法

```Java
Map<String, String> studentMap = studentList.stream()
        .collect(HashMap::new,
                (map, item) -> map.put(item.getId(), item.getName()),
                HashMap::putAll);
```
