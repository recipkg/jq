# 来源与覆盖

- [jqlang/jq `jq-1.8.2`](https://github.com/jqlang/jq/tree/34f7186b86743a083a589741b6cea95293524108)，提交 `34f7186b86743a083a589741b6cea95293524108`。`COPYING` 说明 jq 程序 MIT、文档 CC BY 3.0，以及附带第三方材料的具体条款；不要将全部仓库概括成单一许可。
- 阅读 `docs/content/manual/v1.8/manual.yml` 的调用方式、输入输出选项、参数、退出状态、索引、迭代和管道；尚未全面审阅源码与内建函数。
- 本机 `jq-1.8.2` 实测了验收用例 1–4，输入、stdout 和退出码见 `ACCEPTANCE.md`。与上游源码标签对应的版本号一致，但没有从该 checkout 编译二进制或核验其构建配置。
- 对照 [jq 1.8 手册](https://jqlang.org/manual/v1.8/) 并以本机 `jq-1.8.2` 验证部分赋值、路径和错误处理样本，见 `ASSIGNMENT-OBSERVED.md` 和验收用例 6；这仍是局部上游样本，不是独立重建验收。
- 无独立重建；大部分语言和函数、流式、模块、库 API、边缘情况与平台尚未提取或测试。
