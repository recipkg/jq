# jq JSON 处理工具（提取中）

## 目标

提供命令行工具，通过过滤表达式读取 JSON 数据流并输出结果。按目标功能实现可观察行为，不限定实现语言；此草案只提炼少数命令和表达式，不能声称完整 jq 语言或兼容性。固定来源为上游 jq 1.8.2 手册、部分测试和本机相同版本小样本。

## 基础输入、过滤与输出

- 默认将输入解析为 JSON 值序列；过滤器对每个输入值独立运行，可产生零个、一个或多个结果。相邻值的边界不能一律假设必须有空白：具体样本见 `JSON-PARSING-OBSERVED.md`。通常将结果写 stdout，默认 JSON 格式化输出。`.` 为恒等过滤器；`.foo` 取对象字段，缺少字段返回 null；`.[]` 迭代数组元素或对象值，空数组产生零个输出；`|` 将左侧每个输出依次传给右侧，`,` 将同一输入交给左右过滤器并顺序连接输出。错误类型和所有表达式优先级需补齐。
- `-n` 不自动消费输入，以 null 运行一次，但过滤器中的 `input` 或 `inputs` 仍可显式读取标准输入或文件；`-s` 将全部 JSON 输入聚合为数组运行一次；`-R` 按行作为原始字符串输入，与 `-s` 组合时整个输入成为一个字符串。`-c` 紧凑输出；`-r` 对字符串结果直接输出原始文本、其余结果仍按 JSON 输出。`--arg name value` 绑定字符串，`--argjson name JSON-text` 绑定解析后的 JSON 值。按照使用者的 shell 引用过滤器，不把 shell 的变量展开当 jq 行为。
- `-e` 根据最后输出值设置退出码：非 false/null 为 0，false/null 为 1，无有效结果为 4。普通用法/系统错误和编译错误文档列出 2、3；不同运行时错误按指定版本实际测试，不能将所有失败统一为某个码。诊断写 stderr；JSON、Unicode、精度、流式输入、格式与错误细节均需进一步测试。
- 按 `IO-OBSERVED.md` 的范围处理多个输入文件、标准输入、`inputs`、原始输入、NUL 分隔输出、流式路径记录及错误。已经输出的有效结果不得因后续错误而悄悄消失；区分缺失文件、解析失败和 `--stream-errors` 产生的错误记录。样本外的 I/O 和错误语义仍待补齐。
- 按 `JSON-PARSING-OBSERVED.md` 处理已观察的重复键、数字和字符串边界、先输出再报错及 `--seq` 恢复；区分 decnum 构建与其他构建，不将一般严格 JSON 解析器当作已验证的兼容实现。完整解析语义尚缺。

## 路径赋值与错误处理

按 `ASSIGNMENT-OBSERVED.md` 实现观察到的普通赋值 `=`、更新赋值 `|=`、多路径和输出流行为。特别区分右侧看到的输入、多个输出、`empty` 和逗号括号；修改产生新结果，不能丢失其他字段或把多个结果合并为一次输出。按同一文件实现已观察的可选操作 `?` 和 `try ... catch ...` 样本。更广泛路径、嵌套写入、类型错误和异常传播仍须补齐，不能将这些样本当作完整语言规范。

## 条件和比较

按 `CONDITIONALS-OBSERVED.md` 实现真假值、条件结果流、`//`、布尔短路和已记录的比较规则。保留每个结果及其顺序；不要将宿主语言对空字符串、零或空容器的真假判断直接用于 jq，也不要将整个左侧结果流的回退错误地改成逐值回退。复杂语法、精度和未观察的错误边界仍需补齐。

## 绑定、函数和归约

按 `PROGRAMMING-OBSERVED.md` 实现已观察的变量绑定、解构、词法作用域、函数参数和递归、`reduce` 与 `foreach`。区分过滤器参数和变量参数，保留多结果的顺序和错误前已经输出的结果；多值、空值及作用域边界不得简单按宿主语言循环推断。其他语言功能和复杂边界仍需补齐。

## 集合映射与键查询

按 `COLLECTIONS-OBSERVED.md` 实现 `map`、`map_values`、`keys`、`keys_unsorted`、`has` 和 `in` 的已观察行为。区分多结果与首结果、空结果删除、键顺序、键与容器类型和错误前已输出结果；未测的极端索引和更广泛的内建函数仍须提取。

## 排序、分组和聚合

