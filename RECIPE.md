# jq 配方（提取中）

```toml
format = "recipkg/1"

[package]
name = "jq"
version = "0.1.41"
kind = "software"
description = "命令行 JSON 查询与转换工具 jq 的重建配方草案。"
entry = "BUILD_PROMPT.md"

[dependencies.quality]
git = "https://github.com/recipkg/recipkg.git"
rev = "41e5b55c6612a9a5a10e499b5ace6a1a3fed0f2b"
subdir = "recipes/software-quality"
```

上游 [jqlang/jq](https://github.com/jqlang/jq/tree/jq-1.8.2)，固定标签 `jq-1.8.2`，提交 `34f7186b86743a083a589741b6cea95293524108`。上游 `COPYING` 对程序、文档和内含第三方材料分别说明许可；程序为 MIT，文档为 CC BY 3.0，详见源文件。本配方尚未完整提取，独立重建仍在进行。

模块文件、搜索路径、`include`/`import`、模块元数据及其未验证边界见 [`MODULES-DOCUMENTED.md`](MODULES-DOCUMENTED.md)。`-L` 与 `include` 目前只覆盖文档中标明的有限本机 jq 1.8.2 样本；模块系统整体仍未提取或重建完成，导入、JSON 模块、默认路径和元数据仍是缺口。

字符串过滤器的有限提取和重建样本见 [`STRING-FILTERS-OBSERVED.md`](STRING-FILTERS-OBSERVED.md)。配方仍未覆盖 jq 完整字符串 API。

类型检查和 JSON 文本转换的有限样本见 [`CONVERSION-OBSERVED.md`](CONVERSION-OBSERVED.md)。完整 JSON 解析语义仍未覆盖。

容器包含、`inside` 和数组 `reverse` 的有限合同见 [`CONTAINMENT-OBSERVED.md`](CONTAINMENT-OBSERVED.md)；递归边界与类型错误仍有未验证部分。

字符串与数组的 `indices`、`index`、`rindex` 有限样本见 [`INDEX-OBSERVED.md`](INDEX-OBSERVED.md)；其他输入类型组合仍未完整提取。

`length` 与 `flatten` 的有限样本见 [`ARRAY-VALUES-OBSERVED.md`](ARRAY-VALUES-OBSERVED.md)；完整深度参数和类型边界仍有缺口。

UTF-8 字节计数与 `explode`/`implode` 转换的有限 Unicode 样本见 [`UNICODE-OBSERVED.md`](UNICODE-OBSERVED.md)；其他错误和极端码点仍待提取。

输出流的 `first`、`last`、`nth` 与 `isempty` 有限样本见 [`GENERATOR-OBSERVED.md`](GENERATOR-OBSERVED.md)；复杂生成器和错误时序仍需扩展。

`any`、`all` 对可迭代值、条件和显式生成器的有限样本见 [`PREDICATES-OBSERVED.md`](PREDICATES-OBSERVED.md)；条件多输出与完整错误时序尚未提取。


`range` 的有限样本见 [`RANGE-OBSERVED.md`](RANGE-OBSERVED.md)：覆盖单参数、双参数和三参数形式、正负步长、小数边界、空范围和零步长。生成器参数的多值组合及数值极端仍未完整提取。


对象与数组条目转换的有限样本见 [`ENTRIES-OBSERVED.md`](ENTRIES-OBSERVED.md)：覆盖 `to_entries`、`from_entries` 与 `with_entries`，包括键名别名、键/值更新和筛选。复杂类型与完整错误时序仍有缺口。


数组转置的有限合同见 [`TRANSPOSE-OBSERVED.md`](TRANSPOSE-OBSERVED.md)：包括不齐行补 null、空行和 null 行；其他异常输入与资源边界仍待验证。


路径读取的有限合同见 [`GETPATH-OBSERVED.md`](GETPATH-OBSERVED.md)：覆盖对象键、数组索引、缺失值、部分错误边界，以及由逗号生成器提供多个路径时按顺序输出读取结果。写路径的完整行为仍未提取。


`setpath(PATHS; VALUE)` 的有限写入合同见 [`SETPATH-OBSERVED.md`](SETPATH-OBSERVED.md)：涵盖路径自动建容器、扩展数组、负下标与无效组合。极端索引和资源上限仍待核查。


路径生成的有限行为见 [`PATHS-OBSERVED.md`](PATHS-OBSERVED.md)：包括 `path(expr)`、递归 `paths` 和按条件筛选路径。复杂语法与遍历错误边界仍待提取。

多路径删除的有限行为见 [`DELPATHS-OBSERVED.md`](DELPATHS-OBSERVED.md)：`delpaths(PATHS)` 同时删除对象键与数组元素，数组路径按原始索引解释。重复路径、缺失路径、负/小数索引与错误边界仍只覆盖有限样本。

SQL 风格流操作的有限行为见 [`SQL-OBSERVED.md`](SQL-OBSERVED.md)：包括大写 `INDEX`、三种 `JOIN` 形式与大写 `IN` 的流成员判断。多结果顺序、错误时序和资源边界仍未完整提取。

路径表达式删除的有限行为见 [`DELPATH-OBSERVED.md`](DELPATH-OBSERVED.md)：`del(path_expression)` 覆盖对象字段、数组下标、有限多值选择器与迭代器。复杂表达式与完整错误边界仍待提取。

路径投影的有限行为见 [`PICK-OBSERVED.md`](PICK-OBSERVED.md)：`pick(path_expressions)` 复制所选对象/数组路径并以 null 补缺失字段和稀疏数组位置。负数组索引、切片和复杂路径仍未纳入。
