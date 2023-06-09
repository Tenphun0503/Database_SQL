# MySQL Learning Note

## Introduction
## Content 
### Basic
#### SQL
1. Grammar
   - Use `;` to end a command
   - Case-insensitive, but recommend uppercase
   - `--` or `#`(MySQL) for single line comment
   - `/* */` for multiline comment
2. DDL (Data Definition Language)  
Define database, table, field
   - For database
     - `SHOW DATABASES;` 
     - `SELECT DATABASE();` show present database in use
     - `CREATE DATABASE [IF NOT EXISTS] database_name [DEFAULT CHARSET] [COLLATE];`
       - `IF NOT EXISTS` if exist, won't continue
       - `COLLATE` sorting rule
     - `DROP DATABASE [IF EXISTS] database_name;`
       - `IF EXISTS` if not exist, won't continue
     - `USE database_name;`
   - For table
     - `SHOW TABLES;`
     - `DESC table_name;` describe the structure of the table
     - `SHOW CREATE TABLE table_name;` display the SQL code that was used to create the table
     - `CREATE TABLE table(column_name DATATYPE [CONSTRAINT] [COMMENT] ... )[COMMENT];`
       - [DATATYPE](#datatype)
     - `ALTER TABLE table_name ADD column_name DATATYPE [CONSTRAINT] [COMMENT];`
     - `ALTER TABLE table_name MODIFY column_name new_datatype;`
     - `ALTER TABLE table_name CHANGE column_name new_column_name [new]DATATYPE [CONSTRAINT] [COMMENT]`
     - `ALTER TABLE table_name DROP column_name;`
     - `ALTER TABLE table_name RENAME TO new_table_name;`
     - `DROP TABLE [IF EXISTS] table_name;`
     - `TRUNCATE TABLE table_name;` Drop then create again (empty the table)
3. DML (Data Manipulation Language)  
Insert, delete and update data
   - `INSERT INTO table (c1, c2, ...) VALUES (v1, v2, ...), [(V2),(V3),...];`
     - if values are set for each column, we can omit `(c1, c2, ...)`
   - `UPDATE table SET c1=v1, c2=v2, ... [WHERE condition];`
   - `DELETE FROM table [WHERE condition];`
     - `DELETE` will delete the row.
     - use `UPDATE` can 'delete' a column.
4. DQL (Data Query Language)  
Query data
   - `SELECT`: select columns
     - `SELECT c1, c2, c3 ... FROM table;`
     - `SELECT * FROM table;` Not recommend in real world development
     - `SELECT c1 AS 'alias1', ... FROM table;`  Set alias to the column, `AS` can be omitted
     - `SELECT DISTNICT c1, ... FROM table;`
   - `FROM`: from a table
   - `WHERE`: where condition
     - Comparison Operator
       - `>, >=, <, <=, =, <>or!=, BETWEEN...AND..., IN (...), LIKE ..., IS NULL`
         - `BWTEEEN...AND...` are inclusive
         - `LIKE ...`: `_` matches one character, `%` matches multiple characters
           - e.g. `name LIKE '___'`: query name with length of 3
     - Logical Operator
         - `AND or &&, OR or ||, NOT or !`
   - `GROUP BY`: group by condition
     - Usually use with Aggregate Functions `SELECT AF(column_name) FROM table`
       - `COUNT` `MAX` `MIN` `AVG` `SUM`
       - `NULL` value does not participate in the AF operation 
     - `SELECT c FROM table [WHERE] GROUP BY column_name [HAVING]`
     - selected columns are usually grouping column and AF fields.
     - e.g. `SELECT gender, COUNT(*) FROM emp GROUP BY gender;`
   - `HAVING`: after grouping condition
     - Compare with `WHERE`
       - Execute times are different: 
         - `WHERE` filter data before grouping
         - `HAVING` filter the result of grouping
       - Judgment conditions are different:
         - `WHERE` can not have AF
         - `HAVING` can have AF
     - e.g. `SELECT add, count(*) add_count FROM emp GROUP BY add HAVING add_count > 3`
     - > we filtered grouped data that were 3 or less. We can use alias or `HAVING count(*)>3`
   - `ORDER BY`: sort by condition
     - `SELECT c FROM table ORDER BY c1 sort_rule1, c2 sort_rule2;`
     - `sort_rule`
       - `ASC`(default)
       - `DESC`
   - `LIMIT`: pagination
     - `SELECT c FROM table LIMIT start_index, query_amount`
       - MySQL is 0 indexed
       - start_index = (query_page - 1) * amount_per_page
       - for the first page, start_index can be omitted
       - pagination is a dialect
   - DQL Write Order
     - select - from - where - group by - having - order by - limit
   - DQL Execute Order
     - from - where - group by - having - select - order by - limit
5. DCL (Data Control Language)  
Create and modify database user, grant access
   - Query user
     - `USE mysql;`
     - `SELECT * FROM user;` all info of users are store in table 'user' of database 'mysql'
   - Create user
     - `CREATE USER 'username'@'hostname' IDENTIFIED BY 'password';`
       - `hostname` set as `%` to match any host
   - Edit user password
     - `ALTER USER 'username'@'hostname' IDENTIFIED WITH 'password' BY 'new_password';`
   - Delete user
     - `DROP USER 'username'@'hostname';`
   - Privilege
     - `ALL, ALL PRIVILEGES`
     - `SELECT`, `INSERT`, `UPDATE`, `DELETE`, `ALTER`, `DROP`, `CREATE`
   - Check access
     - `SHOW GRANTS FOR 'username'@'hostname';`
   - grant access
     - `GRANT privilege ON database.table TO 'username'@'hostname';`
     - `*.*`: all tables of all databases
   - remove access
     - `REVOKE privilege ON database.table FROM 'username'@'hostname';`
#### Datatype
- Numeric  

| Data Type      | Size    | Signed Range                                | Unsigned Range     |
|----------------|---------|---------------------------------------------|--------------------|
| TINYINT        | 1 byte  | -128 to 127                                 | 0 to 255           |
| SMALLINT       | 2 bytes | -32,768 to 32,767                           | 0 to 65,535        |
| MEDIUMINT      | 3 bytes | -8,388,608 to 8,388,607                     | 0 to 16,777,215    |
| INT or INTEGER | 4 bytes | -2,147,483,648 to 2,147,483,647             | 0 to 4,294,967,295 |
| BIGINT         | 8 bytes | -2^63 to 2^63 - 1                           | 0 to 2^64 - 1      |
| FLOAT(p)       | 4 bytes | -3.4 x 10^38 to 3.4 x 10^38 (precision p)   | 0 to 3.4 x 10^38   |
| DOUBLE(p)      | 8 bytes | -1.7 x 10^308 to 1.7 x 10^308 (precision p) | 0 to 1.7 x 10^308  |
| DECIMAL(p,s)   | p/2 + 1 | -10^p+1 to 10^p-1 (precision p, scale s)    | 0 to 10^p-1        |
> for unsigned numeric data: i.e. `age TINIYINT UNSIGNED`  
> for decimal numeric data: i.e. `score DOUBLE(4,1)` (4:length, 1: length of decimal part)

- String

| Data Type  | Range                        |
|------------|------------------------------|
| CHAR       | 0 - 255 characters           |
| VARCHAR    | 0 - 65,535 characters        |
| TINYBLOB   | 0 - 255 bytes                |
| TINYTEXT   | 0 - 255 characters           |
| BLOB       | 0 - 65,535 bytes             |
| TEXT       | 0 - 65,535 characters        |
| MEDIUMBLOB | 0 - 16,777,215 bytes         |
| MEDIUMTEXT | 0 - 16,777,215 characters    |
| LONGBLOB   | 0 - 4,294,967,295 bytes      |
| LONGTEXT   | 0 - 4,294,967,295 characters |
> `CHAR(10)` is fixed-length string. blank spaces are saved as ' '. more efficient

- Datetime

| Data Type | Size | Format               |
|-----------|------|----------------------|
| DATE      | 3    | YYYY-MM-DD           |
| TIME      | 3    | HH:MM:SS             |
| YEAR      | 1    | YYYY                 |
| DATETIME  | 8    | YYYY-MM-DD HH:MM:SS  |
| TIMESTAMP | 4    | YYYY-MM-DD HH:MM:SS  |

#### Function
1. String Function
   - `CONCAT(S1,S2,...)`
   - `LOWER(str)`
   - `UPPER(str)`
   - `LPAD(str,n,pad)` pads the left side of the string with a certain character `pad` until total of n in length
   - `RPAD(str,n,pad)` pads the right side of the string with a certain character `pad` until total of n in length
   - `TRIM(str)`
   - `SUBSTRING(str,start,len)`
2. Mathematical  Function
   - `CEIL(x)`
   - `FLOOR(x)`
   - `MOD(x,y)`
   - `RAND()` random number between 0 and 1
   - `ROUND(x,y)` rounds a numeric value `x` to a specified precision `y`
3. Date and Time Function
   - `CURDATE()` return current date
   - `CURTIME()` return current time
   - `NOW()` return current time and date
   - `YEAR(date)` get year from a date
   - `MONTH(date)` get month from a date
   - `DAY(date)` get day from a date
   - `DATE_ADD(date, INTERVAL expr type)` return date + a mount of time `expr`
     - `type` can be `DAY`, `MONTH`, `YEAR`
   - `DATEDIFF(date1, date2)` days between date1 and date2
     - `date` can be form of `'YYYY-MM-DD'`
4. Flow Function
   - `IF(value, t, f)`
   - `IFNULL(value1, value2)` return value1 if value is not null, else return value2
   - `CASE WHEN [val1] THEN [res1] ... ELSE [default] END` if val1 is true, return res1, ...
   - `CASE [expr] WHEN [val1] THEN [res1] ... ELSE [default] END` if expr==val1, return res1 ...
#### Constraint
1. Intro
   - Constraints help to ensure the accuracy, consistency, and integrity of data in a database.
2. Constraint
    - `NOT NULL`
    - `UNIQUE`
    - `PRIMARY KEY`
        - `AUTO_INCREMENT`
    - `DEFAULT`
    - `CHECK` ensure a field satisfies a condition (MySQL 8.0.16)
    - `FOREIGN KEY`
3. Foreign Key
   - A FOREIGN KEY constraint is used to establish a relationship between two tables.
   - It ensures that the values of a column match another table's primary key column.
   - Foreign key ensures the accuracy, consistency, and integrity among different tables
   - Use of Foreign key
     - `[foreign_key_name] FOREIGN KEY (column) REFERENCE parent_table(column_of_parent_table)`
     - `ALTER TABLE table ADD CONSTRAINT foreign_key_name FOREIGN KEY (column) REFERENCE parent_table(column_of_parent_table)`
     - `ALTER TABEL table DROP FOREIGN KEY foregin_key_name`
   - Foreign Key Constraint
     - `NO ACTION`: when delete/update in parent table, if foreign key data exists, take no action
     - `RESTRICT`: same as `NO ACTION`
     - `CASCADE`: when delete/update in parent table, if foreign key data exists, delete them together
     - `SET NULL`: when delete/update in parent table, if foreign key data exists, set to null (can't be not null)
     - `SET DEFAULT`: when update in parent table, set a default value for foreign key column of child table 
   - Foreign Key Constraint Apply
     - `ALTER TABLE table ADD CONSTRAINT fk FOREIGN KEY (c) REFERENCE pt(cop) ON UPDATE CASCADE ON DELETE CASCADE;`
#### Multi-table query 
#### Transaction

### Advance
#### Storage engine
#### Index
#### SQL optimize
#### View / stored  process / trigger
#### Lock
#### InnoDB core
#### MySQL management

### Devops
#### Log
#### replication
#### Sharding (database partitioning)
#### Read-write separation
