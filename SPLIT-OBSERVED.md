# jq 正则 `split` 与 `splits` 观察

## 固定来源

目标为 jq 1.8.2。参数形式与输出规则依据 [jq 1.8 正则表达式手册](https://jqlang.org/manual/v1.8/#regular-expressions)；实现包装参考固定上游提交 `34f7186b86743a083a589741b6cea95293524108` 的 [`src/builtin.jq`](https://github.com/jqlang/jq/blob/34f7186b86743a083a589741b6cea95293524108/src/builtin.jq)，引擎和标志依据 [`src/builtin.c`](https://github.com/jqlang/jq/blob/34f7186b86743a083a589741b6cea95293524108/src/builtin.c)。运行对照来自本机 `/usr/bin/jq`，报告 jq 1.8.2，构建来源未核实。

## 已观察合同

- `split(str)` 保持历史字面分隔符语义；单参数时不会把字符串解释成正则。
- `split(regex; flags)` 按正则匹配分段并返回数组；等价于收集 `splits(regex; flags)` 的流结果。`splits(regex)` 和 `splits(regex; flags)` 则逐段输出字符串流。
- 每个非重叠匹配作为分隔符。开头、结尾或相邻匹配会产生空分段；无匹配时返回原字符串作为一个分段，空输入也保留 jq 观察到的空段。
- 分隔模式中的捕获组不加入结果。零宽匹配沿用 jq `match` 的一字节推进与码点偏移行为；多字节输入的有限结果由差分样本固定，不将偏移重新规范化。
- 正则 flags 在字符串 `"g"` 前拼接以强制全局扫描；flags 为 null 时按无额外 flags 处理。其他非字符串 flags、无效标志和无效模式失败。输入与正则都要求字符串。
- 双参数 forms 的正则生成器在 flags 生成器外层，flags 在内层。两个生成器均求值时的输出顺序也有黑盒用例。

## 验证与边界

`standard-library-verification/tests/test_jq_regex_split.py` 覆盖字面/正则 `split`、流式 `splits`、Unicode、捕获组、零宽与空分隔段、flags、生成器顺序、错误边界，以及 100 组固定种子差分（两种形式各 50 组）。5 项聚焦测试通过。全套 `test_jq_*.py` 在 [jq-workspace 0be11ff](https://github.com/recipkg/jq-workspace/tree/0be11ff2b73dd4f429e8bb52d4371ab8a29d2ec8) 通过 147 项（286.480 秒），固定源码 [jq-source a47cf08](https://github.com/recipkg/jq-source/tree/a47cf08459df80986425949aebd82484917a8122) 与配方 `jq@0.1.54`。该版本归档检查、双次打包和英中 build/discover 通过，47 个 payload 文件均为 Markdown，递归 quality 内容保留。参考端为报告 jq 1.8.2 的本机二进制，构建来源未核实。本实现依赖宿主 Oniguruma；其他平台/版本、超大模式资源、复杂错误时序及完整 jq 兼容性仍未验证。替换过滤器 `sub`/`gsub` 不属于本项。
