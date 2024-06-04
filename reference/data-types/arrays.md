
# 数组

Relyt 支持将表中的列定义为可变长度多维数组。数组内的元素可以是任何内置的或用户自定义的基本类型、枚举类型、复合类型、范围类型、或者域。

---

## 声明数组类型

为了更好的理解数组类型，我们先创建一个表：

```sql
CREATE TABLE test (
  f0 int[],
  f1 int[3],
  f2 int[3][],
  f3 int ARRAY,
  f4 int ARRAY[3]
) USING pg_zdb;

INSERT INTO test VALUES ('{1}', '{1}', '{{1}}', '{1}', '{1}'), ('{2,3}', '{2,3}', '{{2,3}}', '{2,3}', '{2,3}');
```

如上述示例所示，Relyt 支持两种数组声明方式：

- 在数组元素的数据类型名称后面加上方括号（`[]`），例如示例中的 `int[]`。

- 也可以通过数据类型名后跟 `ARRAY` 关键字来命名，例如示例中的 `int ARRAY`。

<br/>

### 注意事项

在声明数组时，需要注意：

- 使用 `ARRAY` 关键字时，如果关键字后跟 `[]`，则 `[]` 中必须有具体数字。

- 使用 `ARRAY` 关键字声明数组类型时，只支持一维数组。

- 方括号（`[]`）中的数字表示当前维度中，数组的长度，不过数组长度只做展示用，实际写入长度不受该数字影响。


在多维数组中，同一条记录中的每一维中的元素必须有一样的长度，不同记录间，相同维度的元素可以有不同的长度。

Relyt 支持的数组最大维度为 6。如建表时，指定的维度大于 6，建表不会报错。但是在数据写入过程中，会报错。如下为一个示例：

```sql
-- f0 维度为 7，建表成功，不报错
CREATE TABLE test (
  f0 int[][][][][][][] 
) USING pg_zdb;

-- 写入时报错
INSERT INTO test VALUES ('{{{{{{{1}}}}}}}');
ERROR:  number of array dimensions (7) exceeds the maximum allowed (6)
LINE 1: INSERT INTO test VALUES ('{{{{{{{1}}}}}}}');
```

<br/>

### 与 PostgreSQL 的差异


在 PostgreSQL 中的数组长度只做展示用，实际写入的长度不受其影响。而 Relyt 的中要求同一列中的所有行的维度必须与 Schema 中定义的一致。例如：

```sql
CREATE TABLE test (
  f0 int[],       -- 维度为 1
  f1 int[][][],   -- 维度为 3
  f2 int ARRAY,   -- 维度为 1
  f3 int ARRAY[3] -- 维度为 1
) USING pg_zdb;

-- 维度一致，写入成功
INSERT INTO test VALUES ('{1}', '{{{1}}}', '{1}', '{1}');

-- 维度不一致，写入失败
INSERT INTO test VALUES ('{{1}}', '{{1}}', '{{1}}', '{{1}}');
```

---

## 数组值输入

将数组值写成字面常量时，需要用大括号将元素值括起来，并用英文逗号（`,`）分隔。你可以在任何元素值周围加上双引号（`" "`），如果某元素值本身包含英文逗号（`,`）或大括号（`{ }`），则必须使用双引号进行包裹。如下为数组常量的一般格式示例：

```sql
'{<val1>, <val2>, ...}'
```

其中，每一个 *`<val>`* 可以是一个常量也可以是一个子数组。如下是一个数组示例：

```sql
'{{1,2,3},{4,5,6},{7,8,9}}'
```

如上示例数组是一个二维的 3 x 3 数组，包含 3 个整数子数组。


如需设置一个数组元素为 NULL，在该元素值处写 `NULL`（任何 `NULL` 的大写或小写变体都有效）。如果需要将该元素设置为字符串值 `NULL`，则需要将 `NULL` 用双引号包裹起来，即将元素设置为 `"NULL"`。

多维数组中，在同一条记录中，每一维中的元素必须有一样的长度，不同记录间，相同维度的元素可以有不同的长度：

```sql
CREATE TABLE test (
  f0 int[],
  f1 int[3],
  f2 int[3][],
  f3 int ARRAY,
  f4 int ARRAY[3]
) USING pg_zdb;

INSERT INTO test VALUES (
  '{1,2,3}',             -- f0: 一维数组
  '{1,2,3}',             -- f1: 一维数组，大小为3
  '{{1,2},{3,4,5}}',     -- f2: 二维数组，内部数组维度不匹配
  '{1,2,3,4}',           -- f3: 一维数组
  '{1,2,3}'              -- f4: 一维数组，大小为3
);

ERROR:  multidimensional arrays must have array expressions with matching dimensions
```

数组字段在指定 `NOT NULL` 时，只对最外层的一维生效，内层不生效：

```sql
CREATE TABLE test (a int[][] not null);

INSERT INTO test VALUES (NULL);

ERROR:  null value in column "a" violates not-null constraint  (seg0 192.168.215.2:7005 pid=31718)
DETAIL:  Failing row contains (null).

--重新插入数据
INSERT INTO test VALUES ('{{0}, {NULL}}');
INSERT 0 1
SELECT * FROM test;


      a
--------------
 {{0},{NULL}}
(1 row)
```
