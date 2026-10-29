# jq · Recipkg

**命令行 JSON 查询与转换：提炼过滤表达式、模块加载、流式输入、赋值和错误行为。**

[标准库](https://github.com/recipkg/standard-library) · [配方清单](RECIPE.md) · [构建要求](BUILD_PROMPT.md) · [验收条件](ACCEPTANCE.md) · [字符串过滤器观察](STRING-FILTERS-OBSERVED.md) · [转换观察](CONVERSION-OBSERVED.md) · [容器观察](CONTAINMENT-OBSERVED.md) · [位置观察](INDEX-OBSERVED.md) · [长度与扁平化](ARRAY-VALUES-OBSERVED.md) · [Unicode 转换](UNICODE-OBSERVED.md) · [生成器选择](GENERATOR-OBSERVED.md) · [逻辑聚合](PREDICATES-OBSERVED.md) · [范围生成器](RANGE-OBSERVED.md) · [对象条目转换](ENTRIES-OBSERVED.md) · [数组转置](TRANSPOSE-OBSERVED.md) · [路径读取](GETPATH-OBSERVED.md) · [路径写入](SETPATH-OBSERVED.md) · [路径生成](PATHS-OBSERVED.md) · [来源](SOURCES.md) · [来源与覆盖](COVERAGE.md) · [模块约定](MODULES-DOCUMENTED.md) · [重建工作区](https://github.com/recipkg/jq-workspace)

![从软件与依赖整理配方，再交给 Agent 重建](https://raw.githubusercontent.com/recipkg/recipkg/main/assets/recipkg-cover.png)

本仓库保存软件重建配方，不是上游软件的源码或官方发行版。上游来源、固定提交和许可证见 [RECIPE.md](RECIPE.md)。配方仍在提取中；已经观察的行为、未验证内容及重建状态见 [COVERAGE.md](COVERAGE.md)。

## 使用

打包会收集配方及其固定的递归依赖，`build` 输出交给 Agent 的完整提示词；它不会调用模型或执行生成的软件。实际重建后应运行 [ACCEPTANCE.md](ACCEPTANCE.md) 中的验收。归档检查通过不等于软件已重建。

```sh
recipe_dir="$(mktemp -d)"
recipkg pack . -o "$recipe_dir/jq.recipe.tar.gz"
recipkg check "$recipe_dir/jq.recipe.tar.gz"
recipkg build "$recipe_dir/jq.recipe.tar.gz" --lang zh-CN --output "$recipe_dir/build.md"
printf '提示词：%s\n' "$recipe_dir/build.md"
```
