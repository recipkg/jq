# 来源与覆盖

- [jqlang/jq `jq-1.8.2`](https://github.com/jqlang/jq/tree/34f7186b86743a083a589741b6cea95293524108)，提交 `34f7186b86743a083a589741b6cea95293524108`。`COPYING` 说明 jq 程序 MIT、文档 CC BY 3.0，以及附带第三方材料的具体条款；不要将全部仓库概括成单一许可。
- 阅读 `docs/content/manual/v1.8/manual.yml` 的调用方式、输入输出选项、参数、退出状态、索引、迭代和管道；尚未全面审阅源码与内建函数。
- 本机 `jq-1.8.2` 实测了验收用例 1–4，输入、stdout 和退出码见 `ACCEPTANCE.md`。与上游源码标签对应的版本号一致，但没有从该 checkout 编译二进制或核验其构建配置。
- 对照 [jq 1.8 手册](https://jqlang.org/manual/v1.8/) 并以本机 `jq-1.8.2` 验证部分赋值、路径和错误处理样本，见 `ASSIGNMENT-OBSERVED.md` 和验收用例 6；这仍是局部上游样本，不是独立重建验收。
- 本机另外验证流式路径和结束记录、错误输出、`inputs`、文件及原始 I/O 的少数样本，见 `IO-OBSERVED.md` 和验收用例 7；未验证大文件内存、性能或完整流式能力。
- 补充本机 `input`、`-n`、跨文件 `-s` 与 NUL 输出失败前的部分结果；修正了此前将 `-n` 错写为完全不读取输入的描述。见 `IO-OBSERVED.md`；其他组合仍需对照。
- 对照手册和本机二进制验证部分条件、回退、布尔短路和比较样本，见 `CONDITIONALS-OBSERVED.md` 和验收用例 8；排序的未测试边界仅来自手册，未验证所有复杂表达式。
- 在本机补充验证嵌套布尔值和数字的相等性、`elif` 与短路空结果；具体样本见 `CONDITIONALS-OBSERVED.md`。
- 本机 decnum 构建对照部分 JSON 字节、重复键、宽松数字、Unicode、错误恢复和大数字算术，见 `JSON-PARSING-OBSERVED.md` 和验收用例 9；其他构建与完整解析语义尚未验证。
- 参考 1.8 手册并用本机 jq 验证部分 `map`、`map_values`、键查询、数组下标和错误传播；详细条件见 `COLLECTIONS-OBSERVED.md`，未验证全部内建函数。
- 依赖候选核查：标准库尚无 JSON 解析器配方。上游 [jsmn](https://github.com/zserge/jsmn/tree/25647e692c7906b96ffd2b05ca54c097948e879c) 提供基于输入偏移的 token，而 jq 还需值解码、数字语义、错误和流式记录；未验证两者完整接口兼容，因此没有将其当作现成依赖。
- 对照手册与本机样本补充绑定、函数和归约，见 `PROGRAMMING-OBSERVED.md` 和验收用例 10；复杂作用域、多值组合和完整函数库仍未知。
- 独立重建：[jq-source](https://github.com/recipkg/jq-source/tree/ef3b3825b1c00325424241bf456f87f4dc7290cc) 提交 `ef3b3825b1c00325424241bf456f87f4dc7290cc` 基于本配方 v0.1.17 与递归质量依赖；其已通过 19 项局部验收、9 项差分测试及新集合操作的有限样本和 1,400 组固定种子对照。早期 48 项失败是 v0.1.6 的历史基线，不是当前统计；详见源码状态。配方和实现都未完成，不能声称完整兼容；大部分语言和函数、模块、库 API、边缘情况与平台尚未完整提取或测试。
