# DDD Architecture Coach

> 繁體中文版：[README.md](README.md)

A Claude Code skill that helps you make architecture decisions for DDD projects. It produces design documents and decision records — not code.

## What It Does

Guides you through four phases of architecture planning:

1. **Domain Discovery** — structured scenario modeling, event/command extraction, BC delineation, User Stories
2. **Architecture Design** — Context Map, per-BC architecture decisions, cloud deployment blueprint, AI-ADRs
3. **Implementation Spec** — Aggregate design, Key Examples (Gherkin/SBE), layered responsibilities, test specs, CI/CD
4. **Review & Iterate** — five health checklists (DDD / AI / engineering / cloud / SBE)

Output is organized in a BC-centric file structure. Each BC independently progresses through discovery → design → spec, enabling incremental development.

## What It Is Not

- **Not a code generator.** Implementation is handled by Claude Code (or your team) based on the specs this skill produces.
- **Not a Domain Storytelling or Event Storming workshop.** Phase 1 borrows vocabulary and formats from DS and ES, but Claude drafting artifacts is fundamentally different from a facilitated workshop with domain experts. The skill is honest about this distinction.
- **Not a replacement for team discussion.** Key Examples produced by the coach are labeled as AI-drafted baselines. Final sign-off should involve cross-functional review.

## Who It's For

- Developers and architects who want structured DDD architecture planning with Claude Code
- Solo developers or small teams building domain-driven systems who need an architecture thinking partner
- Projects considering AI/LLM integration and needing systematic intervention design with fallback planning

## Key Design Decisions

**Decision priority** (when tradeoffs conflict):
1. Domain correctness > technical elegance
2. Fallback completeness > AI feature richness
3. Verifiability > extensibility
4. Team executability > architectural ideal

**AI is not the default.** Every AI proposal must answer: why AI? what's the fallback? how to verify? A two-tier veto applies — one Hard condition (financial/legal harm without human-in-the-loop) rejects AI unconditionally; three Soft conditions (deterministic alternative reaches 95%+, fallback cost/latency comparable, no golden dataset) presume rejection unless explicitly overridden with documented rationale.

**Specification by Example (SBE)** is built into Phase 3. Key Examples in Gherkin format serve simultaneously as spec, test case, and documentation.

## File Structure

```
ddd-architecture-coach/
├── SKILL.md                              # Master instruction
└── references/
    ├── phase1-domain-discovery.md         # Scenario Modeling + Event/Command Extraction
    ├── phase2-architecture-design.md      # Context Map + BC decisions + deployment
    ├── phase3-implementation-spec.md      # Aggregate design + SBE + test specs
    ├── phase4-review-iterate.md           # Health checklists
    ├── agents/
    │   └── bc-developer.md                # TDD subagent (stack-agnostic, model configurable)
    ├── commands/
    │   ├── arch-coach.md                  # Default entry; reads state, picks phase
    │   ├── phase-1.md ... phase-4.md      # Force entry into a specific phase
    │   └── arch-learn.md                  # Append a learning
    └── templates/
        ├── project-context-template.md    # Project description, tech stack, model choice
        ├── arch-state-template.md         # Coach maintains: progress only (high-frequency writes)
        └── arch-learnings-template.md     # Coach maintains: learnings + open questions (append-only)
```

Bootstrap (the coach's first-run handler) copies `commands/`, `agents/`, and `templates/` into your project's `.claude/` directory.

## Project Output Structure

The coach produces artifacts in a BC-centric layout:

```
docs/
  system/
    domain-stories.md          # Scenarios + event/command timeline (cross-BC)
    context-map.md             # BC classification, relationships, deployment
  {bc}/
    discovery.md               # BC-local events, User Stories, UL, AI opportunities
    decisions.md               # Architecture decisions, AI-ADRs
    spec.md                    # Implementation specification
```

## Getting Started

1. Install the skill in your Claude Code project
2. Run `/arch-coach` (or just describe your project to Claude)
3. **Bootstrap is conversational**: Claude asks three short questions (one-line product description, main tech stack, team size), then drafts `.claude/project-context.md` for you to review — no blank YAML to fill
4. Bootstrap also copies `arch-state.md`, `arch-learnings.md`, the bc-developer agent, and the slash command files into `.claude/`
5. Claude asks which model the bc-developer subagent should use (default Sonnet 4.6; Haiku 4.5 for fast routine TDD; Opus 4.7 for deep reasoning)
6. Phase 1 starts: you describe behaviors, Claude produces the initial domain model — you review and challenge, never fill from scratch

## Requirements

- Claude Code
- A product/project description (3-5 sentences minimum)
- Willingness to challenge Claude's architectural decisions

## Origin

Built and validated on a real multi-tenant SaaS project (AI-assisted booking + scheduling). The skill's rules, self-checks, and review findings emerged from iterative development across multiple Bounded Contexts and 1000+ tests.

## License

MIT