按 `AGGREGATION-OBSERVED.md` 实现 `sort_by`、`group_by`、`unique`、`unique_by`、`min`、`max`、`min_by`、`max_by` 和 `add` 的已观察行为。比较完整过滤器结果流，区分键相等时各操作保留的元素，并保留空输入和错误行为；未观察的完整排序及其他聚合能力仍须补齐。

## 字符串过滤器

按 `STRING-FILTERS-OBSERVED.md` 实现有限样本中的 `startswith`、`endswith`、`ltrimstr`、`rtrimstr`、`trimstr`、`ascii_downcase`、`ascii_upcase`、单参数字面分隔符 `split` 和 `join`。前缀/后缀与大小写过滤器按 jq 的字符串语义处理；`ascii_*` 只转换 ASCII 字母。空输入字符串的 `split` 返回空数组，空分隔符按 Unicode 码点切分。`join` 接受数组或对象值，null 转为空串，数字和布尔值转为文本；数组或对象成员不支持。对照验收文件中的错误和多输入样本。两参数正则切分、Unicode 大小写转换、空白 trim 过滤器、完整格式与所有类型组合仍未覆盖。

按 `REGEX-OBSERVED.md` 实现 `test(regex)` 与 `test(regex; flags)`，以 Oniguruma 的 Perl NG 语法处理 Unicode 正则。支持 jq 手册列出的 `g/i/m/n/p/s/l/x` 标志；`g` 对布尔测试无额外影响。单参数还接受字符串或数组简写，数组按前两项取正则和标志；保留正则及标志生成器的输出顺序。当前运行时通过系统 Oniguruma 库提供正则引擎；库不可用时清晰报错。不要据此宣称 `match`、`capture`、`scan`、正则 `split` 或替换过滤器已经实现。

按 `MATCH-OBSERVED.md` 实现 `match(regex)` 与 `match(regex; flags)`：无 `g` 时输出首个匹配对象，有 `g` 时按顺序输出全部非重叠匹配。对象包含 `offset`、`length`、`string`、`captures`；偏移和长度按 jq 观察到的 UTF-8 码点计数。每个捕获项包含 `offset`、`length`、`string`、`name`；未匹配捕获的偏移为 -1、长度为 0、字符串为 null，未命名组的名称为 null。单参数接受字符串或数组简写。零宽全局匹配按观察到的 jq 行为推进一个字节，包含多字节文本时可能产生重复的码点偏移。保留模式/标志生成器顺序，并对照记录中的显式错误。再按 `CAPTURE-SCAN-OBSERVED.md` 实现 `capture(regex)`、`capture(regex; flags)`、`scan(regex)` 与 `scan(regex; flags)`：`capture` 返回命名捕获对象，只保留命名组，未参与匹配的命名组值为 null；`g` 允许依次输出多个对象，单参数支持字符串或 `[regex, flags]` 简写。`scan` 总是全局扫描，无捕获组时输出完整匹配字符串，有捕获组时按组顺序输出字符串/null 数组；其单参数不接受数组简写；两参数 flags 可为字符串或 null，其他非字符串值按观测报错。保留参数生成顺序，并按观测文档验收输入、正则和标志错误。再按 `SPLIT-OBSERVED.md` 实现 `split(str)` 的既有字面分隔符行为、`split(regex; flags)` 的正则数组结果，以及 `splits(regex)`/`splits(regex; flags)` 的正则分段流；后两者都按非重叠全局匹配分段，捕获组不作为分段输出。正则切分 flags 在 `g` 前拼接，null 按无额外 flags 处理，正则流在 flags 流外层；保留空分段和零宽匹配的已观察结果。再按 `SUB-GSUB-OBSERVED.md` 实现有限的 `sub(regex; replacement)`、`sub(regex; replacement; flags)`、`gsub(regex; replacement)` 与三参数形式：sub 默认只替换首个匹配，gsub 强制全局；替换过滤器在命名捕获对象上求值，字符串替换支持有限表达式插值。保留替换输出流按位置合并、空输出、null 加法、flags/参数生成顺序及显式错误的观测样本。只实现记录并测试的语法范围，不得据此声称通用字符串插值或完整正则兼容。

## 类型与 JSON 转换

