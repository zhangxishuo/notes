# Redis

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