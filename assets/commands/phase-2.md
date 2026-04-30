---
description: Force entry into Phase 2 (Architecture Design). Use when you want to (re-)work on Context Map, per-BC architecture decisions, cloud deployment, or AI-ADRs.
---

請啟動 DDD Architecture Coach（`ddd-architecture-coach` skill）並**強制進入 Phase 2**。

執行步驟：
1. 讀取 `.claude/project-context.md` 與 `.claude/arch-state.md`
2. 讀取 `references/phase2-architecture-design.md`，依「Mode Selection」決定 greenfield / incremental
3. 執行 Pre-flight Checks，缺資料則停下並請使用者補
4. 若使用者另外指定了 BC（在 $ARGUMENTS 中），以該 BC 為 per-BC 範圍焦點

$ARGUMENTS
