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
- `first`、`last`、`nth`、`isempty` 参考固定提交的 [jq 1.8 手册生成器过滤器](https://jqlang.org/manual/v1.8/)；数组索引、输出流、空结果和负下标样本见 [GENERATOR-OBSERVED.md](GENERATOR-OBSERVED.md) 及验收用例 20。
- `any`、`all` 参考固定提交的 [jq 1.8 手册逻辑聚合过滤器](https://jqlang.org/manual/v1.8/)；空值身份、对象值、条件及显式生成器样本见 [PREDICATES-OBSERVED.md](PREDICATES-OBSERVED.md) 及验收用例 21。
- 当前材料主要覆盖文档中的 CLI 与过滤器，并以本机二进制验证了少量样本；尚未完整审阅固定源码、全部测试、平台构建或公共 C API。各项证据与边界见 [COVERAGE.md](COVERAGE.md)。

- `range` 的参数形式、边界与步长方向依据固定版本的 [jq 1.8 手册](https://jqlang.org/manual/v1.8/)；升降序、小数和零步长样本以及未验证项见 [`RANGE-OBSERVED.md`](RANGE-OBSERVED.md) 与验收用例 22。
- 独立重建本轮更新至 [jq-source `39fa30b`](https://github.com/recipkg/jq-source/tree/39fa30b)；实现 `range` 的有限数值序列。源码状态记录了 19 项主验收与 59 项定向样本均通过，参考二进制构建来源仍未核实。

- `to_entries`、`from_entries`、`with_entries` 的定义与字段别名参考固定版本 [jq 1.8 手册](https://jqlang.org/manual/v1.8/)；本机对照和差分边界见 [`ENTRIES-OBSERVED.md`](ENTRIES-OBSERVED.md) 与验收用例 23。
- 本轮独立重建位于 [jq-source `25c42f93510d8272ce44a1efa067e7c4e98e58c4`](https://github.com/recipkg/jq-source/tree/25c42f93510d8272ce44a1efa067e7c4e98e58c4)，验证样本位于 [standard-library-verification `eeb7e48`](https://github.com/recipkg/standard-library-verification/tree/eeb7e48ff35c6706f063bb6130558f5cddf9b9d6)。19 项主 jq 合同与 63 项定向样本通过；参考二进制构建来源未核实。

- `transpose` 的补 null、空行及 null 行行为参照固定版本 [jq 1.8 手册](https://jqlang.org/manual/v1.8/)并以本机样本确认；见 [`TRANSPOSE-OBSERVED.md`](TRANSPOSE-OBSERVED.md) 与验收用例 24。
- 独立重建：[jq-source `b09164de264e451a008830a3047a0e18b321d14b`](https://github.com/recipkg/jq-source/tree/b09164de264e451a008830a3047a0e18b321d14b)，验证样本：[standard-library-verification `a89ce265f0f6e244f24971c39e4e7e8d78fef66d`](https://github.com/recipkg/standard-library-verification/tree/a89ce265f0f6e244f24971c39e4e7e8d78fef66d)。本轮 4 项转置测试和此前累计的 19 项主合同、67 项定向测试通过；参考构建来源未核实。

- `getpath` 的路径读取按固定版本 [jq 1.8 手册](https://jqlang.org/manual/v1.8/)提取，并用本机 jq 对照有限边界；详情见 [`GETPATH-OBSERVED.md`](GETPATH-OBSERVED.md) 与验收用例 25。
- 当前独立重建提交：[jq-source `5ae62c6900f957c84e2eed247a8d5159a372f341`](https://github.com/recipkg/jq-source/tree/5ae62c6900f957c84e2eed247a8d5159a372f341)，验证提交：[standard-library-verification `a5d615a80245be0899bf2eb5a3be474d53681d14`](https://github.com/recipkg/standard-library-verification/tree/a5d615a80245be0899bf2eb5a3be474d53681d14)。19 项主 jq 合同及 76 项定向测试通过；上游参考二进制构建来源未核实。

- `setpath` 的自动建容器、数组填充和负下标参照固定版本 [jq 1.8 手册](https://jqlang.org/manual/v1.8/)并由本机样本核对；边界见 [`SETPATH-OBSERVED.md`](SETPATH-OBSERVED.md) 和验收用例 26。
- 本轮独立重建提交：[jq-source `01e0edfac91a7e54ae6b275f527bd79d43486de2`](https://github.com/recipkg/jq-source/tree/01e0edfac91a7e54ae6b275f527bd79d43486de2)，验证提交：[standard-library-verification `eb60085ce514153cf370ddba184b654a9355b576`](https://github.com/recipkg/standard-library-verification/tree/eb60085ce514153cf370ddba184b654a9355b576)。19 项主合同与 73 项定向样本通过，参考二进制构建来源未核实。

- `path(expr)`、`paths` 及条件路径筛选参考固定版本 [jq 1.8 手册](https://jqlang.org/manual/v1.8/)；有限语法和样本边界见 [`PATHS-OBSERVED.md`](PATHS-OBSERVED.md) 与验收用例 27。
- 当前独立重建提交：[jq-source `5ae62c6900f957c84e2eed247a8d5159a372f341`](https://github.com/recipkg/jq-source/tree/5ae62c6900f957c84e2eed247a8d5159a372f341)，验证提交：[standard-library-verification `a5d615a80245be0899bf2eb5a3be474d53681d14`](https://github.com/recipkg/standard-library-verification/tree/a5d615a80245be0899bf2eb5a3be474d53681d14)。主合同 19 项、定向 76 项测试通过；参考构建来源未核实。

- `delpaths` 同时删除多条对象/数组路径，数组下标按原始输入解释；定义参照固定版本 [jq 1.8 手册](https://jqlang.org/manual/v1.8/)，黑盒边界记录于 [`DELPATHS-OBSERVED.md`](DELPATHS-OBSERVED.md) 与验收用例 28。
- 独立重建提交：[jq-source `d7b6cf01b136f30f93cf2d7ae8233308cdbbcc9f`](https://github.com/recipkg/jq-source/tree/d7b6cf01b136f30f93cf2d7ae8233308cdbbcc9f)，验证提交：[standard-library-verification `c19b3b8`](https://github.com/recipkg/standard-library-verification/tree/c19b3b8794236e297c298cedd5d7099d4447380a)。19 项主合同与 80 项定向测试全量通过；补入小数下标样本后，本轮 4 项 delpaths 测试再次通过。参考端报告 jq 1.8.2，构建来源未核实。

- SQL 风格大写 `INDEX`、`JOIN` 与 `IN` 的定义依据固定版本 [jq 1.8 手册 SQL-Style Operators](https://jqlang.org/manual/v1.8/)；本地有限样本见 [`SQL-OBSERVED.md`](SQL-OBSERVED.md) 与验收用例 29。小写 `in` 仍是独立的既有成员过滤器。
- 独立重建提交：[jq-source `d4f116a7b9af6b70c1270253c97eca8269c928b9`](https://github.com/recipkg/jq-source/tree/d4f116a7b9af6b70c1270253c97eca8269c928b9)，验证提交：[standard-library-verification `d1cd01e`](https://github.com/recipkg/standard-library-verification/tree/d1cd01e50edced74232428d5b5740eedae87f558)。85 项定向测试全量通过；更新复合索引键样本后，本轮 5 项 SQL 操作测试再次通过。参考端 `/usr/bin/jq` 报告 1.8.2，构建来源未核实。

- `del(path_expression)` 的对象/数组删除语义依据固定版本 [jq 1.8 手册](https://jqlang.org/manual/v1.8/)并以本机样本核对；有限行为及路径边界见 [`DELPATH-OBSERVED.md`](DELPATH-OBSERVED.md) 与验收用例 30。
- 独立重建提交：[jq-source `d2356495aa8991287fc9e5e97343e4679bcb709b`](https://github.com/recipkg/jq-source/tree/d2356495aa8991287fc9e5e97343e4679bcb709b)，验证提交：[standard-library-verification `e629c9a`](https://github.com/recipkg/standard-library-verification/tree/e629c9ad06c5e85b9b5ba9f4503a84612120f80f)。89 项定向测试全量通过；本轮 4 项 `del` 测试含 100 组固定种子路径删除对照。参考端报告 jq 1.8.2，构建来源未核实。
