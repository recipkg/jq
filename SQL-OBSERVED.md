# SQL 风格流操作观察

目标版本为 jq `1.8.2`。本页仅记录本机黑盒样本和有限独立重建合同。`/usr/bin/jq --version` 报告 `jq-1.8.2`，但该二进制的构建来源未核实。上游定义见 [jq 1.8 手册 SQL-Style Operators](https://jqlang.org/manual/v1.8/)。

## `INDEX(stream; index_expression)`

对 `stream` 产生的每个值计算索引表达式，并将对应值写入一个对象。索引表达式可以产生多个键；重复键由后出现的值覆盖。样本观察到 null、布尔、数字、数组和对象索引值都转换为对象键字符串，例如 `null`、`true`、`7`、`[1,2]`、`{"x":1}`。空流产生 `{}`。

## `JOIN`

| 形式 | 已观察行为 |
| --- | --- |
| `JOIN($idx; stream; idx_expr)` | 对 stream 的每个值产生 `[值, 索引命中值]` |
| `JOIN($idx; stream; idx_expr; join_expr)` | 将上述二元素数组作为 join_expr 的输入 |
| `JOIN($idx; idx_expr)` | 将当前数组元素作为流，收集所有二元素数组为一个数组 |

查无索引键时，二元素数组的第二项为 null。索引值使用和 `INDEX` 相同的字符串键形式。样本包含重复索引键、数值键、匹配/未匹配行和将两侧对象合并的 join 表达式。

## 大写 `IN`

- `IN(stream)`：若当前输入等于 stream 的任一输出则为 true，否则为 false。
- `IN(source; stream)`：若 source 的任一输出出现在 stream 中则为 true，否则为 false。

这与小写对象/数组成员过滤器 `in` 是不同的调用形式。

## 重建验收与边界

用例位于 [`standard-library-verification/tests/test_jq_sql_operators.py`](https://github.com/recipkg/standard-library-verification/blob/main/tests/test_jq_sql_operators.py)。5 个测试方法覆盖索引键序列化、重复键覆盖、多键表达式、`JOIN` 三种形式、缺失匹配、`IN` 单流/双流及 100 组固定种子索引联结差分。参考端为本机 `/usr/bin/jq`，报告 jq 1.8.2，构建来源未核实。

更广泛的流生成、函数副作用/错误顺序、极大数据集、诊断文本、平台和完整 jq 兼容性不在本轮已验证范围内；独立实现状态见 [jq-source/STATUS.md](https://github.com/recipkg/jq-source/blob/main/STATUS.md)。
