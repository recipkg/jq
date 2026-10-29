# jq 数学函数观察记录

本轮以固定上游 jq 1.8.2 的 [数学函数手册](https://jqlang.org/manual/v1.8/#math)及 [`src/libm.h`](https://github.com/jqlang/jq/blob/34f7186b86743a083a589741b6cea95293524108/src/libm.h) 为范围依据，并与本机 `/usr/bin/jq` 逐项对照。参考程序报告版本为 1.8.2；其构建来源未核实。手册列出 40 个单输入、19 个双输入和 1 个三输入 C 数学函数，并明确说明具体可用性取决于操作系统数学库。

此前已实现的 `ceil`、`floor`、`round`、`trunc` 仍由 [`ROUNDING-OBSERVED.md`](ROUNDING-OBSERVED.md)记录。本轮新增并测试以下 32 个函数：

| 类别 | 函数 |
| --- | --- |
| 反三角与反双曲 | `acos`、`acosh`、`asin`、`asinh`、`atan`、`atanh` |
| 三角与双曲 | `cos`、`cosh`、`sin`、`sinh`、`tan`、`tanh` |
| 指数、对数与根 | `cbrt`、`exp`、`exp10`、`exp2`、`expm1`、`log`、`log10`、`log1p`、`log2`、`logb`、`sqrt` |
| 其他 | `erf`、`erfc`、`fabs`、`gamma`、`lgamma`、`nearbyint`、`rint`、`significand`、`tgamma` |

加上此前四个取整函数，目前在本机可用的 C 数学库上覆盖手册列出的 40/40 个单输入函数。本轮实现了 `j0`、`j1`、`y0`、`y1` 四个 Bessel 函数，并保留宿主缺少对应符号时的显式运行时错误。另覆盖 17 个双过滤器参数函数、三参数 `fma`，以及 `frexp`、`modf` 的分解数组结果。

## Bessel 函数

- 当前 `libm.so.6` 提供 `j0`、`j1`、`y0`、`y1`；重建程序通过 `ctypes` 调用这些单输入 C 函数，jq 侧调用形式仍为作用于当前输入的零显式参数过滤器。
- `j0`、`j1` 对正负有限输入均与本机参考端对照；`y0`、`y1` 的数值样本限于正输入。已检查零输入：`j0(0)` 为 1，`j1(0)` 为 0，`y0(0)` 与 `y1(0)` 产生的负无穷由 jq 序列化为负最大有限双精度数；负输入的定义域 NaN 序列化为 `null`。
- 四项测试覆盖有限边界、零/负定义域、负零输出、非数字错误，以及每个函数 100 组固定种子对照。随机输入中 `j0`/`j1` 覆盖正负值，`y0`/`y1` 覆盖约 `1e-200` 至 `1e200` 的正值。
- jq 1.8 手册规定 C 数学函数的可用性取决于宿主库；当前平台四个符号都存在，不代表其他系统也提供它们。

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

实现使用 Python 3.11+ 标准库：大多数单输入函数使用 `math`，Bessel 函数与双/三输入函数通过 `ctypes` 调用宿主 C 数学库。它不加载本机 jq，但依赖宿主提供对应 C 符号；jq 与重建程序的系统数学库仍可能因平台或构建不同而产生差异。参考二进制来源也未核实。因此观察值只构成本机有限样本合同，不代表所有平台上的位级一致性、极端精度、精确错误文本或完整 jq 兼容性。

来源：固定 [jq 1.8 数学函数手册](https://jqlang.org/manual/v1.8/#math)、上游 [`src/libm.h`](https://github.com/jqlang/jq/blob/34f7186b86743a083a589741b6cea95293524108/src/libm.h) 和 [`src/builtin.c`](https://github.com/jqlang/jq/blob/34f7186b86743a083a589741b6cea95293524108/src/builtin.c)。本轮重建提交 [`jq-source 048c762`](https://github.com/recipkg/jq-source/tree/048c76260d5eb373f23cd8ccba54a5c9ac075cdb)，验证提交 [`standard-library-verification 25465a0`](https://github.com/recipkg/standard-library-verification/tree/25465a00d0215822170d4257e6466acac9d5a132)。新增 6 项黑盒测试通过；本机 `test_jq_math*.py` 共 10 项通过（20.458 秒）。`jq-workspace` 提交 `1590b89`（源码 `c9b4264`、配方 `431db44`）下完整 `test_jq_*.py` 回归 123 项全过（107.083 秒）；Recipkg 归档检查、双语 build/discover 与确定性打包也通过。详见 [`COVERAGE.md`](COVERAGE.md)。

本轮实现与差分记录：[`jq-source 898cbecc`](https://github.com/recipkg/jq-source/tree/898cbecc76d01ac70cdc5cdd1c19bce9cb042906)，测试为 [`standard-library-verification ef54cde9`](https://github.com/recipkg/standard-library-verification/tree/ef54cde9cb82c3a924fbafcb13b41a0619dc47e9)。四项 Bessel 测试通过，含每函数 100 组固定种子样本；数学过滤器测试合计 14 项通过（22.981 秒）。更新后的 `jq-workspace` 提交 `3e5928f`（源码 `898cbecc`、配方 `d0cd418`）下完整 `test_jq_*.py` 回归 127 项通过（110.443 秒）。Recipkg 打包/检查、英中双语 build/discover、两份构建提示词中的递归依赖上下文及双次打包字节一致均通过。参考端报告 jq 1.8.2，构建来源未核实；其他平台、极端精度及完整兼容性未验证。
