# ONLY_FULL_GROUP_BY 报错
- 查询model
````
select @@global.sql_mode

select @@sql_mode
````
如果结果存在ONLY_FULL_GROUP_BY
- 修改model
````
set @@global.sql_mode=(select replace(@@global.sql_mode,'ONLY_FULL_GROUP_BY',''));

set @@sql_mode=(select replace(@@global.sql_mode,'ONLY_FULL_GROUP_BY',''));
````
- 查看修改结果
````
select @@global.sql_mode

select @@sql_mode
````