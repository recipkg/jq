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


## 本轮更新：数组转置（配方 v0.1.33）

- 增加 `transpose` 的有限行为合同：[`TRANSPOSE-OBSERVED.md`](TRANSPOSE-OBSERVED.md)。
- 独立重建提交：[jq-source `af4933be75d99583f659bccd4bb1faf01a506627`](https://github.com/recipkg/jq-source/tree/af4933be75d99583f659bccd4bb1faf01a506627)；验证提交：[standard-library-verification `a89ce265f0f6e244f24971c39e4e7e8d78fef66d`](https://github.com/recipkg/standard-library-verification/tree/a89ce265f0f6e244f24971c39e4e7e8d78fef66d)。
- 主 jq 合同 19 项、定向 jq 测试 67 项通过；本轮 4 项转置测试通过，差分覆盖 100 组固定种子矩阵。参考端 `/usr/bin/jq` 报告 1.8.2，但构建来源未核实。
- 极端矩阵、精确错误行为、其他平台和完整 jq 兼容性仍未确认；不能据此标记配方或重建已完成。


## 本轮更新：路径读取（配方 v0.1.34）

- 补充有限 `getpath` 读取合同：[`GETPATH-OBSERVED.md`](GETPATH-OBSERVED.md)。
- 独立重建提交：[jq-source `b09164de264e451a008830a3047a0e18b321d14b`](https://github.com/recipkg/jq-source/tree/b09164de264e451a008830a3047a0e18b321d14b)；验证提交：[standard-library-verification `4af5dddef15951876fc71d92d17cd985d30ccad5`](https://github.com/recipkg/standard-library-verification/tree/4af5dddef15951876fc71d92d17cd985d30ccad5)。
- 主 jq 合同 19 项、定向 jq 测试 70 项通过；本轮 3 项路径测试通过，包含 100 组固定种子路径差分。参考端 `/usr/bin/jq` 报告 1.8.2，但构建来源未核实。
- 这只覆盖路径读取。路径生成和写入、完整错误时序、其他平台与完整 jq 兼容性仍未确认。


## 本轮更新：路径写入（配方 v0.1.35）

- 增加有限 `setpath(PATHS; VALUE)` 合同：[`SETPATH-OBSERVED.md`](SETPATH-OBSERVED.md)。
- 独立重建提交：[jq-source `01e0edfac91a7e54ae6b275f527bd79d43486de2`](https://github.com/recipkg/jq-source/tree/01e0edfac91a7e54ae6b275f527bd79d43486de2)；验证提交：[standard-library-verification `eb60085ce514153cf370ddba184b654a9355b576`](https://github.com/recipkg/standard-library-verification/tree/eb60085ce514153cf370ddba184b654a9355b576)。
- 主 jq 合同 19 项、定向 jq 测试 73 项通过；本轮 3 项路径写入测试及 100 组固定种子差分通过。参考端 `/usr/bin/jq` 报告 1.8.2，构建来源未核实。
- 多结果顺序、极端索引与资源边界、完整路径 API、其他平台及整体 jq 兼容性仍待验证。


## 本轮更新：路径生成（配方 v0.1.36）

- 增加 `path(expr)`、`paths`、`paths(f)` 的有限合同：[`PATHS-OBSERVED.md`](PATHS-OBSERVED.md)。
- 独立重建提交：[jq-source `5ae62c6900f957c84e2eed247a8d5159a372f341`](https://github.com/recipkg/jq-source/tree/5ae62c6900f957c84e2eed247a8d5159a372f341)；验证提交：[standard-library-verification `a5d615a80245be0899bf2eb5a3be474d53681d14`](https://github.com/recipkg/standard-library-verification/tree/a5d615a80245be0899bf2eb5a3be474d53681d14)。
- 主 jq 合同 19 项、定向 jq 测试 76 项通过；本轮 3 项路径生成测试通过，含 100 个固定种子 JSON 值差分。参考端 `/usr/bin/jq` 报告 1.8.2，构建来源未核实。
- 复杂路径语法、全部类型/错误边界、平台和完整 jq 兼容性仍未确认。


## 本轮更新：多路径删除（配方 v0.1.37）

- 增加 `delpaths(PATHS)` 同时删除对象键与数组元素的有限合同：[`DELPATHS-OBSERVED.md`](DELPATHS-OBSERVED.md)。数组删除按原始索引处理；本轮样本包括重复/嵌套/重叠路径、缺失键、越界/负/小数索引、空根路径和非法组合。
- 独立重建提交：[jq-source `d7b6cf01b136f30f93cf2d7ae8233308cdbbcc9f`](https://github.com/recipkg/jq-source/tree/d7b6cf01b136f30f93cf2d7ae8233308cdbbcc9f)；黑盒验证提交：[standard-library-verification `c19b3b8`](https://github.com/recipkg/standard-library-verification/tree/c19b3b8794236e297c298cedd5d7099d4447380a)。19 项主合同与 80 项定向测试全量通过；更新小数下标边界后，本轮 4 项 delpaths 测试再次通过，含 100 组固定种子嵌套值对照。
- 参考端 `/usr/bin/jq` 报告 1.8.2，构建来源未核实。精确错误语义、极端索引和资源边界、平台及完整 jq 兼容性仍未确认。


## 本轮更新：SQL 风格流操作（配方 v0.1.38）

- 增加大写 `INDEX`、`JOIN` 两/三/四参数形式及大写 `IN` 单/双流判断的有限合同：[`SQL-OBSERVED.md`](SQL-OBSERVED.md)。覆盖重复和多值索引键、复合键转字符串、联结未命中、两侧合并，以及固定种子索引联结。
- 独立重建提交：[jq-source `d4f116a7b9af6b70c1270253c97eca8269c928b9`](https://github.com/recipkg/jq-source/tree/d4f116a7b9af6b70c1270253c97eca8269c928b9)；黑盒验证提交：[standard-library-verification `d1cd01e`](https://github.com/recipkg/standard-library-verification/tree/d1cd01e50edced74232428d5b5740eedae87f558)。85 项定向测试全量通过；本轮 5 项 SQL 操作测试在增加复合键覆盖后再次通过，含 100 组固定种子联结差分。
- 参考端 `/usr/bin/jq` 报告 1.8.2，构建来源未核实。完整流求值顺序、错误时序、资源边界、平台及 jq 全量兼容性仍未确认。


## 本轮更新：路径表达式删除（配方 v0.1.39）

- 增加 `del(path_expression)` 的有限删除合同：[`DELPATH-OBSERVED.md`](DELPATH-OBSERVED.md)。覆盖对象/数组路径、多个下标、迭代路径、未命中、重叠路径、负/越界下标和根路径；目标路径按一次性删除处理。
- 独立重建提交：[jq-source `d2356495aa8991287fc9e5e97343e4679bcb709b`](https://github.com/recipkg/jq-source/tree/d2356495aa8991287fc9e5e97343e4679bcb709b)；黑盒验证提交：[standard-library-verification `e629c9a`](https://github.com/recipkg/standard-library-verification/tree/e629c9ad06c5e85b9b5ba9f4503a84612120f80f)。89 项定向测试全量通过；本轮 4 项 `del` 测试再次通过，含 100 组固定种子嵌套值差分。
- 参考端 `/usr/bin/jq` 报告 1.8.2，构建来源未核实。复杂路径表达式、完整错误语义、资源边界、平台和完整 jq 兼容性仍未确认。


## 本轮更新：路径投影（配方 v0.1.40）

- 增加 `pick(path_expressions)` 的稀疏投影合同：[`PICK-OBSERVED.md`](PICK-OBSERVED.md)。对象缺失字段与数组空位填 null；验收包含嵌套、重复/重叠路径、迭代器、空迭代和根路径。
- 独立重建提交：[jq-source `206c30f08bb926ffe606e4221f2f1c0d21fc5893`](https://github.com/recipkg/jq-source/tree/206c30f08bb926ffe606e4221f2f1c0d21fc5893)；黑盒验证提交：[standard-library-verification `41af720`](https://github.com/recipkg/standard-library-verification/tree/41af7208bd1460ebcb7eaa00d9aee7c1a58cf55b)。93 项定向测试全量通过；本轮 4 项 `pick` 测试通过，含 100 组固定种子嵌套值差分。
- 参考端 `/usr/bin/jq` 报告 jq 1.8.2，构建来源未核实。负数组索引、切片、复杂路径、完整错误行为、资源边界、平台和整体兼容性仍未确认。


## 本轮更新：多路径读取（配方 v0.1.41）

- 补充 `getpath(PATHS)` 接受逗号生成的多个路径并按顺序输出结果的有限合同：[`GETPATH-OBSERVED.md`](GETPATH-OBSERVED.md)。
- 独立重建代码未变（基线提交 `206c30f`）；重建仓库文档更新提交为 [`130d0cd`](https://github.com/recipkg/jq-source/tree/130d0cd4aa5f2bf50567939549abb4be44e32fa1)，验证提交为 [`0072dfc`](https://github.com/recipkg/standard-library-verification/tree/0072dfc5526b4831e4dc15382b23d44bcbdb1710)。本轮只补充已有行为的验证和配方记录。
- 主 jq 合同 19 项、定向 jq 测试 94 项全量通过；本轮 4 项 `getpath` 测试通过，含 100 组固定种子对象/数组路径差分。参考端 `/usr/bin/jq` 报告 jq 1.8.2，构建来源未核实。
- 逗号生成路径之外的所有生成器组合、精确错误时序、极端索引、资源边界、平台和完整 jq 兼容性仍未确认。


## 本轮更新：多路径写入（配方 v0.1.42）

- 补充 `setpath(PATHS; VALUE)` 对逗号生成多路径的有限合同：每条路径都基于原输入单独更新，并按产生顺序输出，不累积前一条路径的修改。
- 重建代码基线仍为 [`jq-source 206c30f`](https://github.com/recipkg/jq-source/tree/206c30f08bb926ffe606e4221f2f1c0d21fc5893)；状态文档更新为 [`40b9010`](https://github.com/recipkg/jq-source/tree/40b901064e53f55c7fb173bde03ccf3a4e969671)，黑盒验证为 [`8fa4c5f`](https://github.com/recipkg/standard-library-verification/tree/8fa4c5f3dff3c89ad530557586e4106f6f3e5100)。
- 主 jq 合同 19 项、定向 jq 测试 95 项全量通过；本轮 4 项 `setpath` 测试通过，含 100 组固定种子对象/数组更新差分。参考端 `/usr/bin/jq` 报告 jq 1.8.2，构建来源未核实。
- 其他路径/值生成器组合、极端索引和资源边界、平台及完整 jq 兼容性仍未确认。


## 本轮更新：递归遍历（配方 v0.1.43）

- 实现有限 `walk(f)`：对象值与数组元素先递归转换，再对当前容器应用 f；覆盖后序数组排序、对象与标量变换，以及数组中子节点多结果展开。
- 重建提交为 [`jq-source d522902`](https://github.com/recipkg/jq-source/tree/d5229020ab271d7dd1f286f71efa62c61493874e)，黑盒验证为 [`standard-library-verification d9904e7`](https://github.com/recipkg/standard-library-verification/tree/d9904e713ad122f186615991e10d8107fd5f73de)。
- 主 jq 合同 19 项、定向 jq 测试 99 项全量通过；本轮 4 项 `walk` 测试通过，含 100 组固定种子嵌套 JSON 差分。参考端 `/usr/bin/jq` 报告 jq 1.8.2，构建来源未核实。
- 深递归资源、对象多结果边界、精确错误、平台和整体兼容性仍未确认。


## 本轮更新：递归生成器（配方 v0.1.44）

- 实现有限 `recurse`、`recurse(f)` 和 `recurse(f; condition)`：根值优先、深度优先跟进子值，条件形式在未通过条件时停止该分支。
- 重建提交为 [`jq-source a0d1bd3`](https://github.com/recipkg/jq-source/tree/a0d1bd3da8778a97837ad937d594e59497182e8a)，黑盒验证为 [`standard-library-verification 57ac488`](https://github.com/recipkg/standard-library-verification/tree/57ac488664dad9d55a4aff8b1500e966af4a88c0)。
- 主 jq 合同 19 项、定向 jq 测试 103 项全量通过；本轮 4 项 `recurse` 测试通过，默认遍历差分覆盖 100 个固定种子 JSON。参考端 `/usr/bin/jq` 报告 jq 1.8.2，构建来源未核实。
- 无限生成、递归资源、多个条件结果的全部组合、精确错误、平台和完整兼容性仍未确认。


## 本轮更新：流截取与跳过（配方 v0.1.45）

- 增加有限 `limit(n; expr)` 与 `skip(n; expr)` 合同：按顺序截取/跳过生成器输出，保留零值、空计数流、多计数值及小数实测边界；`limit` 可以截取无限流。详见 [`LIMIT-SKIP-OBSERVED.md`](LIMIT-SKIP-OBSERVED.md)。
- 独立重建本轮增加这两个生成器内建函数；黑盒样本位于 `standard-library-verification/tests/test_jq_limit_skip.py`，包含负数/已测非法类型失败，以及 100 组固定种子数组与计数的参考差分。
- 本轮 4 项定向测试通过；随后完整 jq 回归 107 项全量通过（70.859 秒）。参考端 `/usr/bin/jq` 报告 jq 1.8.2，构建来源未核实。
- 重建提交为 [`jq-source 9cb71e9`](https://github.com/recipkg/jq-source/tree/9cb71e9227ab8ca998e3230281b9ff50e9d14d70)，验证提交为 [`standard-library-verification 13f0198`](https://github.com/recipkg/standard-library-verification/tree/13f01983f7b2c3009b9b14e13256cdf466cfde0c)。
- 精确诊断和错误时序、复杂多值交错、大数与极端数值、递归资源、平台和完整 jq 兼容性仍未确认。


## 本轮提取：循环生成器（配方 v0.1.46）

- 实现有限 `while(cond; update)` 与 `until(cond; next)`：按条件输出当前值或继续处理更新流，并保留多条件/多更新结果产生的独立分支及顺序。固定样本覆盖初始停止、单结果迭代、分支输出和 100 组固定种子正步长输入。
- 本轮 3 项定向测试通过；完整 jq 回归 110 项全量通过（68.008 秒）。参考端 `/usr/bin/jq` 报告 jq 1.8.2，构建来源未核实。
- 重建提交为 [`jq-source f6ead63`](https://github.com/recipkg/jq-source/tree/f6ead6311656aac3c89042f8986897759d72b392)，验证提交为 [`standard-library-verification 8e241b8`](https://github.com/recipkg/standard-library-verification/tree/8e241b8ce6a7eb5db0357ec4ecb662b8a38239ec)。
- 在隔离的 Recipkg 存储中同时安装并构建 `jq@0.1.44`、`jq@0.1.45`、`jq@0.1.46`；三版各自保留递归 `software-quality` 上下文，生成提示词中的循环/流组合器与版本边界相符。
- 上游 jq 手册提到单结果更新可使用尾调用优化；不将此上游属性当作当前重建程序的资源保证。
- 无限循环保护、尾调用优化、递归/内存上限、极端深度、错误时序与平台差异仍未验证。

## Numeric rounding iteration: 2026-10-30

- Input: the `jq` recipe draft v0.1.46, fixed to upstream jq 1.8.2. The versioned manual lists `ceil`, `floor`, `round`, and `trunc` among its single-input math filters.
- Implemented finite numeric samples for all four filters: floor toward negative infinity, ceil toward positive infinity, truncation toward zero, and half-away-from-zero `round`; negative zero is retained in observed cases. Non-number inputs fail explicitly.
- All 3 new black-box methods passed, including 100 fixed-seed finite decimal values per filter. The complete targeted `test_jq_*.py` regression passed: 113 tests in 74.990 seconds. Reference `/usr/bin/jq` reports 1.8.2; its build provenance is unverified.
- Reconstruction commit: [`jq-source 383578d`](https://github.com/recipkg/jq-source/tree/383578dd5e603ea1352efe390cf4de992224b5f5); black-box verification commit: [`standard-library-verification d315210`](https://github.com/recipkg/standard-library-verification/tree/d3152106a13a6803f68ac417bfcbc024a3a144cd).
- Non-finite values, extreme numeric precision, platform math-library variation, exact diagnostics and complete jq compatibility remain unverified.

## Single-argument math iteration: 2026-10-30

- Input: recipe v0.1.48, fixed to jq 1.8.2. The versioned jq manual lists 40 single-argument C math functions and notes that availability depends on the operating system math library.
- Scope: implement the 32 remaining single-argument functions supported by the available Python math library and arithmetic (`acos`, `acosh`, `asin`, `asinh`, `atan`, `atanh`, `cbrt`, `cos`, `cosh`, `erf`, `erfc`, `exp`, `exp10`, `exp2`, `expm1`, `fabs`, `gamma`, `lgamma`, `log`, `log10`, `log1p`, `log2`, `logb`, `nearbyint`, `rint`, `significand`, `sin`, `sinh`, `sqrt`, `tan`, `tanh`, `tgamma`). Together with `ceil`, `floor`, `round`, and `trunc`, this covers 36 of the 40 documented unary functions; `j0`, `j1`, `y0`, `y1` remain explicit gaps.
- Four new black-box methods passed: finite reference comparisons, non-number failures for every function, domain/singularity/overflow and signed-zero cases, and 100 fixed-seed values per added function. Reference `/usr/bin/jq` reports 1.8.2; build provenance is unverified. Gamma-family calculations may differ by a few floating-point units.
- Full `test_jq_*.py` regression passed: 117 tests in 86.261 seconds. Reconstruction commit: [`jq-source b7c3b67`](https://github.com/recipkg/jq-source/tree/b7c3b67f5b57631c5adbcd5e288ad4588483a988); verification commit: [`standard-library-verification 89d925f`](https://github.com/recipkg/standard-library-verification/tree/89d925fab7603516a93993817e2963e03b36461f).
- Two- and three-argument math functions, platform libm variation, extreme precision and full jq compatibility remain unverified.

## Multi-argument math iteration: 2026-10-30

- Input: recipe v0.1.49, fixed to upstream jq 1.8.2. Source signatures were checked against the versioned manual and upstream `src/libm.h` / `src/builtin.c`.
- Scope: 17 two-filter-argument C math functions and three-argument `fma`, plus the unary `frexp` and `modf` filters that return their C out-parameters in a two-element array. Integer C parameters in `jn`, `yn`, `ldexp`, and `scalbln` follow C conversion behavior.
- Six new black-box methods passed against `/usr/bin/jq`: decomposition outputs, finite samples for all functions, domain/zero/signed-zero boundaries, non-number failures in every argument position, jq generator ordering, and 100 fixed-seed comparisons for each of 18 two/three-argument functions. The focused `test_jq_math*.py` run passed all 10 tests in 20.458 seconds. Reconstruction: [`jq-source 048c762`](https://github.com/recipkg/jq-source/tree/048c76260d5eb373f23cd8ccba54a5c9ac075cdb); verification: [`standard-library-verification 25465a0`](https://github.com/recipkg/standard-library-verification/tree/25465a00d0215822170d4257e6466acac9d5a132). The reference reports 1.8.2; build provenance is unverified.
- The full `test_jq_*.py` regression passed all 123 tests in 107.083 seconds through `jq-workspace` commit `1590b89`, pinned to source `c9b4264` and recipe `431db44`. The local reference `/usr/bin/jq` reports 1.8.2; its build provenance is unverified.
- Recipkg checks passed: pack the pinned recursive dependency, check the archive, render build and discover prompts in English and Simplified Chinese, and confirm the recursive quality recipe and build prompt are present in both build prompts. Two independent packs were byte-identical.
- Platform library differences, unavailable C symbols, extreme precision, exact diagnostics and full jq compatibility remain unverified.

## Bessel math iteration: 2026-10-30

- Input: jq recipe draft v0.1.50, fixed to upstream jq 1.8.2. The versioned manual lists `j0`, `j1`, `y0`, and `y1` among the 40 single-input C math functions and says availability depends on the host math library.
- Implemented the four filters through available host C symbols, while keeping them defined with an explicit unavailable-symbol error on systems that lack a symbol.
- All 4 new Bessel test methods passed, including zero/domain/type boundaries and 100 fixed-seed differential samples per function. The focused math suite passed all 14 tests in 22.981 seconds against `/usr/bin/jq` reporting 1.8.2; the reference binary build provenance is unverified.
- The complete `test_jq_*.py` regression passed all 127 tests in 110.443 seconds through `jq-workspace` commit `3e5928f`, pinned to source `898cbecc` and recipe `0.1.50` at `d0cd418`. Reference `/usr/bin/jq` reports 1.8.2; its build provenance is unverified. Recipkg pack/check and English/Simplified Chinese build/discover passed; both build prompts contain the recursive quality recipe and build prompt, and two independent archives were byte-identical. Other platforms, extreme precision, exact diagnostics and full jq compatibility remain unverified.
