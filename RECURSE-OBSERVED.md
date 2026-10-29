# `recurse` 观察记录

目标 jq 1.8.2；行为参考固定版本 [jq 1.8 手册](https://jqlang.org/manual/v1.8/)及上游固定提交 [`src/builtin.jq`](https://github.com/jqlang/jq/blob/34f7186b86743a083a589741b6cea95293524108/src/builtin.jq#L34-L37)，并以本机 `/usr/bin/jq` 对照。参考二进制报告 jq 1.8.2，构建来源未核实。

## 已验证范围

- 无参数 `recurse` 先输出当前值，再按对象/数组迭代顺序深度优先输出子值；有限合同等价于 `recurse(.[]?)`。
- `recurse(f)` 对 f 产生的各个值继续递归，直至 f 不再产生值。
- `recurse(f; condition)` 只递归进入 condition 产生 truthy 结果的后继值；未通过条件的后继值不输出。
- `standard-library-verification/tests/test_jq_recurse.py` 有 4 个方法；默认递归差分覆盖 100 个固定种子 JSON 值。

示例：

```jq
{"a": 0, "b": [1]} | [recurse]
# [{"a":0,"b":[1]},0,[1],1]

2 | [recurse(. * .; . < 20)]
# [2,4,16]
```

无界递归可能产生无限输出；深度、栈/内存资源、多个后继与多个条件结果的全部组合、精确错误时序和平台差异仍未验证。本记录不是完整递归 API 规范。
