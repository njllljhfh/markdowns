# SQL语句记录





### 字符串列自增长语句

- 描述：获取字符串尾部指定范围的str串（该str串为数字），将得到的结果加1，然后转换为指定长度的字符串（左空位补0），最后在字符串前面拼接指定字符。

- sql：

  ```sql
  -- 字符串自增
  -- 表名《DETECT_MACHINE》，列名《DM_NAME》。
  -- DM_NAME列降序排列，得到的第一行的值是 "152WD2"。
  SELECT CONCAT('152WD',LPAD(((SELECT SUBSTRING(DM_NAME,6,1) FROM DETECT_MACHINE WHERE DM_NAME=(SELECT DM_NAME FROM DETECT_MACHINE ORDER BY DM_NAME DESC LIMIT 1))+1),4,0));
  -- 语句执行结果是："152WD0003"。
  ```



---



### 取消分组字段要在select中的限制

```sql
SET sql_mode=(SELECT REPLACE(@@sql_mode,'ONLY_FULL_GROUP_BY',''));
```



---

