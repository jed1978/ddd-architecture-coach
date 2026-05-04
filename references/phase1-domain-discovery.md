# Phase 1: Domain Discovery

## Goal

From the user's product/project description, produce initial Bounded Context boundaries, Domain Events, Commands, Policies, Ubiquitous Language, User Stories, and AI intervention opportunities.

Phase 1 has two scopes:
- **System-level (Steps 1-4)**: scenarios, event/command timeline, BC delineation, classification → output to `{coach_output_root}/system/`
- **Per-BC (Steps 5-6)**: AI opportunities, User Stories, BC-local UL → output to `{coach_output_root}/{bc}/discovery.md`

> Resolve `{coach_output_root}` from `.claude/project-context.md` (default `docs/ddd/`) before writing.

System-level runs once per project. Per-BC runs once per BC and can be done incrementally as new BCs are identified.

---

## Operating Model

**You lead the production; the user reviews and challenges.** This applies to every step below.

Never ask the user to write raw scenario narratives, draft UL tables from scratch, or fill in BC classification. You draft everything; the user's job is to accept, replace specific terms, challenge specific decisions, or flag missing scenarios.

---

## Language Policy

Per SKILL.md: instructions in English, user-facing output in Traditional Chinese, technical terms in English. Chinese phrases inside 「...」 are verbatim templates.

---

## Mode Selection

Determine execution mode by probing `{coach_output_root}/` (per SKILL.md → State Determination):

| Mode | Trigger | Scope |
|------|---------|-------|
| **system-full** | No `{coach_output_root}/system/domain-stories.md` exists | Run Steps 1-4, then per-BC Steps 5-6 for the first BC |
| **system-incremental** | System-level files exist; user adds a new BC | Update `{coach_output_root}/system/domain-stories.md` with new scenarios/events if needed; skip to per-BC Steps 5-6 |
| **per-bc** | System-level complete, starting a new BC | Run Steps 5-6 only; reuse existing UL; do not rebuild system-level artifacts |

Before entering the steps, tell the user which mode and why.

---

## 6-Step Flow (mandatory order, do not skip)

**Step transition rule**: after each step is confirmed by the user, proactively proceed to the next step. Do not wait for the user to ask. Announce which step is complete and which step comes next.

### Step 1: Scenario Modeling (Claude-led)

**You produce structured business scenarios.** Draft at least 1 happy path + 1 exception scenario based on the `project_description`. If the project description mentions multiple core capabilities, expand to cover each.

> Note: this step uses an Actor → Action → Object narrative format inspired by Domain Storytelling's pictographic language, but is not a full DS workshop. Claude drafts; user reviews and challenges.

**Before drafting, run the Self-Check Clean List** (below) on your own output. Do not present narratives that violate the list — fix them before showing the user.

Output format:

```
情境 A — <一句話描述>：

① <Actor> → <Action> → <Object 或 另一個 Actor>
② ...
⑦ ...
```

After each scenario, if **explanation mode is ON**, include:

```
幾個我替你做的決策，你可以挑戰：

1. <UX 流程選擇>：我選了 <A or B>，因為 <理由>。另一種是 <X>，代價是 <Y>。
2. <Actor 命名>：我把 AI 拆成 <NLU Parser / Category Classifier>，因為 Phase 2 fallback 要分開處理。
3. <術語選擇>：我用了「<某詞>」。你們團隊慣用其他詞嗎？

哪個決策你不同意？
```

If **explanation mode is OFF**, skip the decision-point section and just ask 「確認、還是要改哪裡？」

**Step 1 produces three outputs:**

1. **Scenario narratives** (1 happy + 1 exception minimum; more if product has multiple core capabilities)
2. **Actor list** — categorize as human / AI / deterministic system
3. **UL draft** (see UL Emergence Mechanism section below)

---

### Step 2: Event & Command Extraction (Claude-led)

**From Step 1's scenarios, extract Domain Events, Commands, Policies, and External Systems.** Lay them on a timeline.

> Note: this step borrows vocabulary from Event Storming (Events, Commands, Policies, Aggregates, External Systems) but is not a full ES workshop. Claude extracts; user reviews and challenges.

Rules:

