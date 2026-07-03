

## 需要安装专属插件：


```
opencode-codegraph
```



## 修改配置：AGENTS.md

C://Users/Administrator//.confiq//opencodel//AGENTS.md




```
## CodeGraph

In repositories indexed by CodeGraph (a `.codegraph/` directory exists at the repo root), reach for it BEFORE grep/find or reading files when you need to understand or locate code:

- **MCP tool** (when available): `codegraph_explore` answers most code questions in one call — the relevant symbols' verbatim source plus the call paths between them, including dynamic-dispatch hops grep can't follow. Name a file or symbol in the query to read its current line-numbered source. If it's listed but deferred, load it by name via tool search.

- **Shell** (always works): `codegraph explore \"<symbol names or question>\"` prints the same output.

If there is no `.codegraph/` directory, skip CodeGraph entirely — indexing is the user's decision.


===============  帮上面的配置换成下面这个 =====================

## CodeGraph

In repositories indexed by CodeGraph (a `.codegraph/` directory exists at the repo root), try it BEFORE grep/find or reading files when you need to understand or locate code, but treat it as an accelerator rather than a hard dependency:

- **MCP tool** (when available): `codegraph_explore` answers most code questions in one call — the relevant symbols' verbatim source plus the call paths between them, including dynamic-dispatch hops grep can't follow. Name a file or symbol in the query to read its current line-numbered source. If it's listed but deferred, load it by name via tool search.

- **Shell** (fallback): `codegraph explore \"<symbol names or question>\"` prints the same output.

Timeout / fallback policy:

- Do not wait indefinitely for CodeGraph. If a CodeGraph call times out, hangs, or returns an unusable partial result, stop depending on that call.

- Retry at most once with a narrower query naming concrete files or symbols.

- If the narrower retry also fails, fall back to `rg`, direct file reads, LSP, and tests for the rest of that investigation.

- Mention the fallback briefly in the user-facing summary when it affected the workflow.

If there is no `.codegraph/` directory, skip CodeGraph entirely — indexing is the user's decision.

<!-- CODEGRAPH_END -->
```
