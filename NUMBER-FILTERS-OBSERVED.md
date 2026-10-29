# jq 数值筛选器观察

目标固定为 jq 1.8.2。上游 `src/builtin.jq` 定义 `isfinite` 为“当前值是 number 且 `isinfinite` 为 false”，并以此实现 `finites`；`normals` 使用 `isnormal`。本机 `/usr/bin/jq` 报告 1.8.2，构建来源未核实。

| 过滤器 | 保留行为 |
| --- | --- |
| `finites` | 保留数字且 `isinfinite` 为 false 的原值；拒绝非数字和正/负无穷。上游定义会把 NaN 也判为 finite，因 `isinfinite(NaN)` 为 false。 |
| `normals` | 保留 `isnormal` 为 true 的原值；拒绝非数字、正负零、次正规数、NaN 与无穷。 |

边界样本包括：普通正负数被两个筛选器保留；零只被 `finites` 保留；`1e-320` 被视为次正规数，只被 `finites` 保留；双精度最小正规数 `2.2250738585072014e-308` 被两者保留；`1e400` 被视为无穷并被两者拒绝。把非标准数值输入 token `NaN` 送入 jq 时，类型仍是 number；`finites` 输出 JSON `null`（NaN 的 JSON 表示），而 `normals` 无输出。输入 `Infinity` 和 `-Infinity` 被两者拒绝。

两个过滤器均不带参数；非数字输入产生零个结果，不报错，也不迭代容器。验收脚本覆盖上述固定样本、特殊输入 token、无参数边界及 100 个固定种子数值；本候选的 JSON 读取器须保留 NaN/Infinity 的内部数值类型，同时按 jq 观察到的方式序列化。由于本机 jq 构建的数学函数可用性有限，不能用未提供的 `exp`/`sqrt` 函数制造所有特殊值。完整 C 数学分类和跨平台边界仍未验证。

来源：固定提交 [`src/builtin.jq`](https://github.com/jqlang/jq/blob/34f7186b86743a083a589741b6cea95293524108/src/builtin.jq#L51-L58)。验收见 `standard-library-verification/tests/test_jq_finite_filters.py` 与 `ACCEPTANCE.md` 第 48 项。