- Event names **use past tense + business language** (forbid technical terms like `DataSyncedToRedis`, `ApiCalled`)
- Command names **use imperative + business language** (e.g., `ConfirmBooking`, not `UpdateStatus`)
- **Every element tags its scenario origin** (e.g., 「情境 A 第 ⑦ 步」)
- **Cover every main path end-to-end** (at least 1 happy + 1 exception); within each path, events form a continuous chain — no gaps. Event count is whatever the paths require, not a fixed number.
- Mark fallback branches and alternative paths
- **Identify Policies**: wherever an event automatically triggers the next command, make the policy explicit using the pattern 「Whenever <Event>, then <Command>」
- **Identify External Systems**: actors outside the system boundary (payment gateway, messaging API, third-party services)

Output format:

```
1. [Command] SubmitUtterance (Actor: Customer)
   → [Event] TransactionInputReceived        ← 情境 A 第 ①

2. [Command] ParseAmount (Actor: NLU Parser)
   → [Event] AmountExtracted                 ← 情境 A 第 ③
   (or) → [Event] ParsingFailed              ← 情境 B 第 ③

3. [Policy] Whenever AmountExtracted → PredictCategory
   [Command] PredictCategory (Actor: Category Classifier)
   → [Event] CategoryPredicted               ← 情境 A 第 ⑤
   (or) → [Event] CategoryUnresolved         ← 情境 B 第 ⑤

[External System] LINE Messaging API          ← 情境 A 第 ① (channel)
[Hotspot] ⚡ Parsing fallback convergence     ← 情境 B 第 ③-⑤
```

**Step 2 produces five outputs:**

1. **Event + Command timeline** (with Policies inline)
2. **Aggregate candidates** — initial judgment from subject-object stickiness
3. **Hotspots** — regions dense with events or where fallbacks converge (Phase 2 will need most attention here)
4. **Policy list** — each policy as 「Whenever <Event>, then <Command>」; these become Phase 3's event handlers
5. **External System list** — each with integration boundary notes; these become Phase 2's Context Map integration points

When explanation mode is ON, explicitly flag hotspots and explain why they matter for Phase 2.

---

### Step 3: BC Delineation

Based on Step 2's hotspots and aggregate stickiness, draft Bounded Contexts:

| BC 名稱 | 所屬 Aggregate | 主要 Events | 命名理由 |

Each BC gets a one-line rationale. Principles:

- **High cohesion**: aggregates within a BC share language and change together
- **Low coupling**: BCs communicate only via explicit integration events
- **Language consistency**: within one BC a term means one thing; across BCs, same-name-different-meaning is permitted

When explanation mode is ON, for each BC list the borderline aggregates you considered placing in a different BC and why you didn't.

---

### Step 4: Core / Supporting / Generic Classification

| 分類 | Domain | 理由 |

Definitions:

- **Core**: the source of product differentiation; without it the product has no reason to exist
- **Supporting**: necessary but not a differentiation source
- **Generic**: standard problems, use mature solutions (do not rebuild)

