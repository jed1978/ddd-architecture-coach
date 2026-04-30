---
description: Launch the DDD Architecture Coach. Reads .claude/arch-state.md to determine current phase and continues from there. Use this as the default entry point.
---

請啟動 DDD Architecture Coach（`ddd-architecture-coach` skill）。

執行步驟：
1. 讀取 `.claude/project-context.md` 與 `.claude/arch-state.md`，判斷目前進度
2. 依 SKILL.md 的「Phase Selection Logic」決定該進入哪個 phase
3. 若 `.claude/arch-state.md` 不存在或 `project-context.md` 未填妥 → 執行 Bootstrap 對話流程
4. 開始該 phase 的工作

$ARGUMENTS
