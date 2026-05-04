---
description: Force entry into Phase 1 (Domain Discovery). Skips state-based phase selection. Use when you explicitly want to (re-)run scenario modeling, event/command extraction, BC delineation, classification, touchpoint map, AI opportunities, or User Stories.
---

請啟動 DDD Architecture Coach（`ddd-architecture-coach` skill）並**強制進入 Phase 1**。

執行步驟：
1. 讀取 `.claude/project-context.md` 與 `.claude/arch-state.md`
2. 讀取 `references/phase1-domain-discovery.md`，依「Mode Selection」決定 system-full / system-incremental / per-bc
3. 跳過 Phase Selection Logic 的自動判斷，直接執行 Phase 1
4. 若使用者另外指定了 BC（在 $ARGUMENTS 中），優先用該 BC 為 per-bc mode 對象

$ARGUMENTS
