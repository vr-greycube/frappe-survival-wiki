# Mariadb

#### db table size

```
SELECT table_schema AS DB_NAME, TABLE_NAME,
ROUND((DATA_LENGTH+INDEX_LENGTH)/1024/1024,2) AS TABLE_SIZE_in_MB
FROM information_schema.TABLES
where TABLE_SCHEMA <> 'information_schema' -- and table_schema = ''
Order By TABLE_SIZE_in_MB desc
```


#### values statement
```
with t(name)
as (
VALUES 
	('CCU1-T-223'),
	('PNQ3-T-51'),
	('PNQ3-T-48')
)	
select * from t
	
```