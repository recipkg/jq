# `walk(f)` 观察记录

目标 jq 1.8.2。行为参考固定提交 [`src/builtin.jq`](https://github.com/jqlang/jq/blob/34f7186b86743a083a589741b6cea95293524108/src/builtin.jq#L204-L214) 和 [jq 1.8 手册](https://jqlang.org/manual/v1.8/)，并以本机 `/usr/bin/jq` 对照；该二进制报告 1.8.2，构建来源未核实。

## 已验证范围

- 对象值和数组元素先递归应用 `walk(f)`，之后对重建的对象或数组应用 `f`；标量直接应用 `f`。
- 数组样本验证了先处理内层数组、再对父数组排序的后序行为。
- 对象与嵌套数组中的数字可以递增，`f` 也能基于对象条件改变对象本身。
- 当数组元素上的 `f` 产生多个值时，观察到这些值按顺序展开到递归构造的数组中。
- `standard-library-verification/tests/test_jq_walk.py` 包含 4 个方法，其中固定种子差分覆盖 100 个嵌套 JSON 值。

示例：

```jq
[[4, 1, 7], [8, 5, 2], [3, 6, 9]]
| walk(if type == "array" then sort else . end)
# [[1,4,7],[2,5,8],[3,6,9]]

{"a": [1, {"b": 2}], "c": 3}
| walk(if type == "number" then . + 1 else . end)
# {"a":[2,{"b":3}],"c":4}
```

任意深度下的栈/内存行为、对象值变换产生零个或多个结果时的全部语义、错误时序、资源边界和平台差异仍未验证。本记录不代表完整 jq 兼容性。
