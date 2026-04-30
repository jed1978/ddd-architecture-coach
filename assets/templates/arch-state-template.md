# Architecture State

> This file is maintained by the DDD Architecture Coach and tracks **progress only** (current focus, per-phase status, output paths, summary counts).
>
> Learnings, decision history, and cross-phase open questions live in `.claude/arch-learnings.md` (append-only). See SKILL.md → Memory / State / Learnings for the three-layer separation.
>
> Users may edit manually but should preserve the `last_updated` field.
> Format: YAML-in-Markdown. Overwriting fields here is normal — this file represents the current snapshot.
>
> Paths use the placeholder `{coach_output_root}` — when reading or writing, the coach resolves it from `project-context.md`. The default is `docs/ddd/`.

---

## Meta

```yaml
last_updated:            # ISO date, e.g., 2026-04-21
system_level_status:     # not_started | phase_1_complete | phase_2_complete
current_focus_bc:        # BC name currently being worked on; blank if system-level
current_focus_phase:     # phase_1 | phase_2 | phase_3 | phase_4
```

---

## Phase 1: Domain Discovery

### System-level (Steps 1-4)

```yaml
phase_1_system:
  status: not_started      # not_started | in_progress | completed
  output_docs:
    domain_stories: "{coach_output_root}/system/domain-stories.md"
    context_map: "{coach_output_root}/system/context-map.md"   # classification section

  summary:
    bc_count:
    bc_names: []
    core_domains: []
    supporting_domains: []
    generic_domains: []
```

### Per-BC (Steps 5-6)

```yaml
phase_1_bc:
  # session:
  #   status: not_started  # not_started | in_progress | completed
  #   output_doc: "{coach_output_root}/session/discovery.md"
  #   user_stories_count:
  #   ai_intervention_points:
```

> Phase 1 unresolved / resolved questions live in `arch-learnings.md` → `open_questions`.

---

## Phase 2: Architecture Design

### System-level

```yaml
phase_2_system:
  status: not_started
  output_doc: "{coach_output_root}/system/context-map.md"     # relationships + deployment sections

  context_map:
    relationships:
      # - from: <BC_A>
      #   to: <BC_B>
      #   pattern:         # Shared Kernel / Customer-Supplier / Conformist / ACL / OHS+PL / Separate Ways
      #   reason:          # one-line rationale

  deployment:
    platform:
    deployment_decision:   # Modular Monolith / Microservices / etc.
    per_bc:
      # - bc:
      #   deploy_unit:
      #   cloud_services:
      #   scaling:
      #   cost_tier:
      #   observability:
```

### Per-BC

```yaml
phase_2_bc:
  # session:
  #   status: not_started
  #   output_doc: "{coach_output_root}/session/decisions.md"
  #   architecture_style:
  #   persistence:
  #   ai_components:
  #   communication:
  #   testing:
  #   ai_adrs:
  #     - id: adr-001
  #       title:
  #       decision:        # prompting / RAG / agent / fine-tuning / not using AI
```

---

## Phase 3: Implementation Specification

Phase 3 runs per BC; multiple BCs can be done in separate sessions.

```yaml
status: not_started      # not_started = no BC started; once any BC starts, use in_progress
completed_bcs: []
in_progress_bcs: []

output_docs:
  # - bc:
  #   path:              # e.g., "{coach_output_root}/session/spec.md"

per_bc_spec_summary:
  # - bc:
  #   aggregates:
  #     - name:
  #       invariants_count:
  #       events_emitted: []
  #   repositories: []
  #   external_integrations: []
  #   test_scenarios_count:
```

---

## Phase 4: Review History

```yaml
reviews:
  # - date:
  #   phase_reviewed:    # phase_1 | phase_2 | phase_3:<BC_name>
  #   scorecard:
  #     ddd_health:      # e.g., "2 ✅ / 1 ⚠️ / 0 ❌"
  #     ai_health:
  #     eng_health:
  #     cloud_health:
  #   critical_issues:
  #     # - issue:
  #     #   severity: P0 | P1 | P2
  #     #   suggested_fix:
  #   warnings: []
  #   actions_taken: []
```

---

> Learnings and cross-phase open questions live in `.claude/arch-learnings.md`. Do NOT add them here — this file is for high-frequency progress writes only.