按 `CONVERSION-OBSERVED.md` 实现已测试范围内的 `type`、`tostring`、`tojson` 和 `fromjson`。`type` 应返回六类 JSON 值的 jq 类型名；`tostring` 保持字符串不变、其他值编码为紧凑 JSON；`tojson` 将所有值（包括字符串）编码为紧凑 JSON 文本。`fromjson` 只接受字符串，并对恰好一个 JSON 值产生结果；空文本、无效文本或多个 JSON 文本必须报错。完整解析、数值精度、重复键策略和诊断信息仍待提取，不得将这些样本扩展成全解析器兼容声明。

按 `TYPE-FILTERS-OBSERVED.md` 实现 `arrays`、`objects`、`iterables`、`booleans`、`numbers`、`strings`、`nulls`、`values` 与 `scalars`。它们不带参数，只在当前值符合类型条件时原样输出；不匹配时不输出。`values` 保留 false 但丢弃 null；`scalars` 保留 null、布尔、数字和字符串，排除数组与对象；`iterables` 只保留数组和对象。不得把它们实现成容器迭代器。其他类型组合与完整 jq 内建函数集仍待提取。

## 容器包含与数组反转

按 `CONTAINMENT-OBSERVED.md` 实现 `contains`、`inside` 和数组 `reverse` 的有限行为。字符串按子串包含；数组中的每个目标元素需在容器某一元素中递归匹配；对象按键和值递归包含。标量和不同类型的错误/假值处理需遵从验收样本，尤其不要把顶层类型错配与容器内部元素错配合并处理。`reverse` 仅声明支持数组。复杂混合类型、完整递归边界和性能仍待提取。

## 子串和数组位置

按 `INDEX-OBSERVED.md` 实现字符串与数组上的 `indices`、`index`、`rindex`。字符串位置按 Unicode 码点计算并允许重叠命中；数组可查标量元素或连续子数组。空查找项按已观察样本返回无命中，`indices` 在无命中时返回空数组，`index`/`rindex` 返回 null。null、对象及不兼容参数的行为只按已有样本实现，不推导为全部类型合同。

## 长度与数组扁平化

按 `ARRAY-VALUES-OBSERVED.md` 实现已观察的 `length`：字符串按 Unicode 码点数、数组按元素数、对象按键值对数、null 为 0、数字取绝对值；布尔值报错。`flatten` 将数组中的嵌套数组展开；可选的非负整数深度限制展开层数。对象输入按观察样本遍历其值。小数深度当前不支持，必须显式失败；不要把它宣称为与上游兼容。其他接收类型、递归极限和精确错误仍待验证。

按 `UNICODE-OBSERVED.md` 实现字符串 `utf8bytelength`、`explode` 和数组 `implode`。长度按 UTF-8 字节，`explode` 输出 Unicode 码点，`implode` 对数值码点截断小数并对越界或代理项替换为 U+FFFD。对非字符串、非数组及非数字元素明确报错。更极端的数值格式、异常 Unicode 与诊断文本尚未覆盖。

按 `GENERATOR-OBSERVED.md` 实现数组取值形式的 `first`、`last`、`nth(n)`，以及消费过滤器输出流的 `first(expr)`、`last(expr)`、`nth(n; expr)` 和 `isempty(expr)`。数组索引形式沿用已观察的下标行为，包括负下标与未命中 null；生成器形式返回第一个/最后一个/第 n 个输出，越界不产生输出，负 n 报错。`isempty` 在遇到首个输出时即返回 false。复杂多输出索引、精确诊断和错误时序仍待验证。

按 `PREDICATES-OBSERVED.md` 实现 `any` 和 `all` 的无参数、单条件、生成器加条件形式。无参数形式检查当前数组或对象的值；条件形式逐项计算；双参数形式从给定生成器取值。空输入分别返回 false 和 true；一旦发现决定结果的条件就停止。其他输入、条件流及错误传播只按样本实现。

## 模块和库搜索

按 `MODULES-DOCUMENTED.md` 的阶段记录实现模块功能。第一阶段必须支持重复的 `-L directory`、在搜索目录中查找 `path.jq` 与 `path/path.jq`、`include "path";`、递归 include、首个搜索目录优先，以及样本显示的候选顺序；缺失模块应明确编译失败。只对已经运行对照的本机 jq 1.8.2 样本作兼容声明，并保留其二进制构建来源未核实这一限制。暂不支持的 `import`、JSON 模块、默认路径、`module` 元数据和 `modulemeta` 必须继续列为缺口，不能静默忽略或宣称兼容。jq 模块属于目标软件的运行时能力，不是 Recipkg 配方依赖。

