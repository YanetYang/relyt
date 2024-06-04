
# 参考手册


---

## 运算符类列表

| 访问方式 | 输入类型 | 运算符类 | 是否有默认值 | 说明 |
| :- | :- | :- | :- | :- |
| `vectors` | `vector` | `vector_l2_ops` | 否 | 平方欧氏距离。 |
| `vectors` | `vector` | `vector_dot_ops` | 否 | 负点积。 |
| `vectors` | `vector` | `vector_cos_ops` | 否 | 余弦距离。 |
| `vectors` | `vecf16` | `vecf16_l2_ops` | 否 | 平方欧氏距离。 |
| `vectors` | `vecf16` | `vecf16_dot_ops` | 否 | 负点积。 |
| `vectors` | `vecf16` | `vecf16_cos_ops` | 否 | 余弦距离。 |

<br/>

---

## 索引选项

Vector DPS 使用 [TOML 语法](https://toml.io/en/v1.0.0) 来表达索引的配置。以下是配置中每个键所代表的含义：

| 键 | 类型 | 说明 |
| :- | :- | :- |
| `segment` | `table` | 分段（Segment）选项。 |
| `optimizing` | `table` | 后台优化选项。 |
| `indexing` | `table` | 用于索引创建的算法。 |

<br/>

---

### 表 `segment` 支持的选项

| 键 | 类型 | 取值范围 | 默认值 | 说明 |
| :- | :- | :- | :- | :- |
| `max_growing_segment_size` | `integer` | [1, 4_000_000_000] | `20_000` | 未创建索引的向量的最大大小。 |
| `max_sealed_segment_size` | `integer` | [1, 4_000_000_000] | `1_000_000` | 用于索引创建的向量的最大大小。 |

<br/>

---

### 表 `optimizing` 支持的选项


| 键 | 类型 | 取值范围 | 默认值 | 说明 |
| :- | :- | :- | :- | :- |
| `optimizing_threads` | `integer` | [1, 65535] | Core 的数量的平方根 | 索引的最大线程数。 |
| `sealing_secs` | `integer` | [1, 60] | `60` | 详见表格底部说明。 |
| `sealing_size` | `integer` | [1, 4_000_000_000] | `1` | 详见表格底部说明。 |

<br/>

:::info 说明
如果写入段大于 `sealing_size`，且在 `sealing_secs` 秒内不接受新数据，该写入段将被转换为封闭段。
:::

<br/>

### 表 `indexing` 支持的选项

| 键 | 类型 | 说明 |
| :- | :- | :- |
| `flat` | `table` | 如果设置了该表，索引将使用暴力算法。 |
| `ivf` | `table` | 如果设置了该表，索引将试用 IVF 算法。 |
| `hnsw` | `table` | 如果设置了该表，索引将试用 HNSW 算法。|

<br/>

### 表 `flat` 支持的选项

| 键 | 类型 | 说明 |
| :- | :- | :- |
| `quantization` | `table` | 用于量化的算法。 |

<br/>

### 表 `ivf` 支持的选项

| 键 | 类型 | 取值范围 | 默认值 | 说明 |
| :- | :- | :- | :- | :- |
| `nlist` | `integer` | [1, 1_000_000] | `1000` | 聚簇单元的数量。 |
| `nsample` | `integer` | [1, 1_000_000] | `65536` | K-Means 聚簇的样本数量。 |
| `least_iterations` | `integer` | [1, 1_000_000] | `16` | K-Means 聚簇的最小迭代次数。 |
| `iterations` | `integer` | [1, 1_000_000] | `500` | K-Means 聚簇的最大迭代次数。 |
| `quantization` | `table` | N/A | N/A | 使用的量化算法。 |

<br/>

### 表 `hnsw` 支持的选项

| 键 | 类型 | 取值范围 | 默认值 | 说明 |
| :- | :- | :- | :- | :- |
| `m` | `integer` | [4, 128] | `12` | 节点的最大度数。 |
| `ef_construction` | `integer` | [10, 2000] | `300` | 构建中的搜索范围。 |
| `quantization` | `table` | N/A | N/A | 使用的量化算法。 |

<br/>


### 表 `quantization` 支持的选项

| 键 | 类型  | 说明 |
| :- | :- | :- |
| `trivial` | `table` | 如果设置了该表，不使用量化。 |
| `scalar` | `table` | 如果设置了该表，使用标量量化。 |
| `product` | `table` | 如果设置了该表，使用乘积量化。 |

<br/>

### 表 `product` 支持的选项

| 键 | 类型 | 取值范围 | 默认值 | 说明 |
| :- | :- | :- | :- | :- |
| `sample` | `integer` | [1, 1_000_000] | `65535`| 用于量化的样本。 |
| `ratio` | `enum` | "x4", "x8", "x16", "x32", "x64" | `"x4"` | 量化的压缩比。 |

<br/>

:::info 说明
压缩比指定了向量压缩后的空间占用与向量压缩钱的空间占用的比例。例如，`"x4"` 表示 1/4。如果索引的列的类型是 `vecf16`，那么 `"x4"` 则表示 1/2。
:::

---
## 搜索选项

搜索选项通过 [PostgreSQL 的全局统一配置 (Grand Unified Configuration, GUC)](https://www.postgresql.org/docs/current/config-setting.html) 来指定。

<br/>

### `ivf` 支持的选项

如下选项仅在索引使用 IVF 算法时有效。

| 键 | 类型 | 取值范围 | 默认值 | 说明 |
| :- | :- | :- | :- | :- |
| `vectors.ivf_nprobe` | `integer` | [1, 1_000_000] | `10`| 需要扫描的列表数量。 |

<br/>

### `hnsw` 支持的选项

如下选项仅在索引使用 HNSW 算法时有效。

| 键 | 类型 | 取值范围 | 默认值 | 说明 |
| :- | :- | :- | :- | :- |
| `vectors.hnsw_ef_search` | `integer` | [1, 65535] | `100`| HNSW 算法搜索的候选结果集大小。 |

<br/>

### 其他选项

Vector DPS 提供如下针对搜索模式的搜索选项：

| 键 | 类型 | 取值范围 | 默认值 | 说明 |
| :- | :- | :- | :- | :- |
| `vectors.search_mode` | `enum` | `"basic"`, `"vbase"` | `"vbase"` | 搜索模式。 <br/>更多信息，请参考 [使用指南](use.md#向量搜索)。 |
| `vectors.pgvector_compatibility` | `boolean` |   | `off` | 是否开启兼容。 <br/>更多信息，请参考 [使用指南](use.md#与-pgvector-的兼容性)。 |
| `vectors.enable_index` | `boolean` |   | `on` | 是否生产索引查询的执行计划。 |

<br/>