# jq 类型筛选器观察

目标固定为 jq 1.8.2。上游 `src/builtin.jq` 将这些过滤器定义为按 `type` 条件选择当前值；记录仅覆盖下列 JSON 类型样本，不代表全部内建函数已提取。

输入为 `[null,false,true,0,1.5,"","x",[],[null],{}, {"x":1}]` 时，各过滤器返回：

| 过滤器 | 保留的值类型 |
| --- | --- |
| `arrays` | array |
| `objects` | object |
| `iterables` | array、object |
| `booleans` | boolean |
| `numbers` | number |
| `strings` | string |
| `nulls` | null |
| `values` | 除 null 外的所有值；`false` 会保留 |
| `scalars` | null、boolean、number、string；排除 array、object |

每个过滤器检查并原样输出当前输入值，不会迭代数组或对象。类型不匹配时成功地产生零个结果；例如 `arrays` 接收数字时 stdout 为空。过滤器不接受参数。验证脚本使用逐类型样本和固定种子嵌套 JSON 值对照本机 jq 1.8.2；参考二进制的构建来源未核实。

来源：固定上游提交 [`src/builtin.jq`](https://github.com/jqlang/jq/blob/34f7186b86743a083a589741b6cea95293524108/src/builtin.jq#L52-L62)。局部接受结果见 `standard-library-verification/tests/test_jq_type_filters.py` 和 `ACCEPTANCE.md` 第 47 项。
