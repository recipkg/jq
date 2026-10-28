# jq 验收用例（上游本机样本，重建未执行）

用隔离环境内待验收的 `jq` 运行，检查 stdout、stderr 和退出码：

1. 输入 `{"items":[{"n":1,"name":"a"},{"n":2,"name":"b"}]}`，`jq -c '.items[] | select(.n > 1) | .name'` 输出 `"b"` 后接换行，退出 0。
2. 输入 `{"x":"hello"}`，`jq -r '.x'` 输出 `hello` 后接换行，退出 0；输入 `1\n2\n3\n`，`jq -s length` 输出 `3` 后接换行。
3. `jq -n --arg n 123 --argjson x 123 '[$n,$x]'` 输出 JSON 数组，解析值为 `["123",123]`；不因空格和缩进差异误判 JSON 结构。
4. `jq -ne true` 退出 0；`jq -ne false` 和 `jq -ne null` 退出 1；`jq -ne empty` 退出 4 且 stdout 为空。以无效 JSON `{invalid}` 输入 `jq -c '.'`，本机 jq 1.8.2 返回 5、错误写 stderr；记录平台和完整诊断。
5. 对所有声称支持的过滤器、数据类型、函数、错误、精度、模块、文件和平台补充对照与验收。以上几个样本不足以证明完整兼容 jq。
