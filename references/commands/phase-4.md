---
description: Force entry into Phase 4 (Review & Iterate). Reviews any phase or BC artifact against five health checklists (DDD / AI / Engineering / Cloud / SBE) and produces scored findings.
---

請啟動 DDD Architecture Coach（`ddd-architecture-coach` skill）並**強制進入 Phase 4**。

執行步驟：
1. 讀取 `.claude/arch-state.md`、`.claude/arch-learnings.md`（避免重複 flag 已解決的問題）
2. 讀取 `references/phase4-review-iterate.md`
3. 依 $ARGUMENTS 判斷 review scope（Phase 1 / Phase 2 / Phase 3 BC 名稱 / full review）
4. 執行 Pre-flight Checks，依適用的 checklist 產出評分報告

$ARGUMENTS
