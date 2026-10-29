# jq 验收用例（上游本机样本；重建仍未通过）

用隔离环境内待验收的 `jq` 运行，检查 stdout、stderr 和退出码：

1. 输入 `{"items":[{"n":1,"name":"a"},{"n":2,"name":"b"}]}`，`jq -c '.items[] | select(.n > 1) | .name'` 输出 `"b"` 后接换行，退出 0。
2. 输入 `{"x":"hello"}`，`jq -r '.x'` 输出 `hello` 后接换行，退出 0；输入 `1\n2\n3\n`，`jq -s length` 输出 `3` 后接换行。
3. `jq -n --arg n 123 --argjson x 123 '[$n,$x]'` 输出 JSON 数组，解析值为 `["123",123]`；不因空格和缩进差异误判 JSON 结构。
4. `jq -ne true` 退出 0；`jq -ne false` 和 `jq -ne null` 退出 1；`jq -ne empty` 退出 4 且 stdout 为空。以无效 JSON `{invalid}` 输入 `jq -c '.'`，本机 jq 1.8.2 返回 5、错误写 stderr；记录平台和完整诊断。
5. 对所有声称支持的过滤器、数据类型、函数、错误、精度、模块、文件和平台补充对照与验收。以上几个样本不足以证明完整兼容 jq。
6. 按 `ASSIGNMENT-OBSERVED.md` 对照 `=` 与 `|=` 的输入、多输出和空输出，以及多路径、逗号优先级、可选操作和捕获错误。对每个样本检查输出序列、stderr 和退出码；以 [standard-library-verification](https://github.com/recipkg/standard-library-verification) 的 `JqSamples` 执行重复对照。
7. 按 `IO-OBSERVED.md` 测试路径流、无效数据的部分输出与错误模式、`inputs` 消费、多个文件及标准输入、原始输入和 NUL 输出；分别核对 stdout、stderr 和退出码。成功样本不代表完整流式 API 或全部文件错误已验证。
8. 按 `CONDITIONALS-OBSERVED.md` 测试真假值、条件多输出和空输出、`//` 的结果流与括号优先级、布尔短路、多输出顺序及比较。使用 `JqSamples.test_conditions_alternatives_and_comparisons` 比较 JSON 类型、结果顺序、stderr 和退出码；不将这些样本视为完整语言验收。
9. 按 `JSON-PARSING-OBSERVED.md` 用字节输入验证重复键、宽松数字、Unicode、无效输入、部分输出和 `--seq` 恢复，并在 decnum 构建验证大数字面值、比较及算术。记录目标的 `have_decnum`；未启用时精度样本跳过，不能称为通过。
10. 按 `PROGRAMMING-OBSERVED.md` 和 `JqSamples.test_bindings_functions_and_reductions` 检查绑定输入、作用域、解构、函数参数、递归和归约的多结果、空结果及错误。该样本集不是完整语言验收。


11. 按 `COLLECTIONS-OBSERVED.md` 对照 `map`、`map_values` 的多结果、空结果、数组与对象、错误及惰性；对照 `keys` 和 `keys_unsorted` 的对象及数组，以及 `has`、`in` 的多结果、空值、数组小数/负下标、类型错误与错误前输出。明确记录上游构建及实际比较范围。

12. 按 `AGGREGATION-OBSERVED.md` 核对多值与空键、键相同的元素顺序、排序/分组/去重、最大最小值、`add` 的空输入、不同类型和生成器、错误和不可迭代输入；记录固定上游和重建程序的实际输出。

13. 按 `MODULES-DOCUMENTED.md` 检查模块第一阶段：用 `-L` 从临时搜索目录加载 `include "math/step"`，分别覆盖 `math/step.jq` 与仅存在 `math/step/step.jq` 的布局；验证模块定义可调用、模块可递归 include、多个 `-L` 按记录的优先级搜索，并验证缺失模块以编译错误退出。暂不把 `import`、JSON 模块、默认搜索路径、`module` 元数据或 `modulemeta` 记作通过。使用 `standard-library-verification/tests/test_jq_modules.py`，与本机 jq 1.8.2 对照时记录二进制构建来源限制。
14. 按 `STRING-FILTERS-OBSERVED.md` 和 `standard-library-verification/tests/test_jq_strings.py` 验收字符串样本：前缀/后缀判断和裁剪、ASCII 大小写转换、字面分隔符切分、空字符串及空分隔符、数组/对象 join、null/数值/布尔转换和类型错误。设置 `RECIPKG_TEST_JQ_REFERENCE` 对照预期 jq 1.8.2，运行 120 个固定种子字符串、100 个混合值数组和 100 个对象差分样本；对照目标的版本及构建来源需单独记录。该组样本不覆盖正则切分、完整 Unicode/格式语义或全部 jq 内建函数。
15. 按 `CONVERSION-OBSERVED.md` 和 `standard-library-verification/tests/test_jq_conversion.py` 验收六类 jq 类型名、字符串与紧凑 JSON 文本的 `tostring`/`tojson` 差异，以及 `fromjson` 对单个值、Unicode、无效文本、空文本、多值文本和非字符串输入的处理。配置 `RECIPKG_TEST_JQ_REFERENCE` 对照 jq 1.8.2，并记录其二进制构建来源限制；固定种子差分覆盖 90 个标量、数组和对象输入及其 JSON 文本。该样本不代表完整 JSON 解析兼容。
16. 按 `CONTAINMENT-OBSERVED.md` 和 `standard-library-verification/tests/test_jq_containment.py` 验收数组 `reverse` 与 `contains`/`inside`。检查字符串子串、数组逐项递归包含、对象键值子集、数值与布尔标量，以及顶层类型/布尔不匹配错误；同时检查容器内部不匹配按样本返回 false。设置 `RECIPKG_TEST_JQ_REFERENCE` 对照 jq 1.8.2，运行 120 组固定种子容器/目标差分。该样本不覆盖所有复杂混合类型或递归边界。
17. 按 `INDEX-OBSERVED.md` 和 `standard-library-verification/tests/test_jq_indices.py` 验收 `indices`、`index`、`rindex`：字符串重叠子串、Unicode 码点位置、数组标量和连续子数组、空查找项、未命中、观察到的 null/object 行为及参数错误。配置 jq 1.8.2 参考端对比 60 组字符串和 60 组数组输入，并分别检查三个过滤器。未覆盖的接收值/参数组合不得记为通过。
18. 按 `ARRAY-VALUES-OBSERVED.md` 和 `standard-library-verification/tests/test_jq_array_values.py` 验收 `length` 与 `flatten`：检查 Unicode 字符串长度、数组与对象长度、null、数字绝对值、布尔错误、完整及有限深度展开、对象值输入、非数组/对象输入和负深度。配置 jq 1.8.2 参考端对比 80 组嵌套数组和深度 0–4，并覆盖已声明的长度样本。仅支持非负整数深度；小数深度显式失败且不属于兼容范围。
19. 按 `UNICODE-OBSERVED.md` 和 `standard-library-verification/tests/test_jq_unicode.py` 验收 `utf8bytelength`、`explode`、`implode`：覆盖 ASCII、多字节码点、组合字符、NUL、补充平面字符、反向转换、分数截断、越界/代理码点替换及类型错误。设置 jq 1.8.2 参考端对照 100 个固定种子字符串与 100 组合法码点数组；该样本不涵盖完整 Unicode 或任意巨大数值边界。
20. 按 `GENERATOR-OBSERVED.md` 和 `standard-library-verification/tests/test_jq_generators.py` 验收数组形式与生成器形式的 `first`、`last`、`nth`，以及 `isempty`。覆盖 null/空数组、负数组索引、负生成器索引错误、越界行为、empty 生成器和 100 组固定种子数组上的 11 种过滤器对照。该样本不覆盖所有复杂生成器或错误时序。
21. 按 `PREDICATES-OBSERVED.md` 和 `standard-library-verification/tests/test_jq_predicates.py` 验收 `any`/`all` 的直接 iterable、条件和生成器/条件形式。覆盖数组、对象、空输入、非 iterable 错误以及 100 组固定种子值数组上的六种过滤器对照；不将有限短路样本外推为所有条件流和错误时序。


22. 按 `RANGE-OBSERVED.md` 与 `standard-library-verification/tests/test_jq_range.py` 验收 `range(stop)`、`range(start; stop)`、`range(start; stop; step)`，覆盖升序、降序、正负和零步长、小数边界、非法类型，以及 100 组固定种子整数范围差分。参考端为本机 `/usr/bin/jq`（报告 jq 1.8.2，构建来源未核实）；有限样本不覆盖多值参数组合、极端精度、完整错误时序或资源边界。


23. 按 `ENTRIES-OBSERVED.md` 与 `standard-library-verification/tests/test_jq_entries.py` 验收对象/数组 `to_entries`、`from_entries` 的 `key`/`Key`/`name`/`Name` 和 `value`/`Value` 字段、重复键、缺省值，以及用 `with_entries` 更新或筛选条目。设置本机 `/usr/bin/jq` 参考端比较 100 个对象和 100 个数组；参考二进制报告 jq 1.8.2，但构建来源未核实。该有限样本不涵盖所有输入类型、错误时序或完整过滤器兼容性。


24. 按 `TRANSPOSE-OBSERVED.md` 与 `standard-library-verification/tests/test_jq_transpose.py` 验收矩形、锯齿状、空行和 null 行；短行补 null，空矩阵输出空数组，非数组顶层及非数组非 null 行失败。参考端为本机 `/usr/bin/jq`（报告 1.8.2，构建来源未核实），差分覆盖 100 组固定种子矩阵；不据此宣称完整类型、错误或资源兼容。


25. 按 `GETPATH-OBSERVED.md` 与 `standard-library-verification/tests/test_jq_getpath.py` 验收嵌套对象/数组路径、缺失键、越界与负数组下标、小数索引截断、null 中断以及非法路径/组件；配置本机 `/usr/bin/jq` 参考端对照 100 组固定种子路径。参考端报告 jq 1.8.2，构建来源未核实。该用例只覆盖有限读取路径，不代表写入路径或全部错误时序。


26. 按 `SETPATH-OBSERVED.md` 与 `standard-library-verification/tests/test_jq_setpath.py` 验收 null 下自动创建对象/数组、路径写入、数组扩展补 null、负下标、根替换和非法路径/容器组合；以本机 `/usr/bin/jq`（报告 1.8.2，构建来源未核实）对照 100 组固定种子更新。极端索引/资源边界、多结果顺序和完整错误兼容仍未验证。
