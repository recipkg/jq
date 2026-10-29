# 来源与选取依据

- 上游仓库：[jqlang/jq](https://github.com/jqlang/jq)。目标固定为 `jq-1.8.2`，源码提交 [`34f7186b86743a083a589741b6cea95293524108`](https://github.com/jqlang/jq/tree/34f7186b86743a083a589741b6cea95293524108)。
- GitHub API 在 2026-10-29 返回 35,995 stars；同次查询显示最近推送时间为 2026-10-25。星数只用于发现和选取，不证明软件质量或配方完整性。
- 仓库 API 对许可证返回 `NOASSERTION`。固定提交的 `COPYING` 分别说明 jq 程序 MIT、文档 CC BY 3.0，以及内含第三方材料的各自条款；不能将整份源码树概括成单一许可证。
- 主要文档：[jq 1.8 手册](https://jqlang.org/manual/v1.8/)，对应固定源码中的 [`docs/content/manual/v1.8/manual.yml`](https://github.com/jqlang/jq/blob/34f7186b86743a083a589741b6cea95293524108/docs/content/manual/v1.8/manual.yml)。模块条目见 [MODULES-DOCUMENTED.md](MODULES-DOCUMENTED.md)。
- 字符串过滤器使用同一固定提交中的 [jq 1.8 手册](https://jqlang.org/manual/v1.8/)作为合同来源；有限本机对照及未验证边界见 [STRING-FILTERS-OBSERVED.md](STRING-FILTERS-OBSERVED.md) 和验收用例 14。
- 类型与 JSON 转换过滤器也参考固定提交的 [jq 1.8 手册“Convert to/from JSON”](https://jqlang.org/manual/v1.8/)；局部对照、上游二进制来源限制及解析器缺口见 [CONVERSION-OBSERVED.md](CONVERSION-OBSERVED.md) 和验收用例 15。
- `contains`、`inside` 与 `reverse` 参考固定提交的 [jq 1.8 手册容器过滤器](https://jqlang.org/manual/v1.8/)；递归包含和类型错配的局部样本见 [CONTAINMENT-OBSERVED.md](CONTAINMENT-OBSERVED.md) 及验收用例 16。
- `indices`、`index`、`rindex` 参考固定提交的 jq 1.8 手册位置过滤器；重叠、Unicode 码点、数组连续子项和 null/object 边界见 [INDEX-OBSERVED.md](INDEX-OBSERVED.md) 及验收用例 17。
- `length` 与 `flatten` 参考固定提交的 [jq 1.8 手册长度和扁平化过滤器](https://jqlang.org/manual/v1.8/)；类型、深度和对象值边界见 [ARRAY-VALUES-OBSERVED.md](ARRAY-VALUES-OBSERVED.md) 及验收用例 18。小数深度只记录为当前实现限制，不宣称兼容。
- `utf8bytelength`、`explode`、`implode` 参考固定提交的 [jq 1.8 手册 Unicode 转换过滤器](https://jqlang.org/manual/v1.8/)；码点、字节长度和数值替换样本见 [UNICODE-OBSERVED.md](UNICODE-OBSERVED.md) 及验收用例 19。
- 当前材料主要覆盖文档中的 CLI 与过滤器，并以本机二进制验证了少量样本；尚未完整审阅固定源码、全部测试、平台构建或公共 C API。各项证据与边界见 [COVERAGE.md](COVERAGE.md)。
