---
title: 使用shell脚本定时清理运行超时的进程
index_img: https://static.kevinchu.top/blog/assets/img/cover_043.jpeg
date: 2023-02-23 11:37:07
tags:
    - Linux
    - 服务器
    - shell
categories:
    - 雕虫小技
---

### 1.场景

工作中遇到这样场景：一些异步执行的任务可能因为某些原因长时间不结束，一直占用内存影响其他服务的正常运行。临时应对这一问题可以使用脚本定时清理服务器上运行超时的进程。


### 2.代码

kill_process.sh:

```shell
#!/bin/bash

# 定义超时时间（单位为秒）
TIMEOUT=3600

# 定义要杀死的进程名称的关键字
PROCESS_KEYWORD="my_process"

# 定义自定义函数，用于杀死进程并打印进程信息
kill_and_print_info() {
  # 获取进程的PID、运行时间和占用内存
  PID=$1
  ETIMES=$(ps -p $PID -o etimes=)
  RSS=$(ps -p $PID -o rss=)

  # 打印进程信息
  echo "Killing process $PID (running for $ETIMES seconds, using $RSS KB of memory) at $(date)"

  # 杀死进程
  kill $PID
}

# 循环查询运行超过$TIMEOUT秒的进程并杀死
while true
do
  # 查找所有进程的信息
  for pid in $(ps -eo pid,etimes,args | grep $PROCESS_KEYWORD | awk '$2 > '$TIMEOUT'{print $1}')
  do
    # 杀死进程并打印进程信息
    kill_and_print_info $pid
  done

  # 等待一段时间再进行下一次查询
  sleep 300
done
```


## 3.使用

```shell
# 授权
chmod 777 kill_process.sh
# 启动
nohup ./kill_process.sh > kill_process.log &
```