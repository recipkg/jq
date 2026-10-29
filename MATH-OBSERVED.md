# jq 单参数数学函数观察记录

本轮以固定上游 jq 1.8.2 的 [数学函数手册](https://jqlang.org/manual/v1.8/#math)为范围依据，并与本机 `/usr/bin/jq` 逐项对照。参考程序报告版本为 1.8.2；其构建来源未核实。上游手册列出 40 个单参数 C 数学函数，并明确说明具体可用性取决于操作系统数学库。

此前已实现的 `ceil`、`floor`、`round`、`trunc` 仍由 [`ROUNDING-OBSERVED.md`](ROUNDING-OBSERVED.md)记录。本轮新增并测试以下 32 个函数：

| 类别 | 函数 |
| --- | --- |
| 反三角与反双曲 | `acos`、`acosh`、`asin`、`asinh`、`atan`、`atanh` |
| 三角与双曲 | `cos`、`cosh`、`sin`、`sinh`、`tan`、`tanh` |
| 指数、对数与根 | `cbrt`、`exp`、`exp10`、`exp2`、`expm1`、`log`、`log10`、`log1p`、`log2`、`logb`、`sqrt` |
| 其他 | `erf`、`erfc`、`fabs`、`gamma`、`lgamma`、`nearbyint`、`rint`、`significand`、`tgamma` |

加上此前四个取整函数，目前覆盖手册列出的 36/40 个单参数函数。`j0`、`j1`、`y0`、`y1` 四个 Bessel 函数没有在本次重建中实现；需要额外观察的双参数和三参数数学函数也未纳入。

## 观察到的输入与边界

- 函数均接收一个 JSON 数字；非数字输入（null、布尔值、字符串、数组、对象）报 `number required`。
- 普通有限输入与参考端逐函数比较。测试包含每个函数 100 个固定种子的定义域内数值；`gamma`、`lgamma`、`tgamma` 因系统数学库产生少量浮点末位差异，使用很小的相对误差容限，其余差分要求数值一致。
- `nearbyint` 和 `rint` 在当前环境的 ties-to-even 舍入模式下将半值舍入到最近偶数；舍入结果为零时保留输入的负号。更改进程浮点舍入模式的行为未测试。
- `significand` 对有限非零数返回带符号的二进制有效数部分；零保持为零。
- `gamma` 在当前参考环境中表现为 `lgamma`（返回伽马函数绝对值的自然对数）；`tgamma` 对应通常的伽马函数值。此行为可能受 jq 构建时链接的系统数学库影响。
- 数学域错误产生 NaN，jq 将其 JSON 序列化为 `null`。已核对：`acos(2)`、`acosh(0.5)`、`asin(2)`、`sqrt(-1)`。
- 奇点和溢出产生的无穷值由 jq 序列化为带符号的最大有限双精度数 `±1.7976931348623157e+308`。已核对：`atanh(±1)`、`log(0)`、`log10(0)`、`log2(0)`、`log1p(-1)`、`logb(0)`、`exp(1000)`、`exp2(2000)`、`expm1(1000)`、`cosh(1000)`、`sinh(-1000)` 及 `gamma(0)`、`lgamma(0)`、`tgamma(0)`。

## 重建边界

实现使用 Python 3.11+ `math` 库和等价标量运算，不加载本机 jq，也不依赖 Linux 专有接口。函数结果仍可能随 Python、C 运行库和平台变化；参考二进制来源也未核实。因此观察值只构成本机有限样本合同，不代表所有平台上的位级一致性、极端精度、精确错误文本或完整 jq 兼容性。

来源：固定 [jq 1.8 上游 `builtin.jq`](https://github.com/jqlang/jq/blob/34f7186b86743a083a589741b6cea95293524108/src/builtin.jq) 与 [jq 1.8 数学函数手册](https://jqlang.org/manual/v1.8/#math)。重建提交 [`jq-source b7c3b67`](https://github.com/recipkg/jq-source/tree/b7c3b67f5b57631c5adbcd5e288ad4588483a988)，差分测试提交 [`standard-library-verification 89d925f`](https://github.com/recipkg/standard-library-verification/tree/89d925fab7603516a93993817e2963e03b36461f)。四项黑盒测试通过，完整 `test_jq_*.py` 回归 117 项通过（86.261 秒）；详细记录见 [`COVERAGE.md`](COVERAGE.md)。
