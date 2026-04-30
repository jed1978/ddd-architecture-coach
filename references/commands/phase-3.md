---
description: Force entry into Phase 3 (Implementation Specification) for one BC. Produces aggregate definitions, key examples (Gherkin), test specs, slice plan. Requires explicit BC argument.
---

請啟動 DDD Architecture Coach（`ddd-architecture-coach` skill）並**強制進入 Phase 3**。

執行步驟：
1. 讀取 `.claude/project-context.md` 與 `.claude/arch-state.md`
2. 讀取 `references/phase3-implementation-spec.md`，依 `arch-state.md` 決定 template-lead / template-from-prior
3. 從 $ARGUMENTS 取得目標 BC 名稱；若 $ARGUMENTS 為空 → 請使用者明確指定 BC（不要自行挑選）
4. 執行 Pre-flight Checks（Phase 1/2 必須完成且穩定）

$ARGUMENTS
