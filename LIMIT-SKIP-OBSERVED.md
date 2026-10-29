# `limit` 与 `skip` 的观察记录

## 固定来源

目标版本为 jq 1.8.2，来源固定为提交 [`34f7186b86743a083a589741b6cea95293524108`](https://github.com/jqlang/jq/tree/34f7186b86743a083a589741b6cea95293524108)。[jq 1.8 手册](https://jqlang.org/manual/v1.8/#generators-and-iterators)说明：`limit(n; expr)` 从 `expr` 提取最多 n 个输出；`skip(n; expr)` 跳过 `expr` 的前 n 个输出。固定版本 [`src/builtin.jq`](https://github.com/jqlang/jq/blob/34f7186b86743a083a589741b6cea95293524108/src/builtin.jq#L909-L922)分别以 `foreach` 消费流，并对负数报错。

## 本轮验证到的有限行为

- `limit` 保留表达式流的前 n 项，`skip` 丢弃前 n 项后继续输出；都按原顺序处理多输出流。
- n 为 0 时，`limit` 不产生输出，`skip` 原样输出表达式流；n 表达式不产生值时，两者均不产生输出。
- 对本机 jq 1.8.2 参考端测得的小数样本，`limit(1.5; expr)` 输出前两项，`skip(1.5; expr)` 跳过一项。手册只描述 n 个输出，没有规定小数边界；这里记录的是实测结果。
- n 表达式产生多个值时，每个 n 值分别处理同一个 expr 流，结果按 n 值顺序连接。例如 `[limit(1, 2; range(0; 3))]` 得到 `[0,0,1]`。
- `limit` 可以在无限表达式流上提前停止。例如 `limit(5; recurse(. + 1))` 在输入 `0` 时输出 `0` 到 `4`。将有限 `limit` 套在 `skip` 外层，也能从无限流中取到后续有限项。
- 负数计数和已测的字符串/布尔计数都会失败；本轮只验证失败状态，不约定所有输入类型的精确诊断或错误码。

## 可复现样本

参考端 `/usr/bin/jq` 报告 jq 1.8.2；二进制构建来源未核实。差分样本覆盖 100 组固定种子的数组与计数，并含零、小数、超出流长度、多计数值、空计数流、无效类型以及有限选择无限递归流的示例。对应测试为 [`test_jq_limit_skip.py`](https://github.com/recipkg/standard-library-verification/blob/13f01983f7b2c3009b9b14e13256cdf466cfde0c/tests/test_jq_limit_skip.py)。这些有限样本不代表完整 jq 兼容性。

## 尚未覆盖

尚未系统验证大数计数、异常数值、错误发生时的已输出结果保留、计数流与表达式流的复杂多值交错、深度/内存边界、其他平台及精确诊断。只将以上列出的有限合同用于重建验收。
