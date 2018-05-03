```shell
# mysql
show databases;
# postgresql
\list
```

```shell
# mysql
use <database_name>
# postgresql
\c <database_name>
```

## table

```shell
# mysql
show tables;
# postgresql
\dt
```
```shell
# mysql
show create table <table_name>;
# postgresql
\d+ <table_name>
```

```shell
# mysql
SELECT TABLE_NAME, TABLE_ROWS FROM information_schema.TABLES where TABLE_SCHEMA=<database_name> and TABLE_ROWS > 0;
```

```shell
# mysql
SELECT sum(DATA_LENGTH)+sum(INDEX_LENGTH), FROM information_schema.TABLES where TABLE_SCHEMA=<database_name>;

# mysql
SELECT TABLE_NAME, DATA_LENGTH+INDEX_LENGTH, TABLE_ROWS FROM information_schema.TABLES where TABLE_SCHEMA=<database_name> and TABLE_NAME=<table_name>;
```
