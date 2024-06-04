DROP MATERIALIZED VIEW
=====

删除物化视图。

---

语法
--------

```sql
DROP MATERIALIZED VIEW [ IF EXISTS ] <name> [, ...] [ CASCADE | RESTRICT ]
```

---

描述
----------

`DROP MATERIALIZED VIEW` 用于删除物化视图。执行此命令，你必须是被删除的物化视图的所有者。

---


参数
----------

- **`IF EXISTS`**

    如果语句中包含 `IF EXISTS`，当被删除物化视图不存在时，系统不会报错，而是产生一条通知信息。

- *`<name>`*

    要删除的物化视图的名称，支持使用 Schema 名称进行限定。

- **`CASCADE | RESTRICT`**

    指定如果物化视图有依赖的数据库对象时是否删除物化视图。
    
    - `CASCADE`：删除物化视图及依赖于该物化视图的所有数据库对象。
    
    - `RESTRICT`：拒绝整个删除操作。`RESTRICT` 是默认设置。
    

---

示例
-------------

删除物化视图 `test_mv`：

```sql
DROP MATERIALIZED VIEW test_mv;
```

---

SQL 标准兼容性
-------------

`DROP MATERIALIZED VIEW` 是 Relyt 对 SQL 标准的扩展。