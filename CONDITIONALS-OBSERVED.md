# jq 条件、真假值和比较

依据 [jq 1.8 手册](https://jqlang.org/manual/v1.8/#conditionals-and-comparisons)及本机 `jq-1.8.2` 样本。程序版本号与配方固定上游版本一致，但本机二进制的构建来源与配置未核验；以下对照不是独立重建验收。

## 条件与结果流

仅 `false` 和 `null` 为假值；数字 0、空字符串、空数组和空对象都是真值。`if A then B else C end` 对条件 A 的每个输出分别选择一次分支；分支接收该 `if` 的原始输入，不接收条件结果。A 无输出时整个条件无输出。省略 `else` 等同 `else .`；`elif` 允许后续条件。

例如输入 `{"x":1}`，`if (1,2) then . else 0 end` 输出两个独立的 `{"x":1}`；`if false then 1 end` 输出原输入；`if empty then 1 else 2 end` 无输出。`[.[] | if . then "yes" else "no" end]` 输入 `[false,null,0,"",[],{}]` 得到 `["no","no","yes","yes","yes","yes"]`。

## 备选和布尔运算

`A // B` 输出 A 产生的所有非 `false`、非 `null` 值；如果 A 没有这样的值，包括 A 为 `empty`，则输出 B 的所有结果。它不是对 A 的每个假值都单独执行 B。`[(false,null,0,"",[],{}) // "default"]` 得 `[0,"",[],{}]`；`[(false,null,empty) // (1,2)]` 得 `[1,2]`。若想对每项单独回退，可写 `[(false,null,true) | . // "default"]`，得到 `["default","default",true]`。括号和优先级有实际影响：`[false, true // "default"]` 得 `[false,true]`，而 `[(false,true) // "default"]` 得 `[true]`。

`and`、`or`、`not` 输出布尔值；`not` 是接收输入的过滤器，例如 `[false, 0 | not]` 得 `[true,false]`。本机样本显示短路：`false and error("bad")` 输出 `false`、退出 0；`true or error("bad")` 输出 `true`、退出 0；`true and error("bad")` 无 stdout、stderr 含 `bad`、退出 5。短路还影响多结果：`[(true,false) and (true,false)]` 得 `[true,false,false]`；`[(false,true) or (true,false)]` 得 `[true,false,true]`。不能无条件对两边所有输出取笛卡尔积。

## 相等与排序

`==`、`!=` 比较 JSON 值：字符串不自动转为数字；对象键的顺序不影响相等。`[1 == "1",1==1.0,{"a":1,"b":2} == {"b":2,"a":1}]` 得 `[false,true,true]`。`<`、`<=`、`>`、`>=` 使用与 `sort` 相同的排序；手册规定类型顺序为 null、false、true、数字、字符串、数组、对象。字符串按 Unicode 码点，数组按字典序，对象先比较排序后的键集合、再逐键比较值。样本 `[[1,2] < [1,3], {"a":1} < {"b":0}, 1 < "0"]` 得 `[true,true,true]`；`[(1,2) < (2,3)]` 得 `[true,false,true,true]`，结果顺序也应保留。

类型区别也适用于嵌套值：`[1==1.0, [true]==[1], {"a":true}=={"a":1}]` 输出 `[true,false,false]`，不能直接使用会把布尔值视为整数的宿主语言深度相等。`if false then 1 elif true then 2 end` 输出 `2`；`[false and (1,2), true or empty]` 输出 `[false,true]`，两个短路样本均不消费右侧结果。

上述非错误样本均退出 0、stderr 为空；除了第一个条件样本外，样本输入可为 `null` 或文中明确给出的对象。可执行对照在 [standard-library-verification](https://github.com/recipkg/standard-library-verification) 的 `JqSamples.test_conditions_alternatives_and_comparisons`；它还检查空输出和 JSON 类型。复杂表达式优先级、`elif` 边界、全部嵌套对象排序、数字精度、异常传播及其他平台仍未全面验证。
