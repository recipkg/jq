# jq 配方（提取中）

```toml
format = "recipkg/1"

[package]
name = "jq"
version = "0.1.18"
kind = "software"
description = "命令行 JSON 查询与转换工具 jq 的重建配方草案。"
entry = "BUILD_PROMPT.md"

[dependencies.quality]
git = "https://github.com/recipkg/recipkg.git"
rev = "41e5b55c6612a9a5a10e499b5ace6a1a3fed0f2b"
subdir = "recipes/software-quality"
```

上游 [jqlang/jq](https://github.com/jqlang/jq/tree/jq-1.8.2)，固定标签 `jq-1.8.2`，提交 `34f7186b86743a083a589741b6cea95293524108`。上游 `COPYING` 对程序、文档和内含第三方材料分别说明许可；程序为 MIT，文档为 CC BY 3.0，详见源文件。本配方尚未完整提取，独立重建仍在进行。
