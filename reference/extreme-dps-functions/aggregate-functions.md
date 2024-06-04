

# 聚合函数

聚合函数能够对一组数据执行计算，最终得出一个独立的值。Extreme DPS 为您提供多种聚合函数，以便于您更加高效地进行数据分析。


 

---

 

## ARRAY_AGG

将一组值（包括 null）组合成一个数组返回。

### 语法

```sql
ARRAY_AGG (<expression>) [
   ORDER BY <sort_expression>]
```

 

### 参数

*`<expression>`*：表达式或列名，用于确定要放入数据中的值。支持的数据类型包括 `boolean`、`smallint`、`integer`、`bigint`、`real`、`decimal`、`varchar`、`date`、`timestamp`、`timestamp with time zone`。


 

### 返回

`array` 数组。


 

### 示例

```sql
SELECT ARRAY_AGG(order_amount)
FROM orders;
```
 
---

 

## BOOL_AND 或 BOOL_EVERY

判断是否所有非 null 输入值都为真。如是，则返回为真，如否，则返回为假。

:::info
`BOOL_EVERY` 为 `BOOL_AND` 的别名，二者行为完全一致。
:::


 

### 语法

```sql
{BOOL_AND | BOOL_EVERY }(<expression>)
```

 

### 参数

*`<expression>`*：类型为 `boolean` 的列名称或计算结果为 `boolean` 值的表达式。


 

### 返回

`boolean` 值。


 

### 示例

1. 创建名为 `employee_attendance`，其中字段 `present` 记录出勤情况，true 为出勤，false 为缺勤。

   ```sql
   CREATE TABLE employee_attendance (
      employee_id integer,
      present boolean
   );
   ```

2. 插入出勤数据。

   ```sql
   INSERT INTO employee_attendance (employee_id, present) VALUES
   (1, TRUE),
   (1, TRUE),
   (2, FALSE),
   (2, TRUE),
   (3, TRUE),
   (3, FALSE),
   (3, TRUE);
   ```

3. 查询全勤的员工。

   ```sql
   SELECT employee_id, 
         bool_and(present) AS all_days_present
   FROM employee_attendance
   GROUP BY employee_id;
   ```
   
   只有员工 1 全勤。

 

---

 

## BOOL_OR

判断是否非 null 值中至少有一个为真。如是，则返回为真，如否，则返回为假。


 

### 语法

```sql
BOOL_OR(<expression>)
```

 

### 参数

*`<expression>`*：类型为 `boolean` 的列名称或计算结果为 `boolean` 值的表达式。


 

### 返回

`boolean` 值。


 

### 示例

1. 创建名为 `customer_feedback` 的表用于记录客户反馈，其中字段 `positive_feedback` 为真时，表示为正向反馈，为假是，表示为负向反馈。

   ```sql
   CREATE TABLE customer_feedback (
      customer_id INT,
      positive_feedback BOOLEAN
   );
   ```

2. 插入客户反馈数据。

   ```sql
   INSERT INTO customer_feedback (customer_id, positive_feedback) VALUES
   (1, TRUE),
   (1, FALSE),
   (2, FALSE),
   (2, FALSE),
   (3, TRUE),
   (3, TRUE),
   (3, FALSE);
   ```

3. 查询至少有给过一次正面反馈的客户。
   
   ```sql
   SELECT customer_id,
         bool_or(positive_feedback) AS has_positive_feedback
   FROM customer_feedback
   GROUP BY customer_id;
   ```
   其中，用户 1 和用户 3 都曾给出正向反馈。


 

---

 

## SUM

返回非空输入值的总和。

:::info
`SUM` 会自动忽略空值。但是如果所有记录都是空值，将返回 null。
:::


 

### 语法

```sql
SUM([DISTINCT] <column_name> | <expression> )
```

 

### 参数

- *`<column_name>`*

   被求和的非空值所在的列的名称。

- *`<expression>`*

   计算结果为 `SUM` 支持的输入类型的表达式。

   支持的输入类型包括 `real`、`double precision`、`smallint`、`bigint`、`integer` 和 `decimal`。


 

### 返回

