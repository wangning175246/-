#### 修改表

##### 修改表的名称

```go
rename table user to users;
```

##### 修改字段命令

```sql
alter table user change age user_pwd varchar(20);
```

##### 修改字段类型

```sql
ALTER  TABLE 表名 MODIFY [COLUMN] 字段名 新数据类型 新类型长度  新默认值  新注释
```

##### 添加字段

```sql
alter table user add  user_data 字段类型;
```

##### 添加索引

```sql
lter table user  add unique 索引名称(字段名);
```

#### 删除语句

##### 清空表

```sql
delete from user;
truncate table user;
```