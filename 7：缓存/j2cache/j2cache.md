j2Cache是OSChina目前正在使用的两级缓存框架；

L1：进程内缓存：caffeine/echcache；

L2：集中式缓存：Redis/Memcached；

它不是重复造轮子，而是资源整合；

由于大量的缓存读取会导致L2的网络成为整个系统的瓶颈，因此L1的目标是降低对L2的读取次数；



缓存到哪由框架自己实现，使用时只需要操作放入缓存，从缓存中取出；

API：

```java
@Autowired
private CacheChannel cacheChannel;
//从缓存中获取数据,需要指定区域和key
CacheObject cacheObject = cacheChannel.get(region,key);
if(cacheObject.getValue()==null){
    //放入缓存,指定区域，key和数据
    cacheChannel.set(region,key,data);
}
//清理指定缓存
cacheChannel.evict(region,key);
//清理整个区域的缓存
cacheChannel.clear(region);
//检查是否存在
boolean isExist = cacheChannel.exist(region.key);
//检查从L1获取还是L2获取,0没有
int level = cacheChannel.check(region,key);
```

