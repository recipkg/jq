# jq `match` 观察

## 固定来源

目标为 jq 1.8.2，上游固定提交 `34f7186b86743a083a589741b6cea95293524108`。语法与字段依据 [jq 1.8 正则表达式手册](https://jqlang.org/manual/v1.8/#regular-expressions)；包装函数和匹配对象构造参考固定提交的 [`src/builtin.jq`](https://github.com/jqlang/jq/blob/34f7186b86743a083a589741b6cea95293524108/src/builtin.jq) 与 [`src/builtin.c`](https://github.com/jqlang/jq/blob/34f7186b86743a083a589741b6cea95293524108/src/builtin.c)。

运行样本来自本机 `/usr/bin/jq`，该程序报告版本 1.8.2，构建来源未核实。独立实现使用宿主 Oniguruma Perl NG 库。

## 已观察合同

- `match(regex)` 与 `match(regex; flags)` 无 `g` 时最多输出第一个匹配对象；带 `g` 时按搜索顺序输出全部非重叠匹配。没有匹配时不产生输出。
- 每个匹配对象包含 `offset`、`length`、`string` 和 `captures`。偏移与长度按 UTF-8 码点计数。完整匹配从输入字符串中的相应位置截取。
- 每个捕获组都输出 `offset`、`length`、`string`、`name`。未命名组的 `name` 为 null。未参与匹配的可选组为 `{"offset":-1,"length":0,"string":null,"name":...}`。参与匹配但匹配空串的组保留其实际偏移，字符串为空。
- 单参数形式接受正则字符串或数组简写 `[regex, flags]`；仅提供正则时省略 flags。两参数形式的正则与 flags 按 jq 的生成器组合顺序求值。支持 `g/i/m/n/p/s/l/x`。
- 对零宽全局匹配，jq 把下一次搜索起点推进一个 UTF-8 字节。若起点落在多字节字符内部，匹配结果的码点偏移可能重复。例如在 `"aβc"` 上执行 `match("(?=.)"; "g")`，本机参考输出的 offsets 为 `0, 1, 2, 2`。这属于观察到的上游行为，本样本用于兼容而不是规范化偏移。
- 输入非字符串、正则或 flags 类型错误、无效修饰符及无效模式均显式失败。错误检查使用稳定片段，不要求所有构建的完整诊断文本相同。

## 验证与边界

`standard-library-verification/tests/test_jq_match.py` 覆盖匹配对象、命名/未命名及缺失捕获、全局匹配、数组简写、生成器顺序、Unicode 偏移、零宽全局行为、参数错误和 100 组固定种子差分。5 项聚焦测试通过；完整 `test_jq_*.py` 回归 137 项通过（234.289 秒），工作区 `jq-workspace 0abca2d` 固定重建源码 `22a667e` 和本配方 `5d9cbd4`。本机参考程序的精确构建来源未验证。

当前宿主提供 `libonig`。其他操作系统、Oniguruma 版本/ABI、超大输入和模式资源消耗、全部语法选项与完整 jq 兼容性尚未验证。独立 `capture`、`scan`、正则 `split`/`splits`、`sub`、`gsub` 不属于本轮实现。
