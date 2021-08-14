---
title: "tdengine学习日记-01"
date: 2020-11-05T21:33:51+08:00
tags: ["database", "TDEngine"]
categories: ["database"]
draft: true
---


这里主要看一下TDEngine的数据的磁盘格式以及数据是如何存储到磁盘上的。
当前的代码是v2.0.18.0
关于DB数据的提交的代码主要在/tsdb/下面。 首先我们看tsdbCommitQueue.c::tsdbLoopCommit
这个是一个线程的运行函数， 从这里开始进行数据的commit。 这个数据的来源先不管， 反正我也暂时不知道这个数据是怎么来的
这个tsdbLoopCommit函数一共会被nproc个线程运行， 每一个线程都在wait一个queueNotEmpty的条件变量。 被唤醒的线程会从tsCommitQueue
中pop出一个需要commit的数据进行操作. 被pop出来的数据类型是STsdbRepo. 
下面主要就是围绕这个STsdbRepo的数据进行操作的

```c
struct STsdbRepo {
  uint8_t state;

  STsdbCfg        config;
  STsdbAppH       appH;
  STsdbStat       stat;
  STsdbMeta*      tsdbMeta;
  STsdbBufPool*   pPool;
  SMemTable*      mem;
  SMemTable*      imem;
  STsdbFS*        fs;
  tsem_t          readyToCommit;
  pthread_mutex_t mutex;
  bool            repoLocked;
  int32_t         code;  // Commit code
};
```

然后就来到了tsdbCommitData函数， 这个函数实现结构非常清晰， 分别是
1. tsdbStartCommit
2. tsdbCommitMeta
3. tsdbCommitTSData
4. tsdbEndCommit
