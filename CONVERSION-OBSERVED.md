# jq 类型与 JSON 转换观察

## 固定来源

上游为 jq 1.8.2，源码固定提交 `34f7186b86743a083a589741b6cea95293524108`。`type`、`tostring`、`tojson` 和 `fromjson` 的合同见该提交对应的 [jq 1.8 手册](https://jqlang.org/manual/v1.8/)。输出样本在本机报告 `jq-1.8.2` 的 `/usr/bin/jq` 上运行；系统二进制的构建来源未核实。

## 本次实现与样本

- `type` 对 null、boolean、number、string、array、object 分别返回 jq 类型名；布尔值必须先于宿主语言的整数类型判断。
- `tostring` 对字符串原样返回；其他 JSON 值转为紧凑 JSON 文本。
- `tojson` 将所有值编码成紧凑 JSON 文本，包括把字符串编码为带 JSON 引号和转义的文本。
- `fromjson` 仅接受字符串，解析恰好一个 JSON 值。空文本、无效文本、连续多个 JSON 文本和非字符串输入均产生运行错误；本样本不固定完整诊断文本。
- 固定样本覆盖 Unicode、所有 jq JSON 类型、嵌套数组和对象、错误以及前后空白。差分样本用固定随机种子生成 90 个标量/容器输入，比较 `type`、`tostring`、`tojson` 与对应编码文本的 `fromjson` 结果。

验证脚本 `standard-library-verification/tests/test_jq_conversion.py` 的 4 个测试方法通过本机候选实现与报告版本为 jq 1.8.2 的参考程序对照。

## 尚未验证

这些样本不覆盖完整 JSON 语法、重复对象键处理、精确数值和十进制格式边界、异常恢复、全部 Unicode 转义和非法代理项行为、错误诊断、极大输入或其他平台。`fromjson` 样本通过不能证明本实现的解析器与 jq 完全兼容。
