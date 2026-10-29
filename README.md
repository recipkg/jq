# jq · Recipkg

**命令行 JSON 查询与转换：提炼过滤表达式、模块加载、流式输入、赋值和错误行为。**

![从软件与依赖整理配方，再交给 Agent 重建](https://raw.githubusercontent.com/recipkg/recipkg/main/assets/recipkg-cover.png)

本仓库保存软件重建配方，不是上游 jq 源码或官方发行版。上游版本、固定提交和许可证见 [RECIPE.md](RECIPE.md)。配方仍在提取中；已观察行为、未知边界和重建状态见 [COVERAGE.md](COVERAGE.md)。

## 快速开始

打包会收集配方及固定版本的递归依赖。`build` 生成交给 Agent 的完整提示词，不会调用模型或执行生成的软件。归档检查通过也不代表 jq 已完成重建。

```sh
recipe_dir="$(mktemp -d)"
recipkg pack . -o "$recipe_dir/jq.recipe.tar.gz"
recipkg check "$recipe_dir/jq.recipe.tar.gz"
recipkg build "$recipe_dir/jq.recipe.tar.gz" --lang zh-CN --output "$recipe_dir/build.md"
printf '提示词：%s\n' "$recipe_dir/build.md"
```

## 配方与进度

| 配方 | 构建要求 | 验收 | 覆盖进度 | 来源 |
| --- | --- | --- | --- | --- |
| [RECIPE.md](RECIPE.md) | [BUILD_PROMPT.md](BUILD_PROMPT.md) | [ACCEPTANCE.md](ACCEPTANCE.md) | [COVERAGE.md](COVERAGE.md) | [SOURCES.md](SOURCES.md) |

## 行为记录

| 范围 | 文档 |
| --- | --- |
| 表达式、赋值与条件 | [编程结构](PROGRAMMING-OBSERVED.md) · [赋值](ASSIGNMENT-OBSERVED.md) · [条件](CONDITIONALS-OBSERVED.md) · [JSON 解析](JSON-PARSING-OBSERVED.md) |
| 字符串与值 | [字符串过滤器](STRING-FILTERS-OBSERVED.md) · [类型与转换](CONVERSION-OBSERVED.md) · [容器操作](CONTAINMENT-OBSERVED.md) · [位置过滤器](INDEX-OBSERVED.md) · [长度与扁平化](ARRAY-VALUES-OBSERVED.md) · [Unicode](UNICODE-OBSERVED.md) |
| 数值 | [取整](ROUNDING-OBSERVED.md) · [单参数数学函数](MATH-OBSERVED.md) |
| 集合、生成器与聚合 | [集合操作](COLLECTIONS-OBSERVED.md) · [生成器选择](GENERATOR-OBSERVED.md) · [递归生成](RECURSE-OBSERVED.md) · [循环生成](WHILE-UNTIL-OBSERVED.md) · [流截取与跳过](LIMIT-SKIP-OBSERVED.md) · [逻辑聚合](PREDICATES-OBSERVED.md) · [聚合](AGGREGATION-OBSERVED.md) · [范围](RANGE-OBSERVED.md) · [对象条目](ENTRIES-OBSERVED.md) · [数组转置](TRANSPOSE-OBSERVED.md) · [递归变换](WALK-OBSERVED.md) · [SQL 风格操作](SQL-OBSERVED.md) |
| 路径与投影 | [路径读取](GETPATH-OBSERVED.md) · [路径写入](SETPATH-OBSERVED.md) · [路径生成](PATHS-OBSERVED.md) · [多路径删除](DELPATHS-OBSERVED.md) · [路径表达式删除](DELPATH-OBSERVED.md) · [路径投影](PICK-OBSERVED.md) |
| 输入输出与模块 | [输入输出](IO-OBSERVED.md) · [模块约定](MODULES-DOCUMENTED.md) |

## 项目链接

[Recipkg 标准库](https://github.com/recipkg/standard-library) · [jq 重建工作区](https://github.com/recipkg/jq-workspace)
