**redis的删除策略**

- 定时删除

```
在某个key 设置了过期时间时，创建一个定时器，过期时立即执行对其进行删除的操作 
	优点：定时删除对内存是最友好的，能够保证内存的key一旦过期就能立即从内存中删除 
	缺点：对CPU最不友好，在设置过期键比较多的时候，十分消耗CPU资源，影响服务器的响应时间和吞吐量     
```



- 定期删除

   ```
   redis默认每秒检查10次，是否有过期的key需要删除。但redis不是每次将所有的key检查一次，而是随机抽取进行检查 
   	优点：可以根据CPU状况自行调整删除时长和频率，有效释放内存 
   	缺点：如果某个键的过期时间已经到了，但是还没执行定期删除，那么就会获取这个键的值，出现业务错误 可以通过修改配置文件redis.conf 的 hz 选项来调整这个每秒检查次数    
   ```



- 惰性删除

```
在你获取某个key的时候，redis会检查一下这个key是否设置了过期时间，是否已经过期，如果过期了就会删除 
	优点：对 CPU友好，我们只会在使用该键时才会进行过期检查，对于很多用不到的key不用浪费时间进行过期检查 
	缺点：如果数据库中有很多使用不到的过期键，这些键便永远不会被删除，内存永远不会释放，从而造成内存泄漏   
```



**Redis的过期策略**

```
定期删除+惰性删除 
当然，此时仍然无法删除某些永远没有使用的键，于是，我们需要内存淘汰策略      
```



- Redis的内存淘汰策略

```
maxmemory-policy allkeys-lru
```



> 1）noeviction：默认设置，当内存不足以容纳新写入数据时，新写入操作会报错。
>
> 2）allkeys-lru：当内存不足以容纳新写入数据时，在键空间中，移除最近最少使用的key，推荐使用。
>
> 3）allkeys-random：当内存不足以容纳新写入数据时，在键空间中，随机移除某个key。
>
> 4）volatile-lru：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，移除最近最少使用的key。
>
> 5）volatile-random：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，随机移除某个key。
>
> 6）volatile-ttl：当内存不足以容纳新写入数据时，在设置了过期时间的键空间中，有更早过期时间的key优先移除。