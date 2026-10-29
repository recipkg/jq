# 来源与选取依据

- 上游仓库：[jqlang/jq](https://github.com/jqlang/jq)。目标固定为 `jq-1.8.2`，源码提交 [`34f7186b86743a083a589741b6cea95293524108`](https://github.com/jqlang/jq/tree/34f7186b86743a083a589741b6cea95293524108)。
- GitHub API 在 2026-10-29 返回 35,995 stars；同次查询显示最近推送时间为 2026-10-25。星数只用于发现和选取，不证明软件质量或配方完整性。
- 仓库 API 对许可证返回 `NOASSERTION`。固定提交的 `COPYING` 分别说明 jq 程序 MIT、文档 CC BY 3.0，以及内含第三方材料的各自条款；不能将整份源码树概括成单一许可证。
- 主要文档：[jq 1.8 手册](https://jqlang.org/manual/v1.8/)，对应固定源码中的 [`docs/content/manual/v1.8/manual.yml`](https://github.com/jqlang/jq/blob/34f7186b86743a083a589741b6cea95293524108/docs/content/manual/v1.8/manual.yml)。模块条目见 [MODULES-DOCUMENTED.md](MODULES-DOCUMENTED.md)。
- 当前材料主要覆盖文档中的 CLI 与过滤器，并以本机二进制验证了少量样本；尚未完整审阅固定源码、全部测试、平台构建或公共 C API。各项证据与边界见 [COVERAGE.md](COVERAGE.md)。
