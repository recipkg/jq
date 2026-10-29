# `to_entries` / `from_entries` / `with_entries` 观察记录

目标固定为 jq 1.8.2。转换关系依据 [jq 1.8 手册](https://jqlang.org/manual/v1.8/)；定向样本与有限差分使用本机 `/usr/bin/jq`。二进制报告版本 1.8.2，但构建来源未核实。

## `to_entries`

- 对象产生 `{key, value}` 条目数组，键和值来自对象成员；本机样本保留输入对象迭代顺序。
- 数组产生 `{key, value}` 条目数组，其中 key 是从 0 开始的数组索引。
- 空对象和空数组均产生空数组。
- 字符串、数字和 null 在已测样本中报错。

## `from_entries`

- 把数组中的条目，或对象的值所组成的条目流，折叠为一个对象。
- 键按 `key`、`Key`、`name`、`Name` 的顺序选择空值/false 后备项；必须最终得到字符串。缺少有效键的样本报错。
- `value` 优先于 `Value`；两者都缺少时，结果值为 null。显式 `value: null` 保留 null，即使同时有非空 `Value`。
- 重复键由后出现的条目覆盖。空条目数组得到 `{}`。
- 测试覆盖了非对象条目、非字符串键及字符串顶层输入的失败路径；精确诊断文本没有作为稳定合同。

## `with_entries(f)`

按 jq 手册，它是 `to_entries | map(f) | from_entries` 的简写。已验证对条目使用 `f` 改名、修改值、筛除条目及恒等转换。过滤器无输出时相应条目被移除。

## 验证和边界

`standard-library-verification/tests/test_jq_entries.py` 有 4 个方法：对象/数组条目转换、别名/重复键与错误边界、条目更新和筛选，以及参考端差分。差分包含 100 个对象和 100 个数组，覆盖 `to_entries`、值更新和筛选。

本机 `/usr/bin/jq` 报告 jq 1.8.2；它没有从固定源码 checkout 编译，构建选项和来源未核实。深层/特殊输入、错误诊断与时序、极端资源消耗以及全部 jq 兼容性仍未验证；本记录是有限合同。
