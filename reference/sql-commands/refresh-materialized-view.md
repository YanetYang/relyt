REFRESH MATERIALIZED VIEW
=====

刷新物化视图。


---

语法
--------

```sql
REFRESH MATERIALIZED VIEW <name>
    [ WITH [ NO ] DATA ]
```

---

描述
----------

`REFRESH MATERIALIZED VIEW` 用于刷新物化视图的内容，旧的内容将被丢弃。要运行此命令，你必须是物化视图的所有者。如果指定了 `WITH DATA`（或默认），则执行后备查询以提供新数据，并且物化视图处于可扫描状态。如果指定了 `WITH NO DATA`，则不生成新数据，物化视图处于不可扫描状态。


---

参数
----------

- *`<name>`*

    要刷新的物化视图的名称，支持使用 Schema 名称进行限定。
    

- **`WITH [ NO ] DATA`**

    指定是否用数据填充物化视图。
    
    - `WITH DATA`：填充数据，是默认值。

    - `WITH NO DATA`：不填充数据。这会让物化视图处于无法扫描的状态。



---

使用注意事项
-------------

如果物化视图的定义查询中有 `ORDER BY` 子句，物化视图的原始内容将按那种方式排序；但 `REFRESH MATERIALIZED VIEW` 不保证保持该排序。

---

示例
-------------

此命令使用物化视图定义中的查询替换物化视图 `order_summary` 的内容，并使其处于可扫描状态。

```sql
REFRESH MATERIALIZED VIEW order_summary;
```

此命令释放与物化视图 `annual_statistics_basis` 关联的存储，并使其处于不可扫描状态。

```sql
REFRESH MATERIALIZED VIEW annual_statistics_basis WITH NO DATA;
```

---

SQL 标准兼容性
-------------

`REFRESH MATERIALIZED VIEW` 是 Relyt 对 SQL 标准的扩展。