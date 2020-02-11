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