---
title: 解决redis与ehcache缓存冲突的问题
index_img: https://static.kevinchu.top/blog/assets/img/cover_003.jpeg
date: 2021-03-10 19:05:40
update: 2021-03-10 19:05:40
tags:
    - redis
    - springboot
    - ehcache
categories:
    - 踩坑指南
---
## 1 问题
springboot多模块项目，本身架构使用的redis缓存，因功能需要移植进了一个带有ehcache缓存的模块(并非二级缓存)，造成了项目缓存冲突，redis缓存存取异常。


## 2 解决
进行了很多的尝试过后，发现当项目启动扫描到ehcache.xml时，以注解方式使用redis缓存会出现缓存分区判断异常的状况。
可行的解决方案是，不使用ehcache.xml配置文件，改用Java代码对ehcache缓存进行配置。
ehcache配置代码参考：
```Java
Configuration configuration = new Configuration()// 
		.diskStore(new DiskStoreConfiguration().path("java.io.tmpdir"))//临时文件目录 
		//指定除自身之外的网络群体中其他提供同步的主机列表，用“|”分开不同的主机 
		.cacheManagerPeerProviderFactory(new FactoryConfiguration<FactoryConfiguration<?>>()//
				.className(RMICacheManagerPeerProviderFactory.class.getName())// 
				.properties("peerDiscovery=manual,rmiUrls=//localhost:40004/metaCache|//localhost:40005/metaCache")//
				)// 
		//配宿主主机配置监听程序 
		.cacheManagerPeerListenerFactory(new FactoryConfiguration<FactoryConfiguration<?>>()//
				.className(RMICacheManagerPeerListenerFactory.class.getName())// 
				.properties("port=40004,socketTimeoutMillis=2000")// )// 
				.cache(new CacheConfiguration("metaCache", 10000)//缓存名称(必须唯一),maxElements内存最多可以存放的元素的数量 
						.memoryStoreEvictionPolicy(MemoryStoreEvictionPolicy.LFU)//清理机制：LRU最近最少使用 FIFO先进先出 LFU较少使用
						.timeToIdleSeconds(1000)//元素最大闲置时间
						.timeToLiveSeconds(2000)//元素最大生存时间 
						.eternal(false)//元素是否永久缓存
						.diskExpiryThreadIntervalSeconds(120)//缓存清理时间(默认120秒) 
						//LOCALTEMPSWAP当缓存容量达到上限时，将缓存对象（包含堆和非堆中的）交换到磁盘中 
						//NONE当缓存容量达到上限时，将缓存对象（包含堆和非堆中的）交换到磁盘中 
						//DISTRIBUTED按照_terracotta标签配置的持久化方式执行。非分布式部署时，此选项不可用
						.persistence(new PersistenceConfiguration().strategy(PersistenceConfiguration.Strategy.NONE)).maxEntriesLocalDisk(0)//磁盘中最大缓存对象数0表示无穷大) 
						.cacheEventListenerFactory(new CacheConfiguration.CacheEventListenerFactoryConfiguration().className(RMICacheReplicatorFactory.class.getName()))// 
		); 
CacheManager manager = CacheManager.create(configuration); 
Cache cache = manager.getCache("metaCache");//获得缓存
```