# jq 字符串过滤器观察

## 固定来源

上游为 jq 1.8.2，固定提交 `34f7186b86743a083a589741b6cea95293524108`。合同来自该提交的 [jq 1.8 手册](https://jqlang.org/manual/v1.8/)字符串过滤器章节；输出样本在本机报告 `jq-1.8.2` 的 `/usr/bin/jq` 上运行。该系统二进制的构建来源未核实，因此这些运行结果是有限本机对照，不等同于从固定提交构建的参考二进制。

## 本次实现与样本

- `startswith`、`endswith` 根据字符串前缀或后缀产生布尔值。
- `ltrimstr`、`rtrimstr` 各删除一个匹配前缀或后缀；`trimstr` 删除两端匹配的指定文本。空删除文本不改变输入。
- `ascii_downcase`、`ascii_upcase` 只转换 ASCII 字母，其他 Unicode 字符保留。
- 单参数字面 `split` 保留分隔符造成的中间和尾部空项；空字符串输入产生 `[]`。空分隔符按 Unicode 码点切分。
- `join` 处理数组元素或对象值，使用字符串分隔符。null 变成空串，布尔值使用小写文本，数字转为 jq 数字文本；对象和数组成员报错。
- 对非字符串主体/参数、非数组或对象的 `join` 主体及非字符串 join 分隔符，样本要求运行错误、stdout 为空；诊断文本未纳入跨构建兼容合同。

验证脚本 `standard-library-verification/tests/test_jq_strings.py` 的 5 个方法覆盖以上显式样本，并以固定随机种子对 120 个含 Unicode 的字符串、100 个混合数组和 100 个对象执行差分。针对空字符串 `split(",")` 的一处差异曾由参考端发现并修正：jq 返回 `[]`，Python 默认 `str.split` 会返回 `['']`。

正则搜索的 `test(regex)` 与 `test(regex; flags)` 已单独提取，见 [`REGEX-OBSERVED.md`](REGEX-OBSERVED.md)。正则切分和字符串替换仍属未实现范围。

## 尚未验证

此观察文档本身不包括两参数正则 `split`、`trim`/`ltrim`/`rtrim` 空白字符库、Unicode 大小写转换、任意数值格式、分隔符表达式的所有输出组合、完整错误信息、平台差异或完整 jq 字符串 API。正则 `test` 的有限范围另见 `REGEX-OBSERVED.md`。单项通过不代表这些行为已兼容。
