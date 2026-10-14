## ✅ 已合并 PR（30 个 / 9 个项目）

| Project | ★ | Merged |
|---|---|---|
| [langgenius/dify](https://github.com/langgenius/dify) | 158.4k | 1 |
| [bytedance/deer-flow](https://github.com/bytedance/deer-flow) | 83.8k | 1 |
| [HKUDS/DeepCode](https://github.com/HKUDS/DeepCode) | 16.7k | 1 |
| [gpustack/gpustack](https://github.com/gpustack/gpustack) | 5.8k | 1 |
| [ApodexAI/FrontierAgent](https://github.com/ApodexAI/FrontierAgent) | 5.2k | 1 |
| [crmne/ruby_llm](https://github.com/crmne/ruby_llm) | 4.5k | 2 |
| [langroid/langroid](https://github.com/langroid/langroid) | 4.1k | 20 |
| [modelscope/evalscope](https://github.com/modelscope/evalscope) | 3.5k | 2 |
| [ant-research/AntOmniEvo](https://github.com/ant-research/AntOmniEvo) | 1.1k | 1 |

---

## [langgenius/dify](https://github.com/langgenius/dify) — ★ 158.4k · 1 个已合并 PR

### [fix(dify-agent): verify E2B binding Home as well as Workspace on acquire](https://github.com/langgenius/dify/pull/44014)
> merged 2026-10-14

`ExecutionBindingBackend.acquire` is contractually required to "verify that its materialized Home and Workspace still exist" (`dify-agent/src/dify_agent/runtime_backend/protocols.py`). The local, enterprise and openshell backends each check both `layout.home_dir` and `layout.workspace_dir` and raise `BindingLostError` when either is missing. The E2B backend only checked `layout.workspace_dir`, even though its own class docstring states that "Materialized Home and Workspace remain distinct logical resources even though E2B couples their physical lifecycle".

...

---

## [bytedance/deer-flow](https://github.com/bytedance/deer-flow) — ★ 83.8k · 1 个已合并 PR

### [fix(sofya): report a format error for a results list with no usable objects](https://github.com/bytedance/deer-flow/pull/6719)
> merged 2026-10-12

`deerflow.community.sofya.tools._response_results` validates a 200 payload before
normalizing it, but it only closed two of the three cases in the containment rule
this provider family already applies:

- non-object payload → format error ✅ (already there)
- `results` container that is not a list → format error ✅ (already there)
- **non-empty `results` list holding no object entries → format error ❌ missing**

The helper filters non-object entries (`[item for item in results if isinstance(item, dict)]`)
and returns that filtered list. When the list was non-empty but every entry was a
scalar, `None`, a nested list, or a number, the result is `[]`, which the callers
cannot distinguish from a genuinely empty search: both `web_search` and `web_fetch`
...

---

## [HKUDS/DeepCode](https://github.com/HKUDS/DeepCode) — ★ 16.7k · 1 个已合并 PR

### [fix(tools): prune _SKIP_DIRS below the glob base, not the whole path](https://github.com/HKUDS/DeepCode/pull/256)
> merged 2026-10-12

`glob` silently reports `"No files matched."` for a workspace whose path contains a
directory component named `build`, `dist`, `venv`, `.venv`, `node_modules`,
`__pycache__` or `.git` — even though the project has matching files. `grep`, which
shares the same `_SKIP_DIRS` constant in the same module, returns results for the very
same workspace.

`GlobTool.execute` prunes ignored directories against `p.parts` of an **absolute** path:

```python
# core/harness/tools/search.py:196-198 (before)
for p in sorted(base.glob(pattern)):
    if any(part in _SKIP_DIRS for part in p.parts):
        continue
```

`base.glob()` yields absolute paths, so `p.parts` also carries every component **above**
the search base — e.g. `('/', 'home', 'me', 'build', 'app', 'src', 'm.py')`. When any of
...

---

## [gpustack/gpustack](https://github.com/gpustack/gpustack) — ★ 5.8k · 1 个已合并 PR

### [fix(schemas): validate cache provider card icons](https://github.com/gpustack/gpustack/pull/6391)
> merged 2026-10-12

`normalize_cache_provider_yaml` stored a cache provider's card `icon` without the
scheme allowlist this repo already applies to the two sibling content sources. A
document could therefore store `javascript:alert(1)` (or an SVG `data:` URI, or a
protocol-relative URL) and have it served to every reader of `GET /v2/cache-providers`.

The fix runs the icons through the existing `validate_icon`, at the write, in the
same place this module already refuses the injection / localized-text /
template-filter violations it knows about — for the card logo and for each L2
backend's logo, both of which that endpoint returns.

```python
 providers = load_cache_providers_document(raw, strict)
+for provider in providers:
+    if provider.icon:
+        provider.icon = validate_icon(provider.icon)
...

---

## [ApodexAI/FrontierAgent](https://github.com/ApodexAI/FrontierAgent) — ★ 5.2k · 1 个已合并 PR

### [fix(agent-team): let gate ② track the configurable gate ① for bash / run_python_code](https://github.com/ApodexAI/FrontierAgent/pull/66)
> merged 2026-10-13

Fixes a gate ① / gate ② budget drift in the agent_team tool-result post-processor.

`bash` and `run_python_code` overflow to disk at **gate ①**, whose cap is
`meta.max_result_chars` = `TOOL_EXEC_RESULT_MAX_CHARS` (8K by default, and the
knob `docs/tool-result-truncation-ab.md` actually turns between 8000 and 50000).
The preview that comes back carries two things at its **tail**: the
`saved read-only at <path>` pointer and the verdict (`exit status`, the pytest
summary line).

`SwarmToolResultPostProcessor` gave those two tools **hardcoded** gate ② budgets
of 4K and 5K — below gate ①'s own preview:

```python
_BUDGETS: ClassVar[dict[str, int]] = {
    "bash": 4_000,
    "run_python_code": 5_000,
    ...
```

Two consequences, both reproduced against the real code path:

...

---

## [crmne/ruby_llm](https://github.com/crmne/ruby_llm) — ★ 4.5k · 2 个已合并 PR

### [Check Mistral batch embedding row positions before pairing vectors with inputs](https://github.com/crmne/ruby_llm/pull/1052)
> merged 2026-10-11

Mistral batch embedding results carry an `index` for each vector, but
`parse_mistral_batch_body` handed the body straight to
`parse_embedding_response`, which maps `data` in wire order. A result whose rows
arrive out of order therefore paired vectors with the wrong inputs, and
duplicate, gapped, negative, non-integer, or missing positions were all accepted
as a success.

Mistral does return the field. The recorded cassette
`spec/fixtures/vcr_cassettes/embedding_basic_functionality_mistral_mistral-embed_can_handle_multiple_texts.yml`
has three rows carrying `"index": 0, 1, 2`.

Every other embedding batch parser already rejects bad positions:

- Gemini — `Protocols::Gemini::EmbeddingBatches#parse_embedding_batch_group`
...

### [Check embedding batch row positions before pairing vectors with inputs](https://github.com/crmne/ruby_llm/pull/1051)
> merged 2026-10-11

# Check embedding batch row positions before pairing vectors with inputs

A successful `/v1/embeddings` batch result holds one row per input, and each row's `index` says which input the vector belongs to. This parser never read it: `ChatCompletions::EmbeddingBatches#parse_batch_result` inherited `OpenAI::Batches#parse_batch_result`, which hands the body straight to `parse_embedding_response`, and that maps `data` in wire order.

So the `index` the provider sends is ignored on this path. Duplicate, gapped, negative, missing, and non-integer positions all produced a **successful** `Embedding` whose vectors were paired with the wrong texts, and rows that came back out of order were paired by arrival rather than by position.

...

---

## [langroid/langroid](https://github.com/langroid/langroid) — ★ 4.1k · 20 个已合并 PR

### [fix(chat_agent): route the async strict-recovery retry through the async twin](https://github.com/langroid/langroid/pull/1250)
> merged 2026-10-13

`ChatAgent.llm_response_async()`'s strict-recovery block is a line-by-line twin of the
sync `llm_response()` block, and every other statement in it dispatches to the async
variant (`llm_response_messages_async`, `response_async`, ...). One line did not: the
retry after a failed tool call was issued through the **synchronous**
`self.llm_response(augmented_message)` instead of `await self.llm_response_async(...)`.

So on the async path a strict recovery — the "your tool call didn't validate, try again
under the AnyTool JSON schema" retry — issued a **blocking sync LLM request from inside
the coroutine**, and never touched the async machinery at all.

- The twin invariant is stated by the repo itself: `llm_response_async`'s docstring is
...

### [fix(chat_agent): skip strict recovery in the async path when no tool is usable](https://github.com/langroid/langroid/pull/1245)
> merged 2026-10-13

`ChatAgent.llm_response_async()` is missing the `AnyTool is None` guard that the
synchronous twin has. Same recovery block, `chat_agent.py:1676` (sync) versus
`chat_agent.py:1763` (async):

```python
# llm_response()            -- sync
self.tool_error = False
AnyTool = self._get_any_tool_message()
if AnyTool is None:
    return None                       # <-- guard
self.set_output_format(AnyTool, force_tools=True, use=True,
                       handle=True, instructions=True)
...

# llm_response_async()      -- async
self.tool_error = False
AnyTool = self._get_any_tool_message()
self.set_output_format(AnyTool, force_tools=True, use=True,
                       handle=True, instructions=True)
...
```

`_get_any_tool_message()` returns `None` when there is no tool left to wrap —
...

### [fix(doc_chat_agent): return NO_ANSWER for an empty query in the async path](https://github.com/langroid/langroid/pull/1244)
> merged 2026-10-13

`DocChatAgent.llm_response_async()` returns `None` for an empty query, where the
synchronous twin returns a `NO_ANSWER` document:

```python
# llm_response()          (doc_chat_agent.py:973)
if query_str == "":
    return ChatDocument(
        content=NO_ANSWER + " since query was empty",
        metadata=ChatDocMetaData(
            source="No query provided",
            sender=Entity.LLM,
        ),
    )

# llm_response_async()    (doc_chat_agent.py:1024)
if query_str == "":
    return None
```

The two methods are otherwise a line-by-line mirror: the same
`None`/`!` → `""` → `"?"` → `summar…`/`??` → `else` branch chain, in the same
order, with only async-specific adaptations (`apply_nest_asyncio`, `await`,
`asyncio.to_thread`). This is the one branch whose body differs.

...

### [fix(openai_gpt): guard a null `choices` chunk in the async stream path](https://github.com/langroid/langroid/pull/1243)
> merged 2026-10-13

`OpenAIGPT._process_stream_event_async()` reads

```python
choices = event.get("choices", [{}])
if len(choices) == 0:
    choices = [{}]
```

`event.get("choices", [{}])` only substitutes the default when the key is
**absent**; when a chunk carries `"choices": null` it returns `None`, and
`len(None)` raises `TypeError: object of type 'NoneType' has no len()`.

That `TypeError` does not surface. `_stream_response_async()` wraps the whole
`async for event in response` loop in a bare `except Exception` that only logs

```
Error while processing stream response: object of type 'NoneType' has no len()
```

so the loop is abandoned **mid-stream** and the caller receives
`self._create_stream_response(..., completion="")` — an empty message, with no
...

### [fix(repo_loader): report a failed clone as None, not as the target path](https://github.com/langroid/langroid/pull/1242)
> merged 2026-10-13

`RepoLoader.clone()` catches every clone failure, logs it, and then returns
`self.clone_path` — the path it was *going to* clone into, not a path that holds
a repository. A caller therefore cannot tell a failed clone from a successful
one, and `load()` walks a directory that does not exist.

The consequence in `load()` (`langroid/parsing/repo_loader.py:423-427`):

```python
if path is None:
    if self.clone_path is None or not _has_files(self.clone_path):
        self.clone()
    path = self.clone_path
if path is None:
    raise ValueError("Unable to clone repo")     # unreachable today
return self.load_from_folder(path=path, ...)
```

That guard is dead code: `clone()` sets `self.clone_path = path` *before* it
tries, so after a failure the path is non-`None` and the `ValueError` never
...

### [fix(pinecone): raise when add_documents fails instead of only logging](https://github.com/langroid/langroid/pull/1241)
> merged 2026-10-13

`PineconeDB.add_documents` caught `PineconeApiException` per batch, logged it, and
kept going, so a write Pinecone rejected was reported to the caller as done. Any
`add_documents` / `ingest_docs` call returns normally, and the documents that
Pinecone refused are silently missing from the index.

This is the same defect LanceDB had, fixed for LanceDB in #1201.

From #1201's commit message:

> LanceDB.add_documents caught every exception, logged it and returned, so
> a write LanceDB rejected was reported to the caller as done. [...] A later
> session on the same store never finds those chunks.

#1201's `/files` is `langroid/vector_store/lancedb.py` + `tests/main/test_vector_stores.py`
only — `pineconedb.py` was never touched, and its handler is unchanged since the
...

### [fix: allow special-token literals in Parser.truncate_tokens](https://github.com/langroid/langroid/pull/1238)
> merged 2026-10-12

`Parser.num_tokens` explicitly allows `<|endoftext|>` and `Parser.chunk_tokens` passes `disallowed_special=()` to the same tiktoken encoder, but `truncate_tokens` was the one `encode()` call still using tiktoken's default `disallowed_special="all"`. Any text containing a tiktoken special-token literal (`<|endoftext|>`, `<|im_start|>`, `<|end|>` …) crashes it with:

```
ValueError: Encountered text corresponding to disallowed special token '<|endoftext|>'.
```

...

### [fix: match literal types in Agent output extraction](https://github.com/langroid/langroid/pull/1232)
> merged 2026-10-11

Requesting an enum-like `Literal` output from `ChatAgent.from_ChatDocument` raises `TypeError: typing.Literal cannot be used with isinstance()`, both for raw `content_any` and ToolMessage fields. The same failure occurs for nested hints such as `list[Literal["ready", "done"]]`.

Handle `Literal` before the generic `isinstance` branch in the existing recursive matcher. Require matching value and concrete type, so `True` does not match `Literal[1]` and `0` does not match `Literal[False]`, consistent with [PEP 586's literal equivalence rules](https://peps.python.org/pep-0586/#equivalence-of-two-literals). Existing union/container recursion then handles nested literal hints.

...

### [fix: treat empty Redis key deletion as a no-op](https://github.com/langroid/langroid/pull/1231)
> merged 2026-10-11

`RedisCache.delete_keys([])` sends `DEL` without arguments, which raises `redis.exceptions.ResponseError` instead of completing an empty deletion request. This occurs both with the fake cache backend and a real Redis server, whether the cache is empty or contains unrelated data.

Return immediately for an empty key list and document the no-op behavior. Nonempty deletion retains the existing command and error handling.

Regression tests cover empty and populated fake caches, selective nonempty deletion, nonexistent keys and repeated deletion.

Validation (Python 3.11.16, real editable Langroid package):
- New tests against original production code: 2 failed, 1 nonempty-deletion control passed; existing unit control passed.
...

### [fix: raise actionable errors for missing document backends](https://github.com/langroid/langroid/pull/1230)
> merged 2026-10-11

When `fitz` or `markitdown` is unavailable, four document backends construct `LangroidImportError` but do not raise it. Execution continues into an unbound `fitz`/`MarkItDown` name and fails with `UnboundLocalError`, hiding the existing dependency installation instructions.

Raise the existing exception in the Fitz PDF and MarkItDown DOCX/XLSX/PPTX backends. Preserve the configured backend selection and existing package/extra installation hints.

Add eight regression cases through the public `DocumentParser.create` + `get_doc`/`get_doc_chunks` APIs. The tests selectively simulate the missing import, assert the actionable exception/install command, and require no network or optional backend installation.

Validation (Python 3.11.16, real editable Langroid package):
...

### [fix: retain numbered segment paragraph continuations](https://github.com/langroid/langroid/pull/1229)
> merged 2026-10-11

Numbered segments can span paragraphs, but `extract_numbered_segments` currently scans each paragraph independently and drops any text before its first marker. With the documented whole-passage mode (`number_segments(text, granularity=-1)`), extracting segment `1` from a three-paragraph passage returns only the first paragraph. A multi-sentence segment that continues into another paragraph loses that continuation too. `RelevanceExtractorAgent.extract_segments` uses this helper for its tool result.

...

### [fix: preserve text across BPE chunk boundaries](https://github.com/langroid/langroid/pull/1228)
> merged 2026-10-11

`Parser.chunk_tokens` can corrupt Unicode at BPE boundaries and repeat text when advancing through the original token list. For example, with token splitting, `chunk_size=1` and `discard_chunk_chars=0`, `你好世界` becomes `你好��界` and emoji become replacement characters. A decoded prefix can also re-encode to fewer tokens than the original slice, making the next chunk repeat already-emitted ASCII text.

...

### [fix: honor explicit embedding organization configuration](https://github.com/langroid/langroid/pull/1227)
> merged 2026-10-11

An explicit `OpenAIEmbeddingsConfig(organization="org-explicit")` is overwritten during construction with `OPENAI_ORGANIZATION` (or an empty string when unset). Consequently the OpenAI embedding client receives a different organization from the one selected by its caller, and the supplied configuration is mutated to that value.

Use the configured organization when nonempty, falling back to the environment otherwise. This matches the existing API-key precedence and retains .env values loaded after the configuration was created.

...

### [fix: deduplicate PDF split boundaries](https://github.com/langroid/langroid/pull/1226)
> merged 2026-10-11

Repeated PDF split boundaries currently produce reversed page ranges and extra page content. For a four-page PDF, `splits=[2, 2]` creates a `pages_3_to_2.pdf` chunk containing pages in reverse order, in addition to the expected 1–2 and 3–4 chunks. Repeating the last boundary also creates an extra chunk.

Deduplicate valid split points before sorting them. This preserves the existing handling of unsorted/out-of-range points, empty lists and individual-page splitting, while ensuring each original page appears once in order.

...

### [fix: preserve whitespace in line-limited document reads](https://github.com/langroid/langroid/pull/1225)
> merged 2026-10-11

Line-limited document ingestion strips every selected line, removing Python indentation, Markdown hard-break spaces and intentional plain-text whitespace. `RepoLoader.load_from_folder` does this for repository files; `DocumentParser.chunks_from_path_or_bytes` has the same behavior for local paths, bytes, URLs and local HTML. For example, `def greet():\n    return "hello"` becomes invalid Python (`IndentationError`). Reads without a line limit already preserve this whitespace in DocumentParser.

Remove only CR/LF terminators when joining selected lines. Keep whitespace-only repository selections out of the file tree and document list. Preserve existing file selection, path containment, line limits, newline normalization and HTML/Markdown conversion mechanisms.

...

### [fix: preserve duplicate columns when loading tabular data](https://github.com/langroid/langroid/pull/1223)
> merged 2026-10-12

`read_tabular_data` silently drops repeated CSV columns: `price,price,quantity` loads only `price` and `quantity`, losing the second price column. pandas renames duplicates (`price.1`), while the loader's `usecols` list contains the original header names.

Select non-blank header columns by position, leaving duplicate-name disambiguation to pandas. Apply the same selection to the existing multi-character separator path. Regression tests cover comma/semicolon/tab delimiters, automatic and explicit detection, interspersed blank columns, a multi-character separator, and an existing suffix-style name that collides with a generated duplicate name.

...

### [fix: honor zero overlap in Markdown chunking](https://github.com/langroid/langroid/pull/1221)
> merged 2026-10-11

Setting `MarkdownChunkConfig(overlap_tokens=0)` unexpectedly copies the whole previous chunk into the next chunk. The overlap expression uses `[-config.overlap_tokens:]`; Python treats `[-0:]` as `[0:]`.

For three four-word paragraphs with a four-word chunk size, the old implementation produces chunks containing 4, 8 and 12 words, instead of 4, 4 and 4. Skip extracting overlap when the configured overlap is zero.

Tests assert exact chunk contents for overlap values 0, 1 and 2 through `recursive_chunk` and the public `chunk_markdown` entry point with both rollup settings.

Validation:
- Original Markdown suite: 36 passed.
...

### [fix: preserve Markdown hierarchy when heading levels are skipped](https://github.com/langroid/langroid/pull/1220)
> merged 2026-10-11

`parse_markdown_headings` uses a heading's path length as its Markdown level. These differ when a document starts below H1 or skips a level. For `# Root`, `### First`, `### Second`, the second H3 becomes a child of the first, and `chunk_markdown(..., rollup=False)` incorrectly adds `### First` to the second section's context.

Store each heading's actual level alongside its node in the parser stack and use that level when deciding which headings to pop. The public Node structure remains the same.

Regression tests cover repeated H2/H3/H4/H6 roots, siblings after skipped levels, returning to a shallower heading after a gap, and the public chunking result's header context.

Validation:
- Existing Markdown tests before changes: 36 passed.
...

### [fix: handle UTF-8 BOMs when loading tabular data](https://github.com/langroid/langroid/pull/1218)
> merged 2026-10-11

UTF-8 CSV files with a BOM fail in `read_tabular_data`: the preliminary header reader retains `\ufeff`, while `pd.read_csv` removes it, so `usecols` no longer matches the parsed column names. A BOM before an opening quote also prevents `csv.reader` from recognizing that quoted field.

Use `utf-8-sig` for delimiter sampling and header inspection so the BOM is consumed before CSV parsing. Files without a BOM continue to decode as ordinary UTF-8.

Regression coverage includes comma, semicolon and tab delimiters; explicit and inferred separators; ordinary, quoted delimiter-containing and blank first headers; and the same cases without a BOM.

Validation (Python 3.11.16, pandas 2.3.3):
- Original 15 table-loader tests passed before changes.
...

### [fix(llm): handle content-filtered chunks in the async stream path](https://github.com/langroid/langroid/pull/1215)
> merged 2026-10-11

`OpenAIGPT` has two twin implementations for handling one streaming chunk:

| | sync | async |
|---|---|---|
| chunk handler | `_process_stream_event` | `_process_stream_event_async` |
| loop | `_stream_response` | `_stream_response_async` |
| entry point | `chat()` | `achat()` |

Only the **sync** twin converts a `finish_reason == "content_filter"` chunk into a
non-empty message:

```python
# langroid/language_models/openai_gpt.py:1587 (sync only)
finish_reason = choices[0].get("finish_reason", "")
if not event_text and finish_reason == "content_filter":
    filter_names = [
        n
        for n, r in choices[0].get("content_filter_results", {}).items()
        if r.get("filtered")
    ]
    event_text = (
...

---

## [modelscope/evalscope](https://github.com/modelscope/evalscope) — ★ 3.5k · 2 个已合并 PR

### [fix(messages): absolutise local audio and video paths in markdown output](https://github.com/modelscope/evalscope/pull/1833)
> merged 2026-10-10

`messages_to_markdown` renders local media differently depending on the content type. The `ContentImage` branch resolves a local file to an absolute path and skips `max_length` truncation; the `ContentAudio` and `ContentVideo` branches do neither, so:

1. a relative path is emitted verbatim, which downstream renderers cannot tell apart from a base64 payload, and
2. `max_length` truncates a real path into a prefix that no longer exists.

- #1562 (`970e9518`, "fix(web): render local-path media in **both** dashboard chains") introduced the `os.path.isfile` / `os.path.abspath` handling, but only in the `ContentImage` branch of this same function.
...

### [fix(multi-choice): accept a full-width comma between unbracketed answer labels](https://github.com/modelscope/evalscope/pull/1832)
> merged 2026-10-10

`_PLAIN_LABEL_RE` — the unbracketed label pattern used by `parse_answers` — accepts `,`, `、` and `/` as separators between answer labels, but **not** the full-width comma `，` (U+FF0C). A multi-select answer written as `ANSWER: A，C` therefore keeps only its first label and is scored as wrong.

This is the last of the three label paths to accept that separator:

| path | separator `，` | covered by a test |
|---|---|---|
| `parse_answers` + `_BRACKETED_LABEL_RE` (`ANSWER: (A，C)`) | yes | `test_parse_answers_wrapped_multiple_answers_full_width_comma` |
| `parse_answers_zh` + `_PLAIN_LABEL_ZH_RE` (`答案：A，C`) | yes | `test_parse_answers_zh_multiple_correct_slash_and_ideographic_comma` |
| `parse_answers` + `_PLAIN_LABEL_RE` (`ANSWER: A，C`) | **no** | — |

...

---

## [ant-research/AntOmniEvo](https://github.com/ant-research/AntOmniEvo) — ★ 1.1k · 1 个已合并 PR

### [fix(proposer): detect degenerate Claude Code sessions before rollout](https://github.com/ant-research/AntOmniEvo/pull/20)
> merged 2026-10-12

It stops `ClaudeCodeProposer` from promoting a **half-finished** mutation to a candidate when the
upstream gateway starts returning empty assistant messages mid-session.

`find_degenerate_ending()` already exists for exactly this failure. This PR routes it into the
Claude Code backend, so both agent backends apply the same rule.

`trajectory_utils.py` describes itself as *"Generic trajectory utilities **shared across coding
agent implementations**"*, and the detector keys off **span shape** (`span_type == "model"`) — not
any Pi-specific event. But `grep -rn find_degenerate_ending antomnievo/` shows exactly one caller:

```
antomnievo/proposer/pi_coding_agent_proposer.py:31   (import)
antomnievo/proposer/pi_coding_agent_proposer.py:138  degenerate = find_degenerate_ending(trajectory)
```

...

---
