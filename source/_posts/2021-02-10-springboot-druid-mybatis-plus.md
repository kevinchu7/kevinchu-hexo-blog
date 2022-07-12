---
title: SpringBoot整合druid和mybatis-plus
index_img: https://static.kevinchu.top/blog/assets/img/cover_017.jpeg
date: 2021-11-10 14:38:47
tags:
    - SpringBoot
    - mybatis-plus
    - Druid
categories:
    - 雕虫小技
---
## 概念
### 1.1 Druid

- 是什么

Druid是阿里巴巴开发的一个连接池，他提供了一个高效、功能强大、可扩展性好的数据库连接池。Druid是一个高效的数据查询系统，主要解决的是对于大量的基于时序的数据进行聚合查询。数据可以实时摄入，进入到Druid后立即可查，同时数据是几乎是不可变。通常是基于时序的事实事件，事实发生后进入Druid，外部系统就可以对该事实进行查询。

- Druid采用的架构

shared-nothing架构与lambda架构

- Druid设计三个原则

1.快速查询（Fast Query） : 部分数据聚合（Partial Aggregate） + 内存化（In-Memory） + 索引（Index）
2.水平拓展能力（Horizontal Scalability）:分布式数据（Distributed data）+并行化查询（Parallelizable Query）
3.实时分析（Realtime Analytics）：Immutable Past , Append-Only Future

### 1.2 mybatis-plus
MyBatis-Plus (opens new window)（简称 MP）是一个 MyBatis (opens new window)的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生。
>参考：mybatis-plus的[官方文档](https://baomidou.com/)

## 操作步骤
### 2.1 导入依赖
```XML
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.5.1</version>
</dependency>
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
    <version>1.2.4</version>
</dependency>
```

### 2.2 添加配置参数
```YML
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/kevin_db_dev?useUnicode=true&&characterEncoding=utf-8&&useSSL=false
    username: root
    password: 
    type: com.alibaba.druid.pool.DruidDataSource
    driver-class-name: com.mysql.cj.jdbc.Driver
    druid:
      stat-view-servlet:
        # 默认true 内置监控页面首页/druid/index.html
        enabled: true
        url-pattern: /druid/*
        # 允许清空统计数据
        reset-enable: true
        login-username: root
        login-password: 123456
        # IP白名单 多个逗号分隔
        allow:
        # IP黑名单
        deny:
      filter:
        stat:
          # 开启监控sql
          enabled: true
          # 显示并标注慢sql 默认当超过3秒显示
          log-slow-sql: true
          slow-sql-millis: 3000
          merge-sql: true
        # 防SQL注入过滤
        wall:
          config:
            # 允许多条sql同时执行
            multi-statement-allow: true
```
（更多配置）
```YML
spring:
  datasource:
    druid:   #注意配置url、username、password、driver以外的其他配置必须在druid下写，这样会自动包装到DruidDataSourceWrapper里面
      #   数据源其他配置
      initialSize: 5
      minIdle: 5
      maxActive: 20
      maxWait: 60000
      timeBetweenEvictionRunsMillis: 60000
      minEvictableIdleTimeMillis: 300000
      validationQuery: SELECT 1 FROM DUAL
      testWhileIdle: true
      testOnBorrow: false
      testOnReturn: false
      poolPreparedStatements: true
      maxPoolPreparedStatementPerConnectionSize: 20
      useGlobalDataSourceStat: true
      connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500
      # 配置监控统计拦截的filters，去掉后监控界面sql无法统计，'wall'用于防火墙
      filters: stat,wall

    #自定义sql文件名，点进去后这个是一个list
    schema:
      - classpath:department.sql #可以指定多个文件
```

### 2.3 搭建测试环境

- entity

```JAVA
package com.zhu.common.entity.dos;

import com.baomidou.mybatisplus.annotation.TableName;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor
@TableName("tb_student")
public class Student {
    private Integer id;
    private String name;
    private Integer age;
}

```

- mapper

```JAVA
package com.zhu.common.mapper;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.zhu.common.entity.dos.Student;
import org.apache.ibatis.annotations.Mapper;

@Mapper
public interface StudentMapper extends BaseMapper<Student> {
}

```

- service

```JAVA
package com.zhu.common.service;

import com.baomidou.mybatisplus.extension.service.IService;
import com.zhu.common.entity.dos.Student;

public interface StudentService extends IService<Student> {
}
```

```JAVA
package com.zhu.common.service.impl;

import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
import com.zhu.common.entity.dos.Student;
import com.zhu.common.mapper.StudentMapper;
import com.zhu.common.service.StudentService;
import org.springframework.stereotype.Service;

@Service
public class StudentServiceImpl extends ServiceImpl<StudentMapper, Student> implements StudentService {
}

```

- 测试类

```JAVA
package com.zhu.common;


import com.baomidou.mybatisplus.core.conditions.query.LambdaQueryWrapper;
import com.baomidou.mybatisplus.core.toolkit.Wrappers;
import com.baomidou.mybatisplus.extension.conditions.query.LambdaQueryChainWrapper;
import com.zhu.common.entity.dos.Student;
import com.zhu.common.service.StudentService;
import lombok.extern.slf4j.Slf4j;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

@Slf4j
@RunWith(SpringRunner.class)
@SpringBootTest(classes = CommonApplication.class)
public class StudentTest {

    @Autowired
    private StudentService studentService;


    @Test
    public void testInsert(){
        Student stu=new Student();
        stu.setName("张三");
        stu.setAge(18);
        log.info(String.valueOf(studentService.save(stu)));
    }

    @Test
    public void testQuery(){
        log.info(studentService.getOne(
                Wrappers.<Student>lambdaQuery()
                        .eq(Student::getName,"张三"))
                .toString());
    }
}

```

- 测试结果

插入成功：
![](https://static.kevinchu.top/blog/public/20220526153659.png)

![](https://static.kevinchu.top/blog/public/20220526153756.png)


查询成功：
![](https://static.kevinchu.top/blog/public/20220526153858.png)