## 验收和依赖

`ACCEPTANCE.md` 定义可在隔离目录重现的输入、输出、退出状态；只把实际对照过的样本标为已观察。`quality` 是命令行和性能测量基线，不替代 jq 过滤语言与运行时。标准库尚无可直接满足本语义的 JSON 解析器配方；不要将源项目或宿主 jq 冒充递归实现依赖。模块系统只有有限的 `include` 样本；公共 C API、更多内建函数、正则、数值精度和完整语言语义仍需补齐。


按 `RANGE-OBSERVED.md` 实现 `range` 的已观察范围：支持 `range(stop)`、`range(start; stop)` 和 `range(start; stop; step)`；结果包含起点并排除边界，步长方向与区间不符时为空，零步长样本为空。小数参数按有限样本产生小数序列。测试中的参数为有限数值；非法参数应明确失败。不要把有限差分样本扩展成所有多值参数、极端精度、资源上限或错误时序的兼容声明。


按 `ENTRIES-OBSERVED.md` 实现有限的 `to_entries`、`from_entries` 和 `with_entries(f)` 行为。`to_entries` 把对象的键值或数组的索引和值转换成条目数组；`from_entries` 接受文档记录的键名别名，并将条目折叠成对象；`with_entries(f)` 对每个条目应用过滤器，再还原对象。保留已观察的顺序、重复键和空结果行为。非对象条目、非字符串键、错误诊断、复杂生成器组合等仍须按观测记录处理，不得把有限样本扩大成完整类型兼容声明。


按 `TRANSPOSE-OBSERVED.md` 实现已观察的 `transpose` 数组行为：转置二维数组，按最长行补齐较短行，缺失单元格为 null；空数组/零宽矩阵产生空数组，null 行按空行处理，非 null 的非数组行报错。保留有限合同的范围，不要推断未测试的其他输入类型、极端维度、资源边界或完整错误文本。


按 `GETPATH-OBSERVED.md` 实现有限 `getpath(PATHS)` 行为：路径必须是字符串键/数值索引组成的数组；PATHS 表达式可以生成多个路径，依次读取对象键和数组下标并按顺序输出结果。已观察的缺失对象键、越界数组索引和到达 null 后的剩余路径返回 null；负下标及小数截断按记录样本处理。无效路径或与当前值不匹配的路径组件应失败。该合同仅覆盖读取，不代表 `path`、`setpath`、`delpaths` 或所有多值/错误语义。


按 `SETPATH-OBSERVED.md` 实现有限 `setpath(PATHS; VALUE)` 行为：路径为空时替换根值；路径经过 null 时按下一段的字符串键/数值索引创建对象或数组；正数组下标越界时以 null 扩展，负下标仅在已有范围内从尾部计数。PATHS 表达式生成多个路径时，对每条路径分别从原输入更新，并依产生顺序输出。对已测无效路径/容器组合明确失败。不得把有限合同外推为任意多结果和值的组合、极端索引的安全资源上限或完整路径操作兼容。


按 `PATHS-OBSERVED.md` 实现已观察的 `path(expr)`、`paths` 和 `paths(f)`：`path` 为支持的字段/索引/迭代表达式返回路径数组；`paths` 深度遍历对象与数组中的子值并逐个输出路径（不包含根路径）；`paths(f)` 仅输出对路径末端值计算 f 后存在 truthy 结果的路径。保留样本中的顺序、空结果和缺失字段行为。其他路径语法、标量边界和完整错误时序仍未提取。

按 `WALK-OBSERVED.md` 实现有限 `walk(f)`：对数组元素和对象值递归应用 f，再对重建后的当前数组/对象应用 f；标量直接应用 f。保留观察到的后序转换和数组子元素多结果展开行为。递归深度、性能、错误边界以及其他对象多结果组合仍未知，不要外推为完整 jq 兼容性。

按 `RECURSE-OBSERVED.md` 实现有限 `recurse` 遍历：无参数时先输出根值，再按 `.[]?` 深度优先遍历容器子值；`recurse(f)` 对 f 产生的每个值继续递归；`recurse(f; condition)` 仅对条件 truthy 的后继值递归，且不输出未通过条件的值。遵循样本中的流顺序。不要将有限终止样本外推为无限递归、深度安全或完整错误兼容。

