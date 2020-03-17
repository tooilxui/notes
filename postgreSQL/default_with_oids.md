PostgreSQL預設不生成OID (相當於informix的ROWID)，因此要產生OID有兩個做法：

1. 使用set default_with_oids=true; 讓建立表格時默認使用with oids指令。


2. 於Create Table 時加上WIHT OIDS語法。

`` -- Create a table with OID column
   CREATE TABLE aircraft
   (
      name VARCHAR(90)
   ) WITH OIDS; ``