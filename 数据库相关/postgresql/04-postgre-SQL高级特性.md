## PostgreSQL SQL高级特性
#### 1. WITH查询
WITH查询这一特性通常被称为CTE(Common Table Expressions)，WITH查询在复杂查询中定义一个辅助语句（临时表），通常这一特性用于复杂查询或递归查询。

##### 1.1 复杂使用CTE:
例1:
```sql
WITH t as (
    SELECT generate_series(1, 3)
)
SELECT * from t;
```
result:
```sql
generate_series
-----------------
            1
            2
            3
```

例2:
```sql
WITH regional_sales as (
    SELECT region, SUM(amount) AS total_sales
    FROM orders
    GROUP BY region
), top_region AS (
    SELECT region
    FROM regional_sales
    WHERE total_sales > (SELECT SUM(total_sales)/10 FROM resional_sales)
)
SELECT region, 
       product, 
       SUM(quantity) AS product_units,
       SUM(amount) AS product_sales
FROM orders
WHERE region IN(SELECT region FROM top_regions)
GROUP BY region, product;
```
这个例子就是首先定义了regional_sales 和 top_regions 两个辅助语句，regional_sale算出每个区域的总销售量，top_regions算出销售量中占总销售量10%的所有地区，主查询语句通过辅助语句与orders表关联，算出了顶级区域每件商品的销售量和销售额。

##### 1.2 递归查询使用CTE:
WITH查询的一个属性 RECURSIVE(递归), 使用RECURSIVE属性可以引用自己的输出，实现递归，一般用于层次结构和树状结构的应用场景。
例：
```sql
WITH recursive t (x) AS (
    select 1
    union
    select x + 1
    from t
    where x < 5
)
selet SUM(x) FROM t;
```
result:
```sql
sum
------
    15
(1 row)
```

#### 2. 批量插入

##### 2.1 方式一：INSERT INTO...SELECT...
通过表数据或函数批量插入，语法:<br>
`INSERT INTO table_name SELECT...FROM source_table`

```sql
mydb=> INSERT INTO test_table (id, user_name)
SELECT id, user_name FROM test_ini;
或者
mydb=> INSERT INTO test_tablr (id, user_name)
SELECT generate_series(1, n), 'user_name';
```

##### 2.2 方式二：INSERT INTO VALUES(),(),(),...()
postgresql支持一条sql插入多条记录，相比一条sql插入一条记录，减少了和数据库的交互，减少数据库WAL(Write-Ahead Logging)日志的生成，提高插入效率。
例：
```sql
mydb=>INSERT INTO test_table (id, user_name) values (1,'a'),(2,'b'),(3,'c')
    id | user_name
-------+-----------
    1  |  a
    2  |  b
    3  |  c
(3 raw)
```

##### 2.3 COPY 或者 \copy元命令
```sql
mydb=>\copy table_name to '$path'
mydb=>\copy table_name from '$path'

or
mydb=# COPY table_name to '$path'
mydb=# COPY table_name from '$path'
```

#### 3. RERURNINF 返回修改的数据
postgresql的returning特性可以返回DML修改后的数据，（返回INSERT, UPDATE, DELETE）,这一特性在于不需要额外的SQL再去获取值，方便开发。
语法：<br>
`INSERT[,action] INTO table_name VALUES (n, 'n') RETURNING *[,指定column];`

#### 4. UPSERT
用来解决数据插入过程中数据冲突的情况。(用于在批量插入日志数据，如果有一条数据违反约束，插入失败导致回滚)。`INSERT ... ON CONFLICT UPDATE`
语法：
```sql
更新：
mydb=> INSERT INTO table_name 
       VALUES (),(),() 
       ON CONFLICT(column_name)
       DO UPDATE SET
       column=...

忽略：
mydb=> INSERT INTO table_name 
       VALUES (),(),() 
       ON CONFLICT(column_name)
       DO NOTHING
```

#### 5. 数据抽样(TABLESAMPLE)
9.5之前使用ORDER BY random()，性能低。语法：<br>
`(explain analize)select * from table_name order by random() limit 1`
> 注: explain analize是用于分析执行计划和执行时间。
9.5后支持使用TABLESAMPLE数据抽样，语法如下：
```sql
SELECT ...
FROM table_name
TABLESAMPLE sample_method(args:抽样因子)
注： sample_method有两种：SYSTEM BERNOULLI
```
区别：
- system: 随机抽取表上数据块上的数据，基于数据块级别，被选中的数据全部被检索。适用于效率优先场景。
- bernoulli: 随机抽取表上数据行，并返回指定的百分比数据，基于数据行级别，具有更好的随机性。但性能比system抽样低很多。适用于随机性优先场景。
  
#### 6. 聚合函数
- string_agg函数
   string_agg(expression, delimiter) 把结果集中某个字段的所有行按照分隔符连接成字符串。<br>
   例如：`select country, string_agg(city, ',') FROM city group by country`=> 上海，北京.
- array_agg函数
   array_agg(expression)把结果集中某个字段变成一个数组。<br>
   例如：`select country, array_agg(city) FROM city group by country`=> {上海，北京}.

#### 7. 窗口函数
内置窗口函数：row_number(), rank(), dense_rank(), lag(), first_value(), last_value(), nth_value()。或者聚合函数，自定义函数加上OVER()，也可做为窗口函数。
语法说明：
- OVER表示窗口函数的关键字
- PARTITION BY属性对查询返回的结果集进行分组。
- ORDER BY 属性这顶结果集的分组数据排序。

##### 7.1 avg() OVER()
聚合函数后接OVER属性的窗口函数表示在一个查询结果集上应用聚合函数。
例：`select subject, name, score, avg(score) OVER(PARTITION BY subject) from score;`

##### 7.2 row_number()
对结果集分组后的数据标注行号，从 1 开始。
例：`select row_number() over (order by id) as rownum, * from score;`

##### 7.3 rank()
对结果集分组后的数据标注行号，从 1 开始,出现字段相同则行号重复且行号产生间隙。
例：`select rank() over (partition by subject order by score), * from score;`

##### 7.4 dense_rank()
对结果集分组后的数据标注行号，从 1 开始,出现字段相同则行号重复,**但是**行号产生间隙。
例：`select dense_rank() over (partition by subject order by score), * from score;`

##### 7.5 lag()
获取偏移offset那行某个字段的数据。
语法：`lag(value[,offset [,default]])`
- value：指定返回记录的字段
- offset：行偏移量，正数表示取结果集中向上偏移的记录，负数表示取结果集中向下偏移的记录，默认为1。
- default: 指的是不存在offset偏移的行用默认值代替，默认值是NULL。

##### 7.6 first_value()
取结果集每一个分组的第一行数据的字段值。

##### 7.7 last_value()
取结果集每一个分组的最后一行数据的字段值。

##### 7.8 nth_value()
取结果集每一个分组的指定行数据的字段值。
语法：`nth_value(value any, nth integer)`,nth是指定行。