返回类型根据输入类型而异：

- 如果输入类型是 `real` 或 `double precision`，返回类型与输入类型相同。

- 如果输入类型是 `smallint`，返回类型是 `bigint`。 

- 如果输入类型是 `integer`，返回类型是 `bigint`。

- 如果输入类型是 `bigint`，返回类型是 `decimal(19,0)`。

- 如果输入类型是 `decimal`，返回类型是 `decimal`，精度为 `38`，并保持与输入小数相同的小数位数。


 

### 示例

```sql
SELECT SUM(price) FROM orders;

SELECT SUM(price * (1 + tax_rate)) FROM orders;
```

 

---

 

## MIN / MAX

返回指定记录中的最小值或最大值。

:::info
`MIN` 或 `MAX` 会自动忽略空值。如果所有记录都是空值，将返回 null。
:::


 

### 语法

```sql
MAX(<column_name> | <expression>)
MIN(<column_name> | <expression>)
```

 

### 参数

- *`<column_name>`*

   列的名称，将从该列存储的记录中提取最大值或最小值。

- *`<expression>`*

   计算结果为数值类型的表达式。

关于 Relyt 支持的数值类型，请参考 [数值类型](reference/data-types/numeric.md)。


 

### 返回

返回类型与输入类型相同。


 

### 示例

```sql
SELECT MIN(price) FROM orders;
SELECT MAX(price) FROM orders;
SELECT MIN(price), MAX(price) FROM orders;
```

 

---

 

## COUNT

返回非空值的数量或所有值的数量。

 

### 语法

```sql
COUNT ( <column_name> | <expression> )

COUNT( * )

COUNT( "any"  )
```

 

### 参数

- *`<column_name>`*

   将从其中计算非空值的数量的列的名称。

- *`<expression>`*

   计算结果为数值类型的表达式。

- __*__

   指定返回输入行的数量。

- **any**

   指定返回输入值非空的输入行的数量。


关于 Relyt 支持的数值类型，请参考 [数值类型](reference/data-types/numeric.md)。


 

### 返回

`bigint` 值。

 

### 示例

```sql
SELECT COUNT(order_id) FROM orders;
SELECT COUNT(*) FROM orders;
SELECT COUNT("any") FROM orders;
```

 

---

 

## AVG

返回非 null 值的平均值。

:::info
`AVG` 会自动忽略空值。如果所有值都是空值，将返回 null。
:::


 

### 语法

```sql
AVG( <column_name> | <expression> )
```

 

### 参数

- *`<column_name>`*

   需要计算平均值的列的名称。

- *`<expression>`*

   计算结果为 `AVG` 支持的输入类型的表达式。

支持的输入类型包括 `double precision`、`smallint`、`integer` 和 `bigint`。


 

### 返回 

返回类型根据输入类型而异：

- 如果输入类型是 `double precision`，返回类型是 `double precision`。

- 如果输入类型是 `smallint`，返回类型是 `decimal(25,6)`。

- 如果输入类型是 `integer`，返回类型是 `decimal(25,6)`。

- 如果输入类型是 `bigint`，返回类型是 `decimal(25,6)`。

### 示例

```sql
SELECT AVG(price) FROM orders;

SELECT AVG(price * quantity) FROM orders;
```

 

---

 

## PERCENTILE_CONT

根据输入列（在 *`<sort_expression>`* 中指定）的连续分布返回一个百分位数值。如果没有输入行恰好位于期望的百分位上，则结果通过对两个最近输入值进行线性插值计算得出。在计算中忽略 null 值。

:::info
`PERCENTILE_CONT` 工作步骤如下：
1. 根据 *`<sort_expression>`* 对数据集中的值按升序排序。
2. 根据所需百分比计算出一个位置。如对应位置存在值，则返回个该值。如不存在，则执行步骤 3。
3. 使用线性内插在相邻的实际数据值之间计算出一个值并返回该值。
:::

 

### 语法

```sql
PERCENTILE_CONT(<fraction>) WITHIN GROUP (ORDER BY <sort_expression>)
```

 

### 参数

