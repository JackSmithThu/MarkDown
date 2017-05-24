---
title: 利用canal实现MySQL数据同步到redis（二）同步MySQL的数据到redis集群
date: 2017-05-24 15:11:23
tags: [Redis,MySQL,Canal,Windows]
categories: 技术

---

## 1.如何同步

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705240003.png)我们已经解析了能够从 canal 服务器获取 解析过的 MySQL 的 binlog。接下来我们需要根据解析过的 binlog 将相同的数据写入到 redis 中。



## 2.同步程序

我们在 canal.example 的目录下建立两个新的 java 类，分别名为 ClusterSample.java 和 ClusterUtil.java。文件结构如下图所示：

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705240004.png)

在我的目录中，还有一些其他的 java 文件，它们都是不必要的，不必理会就好。只要你的目录中最后有 ClusterSample.java 和 ClusterUtil.java 两个文件就可以。

### ClusterSample.java

```
package com.alibaba.otter.canal.example;

import redis.clients.jedis.Jedis;  
import redis.clients.jedis.JedisPool;  
import redis.clients.jedis.JedisPoolConfig;  
  
public class RedisUtil {  
  
    // Redis服务器IP  
    private static String ADDR = "127.0.0.1";  
  
    // Redis的端口号  
    private static int PORT = 7002;  
  
    // 访问密码  
    //private static String AUTH = "admin";  
  
    // 可用连接实例的最大数目，默认值为8；  
    // 如果赋值为-1，则表示不限制；如果pool已经分配了maxActive个jedis实例，则此时pool的状态为exhausted(耗尽)。  
    private static int MAX_ACTIVE = 1024;  
  
    // 控制一个pool最多有多少个状态为idle(空闲的)的jedis实例，默认值也是8。  
    private static int MAX_IDLE = 200;  
  
    // 等待可用连接的最大时间，单位毫秒，默认值为-1，表示永不超时。如果超过等待时间，则直接抛出JedisConnectionException；  
    private static int MAX_WAIT = 10000;  
  
    // 过期时间  
    protected static int  expireTime = 660 * 660 *24;  
      
    // 连接池  
    protected static JedisPool pool;  
  
    /** 
     * 静态代码，只在初次调用一次 
     */  
    static {  
        JedisPoolConfig config = new JedisPoolConfig();  
        //最大连接数  
        config.setMaxTotal(MAX_ACTIVE);  
        //最多空闲实例  
        config.setMaxIdle(MAX_IDLE);  
        //超时时间  
        config.setMaxWaitMillis(MAX_WAIT);  
        //  
        config.setTestOnBorrow(false);  
        pool = new JedisPool(config, ADDR, PORT, 1000);  
    }  
  
    /** 
     * 获取jedis实例 
     */  
    protected static synchronized Jedis getJedis() {  
        Jedis jedis = null;  
        try {  
            jedis = pool.getResource();  
        } catch (Exception e) {  
            e.printStackTrace();  
            if (jedis != null) {  
                pool.returnBrokenResource(jedis);  
            }  
        }  
        return jedis;  
    }  
  
    /** 
     * 释放jedis资源 
     *  
     * @param jedis 
     * @param isBroken 
     */  
    protected static void closeResource(Jedis jedis, boolean isBroken) {  
        try {  
            if (isBroken) {  
                pool.returnBrokenResource(jedis);  
            } else {  
                pool.returnResource(jedis);  
            }  
        } catch (Exception e) {  
  
        }  
    }  
  
    /** 
     *  是否存在key 
     *  
     * @param key 
     */  
    public static boolean existKey(String key) {  
        Jedis jedis = null;  
        boolean isBroken = false;  
        try {  
            jedis = getJedis();  
            jedis.select(0);  
            return jedis.exists(key);  
        } catch (Exception e) {  
            isBroken = true;  
        } finally {  
            closeResource(jedis, isBroken);  
        }  
        return false;  
    }  
  
    /** 
     *  删除key 
     *  
     * @param key 
     */  
    public static void delKey(String key) {  
        Jedis jedis = null;  
        boolean isBroken = false;  
        try {  
            jedis = getJedis();  
            jedis.select(0);  
            jedis.del(key);  
        } catch (Exception e) {  
            isBroken = true;  
        } finally {  
            closeResource(jedis, isBroken);  
        }  
    }  
  
    /** 
     *  取得key的值 
     *  
     * @param key 
     */  
    public static String stringGet(String key) {  
        Jedis jedis = null;  
        boolean isBroken = false;  
        String lastVal = null;  
        try {  
            jedis = getJedis();  
            jedis.select(0);  
            lastVal = jedis.get(key);  
            jedis.expire(key, expireTime);  
        } catch (Exception e) {  
            isBroken = true;  
        } finally {  
            closeResource(jedis, isBroken);  
        }  
        return lastVal;  
    }  
  
    /** 
     *  添加string数据 
     *  
     * @param key 
     * @param value 
     */  
    public static String stringSet(String key, String value) {  
        Jedis jedis = null;  
        boolean isBroken = false;  
        String lastVal = null;  
        try {  
            jedis = getJedis();  
            jedis.select(0);  
            lastVal = jedis.set(key, value);  
            jedis.expire(key, expireTime);  
        } catch (Exception e) {  
            e.printStackTrace();  
            isBroken = true;  
        } finally {  
            closeResource(jedis, isBroken);  
        }  
        return lastVal;  
    }  
  
    /** 
     *  添加hash数据 
     *  
     * @param key 
     * @param field 
     * @param value 
     */  
    public static void hashSet(String key, String field, String value) {  
        boolean isBroken = false;  
        Jedis jedis = null;  
        try {  
            jedis = getJedis();  
            if (jedis != null) {  
                jedis.select(0);  
                jedis.hset(key, field, value);  
                jedis.expire(key, expireTime);  
            }  
        } catch (Exception e) {  
            isBroken = true;  
        } finally {  
            closeResource(jedis, isBroken);  
        }  
    }  
  }
```

