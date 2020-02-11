# Spring Boot

## JPA

- 建表用`SQL`，不用`JPA`建表。
- 慎用`@OneToMany`和`@ManyToOne`，不利于分库分表。
- 不使用外键，在程序中手动控制。

在生成更新语句时，仅使用被修改过的列：

```java
@DynamicUpdate
```

配置事务，发生异常时回滚；在单元测试中配置，事务自动回滚：

```java
@Transactional
```

实体类字段不映射到数据库：

```java
@Transient
```

控制台打印`Hibernate`生成的`SQL`语句：

```yml
spring:
  jpa:
    show-sql: true
```

## MyBatis

`MyBatis`官方不推荐使用`XML`的方式，建议使用注解方式开发。

配置`Mapper`日志级别，打印`MyBatis SQL`执行情况：

```yml
logging:
  level:
    com.imooc.mapper: trace
```

## 序列化

`Json`序列化时忽略属性：

```java
@JsonIgnore
```

自定义序列化程序：

```java
public class Date2LongSerializer extends JsonSerializer<Date> {

    @Override
    public void serialize(Date date, JsonGenerator jsonGenerator, SerializerProvider serializerProvider) throws IOException, JsonProcessingException {
        jsonGenerator.writeNumber(date.getTime() / 1000);
    }
}
```

```java
/** 创建时间. */
@JsonSerialize(using = Date2LongSerializer.class)
private Date createTime;
```

## Spring Boot

枚举也可以实现接口。

资源文件：`resources/static`目录。

根据前缀映射配置：

```java
@ConfigurationProperties(prefix = "app")
```

获取`request`请求对象：

```java
ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
HttpServletRequest request = attributes.getRequest();
```

## 压力测试

使用简易工具`Apache ab`。