- *`<fraction>`*

   指定百分比，取值范围为大于 0 小于 1 之间的 `double precision` 值。

- *`<sort_expression>`*

   排序表达式，数据类型为 `double precision`、`timestamp` 或 `timestamp with time zone`。

 

### 返回

返回值类型和 *`<sort_expression>`* 的数据类型相同。

 

### 示例

1. 创建名为 `Scores` 的表，其中包含学生的分数：

   ```sql
   CREATE TABLE Scores (
      id INT,
      score DECIMAL (10, 1)
   );

   INSERT INTO Scores (id, score) VALUES 
   (1, 88.5),
   (2, 75.0),
   (3, 92.0);
   ```

2. 计算学生分数的中位数：

   ```sql
   SELECT PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY score) 
   OVER () 
   AS MedianScore
   FROM Scores;
   ```

   返回结果为 `88.5`。

 

---

 

## PERCENTILE_DISC

根据指定表达式对数据集进行排序后，返回第一个在排序后的数据集中位于（或大于）指定百分位的值。

`PERCENTILE_DISC` 是一个假定离散分布模型的逆分布函数。该函数接受一个百分比值和由 `ORDER BY` 子句指定的排序表达式，并返回带有大于或等于指定百分比的最小累积分布值。

:::note
不同于 [PERCENTILE_CONT](#percentile_cont) 可以返回不存在的值，如果指定百分位的值不存在，`PERCENTILE_DISC` 会返回在排序后第一个大于指定百分位的值。
:::


 

### 语法

```sql
PERCENTILE_DISC(<fraction>) WITHIN GROUP (ORDER BY <sort_expression>)
```


 

### 参数

- *`<fraction>`*

   指定百分比，取值范围为大于 0 小于 1 之间的 `double precision` 值。

- *`<sort_expression>`*

   排序表达式，数据类型为 `boolean`、`smallint`、`integer`、`bigint`、`real`、`double precision`、`decimal`、`varchar`、`date`、`timestamp` 或 `timestamp with time zone`。


 

### 返回

返回值类型和 *`<sort_expression>`* 的数据类型相同。

 

### 示例

1. 创建名为 `employees` 的表，表中记录了员工的姓名和薪资：

   ```sql
   CREATE TABLE employees (
      name varchar(100),
      salary decimal(10, 2)
   );
   INSERT INTO employees (name, salary) VALUES 
   ('Alice', 70000.00),
   ('Bob', 48000.50),
   ('Charlie', 54000.00),
   ('David', 65000.00),
   ('Eve', 50000.00);
   ```

2. 调用 `PERCENTILE_DISC` 函数查看员工薪资中位数：

   ```sql
   SELECT PERCENTILE_DISC(0.5) WITHIN GROUP (ORDER BY salary) 
   AS median_salary
   FROM employees;
   ```

   返回结果为 `54000.00`。

 

---

 

## RETENTION

接受 2 到 32 个输入参数作为条件，来指示事件是否满足特定条件：

- 如果条件 1 为真，且条件 N 为真时，在返回的数组里，第一个元素为 `1`（真）且第 N 个元素为 `1`（真）。

- 如果条件 1 为假，则不论条件 N 为真还是假，在返回的数组里，第 N 个元素为 `0`（假），即返回一个全 `0` 数组。

 


### 语法

```sql
RETENTION(<cond1>, <cond2>[,..., <cond32>])
```
 


### 参数

*`<condN>`*：返回为 `0` 或 `1` 的表达式。

 


### 返回

有 `0` 和 `1` 构成的数组：

- `0`：事件不满足条件

- `1`：事件满足条件

 


### 示例


```sql
--创建测试表并插入数据
CREATE TABLE retention_test(on_date date, uid int);

INSERT INTO retention_test(uid, on_date) VALUES 
(1, '2024-01-01'), 
(1, '2024-01-02'), 
(1, '2024-01-03'), 
(2, '2024-01-02'), 
(2, '2024-01-03');


--查询留存
SELECT uid, RETENTION(on_date = '2024-01-01', on_date ='2024-01-02', on_date = '2024-01-03') 
FROM retention_test GROUP BY uid;


--返回结果
 uid | retention
-----+-----------
   1 | {1,1,1}
   2 | {0,0,0}
```
 

---

 

## WINDOW_FUNNEL

搜索滑动时间窗口内的事件列表，计算条件匹配的事件链里的最大连续事件数。

 



### 语法

```sql
WINDOW_FUNNEL(<window>, <mode>, <timestamp>, <cond1>[, <cond2>, ...])
```

 


### 参数

- *`<window>`*

   滑动窗口的大小，表示事件链中第一个事件和最后一个事件的最大间隔，单位由 *`<timestamp>`* 决定，类型为 `bigint`。

- *`<mode>`*

   事件链的筛选模式，类型为 `integer`。可以按下列标记位组合：

   - **'strict_deduplication'**：严格去重，如果遇到一个已经接受过的事件，则停止遍历，窗口向后滑动。

   - **'strict_order'**：严格有序，如果遇到一个时间戳不包含任何事件，或者遇到了非连续事件，停止继续遍历，窗口向后滑动。
   
   - **'strict_increase'**：时间戳严格递增，不允许事件链上相邻事件的时间戳相等。

- *`<timestamp>`*

   时间戳列，类型为 `date`、`bigint`、 `integer`、`smallint` 或 `timestamp`。

- *`<condN>`*

   描述事件链的条件，最多可支持同时指定 32 个条件。


 



### 返回

返回值类型为 `bigint`。值为滑动窗口内满足条件的最大连续事件数。

 



### 示例

1. 创建测试表并插入数据：

   ```sql
   DROP TABLE IF EXISTS window_funnel_test_table;
   CREATE TABLE window_funnel_test_table (
      timestamp timestamp,
      event bigint
   )USING pg_zdb;

   INSERT INTO window_funnel_test_table (timestamp, event) 
   VALUES 
   ('2022-02-01 00:00:01.000000', 1),
   ('2022-02-01 00:00:01.000100', 2),
   ('2022-02-01 00:00:01.000200', 3),
   ('2022-02-01 00:00:01.000300', 1),
   ('2022-02-01 00:00:01.000400', 2),
   ('2022-02-01 00:00:01.000500', 1),
   ('2022-02-01 00:00:01.000600', 2),
   ('2022-02-01 00:00:01.000700', 3),
   ('2022-02-01 00:00:01.000800', 4);

   --查询当前表内容
   SELECT * FROM window_funnel_test_table ORDER BY timestamp;

   --查询结果
            timestamp        | event 
   --------------------------+-------
    2022-02-01 00:00:01      |     1
    2022-02-01 00:00:01.0001 |     2
    2022-02-01 00:00:01.0002 |     3
    2022-02-01 00:00:01.0003 |     1
    2022-02-01 00:00:01.0004 |     2
    2022-02-01 00:00:01.0005 |     1
    2022-02-01 00:00:01.0006 |     2
    2022-02-01 00:00:01.0007 |     3
    2022-02-01 00:00:01.0008 |     4
   ```

2. 以严格去重的模式搜索事件：

   ```sql
   SELECT WINDOW_FUNNEL(1000, 1, time, event=1, event=2, event=3, event=4) 
   FROM window_funnel_test_table;
   
   --查询结果
    window_funnel 
   ---------------
             4
   ```

 
---

 

## APPROX_DISTINCT

返回唯一值的近似数量。

这个函数提供了 `COUNT(DISTINCT x)` 的近似值。如果所有输入值均为 null，则返回零。

:::info 说明
这个函数应该产生 2.3% 的标准误差，这是所有可能集合上（近似正态的）误差分布的标准偏差。它并不能保证任何特定输入集的误差上限。
:::

 



### 语法

```sql
APPROX_DISTINCT (<expression>)
```

 



### 参数

*`<expression>`*：需要计算唯一值数量的表达式。支持的数据类型包括 `boolean`、`smallint`、`integer`、`bigint`、`real`、`double precision`、`decimal`、`varchar`、`date`、`timestamp`、`timestamp with time zone`。


 



### 返回

`bigint` 值。

 


### 示例

```sql
SELECT APPROX_DISTINCT(product_id)
FROM sales_data_table;
```

 

---

 


## STDDEV 或 STDDEV_SAMP

返回一组数值的样本标准差。如所有值均为 null，返回 null。

:::info 说明
`STDDEV_SAMP` 是 `STDDEV` 的别名，二者行为完全一致。
:::


 

### 语法

```sql
{ STDDEV | STDDEV_SAMP } ( [ DISTINCT ] <expression> )
```

 

### 参数

*`<expression>`*：会产生一个数值的表达式。支持的数值类型包括 `smallint`、`integer`、`bigint`、`real`、`double precision`。


 

### 返回

返回类型因输入类型而异：

- 如果输入类型是 `double precision` 或 `real`，则返回类型为 `double precision`。

- 如果输入类型是 `smallint`、`integer` 或 `bigint`，则返回类型为 `decimal(38,6)`。


 


### 示例

```sql
SELECT STDDEV(salary) FROM employee_salaries;
SELECT STDDEV_SAMP(salary) FROM employee_salaries;
```

 

---

 

## STDDEV_POP

返回一组数值的总体标准差。如所有值均为 null，返回 null。

 

### 语法

```sql
STDDEV_POP( [ DISTINCT ] <expression>)
```

 

### 参数

*`<expression>`*：会产生一个数值的表达式。支持的数值类型包括 `smallint`、`integer`、`bigint`、`real`、`double precision`。

 

### 返回

返回类型因输入类型而异：

- 如果输入类型是 `double precision` 或 `real`，则返回类型为 `double precision`。

- 如果输入类型是 `smallint`、`integer` 或 `bigint`，则返回类型为 `decimal(38,6)`。


 


### 示例

```sql
SELECT STDDEV_POP(salary) FROM employee_salaries;
```

 

---

 

## VARIANCE 或 VAR_SAMP

返回一组数值的样本方差。如果所有值均为 null，则返回 null。

:::info
`VAR_SAMP` 是 `VARIANCE` 的别名，二者行为完全一致。
:::


 

### 语法

```sql
{VARIANCE | VAR_SAMP }( [ DISTINCT ] <expression> )
```


 


### 参数

*`<expression>`*：会产生一个数值的表达式。支持的数值类型包括 `smallint`、`integer`、`bigint`、`real`、`double precision`。

 

### 返回

返回类型因输入类型而异：

- 如果输入类型是 `double precision` 或 `real`，则返回类型为 `double precision`。

- 如果输入类型是 `smallint`、`integer` 或 `bigint`，则返回类型为 `decimal(38,6)`。


 


### 示例

```sql
SELECT VARIANCE(salary) FROM employee_salaries;
SELECT VAR_SAMP(salary) FROM employee_salaries;
```

 

---
 

## VAR_POP

返回一组数值的总体方差。如果所有记录均为 null，则返回 null。


 

### 语法

```sql
VAR_POP( [ DISTINCT ] <expression> )
```

 


### 参数

*`<expression>`*：会产生一个数值的表达式。支持的数值类型包括 `smallint`、`integer`、`bigint`、`real`、`double precision`。

 

### 返回

返回类型因输入类型而异：

- 如果输入类型是 `double precision` 或 `real`，则返回类型为 `double precision`。

- 如果输入类型是 `smallint`、`integer` 或 `bigint`，则返回类型为 `decimal(38,6)`。


 


### 示例

```sql
SELECT VAR_POP(salary) FROM employee_salaries;
```

 

---

 


## STRING_AGG

将多个字符串值合并成一个字符串并返回。

 


### 语法

```sql
STRING_AGG ( [ DISTINCT ] <expression> , <delimiter> ) [
   ORDER BY <sort_expression>]
```

 

### 参数

- *`<expression>`*

   需要聚合的 `varchar` 列。

- *`<delimiter>`*

   用于分隔各个值的 `varchar` 字符串。

- *`<sort_expression>`*

   非常量排序表达式。


 

### 返回

返回类型和输入类型相同。




### 示例


```sql
SELECT category, STRING_AGG(name, ', ' ORDER BY name)
FROM products
GROUP BY category;
```

