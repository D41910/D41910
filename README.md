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

向这些项目提交了修复真实缺陷的 PR，覆盖 31 个仓库、合计约**93 万 star** 的 Agent 基础设施，
其中 **28 个已合并进主干**：

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
| [DeepCode](https://github.com/HKUDS/DeepCode) | 17k | [#255](https://github.com/HKUDS/DeepCode/pull/255) — ✅ **已合并** · 修复破坏性命令筛查按字面 token 匹配程序名，`/bin/rm -rf /`、`/usr/bin/sudo reboot` 全部绕过 |
| | | [#256](https://github.com/HKUDS/DeepCode/pull/256) — ✅ **已合并** · 修复 glob 把 `_SKIP_DIRS` 拿绝对路径分量去匹配：工作区路径里只要含 `build`/`dist`/`venv`/`node_modules`，全部文件被跳过、静默返回「No files matched.」，而共用同一常量的 grep 正常返回 |
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
| [RubyLLM](https://github.com/crmne/ruby_llm) | 4.4k | [#1051](https://github.com/crmne/ruby_llm/pull/1051) — ✅ **已合并** · 修复 OpenAI 兼容 embedding 批结果从不读 `index`，乱序行按到达顺序配对、重复/负数/缺失位置全部被当成成功 |
| | | [#1052](https://github.com/crmne/ruby_llm/pull/1052) — ✅ **已合并** · 修复 Mistral 批结果是五个 embedding 批方言里唯一既不校验也不按 `index` 排序的：乱序行把向量配给错误的输入，非法位置静默成功 |
| [Langroid](https://github.com/langroid/langroid) | 4.1k | [#1215](https://github.com/langroid/langroid/pull/1215) — ✅ **已合并** · 修复流式响应的同步 / 异步孪生实现漂移：`finish_reason == "content_filter"` 的空响应转可见提示**只加在同步侧**（PR #660），异步侧没有→ 被内容过滤器拦截时返回空消息，`Task.run_async()` 判定为无响应并原样重发，进入 issue #658 报告的死循环 |
| [GPUStack](https://github.com/gpustack/gpustack) | 5.8k | [#6391](https://github.com/gpustack/gpustack/pull/6391) — ✅ **已合并** · 修复缓存provider 目录的卡片 `icon` 未接入仓库已有的 `validate_icon` scheme 白名单（catalog / community backend 两个兄弟源都接了、各有一条测试点名）：`javascript:`、SVG `data:` URI、协议相对 URL 可被存库并由 `GET /v2/cache-providers` 原样下发给前端渲染 |
| [EvalScope](https://github.com/modelscope/evalscope) | 3.5k | [#1832](https://github.com/modelscope/evalscope/pull/1832) — ✅ **已合并** · 修复英文多选题解析器不接受全角逗号，`ANSWER: A，C` 只取到第一个选项，多选答案被静默判错 |
| | | [#1833](https://github.com/modelscope/evalscope/pull/1833) — ✅ **已合并** · 修复 markdown 链只给图片做本地路径绝对化、音频与视频两个分支没做：相对路径下发后无法与 base64 区分，`max_length`（`pretty_print` 默认 50）还会把真实路径截成不存在的字符串 |
| [SkillHub](https://github.com/iflytek/skillhub) | 5.2k | [#940](https://github.com/iflytek/skillhub/pull/940) — 修复创建命名空间表单的前端正则仍是纯 ASCII，而服务端 `SlugValidator` 自 PR #196 起已接受 Unicode 字母/数字/符号：中文、日文、韩文、emoji 命名的 slug 被前端拦下、请求根本发不出去（1026 例差分中 833 例被误拦），且报错文案与官方发布指南矛盾 · ⏳ 待补 DCO 签名与 CLA |
| [OpenHarness](https://github.com/autonomous-ai/openharness) | 1.2k | [#1162](https://github.com/autonomous-ai/openharness/pull/1162) — 修复 Pi 面板读取器用**整个 capture（含 tmux scrollback）**匹配 picker 正则，而通用读取器早已收窄到「最新 prompt 之下」且同仓有测试点名该不变量：曾打开过一次 reasoning picker 的会话**永久 busy**，模型切换被 `AGENT_BUSY` 拒绝、团队投递被 `team_waiting_user` 拦住。关键证据是仓库自己抓的 pi pane证明**打开的 picker 会把输入框整个替换**——既有测试的夹具保留了输入框，是真实渲染从不出现的形态 |
| | | [#1163](https://github.com/autonomous-ai/openharness/pull/1163) — 修复 Amp 审批读取器的 `stripAnsi` 匹配 CSI 参数时**漏掉引入它们的 ESC**，而面板来自 `capture-pane -e`（保留 SGR）：颜色处残留一个裸 ESC，`^\s*` 锚定的行正则看不见它 → **四选一的审批只剩三个选项，且缺的正是被高亮的那一项**（设备按序号作答，走到的行与 Amp 画出来的那一行不是同一行）。同时 `cat log[0m].txt` 这类**路径/通配符是内容不是样式**，会被吃掉变成 `cat log].txt`。**最硬证据：引入该正则的同一个commit（#926）就修过 hermes 的同一缺陷**，`lib/askQuestion.ts` 的注释明写「真实捕获里边框前面有 SGR码」 |
| [Langroid](https://github.com/langroid/langroid) | 4.1k | [#1218](https://github.com/langroid/langroid/pull/1218) — ✅ **已合并** · 修复读取表格数据时不剥离 UTF-8 BOM，首列名多出`\ufeff` 导致该列永远匹配不上 |
| | | [#1219](https://github.com/langroid/langroid/pull/1219) — ❌ **已关闭（未合并）** · 修复爬虫遇到 301/302 时仍按请求 URL 解析相对链接，落在重定向后的页面上全部失效 |
| | | [#1220](https://github.com/langroid/langroid/pull/1220) — ✅ **已合并** · 修复 Markdown 标题层级跳级（h2→h4）时被逐级下调，文档大纲层级失真 |
| | | [#1221](https://github.com/langroid/langroid/pull/1221) — ✅ **已合并** · 修复 Markdown 分块把 `overlap=0`（显式要求不重叠）当成未设置而回落到默认值 |
| | | [#1222](https://github.com/langroid/langroid/pull/1222) — ❌ **已关闭（未合并）** · 修复模糊检索拼接上下文时按字符硬切，会把单词从中间截断，检索质量与可读性同时下降 |
| | | [#1223](https://github.com/langroid/langroid/pull/1223) — ✅ **已合并** · 修复加载表格数据时丢掉重名列 |
| | | [#1224](https://github.com/langroid/langroid/pull/1224) — 修复 Markdown 分块丢掉段落开头的正文 |
| | | [#1225](https://github.com/langroid/langroid/pull/1225) — ✅ **已合并** · 修复按行读取文档时丢掉行内空白 |
| | | [#1226](https://github.com/langroid/langroid/pull/1226) — ✅ **已合并** · 修复 PDF 分页切分点被重复计入相邻两页 |
| | | [#1227](https://github.com/langroid/langroid/pull/1227) — ✅ **已合并** · 修复显式配置的 embedding 组织参数被忽略 |
| | | [#1228](https://github.com/langroid/langroid/pull/1228) — ✅ **已合并** · 修复 BPE 分块边界处丢字 |
| | | [#1229](https://github.com/langroid/langroid/pull/1229) — ✅ **已合并** · 修复编号段落的续段被当成新段落切掉 |
| | | [#1230](https://github.com/langroid/langroid/pull/1230) — ✅ **已合并** · 修复缺文档后端时抛出的错误不可操作（看不出要装哪个依赖） |
| | | [#1231](https://github.com/langroid/langroid/pull/1231) — ✅ **已合并** · 修复删除不存在的 Redis key 报错，而非按幂等 no-op 处理 |
| | | [#1232](https://github.com/langroid/langroid/pull/1232) — ✅ **已合并** · 修复 Agent 输出抽取不匹配字面量类型 |
| | | [#1238](https://github.com/langroid/langroid/pull/1238) — ✅ **已合并** · 修复 `Parser.truncate_tokens` 不接受特殊 token 字面量
| | | [#1241](https://github.com/langroid/langroid/pull/1241) — 修复 `PineconeDB.add_documents` 吞掉被 Pinecone 拒绝的 upsert：仅打日志后继续，被拒的文档静默丢失、调用方仍被告知写入成功（同一缺陷 LanceDB 已在 #1201 修掉）
| | | [#1242](https://github.com/langroid/langroid/pull/1242) — 修复 `RepoLoader.clone()` 克隆失败后仍返回目标路径（而非 `None`）：使 `load()` 里 `if path is None: raise ValueError("Unable to clone repo")` 成为死代码，克隆失败的仓库被当成空仓库加载 |
| | | [#1243](https://github.com/langroid/langroid/pull/1243) — 修复异步流式解析不防 `choices: null` 的分块：`len(None)` 抛错被 `_stream_response_async` 的兜底 `except` 吞掉，流的其余部分被丢弃，调用方拿到空消息（同步孪生已防，异步侧漏了） |
| | | [#1244](https://github.com/langroid/langroid/pull/1244) — 修复 `DocChatAgent.llm_response_async()` 对空查询返回 `None`（同步孪生返回 `NO_ANSWER` 文档），使 `Task.step_async()` 里针对 NO_ANSWER 的分支在异步路径上永不可达 |
| [Haystack](https://github.com/deepset-ai/haystack) | 23k | [#13206](https://github.com/deepset-ai/haystack/pull/13206) — 修复文档评估器不支持容器级 metadata，使「同一文档、不同容器」的评测结果无法区分 |
| [AntOmniEvo](https://github.com/ant-research/AntOmniEvo) | 1.1k | [#20](https://github.com/ant-research/AntOmniEvo/pull/20) — 修复退化会话检测 `find_degenerate_ending` **只接进 Pi 一个后端**：它按 span 形态判断、本就与后端无关（模块自述 "shared across coding agent implementations"），而 `ClaudeCodeProposer` 直接返回未检查的 trajectory。网关过载时模型返回空 assistant 消息、CLI 内部重试后**exit 0**，result 事件仍是 `subtype=success`/`is_error=false`，于是 Phase 2 看不到 error、只看到工件目录变了 → **把半途而废的改动提升为候选**（正是已合并的 #5 想消除、#14 想阻止的那一幕） |
| [FrontierAgent](https://github.com/ApodexAI/FrontierAgent) | 5.2k | [#66](https://github.com/ApodexAI/FrontierAgent/pull/66) — 修复 `bash` / `run_python_code` 的工具结果**第二道截断预算被硬编码**（4K / 5K），而第一道溢出门槛是可配置的 `TOOL_EXEC_RESULT_MAX_CHARS`（默认 8K，官方 A/B 文档实测取到 50000）：在**默认 8K 下** `run_python_code` 每一次溢出结果的「落盘路径 + 退出判定」就已被第二道截断抹掉；而调大该配置对 agent_team 毫无作用，因为多出来的额度会被原样砍回去。同仓库的 React 后处理器早已按「第二道必须跟随第一道」实现并写明理由（`_CONFIGURED_EXEC_TOOLS`），既有的 grep/glob 10K 条目注释与跨两个后处理器的参数化测试也都在论证这条不变量 —— 唯独这两个可配置的工具没跟上 |
| [Agent Framework](https://github.com/microsoft/agent-framework) | — | [#9137](https://github.com/microsoft/agent-framework/pull/9137) — ❌ **被关闭** · 见下方「被拒绝的修复」 |
| [DeerFlow](https://github.com/bytedance/deer-flow) | 84k | [#6694](https://github.com/bytedance/deer-flow/pull/6694) — 修复 You.com 搜索结果直接裸读 `data["results"]` 与 `sections[section]`：非对象顶层、非对象 `results`、非列表 section 会带着 `TypeError`/`AttributeError` 冲出工具边界，而不是返回该provider 家族既有的结构化格式错误（Brave #6550 / GroundRoute #6603 / Tavily #6645 都已这么做） |
| | | [#6719](https://github.com/bytedance/deer-flow/pull/6719) — ✅ **已合并** · 修复 Sofya 的 `_response_results` 只实现了该家族「畸形载荷收容规则」三条里的两条：它会跳过非对象条目，但**非空列表里一个可用对象都没有时返回空列表**，于是 `web_search` / `web_fetch` 双双落到「No results found」——等于告诉模型这个查询本来就没匹配上，而不是暴露畸形响应。同名函数在 GroundRoute 里有第三条分支且注释写明理由；**#6603 的 commit message 更直接把 sofya 列为「已应用该规则」的成员之一** |
| | | [#6779](https://github.com/bytedance/deer-flow/pull/6779) — 修复 Serper 的 `_response_items` 同样缺第三条收容规则：`organic` / `images` 容器非空却全是标量、`None` 或嵌套列表时返回空列表，`web_search` 落到「No results found」、`image_search` 落到「No images found」。**该 helper 被 search 与 image_search 共用**（`tools.py:448` / `503`），一处守卫盖两个入口；**#6603 的 commit message 也把 serper 点名为「已应用」** |

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
- **同步 / 异步孪生实现的守卫漂移** — 流式响应的两个处理器是同一份逻辑的两份拷贝
  （`_process_stream_event` 与 `_process_stream_event_async`），「内容被过滤器拦截时
  转成可见提示」**只加在了同步那份**（PR #660 的 patch 只有一个 hunk）。
  而 `stream` 默认开启、agent 走的正是异步那条路，于是被拦截时返回空消息，
  `Task.run_async()` 判定为「无响应」并把上一条原样重发，拿到同样的拦截结果，
  形成 issue #658 里描述的死循环。
  判定它是遗漏而非设计取舍的依据很硬：**同文件另一次同类修复（#987 修 `null delta`）
  两份都改了**（两个 hunk，+2/-2）。
- **抽象契约与实现的参数顺序漂移** — `BaseToResponse` 把两个 token 计数声明为
  `(completion_tokens, prompt_tokens)`，而**所有调用点**传的都是
  `(message_tokens, answer_tokens)`，也就是 `(prompt, completion)`。
  位置参数不看名字，于是 `SystemToResponse` / `LoopToResponse` 把 prompt 数
  发布成 `completion_tokens`，反之亦然；`OpenaiToResponse` 恰好把两个名字写反了，
  **反而是唯一正确的那个**。真正让它变成 bug 而不是瑕疵的是往返：
  父应用用 `usage.prompt_tokens` 回读子应用的 `message_tokens` 再原样传下去，
  嵌套子应用每深一层，计数就再翻一次。
  1830 例差分模糊确认唯一变化就是这三个字段的归属。
- **同一段清理逻辑的 N 份拷贝里，只有一份漏了锚点** — `stripAnsi` 在一个仓库里有 9 份拷贝，
  6 份的转义匹配**连引入参数的那个 ESC 一起吃掉**，另外3 份没有：Amp 完全不匹配 ESC，
  Kilo 把 ESC 写成可选 `ESC?`。面板来自 `capture-pane -e`（保留 SGR），于是被删掉的只是
  `[38;5;244m` 这样的参数串，**颜色原处留下一个裸 ESC**，而下游 `^\s*` 锚定的行正则
  和 `/^(\w+):$/` 工具名正则都看不穿它 → 高亮的那一行从选项列表里消失、四选一的审批
  变成三选一（而设备按序号作答，走到的行与画出来的那行不是同一行），工具名也读不出来，
  只剩一句通用的 `Approval required`。反方向同样成立：`cat log[0m].txt` 里的路径是**内容
  不是样式**，被吃掉变成 `cat log].txt`。
  判定它是遗漏的决定性证据：**引入这个正则的同一个 commit（#926）就修过 hermes 的同一处缺陷**，
  `lib/askQuestion.ts` 里那段注释明写「真实捕获里边框前面有 SGR 码，所以 `^│` 从来没匹配上——
  hermes 明明对话框就在屏幕上，却被读成没有对话框」。
  而仓库 33 个真实捕获夹具里**有 23 个带 SGR**（`question-commandcode.txt` 一个就505 个序列），
  **23 个全部与那6 份共识不一致**；唯独 amp 的夹具被裁成了纯文本，所以测试永远碰不到。
  差分 155 例（33 个真实捕获 × 5 种 SGR 位置），以兄弟正则当oracle：
  修复前149 例不一致，修复后 0 例，**纠正 149 例、回归 0 例**。
- **一个通用检测器只接进了一半的后端** — `find_degenerate_ending()` 所在模块自述
  "Generic trajectory utilities **shared across coding agent implementations**"，
  它判断的是 **span 形态**（`span_type == "model"`）而非任何特定后端的事件，
  但全仓 `grep` 只有**一个调用点**：Pi 后端。`ClaudeCodeProposer.invoke_agent`
  拿到解析结果直接返回，不做任何检查。
  于是已合并的 #5 想消除的那个失败模式在 Claude 后端**依然可达**：网关过载时
  模型返回空 assistant 消息，CLI 内部重试几次后 **exit 0**，
  最后的 result 事件仍是 `subtype="success"` / `is_error=false`，
  解析器自然什么也不记录 → Phase 2 看不到 error、只看到工件目录变了，
  **把半途而废的改动提升为候选**，也就是 #14 明说要阻止的
  "a half-finished mutation must not be rolled out as a candidate"。
  判定依据是三条同源证据：① 模块自述与实现形态都表明它与后端无关；
  ② `errors.py` 明写该分类 "is not Pi-specific"、#14 的提交信息写 "one rule to
  **both backends**"；③ #5 的 `/files` 显示它只动了 `pi_coding_agent_proposer.py`。
  修复 = 把已有的检测器接进另一个后端，7 行；用 `if not trajectory.errors` 兜底，
  保证显式的 `is_error` / `error_*` 仍报自己的原因。
  18 例差分：10 相同 / 8 新增拦截（全是退化形态）/ **回归 0**。
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
- **同一个函数里，三条标签路径有两条认这个分隔符，第三条不认** — EvalScope 的多选题解析器
  把模型输出里的选项标签分三条路读：括号式 `_BRACKETED_LABEL_RE`、中文式 `_PLAIN_LABEL_ZH_RE`、
  英文裸式 `_PLAIN_LABEL_RE`。前两条的字符类里都有全角逗号 `，`，**只有英文裸式没有**
  —— 于是 `ANSWER: (A，C)` 和 `答案：A，C` 都正常，`ANSWER: A，C` 却只捕获到 `'A'`，
  **第二个选项被静默丢掉，多选答案判错**。
  更强的证据是**同一个函数下文的注释与代码自相矛盾**：拆分前那行写着
  「The label patterns also accept full-width separators」并做了 `.replace('，', ',')`，
  但这个 pattern 根本不可能捕获到 `，` —— 那次归一化对这条路永远不生效。
  而 #1751 修中文孪生 pattern 时的 commit 信息写的是「Mirror the English path」，
  却把 `_PLAIN_LABEL_RE` 原样留在了上下文行里。
  修复 = 补一个字符。7217 例差分：7016 例完全相同、**402 例变化且全部含 U+FF0C、非预期 0**，
  201 例多选答案被找回。
- **两道串联的守卫，其中第二道忘了第一道是可配置的** — FrontierAgent 的工具结果要过两道截断：
  第一道 `maybe_overflow` 溢落到磁盘、并在**结果尾部**附上「落盘路径」和「退出判定」；
  第二道是后处理器按工具名查预算表再切一刀。第一道的上限是 `TOOL_EXEC_RESULT_MAX_CHARS`
  （可配置，官方 A/B 文档实测从 8000 打到 50000），**但 `agent_team` 那张预算表把
  `bash` / `run_python_code` 硬编码成 4K / 5K**。
 后果有两层，都用真实代码路径复现过：**默认 8K 下**，`run_python_code` 每一次溢出结果的
  落盘路径和退出判定就已经被第二道抹掉（模型只看到一段没有线索的截断文本）；
  而**把配置调大对 agent_team 完全无效** —— 第一道放宽到 50000，第二道照样砍回 4037 / 5116。
  判定它是遗漏而非取舍的证据有三条：同仓库的 React 后处理器早已按这条规则实现，
  并把理由写在代码注释里；同一张表里紧挨着的 grep/glob 条目注释明写
  「the value react has carried for this reason all along」；而那个**跨两个后处理器参数化**的
  回归测试，工具列表偏偏只覆盖了固定门槛的三个工具。
  修复 = 照搬兄弟已有的规则。**2016 例差分：1891 例完全相同、125 例变化且全部限定在这两个工具、
  非预期 0**；React 侧的输出在全部 2016 例里**逐字节未动**。

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
