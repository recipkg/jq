# jq Unicode 字符串转换观察

## 固定来源

上游为 jq 1.8.2，源码固定提交 `34f7186b86743a083a589741b6cea95293524108`。`utf8bytelength`、`explode` 和 `implode` 的目标合同参考该版本的 [jq 1.8 手册](https://jqlang.org/manual/v1.8/)。对照使用本机报告 `jq-1.8.2` 的 `/usr/bin/jq`，其精确构建来源未核实。

## 已实现的有限范围

- `utf8bytelength` 对字符串返回 UTF-8 编码字节数；`explode` 返回 Unicode 码点数组；`explode | implode` 对合法 Unicode 字符串往返。
- `implode` 接受数值码点并将小数向零截断；负值、超出 Unicode 范围的值和代理码点输出 U+FFFD。非数组输入或非数字成员报错。
- `standard-library-verification/tests/test_jq_unicode.py` 有 4 个方法。固定种子对 100 个字符串比较字节长度、码点与往返结果；另对 100 组合法码点数组比较 `implode`。固定样本覆盖 ASCII、多字节字符、组合字符、NUL、补充平面码点和非法码点替换。
- 19 项 jq 主契约测试及 48 项 `test_jq_*.py` 定向测试通过。配置差分的本机参考程序报告 jq 1.8.2，精确构建来源未核实。

## 明确缺口

未验证超大数值边界、非 JSON 字符串中的孤立代理项、完整诊断文本、内存/性能或跨平台差异。通过这些样本不代表完整 jq Unicode 或 CLI 兼容。
