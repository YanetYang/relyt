ALTER MATERIALIZED VIEW
=====

重定义物化视图。

---

语法
--------

```sql
ALTER MATERIALIZED VIEW [ IF EXISTS ] <name> <action> [, ... ]
ALTER MATERIALIZED VIEW [ IF EXISTS ] <name>
    RENAME [ COLUMN ] <column_name> TO <new_column_name>
ALTER MATERIALIZED VIEW [ IF EXISTS ] <name>
    RENAME TO <new_name>
ALTER MATERIALIZED VIEW [ IF EXISTS ] <name>
    SET SCHEMA <new_schema>

其中 <action> 可以是：

    ALTER [ COLUMN ] <column_name> SET STATISTICS <integer>
    ALTER [ COLUMN ] <column_name> SET ( <attribute_option> = <value> [, ... ] )
    ALTER [ COLUMN ] <column_name> RESET ( <attribute_option> [, ... ] )
    OWNER TO { <new_owner> | CURRENT_USER | SESSION_USER }
```

---

描述
-----

你可以运行 `ALTER MATERIALIZED VIEW` 来修改你所有的物化视图的辅助属性。

要重新指定物化视图的 Schema，你必须在新 Schema 上具有 `CREATE` 权限。

要更改物化视图的所有者，你必须直接或间接成为新拥有角色的成员。新的拥有角色必须在物化视图的 Schema 上具有 `CREATE` 权限。

`ALTER MATERIALIZED VIEW` 的语句子形式和支持的操作是 `ALTER TABLE` 的子集。详见 [ALTER TABLE](alter-table.md) 的描述。


---


参数
----------

- **`IF EXISTS`**

    当命令中包含 `IF EXISTS` 时，如果指定的视图不存在，系统不会报错，而是产生一条通知信息。

- *`<name>`*

    物化视图的名称，支持使用 Schema 名称进行限定。

- *`<action>`*

    要执行的动作。详见 [CREATE TABLE](alter-table.md#参数) 中的“参数”章节。

- *`<column_name>`*

    新的或已有列的名称。

- *`<new_column_name>`*

    列的新名称。

- *`<new_owner>`*

    物化视图的新所有者的 DW 用户名称。

- *`<new_name>`*

    物化视图的新名称。

- *`<new_schema>`*

    物化视图的新 Schema。

---

示例
--------

将物化视图 `generalmv` 重命名为 `general_mv`：

```sql
ALTER MATERIALIZED VIEW generalmv RENAME TO general_mv;
```
    
改变物化视图 `generalmv` 的所有者：

```sql
ALTER MATERIALIZED VIEW generalmv OWNER TO relyt;
```

---

SQL 标准兼容性
-------------

`ALTER MATERIALIZED VIEW` 是 Relyt 对 SQL 标准的扩展。