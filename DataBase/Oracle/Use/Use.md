# Oracle基础使用方法

基本使用方法同mysql相同

需要注意大写问题

- 查询所有数据库
    ```sql
    select * from v$database;
    ```

- 查询所有表
    ```sql
    select table_name from user_tables;
    ```

- 查询表所有字段信息
    ```sql
    select * from user_col_comments where table_name='表名';
    select * from cols WHERE TABLE_name=upper('表名');
    ```

- 分页查询(sql中limit方法)
    ```sql
    select * from (select 表名.*, ROWNUM FROM 表名 WHERE ROWNUM <= 结束行号) WHERE ROWNUM >= 起始行号
    ```
