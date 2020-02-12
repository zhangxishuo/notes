# Redis

> http://www.redis.cn/

## 工具

客户端工具：`Redis Desktop Manager`

命令行工具：`redis-cli`

## 项目整合

配置`Redis`：

```yml
spring:
  redis:
    host: 127.0.0.1
    port: 6379
    password: imooc
```

添加依赖：

`spring-boot-starter-data-redis`

示例：

```java
@Autowired
private StringRedisTemplate redisTemplate;

redisTemplate.opsForValue().set(key, value, 过期时间, 时间单位);
redisTemplate.opsForValue().getOperations().delete(key);
```

## 命令

### SETNX

```shell
SETNX key value
```

将`key`设置值为`value`，如果`key`不存在，这种情况下等同`SET`命令。 当`key`存在时，什么也不做。`SETNX`是`SET if Not eXists`的简写。

```shell
redis> SETNX mykey "Hello"
(integer) 1
redis> SETNX mykey "World"
(integer) 0
redis> GET mykey
"Hello"
```

### GETSET

```shell
GETSET key value
```

自动将`key`对应到`value`并且返回原来`key`对应的`value`。如果`key`存在但是对应的`value`不是字符串，就返回错误。

```shell
redis> INCR mycounter
(integer) 1
redis> GETSET mycounter "0"
"1"
redis> GET mycounter
"0"
```

## 分布式锁

```java
public class RedisLock {

    @Autowired
    private StringRedisTemplate redisTemplate;

    /**
     * 加锁
     * @param key
     * @param value 当前时间+超时时间
     * @return
     */
    public boolean lock(String key, String value) {
        /** 尝试使用 SETNX 获取锁 */
        if (redisTemplate.opsForValue().setIfAbsent(key, value)) {
            return true;
        }
        /** 获取该 key 的时间戳信息 */
        String currentValue = redisTemplate.opsForValue().get(key);
        /** 如果锁过期 */
        if (!StringUtils.isEmpty(currentValue)
                && Long.parseLong(currentValue) < System.currentTimeMillis()) {
            /** 获取上一个 key 设置的时间，因为 Redis 单线程，所以保证并发执行肯定有先后顺序 */
            String oldValue = redisTemplate.opsForValue().getAndSet(key, value);
            /** 第一个执行 SETGET 的客户端获取到锁 */
            if (!StringUtils.isEmpty(oldValue) && oldValue.equals(currentValue)) {
                return true;
            }
        }
        /** 锁获取失败 */
        return false;
    }

    /**
     * 解锁
     * @param key
     * @param value
     */
    public void unlock(String key, String value) {
        /** 删除 key */
        redisTemplate.opsForValue().getOperations().delete(key);
    }
}
```

## 缓存

启用缓存：

```java
@EnableCaching
```

缓存的对象需要实现`Serializable`接口：

```java
public class ResultVO<T> implements Serializable {
    private static final long serialVersionUID = 3068837394742385883L;
}
```

注解使用：

```java
/** 统一缓存前缀，加载类上，方法上的注解可不配置 value */
@CacheConfig(value = "app")
/** 缓存方法返回值，可以配置 SPEL 表达式 */
@Cacheable(value = "app", key = "#id")
/** 返回值添加到缓存中 */
@CachePut(value = "app", key = "#id")
/** 方法执行后删除缓存 */
@CachEvict(value = "app", key = "#id")
```