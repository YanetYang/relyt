CREATE MATERIALIZED VIEW
=====

创建物化视图。



---


语法
--------

```sql
CREATE MATERIALIZED VIEW [ IF NOT EXISTS ] <table_name>
    [ (<column_name> [, ...] ) ]
    [ USING <method> ]
    [ WITH ( <storage_parameter> [= <value>] [, ... ] ) ]
    AS <query>
    [ WITH [ NO ] DATA ]
    [DISTRIBUTED {| BY <column> [<opclass>], [ ... ] | RANDOMLY | REPLICATED }]
    [ SYNC | ASYNC ]
```

---

描述
--------

当你使用 `CREATE MATERIALIZED VIEW` 来为一个查询创建物化视图时，除非指定了 `WITH NO DATA`，否则命令发出时会运行查询并用来填充视图。物化视图可以通过 [REFRESH MATERIALIZED VIEW](refresh-materialized-view.md) 来刷新。

`CREATE MATERIALIZED VIEW` 与 `CREATE TABLE AS` 类似。不同之处在于 `CREATE MATERIALIZED VIEW` 记录了用于初始化视图的查询。这个能力使得物化视图可以被刷新。

物化视图具有与表相同的属性，但不支持临时物化视图。


---

参数
----------

- **`IF NOT EXISTS`**

  如果命令中包含 `IF NOT EXISTS`，当存在同名的物化视图时，不会报错，而是产生一条通知信息。

- *`<table_name>`*
  
  物化视图的名称，支持通过 Schema 名称进行限定。


- *`<column_name>`*

  物化视图中列的名称。
  
  在物化视图中，列名是根据位置分配的。第一个列名分配给查询结果的第一列，依此类推。如果没有指定列名，将使用查询的输出列名。

- **`USING <method>`**

  指定用于存储新物化视图内容的方法的子句。方法必须是 `TABLE` 类型的访问方法。
  
  如果省略此子句，则使用默认的表访问方法。

- **`WITH ( <storage_parameter> [= <value>] [, ... ]`**

    此子句指定物化视图的可选存储参数。对于 `CREATE TABLE` 支持的所有参数，也支持 `CREATE MATERIALIZED VIEW`。更多信息见 [CREATE TABLE](create-table.md)。

- *`<query>`*

    查询，可以是 [SELECT](select.md)，[TABLE](select.md#table-命令)，或 [VALUES](values.md) 命令。执行此查询可能会导致安全限制操作，尤其是如果查询调用创建临时表的函数时。在这种情况下，查询将失败。

    :::warning 关于基表的使用限制
    在查询中指定为物化视图基表的表不支持如下操作：
    - 重命名表。
    - 重命名表中的列。
    - 向表中添加列。
    - 从表中移除列。
    :::


- **`WITH [NO] DATA`**

    指定是否用数据填充物化视图。
    
    - `WITH DATA`：填充数据，是默认值。

    - `WITH NO DATA`：不填充数据。这会让物化视图处于无法扫描的状态。

    声明为 `WITH NO DATA` 的物化视图，在通过 `REFRESH MATERIALIZED VIEW` 填充数据之前无法查询。

- **`DISTRIBUTED {| BY <column> [<opclass>], [ ... ] | RANDOMLY | REPLICATED }`**

    物化视图数据的分布策略。

    有关每个策略的详细信息，请参见 [CREATE TABLE](create-table.md)。

- **`SYNC | ASYNC`**

  指定是否在基表更新时自动更新物化视图。

  - **`SYNC`**：随基表的更新而更新。

  - **`ASYNC`**：不更新，并且是默认值。


---


使用注意事项
-------------

物化视图是只读的。系统不允许在物化视图上执行 `INSERT`、`UPDATE` 或 `DELETE`。使用 `REFRESH MATERIALIZED VIEW` 来更新物化视图数据。

如果你希望生成的数据是有序的，必须在物化视图查询中使用 `ORDER BY` 子句。但是，如果在包含 `ORDER BY` 或 `SORT` 子句的物化视图上执行 `SELECT`，数据不一定是有序或排序的。


---


示例
-------------

创建由所有喜剧电影组成的视图：

```sql
CREATE MATERIALIZED VIEW comedies AS SELECT * FROM films 
WHERE kind = 'comedy';
```

如上 SQL 语句将创建一个包含在视图创建时 `film` 表中的列的视图。尽管使用了 `*` 来创建物化视图，但后来添加到表中的列不会成为视图的一部分。
创建获取排名前十的婴儿名字的视图：

```sql
CREATE MATERIALIZED VIEW topten AS SELECT name, rank, gender, year FROM 
names, rank WHERE rank < '11' AND names.id=rank.id;
```

---

SQL 标准兼容性
-------------

`CREATE MATERIALIZED VIEW` 是 Relyt 对 SQL 标准的扩展。