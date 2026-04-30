# DDD Architecture Coach

> English: [README.en.md](README.en.md)

協助你做 DDD 專案架構決策的 Claude Code skill。它產出設計文件與決策記錄 — 不產 code。

## 它做什麼

引導你走完四個階段的架構規劃：

1. **Domain Discovery** — 結構化情境建模、event/command 萃取、BC 劃分、User Stories
2. **Architecture Design** — Context Map、各 BC 內部架構決策、雲端部署藍圖、AI-ADRs
3. **Implementation Spec** — Aggregate 設計、Key Examples（Gherkin/SBE）、分層責任、測試規格、CI/CD
4. **Review & Iterate** — 五份健康度檢查（DDD / AI / engineering / cloud / SBE）

輸出採 BC-centric 檔案結構。每個 BC 獨立走 discovery → design → spec，支援漸進開發。

## 它不做什麼

- **不產 code**。實作交給 Claude Code 或團隊，依本 skill 產出的 spec 進行
- **不是 Domain Storytelling 或 Event Storming workshop**。Phase 1 借用 DS/ES 的詞彙與格式，但 Claude 起草 artifacts 與專家主持的工作坊本質不同 — 本 skill 對此差異保持誠實
- **不取代團隊討論**。coach 產出的 Key Examples 標記為 AI-drafted baseline，最終簽核需要跨職能 review

## 適用對象

- 想用 Claude Code 做結構化 DDD 架構規劃的開發者與架構師
- 一人或小團隊、需要架構思考夥伴的 domain-driven 系統開發者
- 考慮 AI/LLM 整合、需要系統化介入設計與 fallback 規劃的專案

## 設計重點

**決策優先順序**（trade-off 衝突時依此順序裁決）：

1. Domain correctness > technical elegance
2. Fallback completeness > AI feature richness
3. Verifiability > extensibility
4. Team executability > architectural ideal

**AI 不是預設選項**。每個 AI 提案必須回答：為什麼用 AI？fallback 是什麼？怎麼驗證？採兩級 veto — 一個 Hard 條件（financial/legal 損害且無 human-in-the-loop）無條件否決；三個 Soft 條件（deterministic 替代達 95%+ 準確率、fallback 成本/延遲相當、無 golden dataset）推定否決，除非有書面理由 override。

**Specification by Example（SBE）** 內建於 Phase 3。Gherkin 格式的 Key Examples 同時是 spec、測試案例、文件。

## 檔案結構

```
ddd-architecture-coach/
├── SKILL.md                              # 主指令
└── references/
    ├── phase1-domain-discovery.md         # 情境建模 + Event/Command 萃取
    ├── phase2-architecture-design.md      # Context Map + BC 決策 + 部署
    ├── phase3-implementation-spec.md      # Aggregate 設計 + SBE + 測試規格
    ├── phase4-review-iterate.md           # 健康度檢查
    ├── agents/
    │   └── bc-developer.md                # TDD subagent（stack-agnostic、model 可配置）
    ├── commands/
    │   ├── arch-coach.md                  # 預設入口；讀 state、決定 phase
    │   ├── phase-1.md ... phase-4.md      # 強制進入指定 phase
    │   └── arch-learn.md                  # 寫一條 learning
    └── templates/
        ├── project-context-template.md    # 專案描述、tech stack、model 選擇
        ├── arch-state-template.md         # coach 維護：進度（高頻覆寫）
        └── arch-learnings-template.md     # coach 維護：learnings + open questions（append-only）
```

Bootstrap（coach 的首次執行流程）會把 `commands/`、`agents/`、`templates/` 複製到你專案的 `.claude/` 目錄。

## 專案輸出結構

coach 在 BC-centric layout 下產出 artifacts：

```
docs/
  system/
    domain-stories.md          # 情境 + event/command timeline（跨 BC）
    context-map.md             # BC 分類、關係、部署
  {bc}/
    discovery.md               # BC-local events、User Stories、UL、AI 機會
    decisions.md               # 架構決策、AI-ADRs
    spec.md                    # 實作規格
```

## 開始使用

1. 在 Claude Code 專案中安裝這個 skill
2. 執行 `/arch-coach`（或直接向 Claude 描述專案）
3. **Bootstrap 是對話式的**：Claude 問三個短問題（一句話描述產品、主要 tech stack、團隊規模），然後產出 `.claude/project-context.md` 草稿讓你校正 — 不需從零填 YAML
4. Bootstrap 同時複製 `arch-state.md`、`arch-learnings.md`、bc-developer agent、slash command 檔到 `.claude/`
5. Claude 詢問 bc-developer 子 agent 要用哪個 model（預設 Sonnet 4.6；Haiku 4.5 適合快速 routine TDD；Opus 4.7 適合深度推理）
6. Phase 1 開始：你描述行為，Claude 產出初版 domain model — 你 review 與挑戰，不從零填寫

## 系統需求

- Claude Code
- 一段產品 / 專案描述（至少 3-5 句）
- 願意挑戰 Claude 架構決策的態度

## 起源

在一個多租戶 SaaS 專案（AI 輔助預約 + 排程）上開發並驗證。skill 的規則、self-check、review findings 出自跨多個 Bounded Context 與 1000+ 測試的迭代過程。

## 授權

MIT