### ClusterUtil.java

```
package com.alibaba.otter.canal.example;

import java.util.HashSet;
//import java.util.LinkedHashSet;
import java.util.Set;

import redis.clients.jedis.HostAndPort;
//import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisCluster;
//import redis.clients.jedis.JedisPool;
//import redis.clients.jedis.JedisPoolConfig;

public class ClusterUtil {

    // 访问密码
//    private static String AUTH = "admin";

    // 过期时间
    protected static int expireTime = 660 * 660 * 24;
    
//    private static String host="10.0.76.192";  
    private static String host="127.0.0.1";  
    private static JedisCluster cluster;    
      
    static {  
        // 只给集群里一个实例就可以  
        Set<HostAndPort> jedisClusterNodes = new HashSet<HostAndPort>();  
        jedisClusterNodes.add(new HostAndPort(host, 7000));  
        jedisClusterNodes.add(new HostAndPort(host, 7001));  
        jedisClusterNodes.add(new HostAndPort(host, 7002));  
        jedisClusterNodes.add(new HostAndPort(host, 7003));  
        jedisClusterNodes.add(new HostAndPort(host, 7004));  
        jedisClusterNodes.add(new HostAndPort(host, 7005));  
          
        cluster = new JedisCluster(jedisClusterNodes);  
    }  

    /**
     * 获取jedis实例
     */
    protected static synchronized JedisCluster getJedis() {

        return cluster;
    }

    /**
     * 释放jedis资源
     * 
     * @param jedis
     * @param isBroken
     */
    protected static void closeResource(JedisCluster cluster, boolean isBroken) {

    }

    /**
     * 是否存在key
     * 
     * @param key
     */
    public static boolean existKey(String key) {
        JedisCluster jedis = null;
        boolean isBroken = false;
        try {
            jedis = getJedis();
            jedis.select(0);
            return jedis.exists(key);
        } catch (Exception e) {
            isBroken = true;
        } finally {
            closeResource(jedis, isBroken);
        }
        return false;
    }

    /**
     * 删除key
     * 
     * @param key
     */
    public static void delKey(String key) {
        JedisCluster jedis = null;
        boolean isBroken = false;
        try {
            jedis = getJedis();
            jedis.select(0);
            jedis.del(key);
        } catch (Exception e) {
            isBroken = true;
        } finally {
            closeResource(jedis, isBroken);
        }
    }

    /**
     * 取得key的值
     * 
     * @param key
     */
    public static String stringGet(String key) {
        JedisCluster jedis = null;
        boolean isBroken = false;
        String lastVal = null;
        try {
            jedis = getJedis();
//            jedis.select(0);
            lastVal = jedis.get(key);
            jedis.expire(key, expireTime);
        } catch (Exception e) {
            isBroken = true;
        } finally {
           // closeResource(jedis, isBroken);
        }
        return lastVal;
    }

    /**
     * 添加string数据
     * 
     * @param key
     * @param value
     */
    public static String stringSet(String key, String value) {
        JedisCluster jedis = null;
        boolean isBroken = false;
        String lastVal = null;
        try {
            jedis = getJedis();
//            jedis.select(0);
            lastVal = jedis.set(key, value);
            jedis.expire(key, expireTime);
        } catch (Exception e) {
            e.printStackTrace();
            isBroken = true;
        } finally {
            closeResource(jedis, isBroken);
        }
        return lastVal;
    }

    /**
     * 添加hash数据
     * 
     * @param key
     * @param field
     * @param value
     */
    public static void hashSet(String key, String field, String value) {
        boolean isBroken = false;
        JedisCluster jedis = null;
        try {
            jedis = getJedis();
            if (jedis != null) {
                //jedis.select(0);
                jedis.hset(key, field, value);
                jedis.expire(key, expireTime);
            }
        } catch (Exception e) {
            isBroken = true;
        } finally {
            //closeResource(jedis, isBroken);
        }
    }
}

```

注意，redis 单点模式和集群模式的连接方法是不同的。详情参考：

[Redis 单点模式和集群模式代码测试及问题记录](http://blog.csdn.net/boonya/article/details/49466003)

由于我们现在要连接的是 redis 集群，所以使用集群模式。



## 3.测试结果

启动 MySQl，Canal 服务器和 redis 集群，运行 ClusterSample.java 类。

### 3.1.在 MySQL 数据库中添加数据：

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705240005.png)

### 3.2.这时我们打开 Eclipse，就可以看到读取 binlog 成功的提示。

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705240006.png)

### 3.3.打开 redis 的客户端，查询响应的数据

![](https://raw.githubusercontent.com/JackSmithThu/MarkdownPhotos/master/201705240007.png)

红色方框内就是最新插入 MySQL 的三条数据。可见，redis 和 MySQL 的数据已经同步成功了。



## 4.参考链接

[Redis 单点模式和集群模式代码测试及问题记录](http://blog.csdn.net/boonya/article/details/49466003)

[jedis针对三种redis工作模式的连接方式](https://my.oschina.net/shyloveliyi/blog/502017)



## 5.操作系统：Windows 10