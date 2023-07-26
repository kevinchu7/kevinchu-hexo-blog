---
title: SpringBoot使用线程池的一种方式
index_img: https://static.kevinchu.top/blog/assets/img/cover_006.jpeg
date: 2021-04-26 22:04:52
updated: 2021-04-26 22:04:52
tags:
    - Java
    - SpringBoot
    - 多线程
categories:
    - 雕虫小技
---
## 1 创建线程池
通过static代码块创建线程池：
```Java
import com.google.common.util.concurrent.ThreadFactoryBuilder;

import java.util.concurrent.ExecutorService;
import java.util.concurrent.ThreadPoolExecutor;
import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.TimeUnit;

/**
 * @author :kzhu
 * @version :1.0
 * @date :Created in 2021/3/18 10:11
 * @description :自定义线程池
 * @modified By:
 */
@SuppressWarnings("all")
public class HttpApiThreadPool {
    /**
     * 获取当前系统的CPU数目
     */
    private static int cpuNums = Runtime.getRuntime().availableProcessors();
    /**
     * 线程池核心池的大小
     */
    private static int corePoolSize = 10;
    /**
     * 线程池的最大线程数
     */
    private static int maximumPoolSize = cpuNums * 5;

    public static ExecutorService httpApiThreadPool = null;

    static {
        //建立10个核心线程，线程请求个数超过最大线程数时，则进入队列等待
        httpApiThreadPool = new ThreadPoolExecutor(corePoolSize, maximumPoolSize, 0L,
                TimeUnit.MILLISECONDS, new ArrayBlockingQueue<Runnable>(100), new ThreadFactoryBuilder().setNameFormat("PROS-%d").build());
    }

    /**
     * 判断线程池内是否有空闲的线程
     * @return
     */
    public static boolean haveIdleThread(){
        if(getActiveCount()<maximumPoolSize){
            return true;
        }
        return false;
    }

    /**
     * 获取当前活跃线程数
     * @return
     */
    public static int getActiveCount() {
        System.out.println(((ThreadPoolExecutor) httpApiThreadPool).getActiveCount());
        return ((ThreadPoolExecutor) httpApiThreadPool).getActiveCount();
    }

}


```
>注意：
1.阿里巴巴Java开发手册中明确指出:"线程池不允许使用Excutors去创建，而是通过ThreadPoolExecutor的方式，这样的处理方式让程序员更加明确线程池的运行规则，避免资源耗尽的风险"。
2.```new ThreadFactoryBuilder().setNameFormat("PROS-%d").build()``` 给每个线程命名，方便调试。


## 2 使用线程池
配合Java 8的lambdas表达式去使用：
```Java
HttpApiThreadPool.httpApiThreadPool.execute(
                        ()->{
                            //do something...
                        }
                );
```
