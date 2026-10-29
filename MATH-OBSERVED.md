# jq 数学函数观察记录

本轮以固定上游 jq 1.8.2 的 [数学函数手册](https://jqlang.org/manual/v1.8/#math)及 [`src/libm.h`](https://github.com/jqlang/jq/blob/34f7186b86743a083a589741b6cea95293524108/src/libm.h) 为范围依据，并与本机 `/usr/bin/jq` 逐项对照。参考程序报告版本为 1.8.2；其构建来源未核实。手册列出 40 个单输入、19 个双输入和 1 个三输入 C 数学函数，并明确说明具体可用性取决于操作系统数学库。

此前已实现的 `ceil`、`floor`、`round`、`trunc` 仍由 [`ROUNDING-OBSERVED.md`](ROUNDING-OBSERVED.md)记录。本轮新增并测试以下 32 个函数：

| 类别 | 函数 |
| --- | --- |
| 反三角与反双曲 | `acos`、`acosh`、`asin`、`asinh`、`atan`、`atanh` |
| 三角与双曲 | `cos`、`cosh`、`sin`、`sinh`、`tan`、`tanh` |
| 指数、对数与根 | `cbrt`、`exp`、`exp10`、`exp2`、`expm1`、`log`、`log10`、`log1p`、`log2`、`logb`、`sqrt` |
| 其他 | `erf`、`erfc`、`fabs`、`gamma`、`lgamma`、`nearbyint`、`rint`、`significand`、`tgamma` |

加上此前四个取整函数，目前覆盖手册列出的 36/40 个单输入函数。`j0`、`j1`、`y0`、`y1` 四个 Bessel 函数仍未实现。本轮另外覆盖 17 个双过滤器参数函数、三参数 `fma`，以及 `frexp`、`modf` 的分解数组结果。

## 观察到的输入与边界

- 函数均接收一个 JSON 数字；非数字输入（null、布尔值、字符串、数组、对象）报 `number required`。
- 普通有限输入与参考端逐函数比较。测试包含每个函数 100 个固定种子的定义域内数值；`gamma`、`lgamma`、`tgamma` 因系统数学库产生少量浮点末位差异，使用很小的相对误差容限，其余差分要求数值一致。
- `nearbyint` 和 `rint` 在当前环境的 ties-to-even 舍入模式下将半值舍入到最近偶数；舍入结果为零时保留输入的负号。更改进程浮点舍入模式的行为未测试。
- `significand` 对有限非零数返回带符号的二进制有效数部分；零保持为零。
- `gamma` 在当前参考环境中表现为 `lgamma`（返回伽马函数绝对值的自然对数）；`tgamma` 对应通常的伽马函数值。此行为可能受 jq 构建时链接的系统数学库影响。
- 数学域错误产生 NaN，jq 将其 JSON 序列化为 `null`。已核对：`acos(2)`、`acosh(0.5)`、`asin(2)`、`sqrt(-1)`。
- 奇点和溢出产生的无穷值由 jq 序列化为带符号的最大有限双精度数 `±1.7976931348623157e+308`。已核对：`atanh(±1)`、`log(0)`、`log10(0)`、`log2(0)`、`log1p(-1)`、`logb(0)`、`exp(1000)`、`exp2(2000)`、`expm1(1000)`、`cosh(1000)`、`sinh(-1000)` 及 `gamma(0)`、`lgamma(0)`、`tgamma(0)`。

## 双输入、三输入与 C 输出参数

- 手册中的 17 个普通双输入过滤器为 `atan2`、`copysign`、`drem`、`fdim`、`fmax`、`fmin`、`fmod`、`hypot`、`jn`、`ldexp`、`nextafter`、`nexttoward`、`pow`、`remainder`、`scalb`、`scalbln`、`yn`；三输入过滤器为 `fma`。逐项与本机参考程序有限数值对照。
- 手册把 `frexp`、`modf` 列在双输入 C 函数中；上游 `src/libm.h` 的 jq 包装实际只接收当前输入，并把 C 输出参数作为 `[函数返回值, 输出参数]` 返回。观察到 `8 | frexp` 为 `[0.5,4]`，`3.25 | modf` 为 `[0.25,3]`。
- `jn`、`yn` 的阶数，`ldexp` 的指数，以及 `scalbln` 的指数在 C 原型中是整数类型；jq 传入的 double 按 C 转换规则截断。小数阶数/指数样本与参考端一致。
- 对每个普通双/三输入函数比较 100 组固定种子数值。另检查零、定义域边界、负零、每个参数位置的非数字错误，并验证 `pow((1,2);(3,4))` 与三参数 `fma` 的生成器输出次序。多参数表达式中，右侧参数生成器在外层，左侧参数生成器在内层。
- 遇到零除或无效定义域时，C 数学库产生的 NaN 由 jq 序列化为 `null`；有限样本、类型错误诊断和输出结果已做黑盒对照。

## 重建边界

实现使用 Python 3.11+ 标准库：单输入函数使用 `math`，双/三输入函数通过 `ctypes` 调用宿主 C 数学库。它不加载本机 jq，但依赖宿主提供对应 C 符号；jq 与重建程序的系统数学库仍可能因平台或构建不同而产生差异。参考二进制来源也未核实。因此观察值只构成本机有限样本合同，不代表所有平台上的位级一致性、极端精度、精确错误文本或完整 jq 兼容性。

来源：固定 [jq 1.8 数学函数手册](https://jqlang.org/manual/v1.8/#math)、上游 [`src/libm.h`](https://github.com/jqlang/jq/blob/34f7186b86743a083a589741b6cea95293524108/src/libm.h) 和 [`src/builtin.c`](https://github.com/jqlang/jq/blob/34f7186b86743a083a589741b6cea95293524108/src/builtin.c)。本轮重建提交 [`jq-source 048c762`](https://github.com/recipkg/jq-source/tree/048c76260d5eb373f23cd8ccba54a5c9ac075cdb)，验证提交 [`standard-library-verification 25465a0`](https://github.com/recipkg/standard-library-verification/tree/25465a00d0215822170d4257e6466acac9d5a132)。新增 6 项黑盒测试通过；本机 `test_jq_math*.py` 共 10 项通过（20.458 秒）。完整 `test_jq_*.py` 回归在精确工作区 pin 下 123 项全过（122.394 秒）；Recipkg 归档检查、双语 build/discover 与确定性打包也通过。详见 [`COVERAGE.md`](COVERAGE.md)。
