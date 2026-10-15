## ✅ 已合并 PR（48 个 / 14 个项目）

| Project | ★ | Merged |
|---|---|---|
| [langgenius/dify](https://github.com/langgenius/dify) | 158.6k | 8 |
| [bytedance/deer-flow](https://github.com/bytedance/deer-flow) | 83.9k | 2 |
| [BerriAI/litellm](https://github.com/BerriAI/litellm) | 61.3k | 1 |
| [labring/FastGPT](https://github.com/labring/FastGPT) | 29.8k | 1 |
| [1Panel-dev/MaxKB](https://github.com/1Panel-dev/MaxKB) | 23.0k | 1 |
| [HKUDS/DeepCode](https://github.com/HKUDS/DeepCode) | 16.7k | 2 |
| [neuml/txtai](https://github.com/neuml/txtai) | 13.0k | 1 |
| [MervinPraison/PraisonAI](https://github.com/MervinPraison/PraisonAI) | 9.2k | 2 |
| [gpustack/gpustack](https://github.com/gpustack/gpustack) | 5.8k | 1 |
| [ApodexAI/FrontierAgent](https://github.com/ApodexAI/FrontierAgent) | 5.3k | 1 |
| [crmne/ruby_llm](https://github.com/crmne/ruby_llm) | 4.5k | 2 |
| [langroid/langroid](https://github.com/langroid/langroid) | 4.1k | 21 |
| [modelscope/evalscope](https://github.com/modelscope/evalscope) | 3.5k | 2 |
| [ant-research/AntOmniEvo](https://github.com/ant-research/AntOmniEvo) | 1.1k | 1 |

---

| Project | Merged PR |
|---|---|
| [dify](https://github.com/langgenius/dify) | [#44120](https://github.com/langgenius/dify/pull/44120) — fix(nodejs-client): replace stale user fields in native upload forms |
|  | [#44097](https://github.com/langgenius/dify/pull/44097) — fix(nodejs-client): allow empty keyword filters in list APIs |
|  | [#44090](https://github.com/langgenius/dify/pull/44090) — fix(nodejs-client): override default headers case-insensitively |
|  | [#44085](https://github.com/langgenius/dify/pull/44085) — fix(dify-agent): treat a confirmed-missing Home Snapshot as a success on Enterprise delete |
|  | [#44084](https://github.com/langgenius/dify/pull/44084) — fix(api): stage hit-testing query log with flush instead of committing caller session |
|  | [#44083](https://github.com/langgenius/dify/pull/44083) — fix(nodejs-client): preserve SSE field value whitespace |
|  | [#44017](https://github.com/langgenius/dify/pull/44017) — fix(knowledge): only batch-enable segments whose indexing completed |
|  | [#44014](https://github.com/langgenius/dify/pull/44014) — fix(dify-agent): verify E2B binding Home as well as Workspace on acquire |
| [deer-flow](https://github.com/bytedance/deer-flow) | [#6779](https://github.com/bytedance/deer-flow/pull/6779) — fix(serper): report a format error for results with no usable objects |
|  | [#6719](https://github.com/bytedance/deer-flow/pull/6719) — fix(sofya): report a format error for a results list with no usable objects |
| [litellm](https://github.com/BerriAI/litellm) | [#44800](https://github.com/BerriAI/litellm/pull/44800) — fix(anthropic): let /v1/messages mid-stream failures reach the proxy failure boundary |
| [FastGPT](https://github.com/labring/FastGPT) | [#7924](https://github.com/labring/FastGPT/pull/7924) — fix(workflow): 判断器「包含/不包含」对非字符串值恒返回 false |
| [MaxKB](https://github.com/1Panel-dev/MaxKB) | [#7311](https://github.com/1Panel-dev/MaxKB/pull/7311) — fix: split at full-width exclamation/question marks in smart_split_paragraph |
| [DeepCode](https://github.com/HKUDS/DeepCode) | [#256](https://github.com/HKUDS/DeepCode/pull/256) — fix(tools): prune _SKIP_DIRS below the glob base, not the whole path |
|  | [#255](https://github.com/HKUDS/DeepCode/pull/255) — fix(security): normalize the program path in the destructive-command screen |
| [txtai](https://github.com/neuml/txtai) | [#1362](https://github.com/neuml/txtai/pull/1362) — Honor an empty allow list in the bash agent tool |
| [PraisonAI](https://github.com/MervinPraison/PraisonAI) | [#5732](https://github.com/MervinPraison/PraisonAI/pull/5732) — fix(memory): raise on unsupported URL scheme in array form instead of silently using the file backend |
|  | [#5731](https://github.com/MervinPraison/PraisonAI/pull/5731) — fix: normalise preset name lookup for array-form parameters |
| [gpustack](https://github.com/gpustack/gpustack) | [#6391](https://github.com/gpustack/gpustack/pull/6391) — fix(schemas): validate cache provider card icons |
| [FrontierAgent](https://github.com/ApodexAI/FrontierAgent) | [#66](https://github.com/ApodexAI/FrontierAgent/pull/66) — fix(agent-team): let gate ② track the configurable gate ① for bash / run_python_code |
| [ruby_llm](https://github.com/crmne/ruby_llm) | [#1052](https://github.com/crmne/ruby_llm/pull/1052) — Check Mistral batch embedding row positions before pairing vectors with inputs |
|  | [#1051](https://github.com/crmne/ruby_llm/pull/1051) — Check embedding batch row positions before pairing vectors with inputs |
| [langroid](https://github.com/langroid/langroid) | [#1253](https://github.com/langroid/langroid/pull/1253) — fix(relevance_extractor_agent): send the extraction instructions in the async path too |
|  | [#1250](https://github.com/langroid/langroid/pull/1250) — fix(chat_agent): route the async strict-recovery retry through the async twin |
|  | [#1245](https://github.com/langroid/langroid/pull/1245) — fix(chat_agent): skip strict recovery in the async path when no tool is usable |
|  | [#1244](https://github.com/langroid/langroid/pull/1244) — fix(doc_chat_agent): return NO_ANSWER for an empty query in the async path |
|  | [#1243](https://github.com/langroid/langroid/pull/1243) — fix(openai_gpt): guard a null `choices` chunk in the async stream path |
|  | [#1242](https://github.com/langroid/langroid/pull/1242) — fix(repo_loader): report a failed clone as None, not as the target path |
|  | [#1241](https://github.com/langroid/langroid/pull/1241) — fix(pinecone): raise when add_documents fails instead of only logging |
|  | [#1238](https://github.com/langroid/langroid/pull/1238) — fix: allow special-token literals in Parser.truncate_tokens |
|  | [#1232](https://github.com/langroid/langroid/pull/1232) — fix: match literal types in Agent output extraction |
|  | [#1231](https://github.com/langroid/langroid/pull/1231) — fix: treat empty Redis key deletion as a no-op |
|  | [#1230](https://github.com/langroid/langroid/pull/1230) — fix: raise actionable errors for missing document backends |
|  | [#1229](https://github.com/langroid/langroid/pull/1229) — fix: retain numbered segment paragraph continuations |
|  | [#1228](https://github.com/langroid/langroid/pull/1228) — fix: preserve text across BPE chunk boundaries |
|  | [#1227](https://github.com/langroid/langroid/pull/1227) — fix: honor explicit embedding organization configuration |
|  | [#1226](https://github.com/langroid/langroid/pull/1226) — fix: deduplicate PDF split boundaries |
|  | [#1225](https://github.com/langroid/langroid/pull/1225) — fix: preserve whitespace in line-limited document reads |
|  | [#1223](https://github.com/langroid/langroid/pull/1223) — fix: preserve duplicate columns when loading tabular data |
|  | [#1221](https://github.com/langroid/langroid/pull/1221) — fix: honor zero overlap in Markdown chunking |
|  | [#1220](https://github.com/langroid/langroid/pull/1220) — fix: preserve Markdown hierarchy when heading levels are skipped |
|  | [#1218](https://github.com/langroid/langroid/pull/1218) — fix: handle UTF-8 BOMs when loading tabular data |
|  | [#1215](https://github.com/langroid/langroid/pull/1215) — fix(llm): handle content-filtered chunks in the async stream path |
| [evalscope](https://github.com/modelscope/evalscope) | [#1833](https://github.com/modelscope/evalscope/pull/1833) — fix(messages): absolutise local audio and video paths in markdown output |
|  | [#1832](https://github.com/modelscope/evalscope/pull/1832) — fix(multi-choice): accept a full-width comma between unbracketed answer labels |
| [AntOmniEvo](https://github.com/ant-research/AntOmniEvo) | [#20](https://github.com/ant-research/AntOmniEvo/pull/20) — fix(proposer): detect degenerate Claude Code sessions before rollout |
