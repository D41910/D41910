# Hi, I'm D41910

**Java 后端出身，正在向 AI Agent 工程方向转型。**

我做的事是**给高星的 Agent / LLM 开源项目提交真实、可验证的 PR**——不是写文档、不是加 demo，
而是读源码、复现 bug、修掉它、并让测试证明这个 bug 以后不会再出现。

GitHub: [D41910](https://github.com/D41910) · 开源贡献主页：[github.com/D41910](https://github.com/D41910)

---

## ✅ 已被合并的修复

**[PraisonAI#5731](https://github.com/MervinPraison/PraisonAI/pull/5731) — 修复数组形式参数未走预设名归一化，静默回落默认后端**

> `merged=true` · 2026-10-08 · +79/-4 于 2 个文件 · base `main`

`param_resolver` 给预设名定了一条拼写规则（大小写、空白、`-`/`_` 通兑），
字符串分支老老实实用 `strip().lower().replace("-", "_")` 实现了，注释还写明
「要与 `validate_preset_string` 的闭集校验接受同一批拼法」；两个**数组**分支却仍是裸
`in presets` 比较。于是 `output="VERBOSE"` 正常解析，`output=["VERBOSE"]` 抛
`Invalid output value: 'VERBOSE'. Did you mean 'verbose'?`——**报错建议的正是它自己拒绝的值**；
更糟的是 `memory=["SQLITE"]` 连错都不报，裸比较未命中后直接落到 `config_class()`，
静默返回默认的 `backend='file'`。1502 例差分模糊：1409 相同 / 93 变化，
93 条逐一验证「与规范化拼法此前的既有行为一致」，非预期 0。

**[PraisonAI#5732](https://github.com/MervinPraison/PraisonAI/pull/5732) — 修复数组形式的 `memory` 缺 URL scheme 守卫，静默落到本地文件后端**

> `merged=true` · 2026-10-08 · +34/-0 于 2 个文件 · base `main`

`_resolve_string` 对不支持的 scheme 明写 `# Unknown scheme - raise helpful error` 并抛
`Unsupported URL scheme 'redis' for memory`；数组分支的注释写「try as URL or return as-is」，
**代码却 `return config_class()`**——注释与代码直接矛盾。于是
`memory=["redis://localhost:6379/0"]` 静默得到本地文件后端，而 `memory="redis://..."` 正常报错。
仓库自己的契约测试 `test_url_scheme_without_adapter_raises` 断言了这个不变量，
**但只测了字符串形式**；`validate_memory_backend` 的报错原文也写着
「Earlier releases accepted it and silently stored to a local file instead」——
仓库已把「静默存到本地文件」定性为 bug。314 例差分：273 相同 / 41 变化，非预期 0。

**[litellm#44800](https://github.com/BerriAI/litellm/pull/44800) — 修复 `/v1/messages` 流式中途失败未触达 proxy 失败边界**

> `merged=true` · 合并人 `mateo-berri`（litellm 维护者） · 2026-10-08 ·
> merge commit `87e961fa` · +649/-1 于 4 个文件 · Codecov「改动行全部被测试覆盖」

Anthropic Messages 流式适配器在中途出错时，只更新了内部的 `litellm_logging`
状态，就把异常吞掉了。后果是**上游已经失败，请求却继续往下流**，
失败既不会进入 proxy 的失败边界，也不会触发失败回调——监控里看不到，
重试逻辑不触发，日志里也没有。

修复后失败会走通 `dispatch_failure_handlers(..., prefer_async_handlers=True)`，
同步与异步两条回调路径都能收到。

这个 PR 的评审过程值得单独记一笔：`greptile-apps[bot]` 最初报了一个
「同步失败回调被漏发」的 P1，我当时判断是**误报**（它把「同步路径」
当成了「同步 handler 列表」）。真正去读 `litellm_logging.py:3933-3936` 之后发现
**bot 是对的、是我错了**——`async_failure_handler` 只读 `_async_failure_callback`，
而同步列表由 `dispatch_failure_handlers` 单独调度。改完之后跑探针验证：
修复前同步列表能触发、修复后也能触发。**这正是我之前给自己定的规则：
AI reviewer 的每一条意见都要回去读代码，不能凭直觉判定误报。**

---

## 开源贡献

向这些项目提交了修复真实缺陷的 PR，合计覆盖约**113 万 star** 的 Agent 基础设施，
其中 **5 个已合并进主干**：

| 项目 | Stars | 我的 PR |
|---|---|---|
| [Langflow](https://github.com/langflow-ai/langflow) | 156k | [#15632](https://github.com/langflow-ai/langflow/pull/15632) — 修复向量库按自定义 id 写入后删除静默命中 0 行 |
| | | [#15648](https://github.com/langflow-ai/langflow/pull/15648) — 修复「MMR traversal」搜索类型因大小写不匹配静默降级为普通 traversal |
| [browser-use](https://github.com/browser-use/browser-use) | 117k | [#6004](https://github.com/browser-use/browser-use/pull/6004) — 修复 DOM 提取时结尾换行产生零宽chunk |
| [cline](https://github.com/cline/cline) | 70k | [#14803](https://github.com/cline/cline/pull/14803) — 修复 diff 生成时清空文件产生幻影行 |
| [LiteLLM](https://github.com/BerriAI/litellm) | 60k | [#44365](https://github.com/BerriAI/litellm/pull/44365) — 修复嵌套 `metadata` JWT claim 不可达 |
| | | [#44800](https://github.com/BerriAI/litellm/pull/44800) — ✅ **已合并** · 修复流式中途失败未触达 proxy 失败边界 |
| | | [#44801](https://github.com/BerriAI/litellm/pull/44801) — 修复 model 更新接口的授权顺序缺陷 |
| | | [#44819](https://github.com/BerriAI/litellm/pull/44819) — 修复 image token 成本未计入 |
| | | [#44826](https://github.com/BerriAI/litellm/pull/44826) — 修复 Langfuse OTel 观测输出映射 |
| [crewAI](https://github.com/crewAIInc/crewAI) | 59k | [#7801](https://github.com/crewAIInc/crewAI/pull/7801) — 修复异步 task 取消时终态事件丢失 |
| | | [#7930](https://github.com/crewAIInc/crewAI/pull/7930) — 修复 Bedrock Converse 多模态内容未归并导致请求被拒 |
| | | [#7932](https://github.com/crewAIInc/crewAI/pull/7932) — 修复 Anthropic / OpenAI Responses 多模态内容未归并导致请求被拒 |
| [AgentScope](https://github.com/agentscope-ai/agentscope) | 33k | [#2945](https://github.com/agentscope-ai/agentscope/pull/2945) — 修复写权限被当作八进制解析 |
| [DeepCode](https://github.com/HKUDS/DeepCode) | 17k | [#255](https://github.com/HKUDS/DeepCode/pull/255) — 修复破坏性命令筛查按字面 token 匹配程序名，`/bin/rm -rf /`、`/usr/bin/sudo reboot` 全部绕过 |
| [WeKnora](https://github.com/Tencent/WeKnora) | 32k | [#3996](https://github.com/Tencent/WeKnora/pull/3996) — 修复用户搜索在 SQLite(Lite) 上因 ILIKE 硬失败且未转义 LIKE 通配符 |
| [gpt-researcher](https://github.com/assafelovic/gpt-researcher) | 30k | [#2199](https://github.com/assafelovic/gpt-researcher/pull/2199) — 修复图片生成开关因配置属性大小写不匹配而永久失效（且零日志） |
| | | [#2200](https://github.com/assafelovic/gpt-researcher/pull/2200) — 修复在线文档加载器在 `metadata` 缺 `source` 时把 url 写成 `None`（缺 `metadata` 属性则整批加载中止） |
| [FastGPT](https://github.com/labring/FastGPT) | 30k | [#7924](https://github.com/labring/FastGPT/pull/7924) — ✅ **已合并** · 修复判断器「包含 / 不包含」对 `any` 类型变量里的数字、布尔值恒返回 `false`，工作流静默走错分支 |
| [Semantic Kernel](https://github.com/microsoft/semantic-kernel) | 29k | [#14563](https://github.com/microsoft/semantic-kernel/pull/14563) — 修复上传路径未拒绝 `.`/`..` 路径段 |
| [instructor](https://github.com/567-labs/instructor) | 14k | [#2752](https://github.com/567-labs/instructor/pull/2752) — 修复 data URI 媒体类型未归一化导致图片静默降级为文本 |
| [txtai](https://github.com/neuml/txtai) | 13k | [#1362](https://github.com/neuml/txtai/pull/1362) — 修复 `BashTool(allowed=[])` 被 `if allowed else` 吞掉，静默恢复 7 个默认命令 |
| [MaxKB](https://github.com/1Panel-dev/MaxKB) | 23k | [#7253](https://github.com/1Panel-dev/MaxKB/pull/7253) — 修复 token 用量按 `completion/prompt` 反向发布，嵌套子应用逐层复利式放大 |
| [VoltAgent](https://github.com/VoltAgent/voltagent) | 11k | [#1441](https://github.com/VoltAgent/voltagent/pull/1441) — 修复脚手架生成的 Dockerfile 按包管理器渲染依赖层|
| [mobile-mcp](https://github.com/mobile-next/mobile-mcp) | 8.8k | [#487](https://github.com/mobile-next/mobile-mcp/pull/487) — 修复滑动 `distance` 无下界，负值使三份Robot 实现全部反向滑动且仍报告成功 |
| [PraisonAI](https://github.com/MervinPraison/PraisonAI) | 9k | [#5731](https://github.com/MervinPraison/PraisonAI/pull/5731) — ✅ **已合并** · 修复数组形式参数（如 `memory=["SQLITE"]`）未走预设名归一化而静默回落到默认后端 |
| | | [#5732](https://github.com/MervinPraison/PraisonAI/pull/5732) — ✅ **已合并** · 修复 `memory=["redis://..."]` 缺少不支持 URL scheme 的守卫，静默落到本地文件后端（字符串形式会正常报错） |
| [RubyLLM](https://github.com/crmne/ruby_llm) | 4.4k | [#1051](https://github.com/crmne/ruby_llm/pull/1051) — 修复 embedding 批结果从不读 `index`，乱序行按到达顺序配对、重复/负数/缺失位置全部被当成成功 |

### 被拒绝的修复

不是每个提上去的 PR 都站得住。记录被关闭的，比只记录成功的更有信息量：

**agent-framework#9137** — 我给 `_storage_key_segment` 的 literal 分支补上了模块自己
定义的 180 字符上限（base32 和 digest 分支一直遵守，只有 literal 漏了）。
维护者 `eavanvalkenburg` 直接关闭，未留评论；issue #9136 被标 `not_planned`。
真正的原因来自 Copilot 的评审意见，**指出了一个我漏掉的维度**：

> 这会重映射此前合法的存储位置，让存量数据变得不可达。比如 181 字符的字面
> session id 拼上 `.jsonl` 是 187 字节，在常见的 255 字节分量上限下完全合法，
> 但现在所有读取都走 digest 路径，不再探测旧的 literal 路径。

我的差分模糊测试把「181 字符从 literal 变成 digest」判成了预期变化——
**因为基线取的是修复前的行为，而修复前的行为本身就是 bug 的一部分后果。**
差分测试只能证明「与旧行为一致或不一致」，无法发现「旧行为该改」。
凡是改动**持久化 key 或文件路径**的修复，必须单独做一遍存量数据影响分析。

正确修法应该是 caller-aware 的上限：只重映射「加上调用方扩展名后此前根本
不可能存在」的名字，而不是无差别套 180。

### 修的是什么类型的 bug

 Mostly 真实用户会踩到的边界条件与并发缺陷：

- **切片越界** — 结尾换行被 `split('\n')` 多算一个元素，产生 `char_start == char_end` 的零宽块，
  分页时把越界起点传给模型，导致整轮编辑失败。
- **哨兵值误判** — JWT 解析无条件剥离 `metadata.` 前缀，使真正嵌套的 claim 完全读不到，
  静默错配到 default team。
- **回调漏发** — proxy 管理的流在失败时只重抛、不记账，同步回调被绕过。
- **事件竞态** — 取消与完成同时发生，terminal 事件先于 started 到达，span 永久泄漏。
- **文件模式解析** — `644` 被 Python 当成八进制字面量，实际权限与预期不符。
- **分支间防御漂移** — 模块自己定的 180 字符上限，base32 分支和 digest 分支都遵守了，
  唯独 literal 分支漏掉，308 字符的 session id 拼上扩展名后撞上文件系统 255 字节上限
  （这个修复因为没做存量影响分析被关闭了，见上）。
- **同一插件两个方向行为不一致** — `download_file` 三天前刚加上 `.`/`..` 路径段守卫，
  `upload_file` 却没加，模型可通过工具调用让文件写到沙箱根目录之外。
- **路径穿越** — 模型可控的 `remote_file_path` 里 `.` / `..` 路径段原样透传出网。
- **抽象契约与实现的参数顺序漂移** — `BaseToResponse` 把两个 token 计数声明为
  `(completion_tokens, prompt_tokens)`，而**所有调用点**传的都是
  `(message_tokens, answer_tokens)`，也就是 `(prompt, completion)`。
  位置参数不看名字，于是 `SystemToResponse` / `LoopToResponse` 把 prompt 数
  发布成 `completion_tokens`，反之亦然；`OpenaiToResponse` 恰好把两个名字写反了，
  **反而是唯一正确的那个**。真正让它变成 bug 而不是瑕疵的是往返：
  父应用用 `usage.prompt_tokens` 回读子应用的 `message_tokens` 再原样传下去，
  嵌套子应用每深一层，计数就再翻一次。
  1830 例差分模糊确认唯一变化就是这三个字段的归属。
- **大小写与参数归一化漏一处** — 模块自己有归一化 helper，HTTP header 路径都走了，
  data URI 路径却直接比较原始字符串；`data:image/PNG;base64,...` 被判为非图片，
  而异常又被 `autodetect_safely` 吞掉降级成纯文本 —— **模型收到 base64 文本而非图片，无报错**。
- **孪生实现的守卫漂移** — 同一个仓库里两份「加载文档 → `{"raw_content", "url"}`」的实现：
  本地的 `DocumentLoader` 在 #2152 已合并的修复里加了守卫
  （`getattr(page, "metadata", None) or {}` + `source or file_path or ""`，注释明写
  「loaders 偶尔会漏掉 `metadata["source"]`」），而在线的 `OnlineDocumentLoader`
  仍是裸读 `page.metadata.get("source")` —— 漏掉时 url 变成 `None`，
  顺着 `SearchAPIRetriever` 原样进上下文，喂给模型的是 `Source: None`；
  页对象连 `metadata` 属性都没有时抛 `AttributeError`，**整批文档一起丢**，
  不限于出问题的那一页。差分 24 个变体：14 相同 / 10 变化 / 非预期 0。
- **同一套规则在兄弟分支里只漏一处** — `param_resolver` 为预设名定了一条拼写规则
  （大小写、空白、`-`/`_` 通兑），字符串分支用 `strip().lower().replace("-","_")`
  实现了，注释还写明「要与 `validate_preset_string` 的闭集校验接受同一批拼法」；
  两个数组分支（`SINGLE_OR_LIST`、`PRESET_OVERRIDE`）却仍用裸 `in presets` 比较。
  于是 `output="VERBOSE"` 能解析、`output=["VERBOSE"]` 抛
  `Invalid output value: 'VERBOSE'. Did you mean 'verbose'?` —— **报错信息建议的正是它自己拒绝的值**；
  更糟的是 `memory=["SQLITE"]` 不报错，裸比较未命中后直接落到 `config_class()`，
  **静默返回默认的 `backend='file'` 而不是用户指定的 sqlite**。
  1502 例差分模糊：1409 相同 / 93 变化，且 93 例逐一验证「与规范化拼法此前的既有行为一致」，非预期 0。
- **N 个兄弟实现里，只有一个把同一件事做全** — RubyLLM 的 batch 解析有四条路
  （OpenAI 兼容 / Gemini / Bedrock / OpenRouter）。后三条都要求结果的 `index` 恰好覆盖
  `0...N`，否则用各自既有的 `batch_failure` 把**这一条请求**标失败
  （Gemini 是 #918，OpenRouter 是 #1000）。唯独 OpenAI 那条把 body 直接丢给
  `parse_embedding_response`，而后者按 **wire 顺序** `map` `data` ——
  **provider 明明在每一行里写了 `index`，这条路径从头到尾没读过它**。
  于是乱序返回的行按到达顺序配对，重复 / 负数 / 缺失 / 非整数的 `index`
  **全部被当成成功**，向量配错文本也不报错。
  这是我在这仓库上**第 13 个候选**，前 12 个都被证伪；最后靠的不是「长得像漂移」，
  而是「三个兄弟都做了而它没做」这件可验证的事实。
  76 组 position × wire-order 组合的差分：合法位置只发生重排，
  非法位置转为失败，**其余 0 变化**。
- **写入与读取的id 格式假设不一致** — `add_texts` 只对**自己生成**的 id 做哈希+大写，
  调用方传入的 id 按原样存库（截到 `VARCHAR(100)`）；`delete` 却无条件重新大写并截到 16 位。
  于是「按自定义 id 写入 → 按同一 id 删除」这条完全合法的路径，`WHERE id IN (...)`
  一个都匹配不上，**DELETE 静默影响 0 行并返回 `None`**，
  而 `VectorStore.delete` 的契约把 `None` 表达为「未实现」而非「删了 0 行」，
  调用方无从察觉。差分模糊测试：851 个 id 形态，修复前600 个匹配不上存量值，修复后 0 个。
- **UI 选项与分支条件的字符串漂移** — 下拉框提供的是 `"MMR traversal"`（小写 t），
  `_map_search_type` 却拿 `"MMR Traversal"`（大写 T）去比，这个分支成为死代码，
  选项静默落到兜底的普通 `traversal`：**界面显示 MMR、日志打印 MMR、实际跑的是非 MMR
  的图遍历，全程无报错**；同一映射还被 `get_retriever_kwargs()` 复用，retriever 同样降级。
  同仓库 5 个兄弟实现（hcd / cassandra / astradb_vectorstore / astradb_graph / oracle）
  的分支字符串都与各自下拉框一致，只有这一份漂了。差分：592 个无关输入 0 变化，
  98 个大小写/空白变体中仅目标两项命中 `mmr_traversal`。
- **同一组兄弟条件里，只有两个没跟上仓库自己定下的契约** — FastGPT 判断器（if-else 节点）
  的 `operations` 表里，`equalTo` / `notEqual` 早已 `String(x).trim()`，
  #7870 给 `startWith` / `endWith` 引入了 `getInputText()` 并在注释里写明
  「any 类型的变量也提供「开始为/结束为」，运行时可能拿到数字、布尔等非字符串值；
  与 equalTo 一样先转成字符串」，#7903 又让 `reg` 复用同一个 helper。
  但 `include` / `notInclude` 走的 `isInclude` 仍是
  `typeof value === 'string'` 之外**一律 `return false`**。
  而 `any` 类型用的 `allConditionList` 恰恰包含这两个条件，运行时拿到数字就成了：
  「包含 `001`」对 `2001` → false，**「不包含 `001`」→ `!false` = true**。
  两条都判反，工作流**静默走错分支且不报错**。
  定位线索正是既有测试：`runIfElse.test.ts` 已经为 `startWith/endWith` 和 `reg`
  各自断言了 `should ... a non-string value of an any-typed variable as text`，
  **唯独没有 `include` / `notInclude` 的用例**——补上这对孪生用例就直接命中。
  595 例差分模糊：560 相同 / 35 变化，**变化的输入类型分布 `{"number":28,"boolean":7}`，
  非预期 0**；数组、字符串、`null`、`undefined`、对象、`Date`、`RegExp`、`Symbol`、`BigInt`
  逐例不变。源码改动 +4/-2，零持久化影响。
- **同一个 `__init__` 里两个参数，一个尊重显式假值、一个不尊重** — txtai 的
  `BashTool.__init__` 用 `allowed if allowed else DEFAULTS` 解析命令白名单，
  `[]` 是 falsy，于是**显式传空列表和压根没传无法区分**：
  `BashTool(allowed=[])` 拿到的是 7 个默认命令，`cat` 真的执行。
  而这个白名单的 docstring 自称「best effort **not a sandbox**」，
  传空列表是表达「本 agent 不许跑 shell」的唯一方式——**一次静默的 opt-out 变 opt-in**。
  最强证据在**同一个函数里**：兄弟参数 `timeout` 由 #1210 引入，原样存储，
  既有测试就断言 `assertIsNone(BashTool(timeout=None).timeout)`。
  1680 例差分（21 种 `allowed` × 4 种 timeout × 20 条命令）：1120 相同 / 560 变化，
  **560 条全部是「显式 falsy 非 None 的 allowed」**，非预期 0。源码改动 +1/-1。
- **同一个模块里，三个筛查器有两个用了规范化函数，只有第三个没用** — DeepCode 的
  破坏性命令筛查 `screen_command` 用 `tokens[0].lower()` **按字面 token 匹配程序名**，
  而同文件的 `screen_egress`（第 467 行）和 `screen_install`（第 600、609 行）
  都先经 `_program()` 归一——那个函数的 docstring 写的就是
  「不带目录和 Windows 扩展名的裸程序名」。于是同一条命令**裸写被拦、带路径放过**：
  `rm -rf /` 命中，`/bin/rm -rf /` 返回 `None`；`sudo reboot` 命中，
  `/usr/bin/sudo reboot` 放过；`dd`/`mkfs`/`chmod` 同样全部绕过，
  `ls | /usr/bin/rm -rf /` 这种分段场景也漏。
  仓库自己的模块 docstring 写明存在的理由正是「诚实覆盖它**声称**能覆盖的」，
  而修复方式就是调用**这个文件里已经写好的那个函数**——一行。
  28200 例差分模糊（程序 × 多种 flag 拼法 × 路径 × shell 运算符 + 4000 条随机多段管道）：
  **635 例新增拦截、0 例新增放行**，635 条逐一验证「确实含破坏性程序段」，非预期 0。
  源码改动 +1/-1。
- **一组兄弟数值参数都有下界，只有这一个漏了** — mobile-mcp 的 `mobile_swipe_on_screen`
  里 `x`/`y` 是 `.min(0)`、同族的 `duration` 是 `.min(1).max(10000)`、
  `angle` 是 `.min(0).max(180)`、`maxSize` 是 `.int().positive()`，
  **只有 `distance` 是裸的 `z.coerce.number().optional()`**。
  端点按 `起点 ± distance` 计算，符号一翻**滑动方向与请求完全相反**，
  而工具仍返回 `Swiped up -400 pixels` —— **反向滑动且报告成功**，
  对滚动类手势意味着「想上滑却下滚」。三份`Robot` 实现
  （mobilecli / AndroidRobot / WebDriverAgent）**实测全部反转**；
  `distance: 0` 也同样不一致（mobilecli `|| 400` 静默变 400px，iOS 默认参数只兜`undefined` 故为 0）。
  仓库自己已把这类输入定性为 bug：CHANGELOG 1.0.7「`mobile_long_press_on_screen_at_coordinates`
  rejects a negative duration」——**同族参数修过，`distance` 漏了**。
  47 例差分（`undefined`/`NaN`/`±Infinity`/数字字符串/空白串/0/-0/小数/数组/对象/`MAX_SAFE_INTEGER`）：
  32 例结果相同、**新增接受 0 例、误拒 >=1 的值 0 例**。源码改动 +1/-1。

---

## 我的工作方式

提一个能真正被合并的 PR，和写一个看起来像 PR 的 PR，要求是不一样的。我的标准流程：

1. **先读贡献规范** — 尤其是 `AGENTS.md`。LiteLLM 的 `AGENTS.md` 里有三条明文硬要求
   （bug 修复必须扩展既有测试文件、测试必须真正走到被改的代码、禁止 monkeypatch 类属性），
   `greptile-apps[bot]` 会逐条对照并写明 "This requirement must be satisfied before merging"。
   提 PR 前不看，等于白提。

2. **读真实调用链，不靠函数名猜** — 确认缺陷在生产路径上可达，触发条件是什么。
   报错的 `traceback` 要能逐行对上源码行号。

3. **回退实现证明测试有效** — 改完代码后，把修复回退，确认新测试**真实失败**；
   再恢复修复，确认通过。一个在修复前也能通过的测试是装饰品，不是回归防护。

4. **差分模糊测试防回归** — 在**未修复**的代码上采基线，再用几百个随机 / 边界输入跑修复版，
   逐一对比输出。目标是「零个 case 出现非预期的语义变化」。
   LiteLLM 那个 PR 是 4000 个随机输入，browser-use 是 1500 例。

5. **只用真实测试结果** — 测试环境装不起来就如实写「未跑」，不会编造通过数。
   反过来也会主动核对：某个测试在**未修改的 main 上是否同样失败**，
   避免把环境性问题误报成自己的回归。

6. **查抢跑** — 高星仓库的 issue 池被抢得极快（实测 E2B #1933 被 bot 几小时内抢跑、
   agno 四个候选全被抢、smolagents 的 issue 平均有 3-25 个 PR 在抢）。
   下单前必须用 `search/issues?q=...+is:pr+<issue号>+in:body` 逐号复核。
   被抢跑就换目标，不硬挤。

7. **查门禁机器人** — 这是最大的坑，已踩过 5 次：`langgraph` 的 `require_issue_link.yml`、
   `pydantic-ai` 的 `pr-guard.yml`、`mastra` 的 triage 机器人、`agno` 的 `pr-triage.yml`、
   `sst/opencode` 的 `compliance-close.yml`——**都会自动关掉未关联 issue 的外部 PR**。
   更隐蔽的是：外部合并率异常高往往是假信号（langgraph 92%、pydantic-ai 99%，
   恰恰因为外部 PR 过不了门禁，合进去的都是内部 PR）。

8. **对评审意见逐条核实，不全盘接受** — 也会识别误报。LiteLLM 有一个评审意见
   说 PR 新建了测试文件，实际是 `+101/-2` 的扩展（`test_sse_wrapper.py` 在 main 上已存在）。
   核实后据实回复，维护者确认「这个是对的，我搞反了」。

---

## 技术栈

- **语言**：Java（主力）、Python、TypeScript
- **Agent 方向**：LLM 应用工程、多 Agent 编排、RAG、工具调用、Agent 可观测性
- **基础设施**：SDK / 网关 / 代理层 / 前端 Agent 界面
- **工具链**：pytest、vitest、ruff、flake8、GitHub API 自动化

## 联系方式

欢迎就上面任何一个 PR 里的问题交流——特别是如果你也踩过相同的坑，很想知道我有没有找错根因。

[![GitHub](https://img.shields.io/badge/GitHub-D41910-181717?style=flat-square&logo=github)](https://github.com/D41910)
