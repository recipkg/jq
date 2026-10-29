# jq 正则替换观察

## 合同

- `sub(regex; replacement)` 只替换首个非重叠匹配；第三参数 flags 可启用全局行为。
- `gsub(regex; replacement)` 总是全局替换。三参数形式按 jq 的字符串加法规则在 flags 末尾追加 `g`。
- pattern 生成器在 flags 生成器外层求值；输入必须是字符串，正则与标志沿用现有 Oniguruma Perl NG 支持范围。
- 每次匹配的替换过滤器以该匹配的命名捕获对象为输入。命名但未参与匹配的组值为 null；未命名组不进入该对象。
- 替换过滤器产生多个值时，按输出位置组成多份结果；连续匹配的第 n 个替换值追加到第 n 份结果。没有匹配，或所有替换过滤器都不产生值时，原输入作为结果。
- 替换值按 jq 加法规则拼接：字符串连接，null 不改变已有文本；其他类型在已测匹配场景中报错。没有匹配时不求值替换过滤器。
- 替换字符串支持有限 jq 字符串插值 `\\(expression)`，包括命名捕获、回退表达式、嵌套字符串、Unicode 和表达式生成器。它不代表通用 jq 字符串插值或完整表达式语法。
- 零宽匹配、Unicode、多标志、替换输出流、缺失捕获、类型/正则/标志错误均只以黑盒样本定义边界。

## 验证

`standard-library-verification/tests/test_jq_regex_replace.py` 有 8 个黑盒测试方法：首个/全局替换、命名捕获插值、嵌套插值、替换输出流位置合并、空输出、零宽及 Unicode、参数生成顺序、错误类型和 100 组固定种子差分。实现与本地报告 jq 1.8.2 的 `/usr/bin/jq` 对照通过；参考二进制构建来源未核实。

## 依据与边界

- [jq 1.8 正则表达式手册](https://jqlang.org/manual/v1.8/#regular-expressions)说明 `sub`、`gsub`、flags 和替换字符串插值。
- 上游包装实现见固定 jq 1.8.2 提交的 [src/builtin.jq](https://github.com/jqlang/jq/blob/34f7186b86743a083a589741b6cea95293524108/src/builtin.jq#L108-L125)。
- 候选仍是有限独立重建；Oniguruma 以外平台、其他库版本、完整正则语法、完整 jq 插值语法、资源边界及完整 jq 兼容性没有由这些测试证明。
