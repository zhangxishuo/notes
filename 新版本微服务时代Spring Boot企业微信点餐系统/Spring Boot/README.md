# Spring Boot

## JPA

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