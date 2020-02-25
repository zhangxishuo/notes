# 数据库

## 数据表结构设计

唯一索引：

```sql
UNIQUE KEY `uqe_username` (`username`) USING BTREE
```

组合索引：

```sql
KEY `idx_user_id_order_no` (`user_id`, `order_no`) USING BTREE
```

`DECIMAL`，一共`20`位，整数`18`位，小数`2`位，用于高精确度计算。

```sql
`price` DECIMAL(20, 2)
```