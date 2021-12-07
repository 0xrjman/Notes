# PostgreSql

## Intro

1. What's DBMS

   > 存储，操作，检索 数据

2. What's Relational database

   > 采用关系模型来组织数据，采用行、列形式存储数据

3. 常见非关系型数据库（Mongodb，Redis）
4. 常见关系型数据库
   + PostgreSQL
     + 基于BSD/MIT
     + 源代码清晰易读
     + 支持所有SQL标准，支持JSON和其他NoSQL功能
     + 欧美使用者较多
   + Mysql
     + 只支持部分SQL标准，不够严谨
     + 国内使用者较多

## Getting Start

[PostgreSQL Download Link](https://www.postgresql.org/download/)

Or `brew install postgresql` in Mac OSX

[Getting Start in MacOSX](https://dyclassroom.com/howto-mac/how-to-install-postgresql-on-mac-using-homebrew)

## Usage

### Basic common

```shell
# Install and start
brew install postgres
psql --version
brew services start postgres

# Stop and restart
brew services stop postgres
brew services restart postgres

# Launch psql
psql `db_name` # default `postgres`
psql postgres

# console

## command
\du # List users
\l  # List databases
\c  # Connect to a db
\d  # List tables inside a db
\q  # Quit

\password # set pwd for currency user
\h `command_name` # ie. select
\? help
```

#### Users

```sql
CREATE USER `user_name` WITH PASSWORD 'XXXXXX';
ALTER USER `user_name` WITH PASSWORD 'XXXXXX';
DROP user `user_name`;
```

#### DB

```sql
CREATE DATABASE `db_name`;
CREATE DATABASE `db_name` OWNER `user_name`;
DROP DATABASE `db_name`;
```

#### Privaledges

```sql
GRANT ALL PRIVILEGES ON DATABASE `db_name` to `user_name`;
ALTER ROLE `user_name` CREATEDB;
```

#### Tables

```sql
CREATE TABLE users (
  id BIGSERIAL PRIMARY KEY,
  firstName VARCHAR(200) NOT NULL,
  middleName VARCHAR(200) DEFAULT NULL,
  lastName VARCHAR(200) DEFAULT NULL
);
DROP TABLE `table_name`;
```

### Query

```sql
SELECT * FROM `table_name`
```

### Login in

`psql -U [user] -d [database] -h [host] -p [port]`

> default port is 5432
