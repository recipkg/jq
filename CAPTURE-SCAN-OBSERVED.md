# jq `capture` 与 `scan` 观察

## 固定来源

目标为 jq 1.8.2，上游固定提交 `34f7186b86743a083a589741b6cea95293524108`。语法依据 [jq 1.8 正则表达式手册](https://jqlang.org/manual/v1.8/#regular-expressions)；包装函数参考该提交的 [`src/builtin.jq`](https://github.com/jqlang/jq/blob/34f7186b86743a083a589741b6cea95293524108/src/builtin.jq) 与 [`src/builtin.c`](https://github.com/jqlang/jq/blob/34f7186b86743a083a589741b6cea95293524108/src/builtin.c)。运行样本来自本机 `/usr/bin/jq`，报告 jq 1.8.2，精确构建来源未核实。

## 已观察合同

- `capture(regex)` 使用首个匹配；若无匹配则不产生输出。结果对象只包含有名称的捕获组。未参与匹配的可选命名组保留键，值为 null；未命名组不进入对象。
- `capture(regex; flags)` 将 flags 传给 `match`。`g` 产生按输入顺序排列的多个捕获对象。单参数形式接受正则字符串或 `[regex, flags]` 数组简写。
- `scan(regex)` 与 `scan(regex; flags)` 总是全局扫描。没有捕获组时，每个结果是完整匹配字符串；有捕获组时，每个结果是按组声明顺序排列的数组，未参与匹配的组对应 null。即使捕获组有名称，`scan` 仍输出数组，不输出对象。
- `scan` 的单参数形式只接受正则字符串，不支持 `capture`/`match` 的数组简写。两参数形式通过把 `g` 加在 flags 前来强制全局；flags 为 null 时按 jq 的加法规则等同于仅使用 `g`，其他非字符串值会报错。输入必须是字符串。
- 两参数形式保留 jq 观察到的参数生成顺序：flags 流在外、正则流在内。无匹配时不产生结果。空匹配与 `n` 标志按 Oniguruma/jq 的实际规则处理；有限样本见验收测试。
- 本轮比较了类型错误中的稳定诊断片段，不要求不同 jq 构建输出完全相同的错误文本。

## 验证与边界

`standard-library-verification/tests/test_jq_capture_scan.py` 覆盖命名、未命名及未匹配组，零/多个结果，数组简写、全局和生成器顺序、空匹配标志、输入/参数错误，以及 100 组固定种子差分（`capture` 与 `scan` 各 50 组）。五项聚焦测试在 verification 提交 [`a57a953`](https://github.com/recipkg/standard-library-verification/tree/a57a95377baf5a1d958064e245ff9acc6ed7de4e) 通过。完整 `test_jq_*.py` 回归在源码 [`d74c061`](https://github.com/recipkg/jq-source/tree/d74c06189c7c8c9634ce6d89065c350e47690d13) 与 verification [`ea2117e`](https://github.com/recipkg/standard-library-verification/tree/ea2117e) 下通过 142 项（180.509 秒）。参考二进制构建来源未核实。

重建仍依赖宿主 Oniguruma。其他操作系统和库版本、极端输入/模式的资源行为、完整 jq 正则语法及完整 jq 兼容性未验证。`split`、`splits`、`sub`、`gsub` 不属于本轮合同。
