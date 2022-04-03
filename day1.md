# 1.分布式锁有哪些解决方法?

1. Redis的分布式锁:  
setnx，如果原来已经有了这个key那么设置不成功，保证了锁的互斥性  
锁失效的问题：  
1） 有一个锁已经存在，在执行业务的过程中还没有执行到要删除key的时候程序就挂了，这样的话这个key就会一直存在在redis中，别的程序想再来加锁就永远设置不成功了  
解决办法:给锁设置过期时间，当然了，setnx与expire之间程序挂了那也不行，所以得将这两个操作设置成原子操作,setnx key value ex 10s
2)程序执行时间大于锁过期时间，然后这个锁被其他程序得到了，那么我deletekey删除的就是别的锁了  
解决办法:在删除的时候做判断，从key里getvalue，和设置的value作对比，如果相等才删除
3）程序执行时间大于key过期时间，别的程序来加锁是可以成功的，两个程序都进入了同样的方法中，造成数据不一致问题  
解决办法：watchdog，比如说过期时间为30s，每次过了10s后watchdog就恢复过期时间为30s； 
上述问题较为繁琐，我可以用Redisson框架的lock和unlock方法来解决


![image](https://user-images.githubusercontent.com/95333628/161439350-ef2f242e-df21-4bba-83bf-51b34c01479d.png)
![image](https://user-images.githubusercontent.com/95333628/161439387-70a33c5d-02ce-4783-9059-a5508cf4ee50.png)


2. zookeeper  
临时节点，顺序节点

3.Mysql主键或唯一索引  
秒杀时对同一个商品购买，向数据库添加购买记录，利用主键冲突防止插入相同id的数据
