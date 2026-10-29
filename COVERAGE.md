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
- 补充字符串与数组上的 `indices`、`index`、`rindex`，包括重叠子串、Unicode 码点位置、连续子数组和选定的 null/object 边界；4 个黑盒方法及 120 组差分见 [standard-library-verification `653fdff998db83d617eb5c5aee7aeeaf2c4e08d8`](https://github.com/recipkg/standard-library-verification/tree/653fdff998db83d617eb5c5aee7aeeaf2c4e08d8)。
- 补充 `length` 与 `flatten` 的有限数组/值语义：Unicode 字符串和容器长度、null、数字、布尔错误、递归及有限深度展开和对象值迭代。4 个黑盒方法和 80 组嵌套数组差分见 [standard-library-verification `7af6586a9ca7958ef7f42919f5c1a09dff9e6c0d`](https://github.com/recipkg/standard-library-verification/tree/7af6586a9ca7958ef7f42919f5c1a09dff9e6c0d)；小数深度明确不支持。
- 添加 `utf8bytelength`、`explode`、`implode` 的有限码点转换：覆盖 100 个固定种子字符串和 100 组合法码点数组，以及替换字符边界。4 个黑盒方法见 [standard-library-verification `0f507d9a35048958e7ea5e42697506722b454384`](https://github.com/recipkg/standard-library-verification/tree/0f507d9a35048958e7ea5e42697506722b454384)。
- 补充数组及生成器形式的 `first`、`last`、`nth` 和 `isempty`；4 个黑盒方法比较 100 组固定种子数组及 11 种过滤器，样本见 [standard-library-verification `68c8c08767e2aed6a7fbc8ab901524c34485c414`](https://github.com/recipkg/standard-library-verification/tree/68c8c08767e2aed6a7fbc8ab901524c34485c414)。
- 添加 `any`、`all` 的直接、条件与 generator/condition 形式；4 个黑盒方法对照 100 组固定种子数组及 6 种过滤器，见 [standard-library-verification `6dba3922621bb75dd32f07423a8e0a9bd3c58f86`](https://github.com/recipkg/standard-library-verification/tree/6dba3922621bb75dd32f07423a8e0a9bd3c58f86)。
- 依赖候选核查：标准库尚无 JSON 解析器配方。上游 [jsmn](https://github.com/zserge/jsmn/tree/25647e692c7906b96ffd2b05ca54c097948e879c) 提供基于输入偏移的 token，而 jq 还需值解码、数字语义、错误和流式记录；未验证两者完整接口兼容，因此没有将其当作现成依赖。
- 对照手册与本机样本补充绑定、函数和归约，见 `PROGRAMMING-OBSERVED.md` 和验收用例 10；复杂作用域、多值组合和完整函数库仍未知。
- 独立重建：[jq-source `b1c2ecebf386b5ad69df8f2a80b765df2880669b`](https://github.com/recipkg/jq-source/tree/b1c2ecebf386b5ad69df8f2a80b765df2880669b)，对应源码状态见 [STATUS.md](https://github.com/recipkg/jq-source/blob/b1c2ecebf386b5ad69df8f2a80b765df2880669b/STATUS.md)。模块样本位于验证仓库 `7fa07efd4df374a91a7f46d4e0af724100e4ccb4`，字符串样本位于 `f18f7743f82deeda66b7af7a06ae3f15fe161244`，类型转换样本位于 `c2aad12629b0ab36d1e162f891cd1d03284ed904`，容器样本位于 `6700bd7304a4ae1f734192bba43b7883f6f2c1fa`，位置样本位于 `653fdff998db83d617eb5c5aee7aeeaf2c4e08d8`，长度/扁平化样本位于 `7af6586a9ca7958ef7f42919f5c1a09dff9e6c0d`，Unicode 样本位于 `0f507d9a35048958e7ea5e42697506722b454384`，生成器选择样本位于 `68c8c08767e2aed6a7fbc8ab901524c34485c414`，any/all 样本位于 `6dba3922621bb75dd32f07423a8e0a9bd3c58f86`。重建先前通过 19 项局部验收、9 项差分测试及集合操作的有限样本和 1,400 组固定种子对照，以及排序和聚合样本及 1,200 组固定种子对照；模块 9 项、字符串 5 项、类型转换 4 项、容器 3 项、位置 4 项、长度/扁平化 4 项、Unicode 4 项、生成器选择 4 项、any/all 4 项测试通过，位置差分为 60 组字符串和 60 组数组，扁平化差分为 80 组数组，Unicode 差分为 100 个字符串和 100 组码点数组，生成器差分为 100 组数组，any/all 差分为 100 组数组。早期 48 项失败是 v0.1.6 的历史基线，不是当前统计；详见源码状态。配方和实现都未完成，不能声称完整兼容；大部分语言和函数、库 API、边缘情况与平台也尚未完整提取或测试。


## 本轮更新：`range`（配方 v0.1.31）

- 补充单参数、双参数、三参数 `range` 的有限合同和观察记录：`RANGE-OBSERVED.md`。
- 独立重建提交：[jq-source `39fa30b`](https://github.com/recipkg/jq-source/tree/39fa30b)；黑盒验证提交：[standard-library-verification `2b84e93`](https://github.com/recipkg/standard-library-verification/tree/2b84e93df81359eb7a2dceda934cb57f018eee16)。
- 主 jq 合同 19 项、定向 jq 测试 59 项通过；本轮 3 项范围测试通过，含 100 组固定种子整数范围差分。参考端 `/usr/bin/jq` 报告 1.8.2，但构建来源未核实。
- 参数生成多个值时的组合与错误时序、极端数值、完整 jq 兼容性仍未验证；这项进展不表示整个配方或重建已完成。


## 本轮更新：对象条目转换（配方 v0.1.32）

- 增加 `to_entries`、`from_entries`、`with_entries` 的有限合同：[`ENTRIES-OBSERVED.md`](ENTRIES-OBSERVED.md)。
- 独立重建提交：[jq-source `25c42f93510d8272ce44a1efa067e7c4e98e58c4`](https://github.com/recipkg/jq-source/tree/25c42f93510d8272ce44a1efa067e7c4e98e58c4)；黑盒样本提交：[standard-library-verification `eeb7e48`](https://github.com/recipkg/standard-library-verification/tree/eeb7e48ff35c6706f063bb6130558f5cddf9b9d6)。
- 主 jq 合同 19 项、定向 jq 测试 63 项通过；本轮 4 项转换测试通过，差分覆盖 100 个对象和 100 个数组。参考端 `/usr/bin/jq` 报告 1.8.2，但构建来源未核实。
- 全部类型/键边界、错误时序、其他平台与完整 jq 兼容性仍未验证；整个配方及重建仍在提取中。
