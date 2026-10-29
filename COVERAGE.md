# 来源与覆盖

- [jqlang/jq `jq-1.8.2`](https://github.com/jqlang/jq/tree/34f7186b86743a083a589741b6cea95293524108)，提交 `34f7186b86743a083a589741b6cea95293524108`。`COPYING` 说明 jq 程序 MIT、文档 CC BY 3.0，以及附带第三方材料的具体条款；不要将全部仓库概括成单一许可。
- 查阅固定提交中的 jq 1.8 手册“Modules”章节，并在本机报告 `jq-1.8.2` 的二进制上对照 `-L`、两种候选布局、递归 include、搜索顺序和缺失模块错误；记录在 `MODULES-DOCUMENTED.md`。9 项独立重建模块样本全部通过，其中 6 项已由本机 jq 1.8.2 对照；循环 include 仅验证了候选实现的保护，不代表上游语义。上游二进制的构建来源未核实。
- 阅读 `docs/content/manual/v1.8/manual.yml` 的调用方式、输入输出选项、参数、退出状态、索引、迭代和管道；尚未全面审阅源码与内建函数。
- 本机 `jq-1.8.2` 实测了验收用例 1–4，输入、stdout 和退出码见 `ACCEPTANCE.md`。与上游源码标签对应的版本号一致，但没有从该 checkout 编译二进制或核验其构建配置。
- 对照 [jq 1.8 手册](https://jqlang.org/manual/v1.8/) 并以本机 `jq-1.8.2` 验证部分赋值、路径和错误处理样本，见 `ASSIGNMENT-OBSERVED.md` 和验收用例 6；这仍是局部上游样本，不是独立重建验收。
- 本机另外验证流式路径和结束记录、错误输出、`inputs`、文件及原始 I/O 的少数样本，见 `IO-OBSERVED.md` 和验收用例 7；未验证大文件内存、性能或完整流式能力。
- 补充本机 `input`、`-n`、跨文件 `-s` 与 NUL 输出失败前的部分结果；修正了此前将 `-n` 错写为完全不读取输入的描述。见 `IO-OBSERVED.md`；其他组合仍需对照。
- 对照手册和本机二进制验证部分条件、回退、布尔短路和比较样本，见 `CONDITIONALS-OBSERVED.md` 和验收用例 8；排序的未测试边界仅来自手册，未验证所有复杂表达式。
- 在本机补充验证嵌套布尔值和数字的相等性、`elif` 与短路空结果；具体样本见 `CONDITIONALS-OBSERVED.md`。
- 本机 decnum 构建对照部分 JSON 字节、重复键、宽松数字、Unicode、错误恢复和大数字算术，见 `JSON-PARSING-OBSERVED.md` 和验收用例 9；其他构建与完整解析语义尚未验证。
- 参考 1.8 手册并用本机 jq 验证部分 `map`、`map_values`、键查询、数组下标和错误传播；详细条件见 `COLLECTIONS-OBSERVED.md`，未验证全部内建函数。
- 参考 1.8 手册并以本机 jq 对照部分排序、分组、去重、极值和加法归约；多结果键及相等键样本见 `AGGREGATION-OBSERVED.md`。
- 参照固定 jq 1.8 手册提取并实现有限字符串过滤器，具体范围及边界见 `STRING-FILTERS-OBSERVED.md`。本机 `jq-1.8.2` 验收覆盖前缀/后缀、裁剪、ASCII 大小写、字面 split、数组/对象 join 和错误；相关 5 个黑盒方法及固定种子样本位于 [standard-library-verification `f18f7743f82deeda66b7af7a06ae3f15fe161244`](https://github.com/recipkg/standard-library-verification/tree/f18f7743f82deeda66b7af7a06ae3f15fe161244)。
- 参照固定手册实现有限 `type`、`tostring`、`tojson` 与单 JSON 值 `fromjson` 行为；运行样本和解析边界见 `CONVERSION-OBSERVED.md`。4 个黑盒方法及固定种子对照位于 [standard-library-verification `c2aad12629b0ab36d1e162f891cd1d03284ed904`](https://github.com/recipkg/standard-library-verification/tree/c2aad12629b0ab36d1e162f891cd1d03284ed904)。
- 参考固定手册和本机样本实现数组 `reverse` 及有限递归 `contains`/`inside`；测试包含顶层错误、嵌套错配和 120 组固定种子差分。相关 3 个黑盒方法位于 [standard-library-verification `6700bd7304a4ae1f734192bba43b7883f6f2c1fa`](https://github.com/recipkg/standard-library-verification/tree/6700bd7304a4ae1f734192bba43b7883f6f2c1fa)。
- 依赖候选核查：标准库尚无 JSON 解析器配方。上游 [jsmn](https://github.com/zserge/jsmn/tree/25647e692c7906b96ffd2b05ca54c097948e879c) 提供基于输入偏移的 token，而 jq 还需值解码、数字语义、错误和流式记录；未验证两者完整接口兼容，因此没有将其当作现成依赖。
- 对照手册与本机样本补充绑定、函数和归约，见 `PROGRAMMING-OBSERVED.md` 和验收用例 10；复杂作用域、多值组合和完整函数库仍未知。
- 独立重建：[jq-source `d16d41fb35020add5c20f72864444f1c1e54803c`](https://github.com/recipkg/jq-source/tree/d16d41fb35020add5c20f72864444f1c1e54803c)，对应源码状态见 [STATUS.md](https://github.com/recipkg/jq-source/blob/d16d41fb35020add5c20f72864444f1c1e54803c/STATUS.md)。模块样本位于验证仓库 `7fa07efd4df374a91a7f46d4e0af724100e4ccb4`，字符串样本位于 `f18f7743f82deeda66b7af7a06ae3f15fe161244`，类型转换样本位于 `c2aad12629b0ab36d1e162f891cd1d03284ed904`，容器样本位于 `6700bd7304a4ae1f734192bba43b7883f6f2c1fa`。重建先前通过 19 项局部验收、9 项差分测试及集合操作的有限样本和 1,400 组固定种子对照，以及排序和聚合样本及 1,200 组固定种子对照；模块 9 项、字符串 5 项、类型转换 4 项、容器 3 项测试通过，容器差分包含 120 组比较。早期 48 项失败是 v0.1.6 的历史基线，不是当前统计；详见源码状态。配方和实现都未完成，不能声称完整兼容；大部分语言和函数、库 API、边缘情况与平台也尚未完整提取或测试。