按 `DELPATHS-OBSERVED.md` 实现有限 `delpaths(PATHS)` 行为：路径列表同时应用于同一原始值；数组删除按原始索引处理，不因前序删除而移位。支持已观察的对象键、嵌套路径、重复路径、缺失键、越界索引、负/小数数组索引，以及空路径删除根值为 null。无效路径形状及与对象/数组/标量不匹配的组件应失败。不得把有限样本外推为完整错误时序、多结果语义或资源安全保证。

按 `SQL-OBSERVED.md` 实现有限的大写 `INDEX(stream; expr)`、`JOIN` 两/三/四参数形式和大写 `IN` 单/双流成员判断。索引表达式可产生多个键，重复键以后值覆盖；`JOIN` 未命中时配对值为 null，双参数形式将配对收集成数组。对象键转换、输出形状、空流和样本内顺序须与记录一致。不要将大写 `IN` 与小写成员过滤器 `in` 混为一谈。更广泛的流求值时序、错误和资源边界仍待提取。

按 `DELPATH-OBSERVED.md` 实现已观察的 `del(path_expression)`：用支持的路径语法找出所有目标，并按原始对象/数组一次性删除；路径重叠时父级删除优先，未命中的路径不改变输入，删除根路径返回 null。`del(.[])` 对象/数组样本也须通过。不要外推到尚未支持的复杂路径表达式或完整错误语义。

按 `PICK-OBSERVED.md` 实现有限 `pick(path_expressions)`：从原输入读取所有已选路径并构造稀疏对象/数组；缺失对象键和值为空的数组位置填 null，数组索引保留原位置，空迭代选择产生 null。多个/重叠路径遵循已观察结果。负数组索引、切片、复杂路径与错误边界不在本合同内。

按 `LIMIT-SKIP-OBSERVED.md` 实现有限 `limit(n; expr)` 与 `skip(n; expr)`：前者至多输出 expr 的前 n 项，后者跳过前 n 项并继续输出；保留顺序、零值、空计数流和多计数值样本。支持用有限 `limit` 截取无限 expr 流。遵循验收中记录的小数边界和负数/已测非法类型失败行为；不要外推为完整数值边界、错误诊断、资源控制或 jq 兼容性。

按 `WHILE-UNTIL-OBSERVED.md` 实现有限 `while(cond; update)` 与 `until(cond; next)`：`while` 在条件为真时先输出当前值，再递归处理更新结果；`until` 在条件为真时输出当前值，否则递归处理 next 的各个结果。遵循条件真假、多结果分支及顺序样本。仅声明有限测试覆盖的行为；不要宣称无限循环安全、尾调用优化、内存/深度上限或完整错误兼容。

## 数值函数

按 `ROUNDING-OBSERVED.md` 实现 `ceil`、`floor`、`round`、`trunc`；按 `MATH-OBSERVED.md` 实现已记录的数学函数。单输入过滤器包括 `acos`、`acosh`、`asin`、`asinh`、`atan`、`atanh`、`cbrt`、`cos`、`cosh`、`erf`、`erfc`、`exp`、`exp10`、`exp2`、`expm1`、`fabs`、`gamma`、`j0`、`j1`、`lgamma`、`log`、`log10`、`log1p`、`log2`、`logb`、`nearbyint`、`rint`、`significand`、`sin`、`sinh`、`sqrt`、`tan`、`tanh`、`tgamma`、`y0`、`y1`、`frexp`、`modf`；`frexp` 和 `modf` 返回 `[函数返回值, C 输出参数]`。双参数过滤器包括 `atan2`、`copysign`、`drem`、`fdim`、`fmax`、`fmin`、`fmod`、`hypot`、`jn`、`ldexp`、`nextafter`、`nexttoward`、`pow`、`remainder`、`scalb`、`scalbln`、`yn`；三参数函数为 `fma`。使用 Python 标准库 `ctypes` 调用可用的宿主 C 数学库，并按 C 签名转换整数参数；缺失的 C 符号应报函数不可用错误。函数参数流按 jq 的生成顺序组合。输入须为数字，遵循记录的负零、定义域错误、奇点、溢出和样本输出。`gamma`、`lgamma` 的目标行为和底层系统数学库有关；四个 Bessel 函数调用宿主 C 数学库；缺少对应符号时显式报不可用。有限样本之外不要推断精确诊断、极端精度或跨平台一致性。