Write rationale as either 「沒它產品不成立」 or 「有替代實作方案」 — not 「因為這是主要功能」 (which doesn't actually answer the classification question).

---

### Step 5: AI Intervention Opportunities + Open Questions

**AI Intervention Opportunities**:

| 流程節點 | AI 介入方式 | 預期效益 | 風險 | Fallback |

For every row, verify it passes the three-question test:

1. Why must this be AI?
2. What's the fallback when AI fails?
3. How do you verify AI output correctness?

If any intervention point fails the test → apply SKILL.md's AI veto conditions and flag as 「不建議用 AI」. When explanation mode is ON, explain which veto condition applied.

**Open Questions** (max 5): observed information gaps, contradictions, ambiguities. Append into `arch-learnings.md` → `open_questions:`.

---

### Step 6: User Stories Derivation (Claude-led)

**From Step 1's scenarios, derive User Stories.** Group consecutive scenario steps that form a coherent deliverable behavior unit.

Each scenario typically yields 2-4 User Stories.

Output format:

```
US-<BC 縮寫>-<序號>：<User Story 標題>

作為 <角色>，我想要 <行為>，以便 <業務價值>

出處：情境 A 第 ①-③ 步
所屬 BC：<Bounded Context 名稱>
```

Principles:

- **One User Story = one deliverable behavior**: not a single scenario step (too small) and not an entire scenario (too big)
- **Role uses UL terms**: not 「使用者」 but the specific actor name from Step 1
- **Business value is explicit**: not 「方便使用」 but the actual outcome (e.g., 「不需切換 App 即可完成預約」)
- **Tag scenario origin + BC assignment**: every User Story traces back to scenario steps and forward to a BC
- **User Stories are Phase 3's anchor**: Key Examples (Gherkin) in Phase 3 will be organized under these User Stories

When explanation mode is ON, for borderline groupings (steps that could belong to this story or the next), explain the split rationale.

**Step 6 produces two outputs:**

1. **User Story list** with scenario traceability + BC assignment
2. **Updated UL table** if new terms emerged during derivation

---

## Self-Check Clean List

Before presenting any scenario narrative, UL table, or BC classification to the user, verify your draft passes every item below. Do not show the user output that fails these checks — fix it first.

### Scenario Narrative Checks

| # | 檢查項 | 對的長相 | 錯的長相 |
|---|-------|---------|---------|
| 1 | 每步都有具體 actor（主詞） | `① User → 輸入 → ...` | `① 檢查記錄是否正確` |
| 2 | 用第三人稱（主角叫 User，不是「我」） | `⑤ User → 確認 → 正確` | `⑤ 讓我確認這筆資料` |
| 3 | AI 的動作顯式出現，不省略 | `② NLU Parser → 解析 → 金額 580、日期 昨天` | 從 ①「User 輸入」直接跳到 ②「顯示結果」 |
| 4 | Actor 用具體名字，不是「系統」「平台」「後端」 | `⑥ Transaction Service → 建立並持久化 → 一筆流水帳` | `⑥ 系統寫入資料庫` |
| 5 | 隱含的 UX 流程（例：即時儲存 vs 預覽後儲存）已明確選擇、不曖昧 | 敘事前先註明 `B 流程：先預覽，確認後儲存` | ④⑤⑥順序暗示某流程但沒說 |
| 6 | action 和 object 用業務語言，不用技術詞（DB、API、cache、endpoint） | `⑥ → 建立並持久化 → 一筆流水帳` | `⑥ → 寫入 → DB` |
| 7 | 單筆/多筆顆粒度前後一致 | 若選多筆，整條 flow 的 object 都是「1~N 筆」 | ① 一筆、⑥ 多筆 |
| 8 | 每步用 `→` 箭頭，不用空格 | `User → 輸入 → 文字` | `User 輸入 文字` |

### Event & Command Extraction Checks

| # | 檢查項 | 對的長相 | 錯的長相 |
|---|-------|---------|---------|
| 9 | Event 名稱過去式 | `TransactionRecorded` | `RecordTransaction` |
| 10 | Event 名稱用業務語言 | `CategoryPredicted` | `ApiCalled` / `DataSyncedToRedis` |
| 11 | 每個 event 標情境出處 | `3. CategoryPredicted ← 情境 A 第 ⑤` | 沒標出處 |
| 12 | 每條主要路徑（happy + 至少 1 exception）事件連續、不跳步 | 每條路徑從觸發 command 到終結 event 連得起來 | 路徑斷裂、跳過中間步驟、或只有零散 event |
| 13a | 每個 event 都有對應的 command | `[Command] ConfirmBooking → [Event] BookingConfirmed` | Event 沒有觸發源 |
| 13b | 反應鏈有顯式 policy | `[Policy] Whenever ProposedToShop → ConfirmShopAvailability` | Event 直接跳到下一個 command 沒有標 policy |
| 13c | 跨系統邊界的 actor 標記為 External System | `[External System] LINE Messaging API` | 外部服務混入一般 actor |

### BC & Classification Checks

| # | 檢查項 | 對的長相 | 錯的長相 |
|---|-------|---------|---------|
| 13 | UL 表有「易混淆項」欄（標示禁止用語） | 欄位包含 `❌ Expense、❌ Record` | 只有定義 |
| 14 | BC 分類理由回答「為什麼這個分類」 | `Core：沒它產品不成立` / `Generic：有替代實作方案` | `Core：因為這是主要功能` |
| 15 | AI 介入機會點每一條都有 fallback 欄 | `Fallback: 候選清單讓使用者手選` | fallback 欄空白 |

### User Story Checks

| # | 檢查項 | 對的長相 | 錯的長相 |
|---|-------|---------|---------|
| 16 | 每個 User Story 標記情境出處 | `出處：情境 A 第 ①-③ 步` | 沒有出處 |
| 17 | 每個 User Story 標記所屬 BC | `所屬 BC：Session` | 沒有 BC 標記 |
| 18 | 角色用 UL 具體名稱，不用「使用者」 | `作為 顧客` / `作為 店家管理員` | `作為 使用者` |
| 19 | 業務價值是具體結果，不是空泛描述 | `以便 不需切換 App 即可完成預約` | `以便 方便使用` |
| 20 | 顆粒度是可交付行為單元 | 2-5 個情境步驟收斂為一個 story | 一個步驟 = 一個 story（太碎），或整條情境 = 一個 story（太大）|

---

## Claude's Proactive Mechanisms

These are not error checks — they are positive behaviors to execute as part of Step 1-6.

### UL Emergence Mechanism

When the user challenges a term you used or naturally uses a different term when speaking (e.g., during pushback, they refer to the artifact with their own word), **actively capture their word into UL**:

- If the user uses a Chinese business term, map it to an English technical term for code context
- Example: user says 「流水帳」while reviewing your draft → add to UL:

  | 術語 | 定義 | 易混淆項 | 中英對照 |
  |------|------|---------|---------|
  | Transaction | 一筆消費紀錄 | ❌ Expense, ❌ Record | 流水帳 |

- When explanation mode is ON, tell the user 「我把『流水帳』收進 UL，對應英文 term `Transaction`」so they know their language is being preserved.

### Phase 2 Hotspot Flagging

While producing Step 2's event timeline, whenever you encounter a region dense with fallback branches or AI-uncertainty events, flag it explicitly:

> 「這個區域是 hotspot（fallback 匯集處），Phase 2 設計 AI-ADR 和 aggregate 邊界時要特別注意。我會記到 arch-learnings.md 的 open_questions。」

### Deferred Challenge

If you notice a problem that is better addressed in a later step (e.g., while writing Step 1 you realize an AI actor might not need AI at all), **do not interrupt Step 1's flow**. Note it explicitly and defer:

> 「最後一點我先記下來，Step 5 會挑戰『Budget Alert Evaluator 真的需要 AI 嗎？』—— 因為『超支就提醒』像是規則判斷。先不爭論，往下走。」

---

## System-Incremental Mode Special Handling

When `{coach_output_root}/system/` already has domain-stories.md + context-map.md and the user is adding a new BC:

1. **Read existing UL first**: new BC and Aggregate names must be compatible; flag conflicts explicitly
2. **System-level update**: if new BC introduces new scenarios or events, append to `{coach_output_root}/system/domain-stories.md`; do not rewrite existing content
3. **Event extraction focus**: extract only events produced by the new BC; mark integration points with existing BCs using the `IntegrationEvent` suffix
4. **BC delineation**: add-only; do not modify existing BC boundaries unless the user explicitly requests it
5. **Learnings check**: read `arch-learnings.md` for pitfalls prior BCs hit; proactively avoid them

---

## Phase 1 Output Checklist

Phase progress is derived from filesystem (see SKILL.md → State Determination); do NOT write per-phase status, output paths, or summary counts to `arch-state.md`. Only update `current_focus` and `last_updated`.

At the end of Phase 1 system-level (Steps 1-4):

- Write/append `{coach_output_root}/system/domain-stories.md` and the classification section of `{coach_output_root}/system/context-map.md` (these files' existence IS the completion signal).
- Update `arch-state.md` `current_focus` (typically clear `bc` and set `phase: phase_1_step_5_6` once the user picks the first BC).
- Update `last_updated`.

At the end of Phase 1 per-BC (Steps 5-6):

- Write `{coach_output_root}/{bc}/discovery.md` (its existence IS the completion signal).
- Update `arch-state.md` `current_focus.{bc, phase}` to reflect what's next (typically `phase_2` for the same BC, or another BC's `phase_1_step_5_6` if interleaving).
- Update `last_updated`.

Append to `arch-learnings.md`:

- `unresolved_questions` (the cross-phase open questions this BC raised; the file's `open_questions` section)
- Any learnings that emerged: `source: phase_1`

---

