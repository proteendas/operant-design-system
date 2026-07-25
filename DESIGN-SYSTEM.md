# Operant — an AI Workflow Design System

> A self-contained design language for **AI-assisted, agentic, human-in-the-loop products**:
> copilots, workflow automation, incident triage, research assistants, prompt orchestration,
> and approval/review pipelines.
>
> **Personality:** intelligent · operational · trustworthy · efficient · calm under complexity.
> **Reference peers:** Linear, GitHub, Vercel, Stripe Dashboard — *not* landing-page kits.

This document is the single source of truth. Machine-readable tokens live in
[`css/tokens.css`](css/tokens.css); components in [`css/components.css`](css/components.css);
runnable screens in [`examples/`](examples/); deterministic rules for coding agents in
[`AGENTS.md`](AGENTS.md).

---

## Table of contents
1. [Design philosophy](#1-design-philosophy)
2. [Token system](#2-token-system)
3. [Typography system](#3-typography-system)
4. [Layout system](#4-layout-system)
5. [Core components](#5-core-components)
6. [AI-specific patterns](#6-ai-specific-patterns)
7. [Example screens](#7-example-screens)
8. [CSS variable layer](#8-css-variable-layer) → [`css/tokens.css`](css/tokens.css)
9. [Component markup examples](#9-component-markup-examples)
10. [Design rules for coding agents](#10-design-rules-for-coding-agents) → [`AGENTS.md`](AGENTS.md)

---

## 1. Design philosophy

### 1.1 Product personality
Operant is the visual language of an **operations console for intelligent systems**. The user is
usually a professional supervising, steering, or auditing autonomous and semi-autonomous work. The
interface must earn trust the way an aircraft cockpit or a trading terminal does: every state is
legible, every automated action is attributable, and nothing important is hidden behind delight.

Five personality anchors:
- **Intelligent** — surfaces reasoning, evidence, and confidence, never just verdicts.
- **Operational** — built for monitoring, intervening, and auditing, not casual browsing.
- **Trustworthy** — provenance, versioning, and reversibility are first-class, not footnotes.
- **Efficient** — high information density with zero decorative tax; keyboard-first.
- **Calm under complexity** — restrained color and motion so that *meaningful* signals stand out.

### 1.2 UX principles
1. **The human is the supervisor, not the operator.** Default to review-and-approve flows. Any
   consequential agent action exposes an approve / edit / reject / retry path.
2. **Every automated action is attributable.** Who (which agent/model/prompt version), when, on what
   input, with what result — always reachable in ≤1 interaction.
3. **Show confidence, not just conclusions.** Distinguish *confidence* (a model's self-estimate) from
   *certainty* (verified fact). Never present a low-confidence guess as settled.
4. **Progressive disclosure of reasoning.** Answer first; expose chain-of-thought, tools, and sources
   on demand. Dense by default, deep on click.
5. **State is never ambiguous.** Thinking, streaming, waiting-for-human, succeeded, failed, and
   escalated are visually distinct and consistently coded system-wide.
6. **Reversibility over confirmation dialogs.** Prefer undo + audit trail to modal gatekeeping,
   except for irreversible or high-blast-radius actions.
7. **Keyboard is a first-class input.** Command palette, focus management, and shortcuts are baseline,
   not enhancements.
8. **Fail loud, fallback gracefully.** Errors propose the next action (retry, escalate, edit input).

### 1.3 Visual principles
- **Neutral canvas, semantic ink.** Cool-slate neutrals carry structure; color is reserved for
  *meaning* (status, agent state, confidence). If everything is colorful, nothing is a signal.
- **Density with air.** Tight 4px grid, but consistent rhythm so dense tables and logs stay scannable.
- **Borders over shadows for structure; shadows only for true elevation** (menus, modals, toasts).
- **Monospace is a semantic surface**, not a style. Anything machine-authored or machine-parsed —
  prompts, logs, traces, tool calls, JSON, diffs, citations — renders in the mono sub-system.
- **Motion is confirmation, not decoration.** 120–260ms, ease-out, purposeful. The only continuous
  motion allowed is *live-system feedback*: streaming caret, thinking dots, active pulse.
- **Dark mode is a peer, not an afterthought.** Ops teams live in dark; both themes are fully specified.

### 1.4 How this differs from a standard CRUD app
| Standard CRUD app | Operant (AI workflow) |
|---|---|
| Forms create/edit records | **Prompts** initiate probabilistic work |
| Deterministic success/error | **Confidence, uncertainty, partial** + streaming/thinking states |
| A row is data | A row is often a **run** — an attributable, replayable unit of agent work |
| Actions are instant | Actions are **long-running, cancelable, resumable, escalatable** |
| Audit is optional | **Traceability is core** — provenance, prompt versions, tool traces |
| One correct output | Frequently **multiple candidate outputs** to compare and pick |
| User does the work | User **supervises, reviews, and hands off** work |

---

## 2. Token system

Tokens are layered: **primitives → semantic → theme → density**. Components consume *only* semantic
tokens. Full values in [`css/tokens.css`](css/tokens.css); this section is the mental model.

### 2.1 Color
Semantic color families (each resolves per theme):

- **Surfaces:** `--color-bg` (canvas), `--color-surface` (cards/panels),
  `--color-surface-elevated` (menus/modals + shadow), `--color-surface-sunken` (wells, code, console),
  `--color-surface-hover` / `-active` / `-selected`.
- **Text:** `--color-text`, `--color-text-muted`, `--color-text-subtle`, `--color-text-faint`,
  `--color-text-onbrand`, `--color-text-link`.
- **Borders:** `--color-border`, `--color-border-strong`, `--color-border-subtle`, `--color-divider`.
- **Brand:** `--color-primary` (+ `-hover`/`-active`/`-subtle`/`-border`), `--color-accent`.
- **Status:** `--color-success`, `--color-warning`, `--color-danger`, `--color-info` —
  each with a `-subtle` (fill) and `-border` variant.
- **AI / agent semantics (the differentiators):**
  - `--color-agent-active` — thinking/running (iris/violet, distinct from status)
  - `--color-agent-idle` — queued/available
  - `--color-agent-error` — agent failed
  - `--color-human-review` — needs a person (amber)
  - `--color-automation-step` — deterministic/tool step (teal)
  - `--color-streaming` — live token stream accent
  - `--color-confidence-high` / `-med` / `-low`

**Why agent color ≠ status color:** status (green/amber/red/blue) describes *outcomes*; agent color
(iris) describes *identity and activity*. Keeping them on different hues prevents "the AI is working"
from being confused with "success/warning/error."

### 2.2 Typography, spacing, radius, borders
See [§3](#3-typography-system). Spacing is a **4px grid** exposed as `--space-1`…`--space-32`
(never use raw px). Radius scale `--radius-xs`…`--radius-xl` + `--radius-full`; default control radius
is `--radius-md` (7px), cards `--radius-lg` (10px). Borders: `--border-hairline` (1px) for structure,
`--border-strong` (1.5px) for emphasis, `--border-focus` (2px) for focus rings.

### 2.3 Shadows, layout widths, motion, z-index, icons
- **Shadows:** `--shadow-xs`…`--shadow-lg`. Structure uses borders; shadow signals *elevation only*.
- **Layout widths:** `--w-rail` 56, `--w-nav` 240, `--w-context` 360, `--w-content-max` 1440,
  `--w-reading-max` 720 (max prose line length), plus `--h-topnav`, `--h-toolbar`, `--h-row`.
- **Motion:** `--dur-fast` 120 / `--dur-base` 180 / `--dur-slow` 260 with `--ease-out`.
  Auto-zeroed under `prefers-reduced-motion`.
- **Z-index:** named scale `--z-sticky` 100 → `--z-command` 900 (palette above all).
- **Icons:** `--size-icon-xs`…`-xl`; default `--size-icon-md` 16px.

### 2.4 Data density modes
Two modes via `data-density` on `<html>` **or any subtree** (e.g. a single table):
- `comfortable` (default) — 40px rows, 14px body.
- `compact` — 32px rows, 13px body, tighter padding.
Components read `--row-h`, `--control-h`, `--density-pad-x/y`, `--density-gap`, `--density-font`,
so density cascades without per-component overrides.

---

## 3. Typography system

- **Primary UI font:** Inter (system-ui fallback stack). Chosen for legibility at 12–14px in dense
  tables and its tabular-figure support for metrics.
- **Display:** Inter with tight tracking (`--tracking-tight`) for large titles. No separate display
  face — coherence over flourish. (Swap `--font-display` to introduce one if a brand requires it.)
- **Monospace:** JetBrains Mono (ui-monospace fallback). This is a **semantic sub-system**, not
  decoration — see §3.3.

### 3.1 Type scale
Minor-third-ish scale tuned denser than a marketing site:

| Token | px | Role |
|---|---|---|
| `--fs-2xs` | 11 | timestamps, dense table meta, cite refs |
| `--fs-xs`  | 12 | labels, badges, captions, eyebrows |
| `--fs-sm`  | 13 | dense body, table cells, logs |
| `--fs-md`  | 14 | **default UI body** |
| `--fs-lg`  | 16 | prompt & response reading surfaces |
| `--fs-xl`  | 18 | card titles, section heads |
| `--fs-2xl` | 22 | page titles |
| `--fs-3xl` | 28 | display / metric values |
| `--fs-4xl` | 36 | hero (rare in-app) |

Line-height: `--lh-tight` 1.2 (headings) · `--lh-normal` 1.5 (UI) · `--lh-relaxed` 1.65 (AI prose).

### 3.2 Heading & body usage
- **Page title** → `.text-title` (22/semibold). One per screen.
- **Section head** → `.text-heading` (18/semibold) or `.text-eyebrow` (uppercase 12) for dense groups.
- **Body** → `.text-body` (14). **AI response prose** → `.text-body-lg` (16/relaxed) capped at
  `--w-reading-max` for readability.
- **Meta/caption** → `.text-caption` / `.text-sm` in muted/subtle color.
- **Numbers** → add `.tabular` (tabular-nums) for any aligned/animating figure.

### 3.3 Monospace sub-system (prompts, logs, traces, tool calls, structured output)
Use `--font-mono` for anything **machine-authored or machine-parsed**:

| Surface | Component | Notes |
|---|---|---|
| Prompt text (raw/version view) | `pre`, `.mono` | preserve whitespace |
| Logs & event traces | `.ods-log` | fixed grid: time · level · message · duration |
| Tool invocations | `.ods-tool` | teal `automation-step`; shows name + args |
| Structured output (JSON/YAML) | `pre` | sunken surface, hairline border |
| Diffs / review | `.ods-diff` | add=green-subtle, del=red-subtle |
| Citations | `.ods-citation` | mono index chip |
| IDs, hashes, model names, tokens | `code` | inline mono chip |

Rule: if a human wrote it as prose → sans. If a machine emitted or will parse it → mono.

---

## 4. Layout system

### 4.1 App shell
Grid-based `.ods-shell`: **left nav rail (240) · top bar (52) · main · optional right context (360)**.
`.-with-context` adds the right pane; `.-rail` collapses nav to a 56px icon rail.

```
┌───────┬──────────────────────────────┬───────────┐
│       │  Top bar (breadcrumb, search, │           │
│ Nav   │  command-K, env, user)        │  Context  │
│ rail  ├──────────────────────────────┤  panel    │
│ (240) │  Main (page / split panes)    │  (360)    │
│       │                              │  (details,│
│       │                              │  props,   │
│       │                              │  chat)    │
└───────┴──────────────────────────────┴───────────┘
```

### 4.2 Regions
- **Top nav (`.ods-topbar`, 52px):** breadcrumb/context on the left; global search + `⌘K` command
  palette center/right; environment switcher (prod/staging), notifications, theme, avatar on the right.
  Sticky, hairline bottom border, no shadow.
- **Left rail (`.ods-nav`, 240px):** workflow/section navigation grouped by `.ods-nav-group` with
  `.text-eyebrow` labels. Items carry live counts (queue depth, pending reviews). Active item uses
  `--color-surface-selected` + primary text. Collapses to icon rail < 1100px, off-canvas < 760px.
- **Right context panel (`.ods-context`, 360px):** contextual to selection — run details, node
  properties, evidence, or an embedded copilot chat. Hidden < 1100px; promote to a `.ods-drawer`.
- **Split panes (`.ods-split`):** `-2col` / `-3col` with a hairline gutter. Canonical for
  copilot workspaces (source | chat | evidence) and builders (canvas | inspector).
- **Stacked cards:** vertical rhythm at `--space-12`; use for feeds of run cards / candidates.
- **Console/log views (`.ods-log`):** sunken surface, mono, sticky header toolbar; virtualize long logs.
- **Review screens:** content (diff/candidate) scrolls; a sticky `.ods-approval-bar` pins the decision.

### 4.3 Mobile collapse behavior
< 1100px: hide right context (promote to drawer/tab). < 760px: nav goes off-canvas (`.is-open`),
split panes stack single-column, tables switch to card rows or horizontal scroll with a frozen first
column. Approval bars become fixed bottom bars. Touch targets ≥ 44px.

---

## 5. Core components

Naming convention (used everywhere): **`.ods-<component>`** base, **`.-<variant>`** modifier,
**`.is-<state>`** state. Anatomy is documented as `Element › part`.

For each component: **Purpose · Anatomy · Variants · States · A11y · Use / Don't**.

### 5.1 Buttons — `.ods-btn`
- **Purpose:** trigger an action. In Operant, buttons frequently commit *consequential* agent actions,
  so hierarchy is strict.
- **Anatomy:** `[icon?] label [icon?]`. Height = `--control-h` (density-aware).
- **Variants:** `-primary` (one per view/region), `-secondary`, `-ghost`, `-danger`, `-danger-ghost`,
  `-success`; sizes `-sm` / `-lg` / `-block`.
- **States:** default · hover · active · focus-visible (ring) · disabled (`.is-disabled`) · loading
  (swap leading icon for spinner, keep width, `aria-busy`).
- **A11y:** real `<button>`; label ≥ icon-only needs `aria-label`; loading sets `aria-busy="true"`;
  never rely on color alone for danger — use label + variant.
- **Use:** commit actions, approvals. **Don't:** use `-primary` more than once per region; use a
  button for navigation (use a link).

### 5.2 Icon button — `.ods-iconbtn`
Square, borderless-by-default action (toolbar, row action, close). Always `aria-label`. Min 32/28px
target; on mobile pad to 44px. Don't use for primary CTAs.

### 5.3 Tabs — `.ods-tabs` / `.ods-tab`
Switch views within one context (e.g. run: *Output · Trace · Sources · Metrics*). Underline indicator.
`role="tablist"`, arrow-key navigation, `aria-selected`. Don't use for sequential steps (use stepper).

### 5.4 Segmented control — `.ods-segmented` / `.ods-segment`
Compact mutually-exclusive choice (density mode, live/paused, list/board). ≤ 4 options. Filled active
segment. For > 4 or long labels use tabs or a select.

### 5.5 Inputs / textarea / select — `.ods-input` / `.ods-textarea` / `.ods-select`
- **Anatomy:** `.ods-field › .ods-label + control + (.ods-hint | .ods-error-text)`.
- **States:** default · focus (ring) · invalid (`.is-invalid` + `aria-invalid` + error text) · disabled.
- **A11y:** every control has a programmatic label; errors referenced via `aria-describedby`; invalid
  is text + border, never color alone. `.ods-input-group` wraps leading/trailing icons.

### 5.6 Prompt composer — `.ods-composer`
- **Purpose:** the primary AI input surface — multiline, model/context aware.
- **Anatomy:** `.ods-composer › .ods-composer-input (autogrow textarea) + .ods-composer-toolbar
  (attachments · model/context chips · token/char meta · Send)`.
- **States:** empty (placeholder guidance) · typing (token meter) · submitting (Send→Stop) ·
  streaming (Stop button) · error (inline retry) · disabled (agent busy).
- **A11y:** `⌘/Ctrl+Enter` submits; `Esc` stops a run; token meter announced politely; textarea labeled.
- **Use:** any freeform instruction to an agent. **Don't:** hide the submit affordance or the active
  model/context — users must know *what* and *with what* they're invoking.

### 5.7 Select menus & command palette — `.ods-select` / `.ods-command`
- **Command palette (`⌘K`, `.ods-command`):** global action + navigation surface; fuzzy search;
  grouped results; keyboard-driven (`↑↓` move, `↵` run, `Esc` close); `--z-command` above all.
  This is a **required** component — agentic tools live and die by keyboard velocity.

### 5.8 Cards — `.ods-card`
Container for a discrete unit (metric, run, candidate). Variants `-elevated`, `-interactive`.
Anatomy: `header · body · footer`. Metric tile: `.ods-metric` (`-label`, `-value.tabular`, `-delta`).
Don't nest cards more than one level; don't add shadow to non-elevated cards.

### 5.9 Tables — `.ods-table`
Dense data (runs, queue, incidents). Sticky uppercase header; hairline row borders; hover + selected
rows; `.-num` right-aligns tabular figures; density-aware row height. A11y: real `<table>` semantics,
`scope` on headers, sortable headers as buttons with `aria-sort`. Virtualize > ~100 rows. Don't put
long prose in cells — link out to a detail/run page.

### 5.10 Badges / status pills / counts — `.ods-badge` / `.ods-status` / `.ods-count`
- **Badge:** static label (env, model, tag). Semantic variants mirror status colors.
- **Status pill (`.ods-status` + `.ods-dot`):** dot color encodes state; **always paired with a text
  label** (color-blind safety). `.ods-dot.-pulse` for live/active.
- **Count:** numeric indicator for queues/notifications; `.tabular`.

### 5.11 Progress stepper — `.ods-stepper`
Sequential workflow execution (steps of a run/pipeline). Node states: `is-done` (green check),
`is-active` (iris, current), `is-error` (red), `is-review` (amber, awaiting human), pending (outline).
Vertical for run detail; keep step titles + meta (duration, actor). A11y: `aria-current="step"` on active.

### 5.12 Timeline / event log — `.ods-log`
Chronological machine events (traces, audit). Fixed mono grid: `time · level · message · duration`.
Levels: `-info -warn -error -ok -tool`. Streamable (append + autoscroll with a "jump to live" affordance).
Filterable by level/actor. Don't mix human comments here — use a separate activity/comment thread.

### 5.13 Accordion — `.ods-accordion`
Progressive disclosure of secondary detail (reasoning, raw payloads, advanced options). Chevron
rotates on open. A11y: trigger is a `<button>` with `aria-expanded`; panel `role="region"`.

### 5.14 Toast — `.ods-toast`
Transient, non-blocking feedback (run started, approval recorded, export ready). Semantic left border.
Auto-dismiss 5–8s (persist errors + offer action). `role="status"` (info) / `role="alert"` (error).
Never put a *required* decision in a toast — use inline UI or a modal.

### 5.15 Modal / drawer / side panel — `.ods-modal` / `.ods-drawer` / `.ods-context`
- **Modal:** blocking decision or focused create; `--z-modal`; trap focus; `Esc` closes; restore focus.
  Reserve for irreversible/destructive confirms and short focused tasks.
- **Drawer:** side-anchored detail without losing page context (run detail, node inspector on mobile).
- **Side panel (context):** persistent, non-blocking companion to the main view.

### 5.16 Empty / error / skeleton — `.ods-empty` / `.ods-error-state` / `.ods-skeleton`
- **Empty:** icon + title + one-line guidance + primary next action ("Run your first workflow").
- **Error:** danger-subtle surface, cause + **recovery action** (retry/escalate/edit). Never a dead end.
- **Skeleton:** shimmer placeholders matching final layout; use for initial loads, *not* for streaming
  (streaming uses the caret/thinking pattern).

### 5.17 Streaming response block — `.ods-response`
Renders live/complete AI output. `-streaming` adds primary border + trailing `.ods-caret`. Prose capped
at `--w-reading-max`, `--lh-relaxed`. Supports inline `.ods-cite-ref` superscripts. See §6.2.

### 5.18 Citation / source block — `.ods-citation`
Evidence backing a claim. Mono index chip + title + source meta + link. Inline refs (`.ods-cite-ref`)
scroll to the matching block. Required whenever the AI asserts retrievable facts. See §6.11.

### 5.19 Diff / review block — `.ods-diff`
Line-level before/after for AI-proposed edits (code, config, docs). `-add` / `-del` line variants,
mono, line numbers. Pairs with the approval action bar. See §6.9.

### 5.20 Approval action bar — `.ods-approval-bar`
Sticky human-in-the-loop decision surface: context summary + **Reject · Edit · Retry · Approve**
(primary). Elevated. Keyboard: `A` approve, `R` reject, `E` edit. See §6.5.

### 5.21 Agent run card — `.ods-run`
A single unit of agent work. Anatomy: `header (avatar · name · status · duration) · body
(task/summary/output preview) · footer (tools used · confidence · actions)`. `-active` = iris border.
Central object of AI ops — see §6 for its state matrix.

### 5.22 Workflow node card — `.ods-node`
A step in a visual workflow builder/canvas. Typed heads: `-agent` (iris), `-tool` (teal),
`-review` (amber), plus trigger/condition. Input/output `.ods-node-port`s for edges. Keep to
title + 1-line config; details go to the inspector panel.

---

## 6. AI-specific patterns

These are the load-bearing patterns that make Operant an *AI* system rather than a dashboard skin.
Each: **when it fires · how it looks · rules.**

### 6.1 "AI is thinking" (pre-token latency)
- **Look:** `.ods-thinking` — iris label + animated `.ods-thinking-dots`; optional current sub-step
  ("Searching knowledge base…"). Status pill uses `.ods-dot.-active.-pulse`.
- **Rules:** show within 400ms of invocation. Prefer a *specific* phase label over a generic spinner.
  Always cancelable (`Esc` / Stop). Never a blocking modal — the user can keep working.

### 6.2 "AI is streaming output"
- **Look:** `.ods-response.-streaming` with a trailing `.ods-caret`; content grows top-down; autoscroll
  with a "jump to latest" pill if the user scrolled up.
- **Rules:** render partial markdown safely; keep the **Stop** control visible the entire time; do not
  reflow surrounding layout as tokens arrive (reserve space). Announce completion via `role="status"`.

### 6.3 "AI used tools"
- **Look:** `.ods-tool` chips/rows (teal `automation-step`) inline in the trace and summarized on the
  run card footer ("3 tools"). Running = dashed border; error = danger. Expandable to show args/results.
- **Rules:** every tool call is logged with inputs, outputs, duration, and status. Tool calls are
  *automation-step* colored, never *agent* colored — they're deterministic actions the agent invoked.

### 6.4 "AI is uncertain" — confidence vs certainty
- **Look:** `.ods-confidence` meter (`-high/-med/-low`) **with a numeric/text label**; low-confidence
  claims get a warning-subtle inline treatment and an explicit "verify" affordance.
- **Rules:** Never present confidence as a bare color. Distinguish **confidence** (model self-estimate,
  meter) from **certainty** (verified/cited fact, citation block). A cited fact is *certain*; a
  high-confidence uncited claim is still just *confident*. Force low-confidence into human review for
  consequential actions.

### 6.5 "AI needs human input" (human-in-the-loop)
- **Look:** amber `human-review` everywhere — stepper node `is-review`, queue badge, sticky
  `.ods-approval-bar`. The run pauses; the pending item rises in the review inbox.
- **Rules:** state *what* decision is needed and *why now*. Provide full context inline (no hunting).
  Offer Approve / Edit / Reject / Retry + optional comment. Record decision-maker + timestamp to the trace.

### 6.6 "AI completed step successfully"
- **Look:** green `is-done` node/badge, success toast, run card collapses to a summary with output
  preview + confidence + evidence count. **Rules:** success still shows provenance; make the output and
  its "how" one click apart.

### 6.7 "AI failed and suggests retry"
- **Look:** `is-error` node, `.ods-error-state` with cause (mono error/trace excerpt) + recovery
  actions: **Retry**, **Retry with edits**, **Escalate to human**, **Switch model**.
- **Rules:** never a dead end; always propose a next action. Show the failing step in the trace.
  Distinguish transient (retry) from terminal (escalate) failures.

### 6.8 "AI produced multiple candidate outputs"
- **Look:** `.ods-candidates` — side-by-side candidate cards, each with its own confidence + rationale;
  select one (`is-selected`), or merge/regenerate. **Rules:** show *why they differ* (model/temp/prompt
  variant). Selecting a candidate is an attributable, logged decision.

### 6.9 "Human approved / edited / overrode AI"
- **Look:** `.ods-diff` shows human edits vs AI proposal; the run trace records
  `approved | edited | overridden | rejected` with actor + diff. Overrides get a distinct badge.
- **Rules:** human edits are first-class events, not silent mutations. Preserve the original AI output
  for audit. An override should be as traceable as an agent action.

### 6.10 Traceability / auditability
- **Look:** every run has a **Trace** tab (`.ods-log`) — full ordered event stream: prompt version,
  inputs, each reasoning/tool step, outputs, human decisions, model + token usage. Exportable.
- **Rules:** immutable, timestamped, attributable. One click from any output to its complete lineage.
  This is the trust backbone — treat it as a product surface, not a debug dump.

### 6.11 Citations & evidence
- **Look:** inline `.ods-cite-ref` superscripts link to `.ods-citation` blocks (source, snippet, link,
  retrieval score). A "Sources" tab aggregates them. **Rules:** any retrievable factual claim carries a
  citation; uncited claims are visually marked as model-generated (not evidence-backed).

### 6.12 Prompt versioning
- **Look:** prompts are versioned artifacts (`v3` badge, `code`-styled id); a version picker + diff
  view (`.ods-diff`) compares prompt revisions; each run records the exact version used.
- **Rules:** never mutate a prompt in place without a new version; runs pin to a version for
  reproducibility; expose "which prompt produced this?" from every run.

### 6.13 Workflow branching
- **Look:** in the builder, condition/branch nodes fork edges; in run detail, taken branches are solid,
  untaken are faint. **Rules:** show the branch *condition* and *why* a branch was taken (the deciding
  value). Support parallel branches and joins.

### 6.14 Collaboration & handoff
- **Look:** assignment avatars on runs/reviews; "handed off to @user" events in the trace; presence on
  shared reviews. **Rules:** handoffs (agent→human, human→human, human→agent) are explicit, logged
  events with context carried forward. Never lose the thread across a handoff.

---

## 7. Example screens

Six screen concepts. Two are provided as runnable HTML in [`examples/`](examples/):
the **AI Operations Dashboard** (dense) and the **Approval Inbox** (review), plus a **Run Detail /
Trace** page. Each spec: *layout · major components · hierarchy · interaction priorities.*

### 7.1 AI Operations Dashboard — [`examples/dashboard.html`](examples/dashboard.html)
- **Layout:** full shell + right context. Top: 4 KPI tiles (active runs, pending reviews, success rate,
  avg latency). Middle: split — live **run feed** (`.ods-run` cards / `.ods-table`) left, **agent
  fleet status** right. Bottom: recent **event log**.
- **Components:** metrics, run cards, table, status pills (`.-pulse`), log, confidence meters.
- **Hierarchy:** *what needs me now* (pending reviews, errors) > *what's happening* (active runs) >
  *how are we doing* (KPIs, trends).
- **Interaction priorities:** 1-click into any run's trace; filter by agent/status; `⌘K` to jump;
  live updates without layout jump; pending-review count is the loudest element.

### 7.2 Incident triage workflow
- **Layout:** 3-pane split — incident list (left) · triage detail + AI summary/streaming (center) ·
  evidence/citations + suggested actions (right).
- **Components:** stepper (detect→triage→diagnose→remediate→verify), streaming response, tool chips
  (logs/metrics queried), confidence, approval bar for remediation, timeline.
- **Hierarchy:** severity + AI-proposed action first; evidence one glance away; human confirms
  remediation.
- **Interaction priorities:** speed to decision; approve/deny remediation; escalate; everything keyboard.

### 7.3 Research / copilot workspace
- **Layout:** 2–3 panes — sources/documents · copilot chat (composer + streaming) · evidence panel.
- **Components:** prompt composer, streaming response with inline citations, citation blocks, candidate
  outputs, accordion (reasoning), history.
- **Hierarchy:** the answer + its evidence co-equal; reasoning on demand.
- **Interaction priorities:** ask→stream→cite loop; pin/save findings; compare candidates; cite-to-source.

### 7.4 Approval Inbox — [`examples/approval-inbox.html`](examples/approval-inbox.html)
- **Layout:** shell + main list/detail. Left: filterable queue of pending items (agent, type, age,
  confidence). Center: selected item with AI proposal (`.ods-diff` / output), rationale, evidence.
  Sticky `.ods-approval-bar`.
- **Components:** table/list, diff block, citation, confidence, approval bar, candidate compare.
- **Hierarchy:** oldest/highest-risk pending first; the *decision* is the focal action.
- **Interaction priorities:** triage velocity — `J/K` move, `A` approve, `R` reject, `E` edit; bulk
  approve low-risk; every decision logged with actor.

### 7.5 Workflow builder
- **Layout:** canvas center (`.ods-node` graph) · left node palette · right inspector (`.ods-context`).
- **Components:** node cards (agent/tool/review/condition), ports/edges, inspector fields, test-run panel,
  prompt version picker.
- **Hierarchy:** the graph is primary; inspector is contextual to selection.
- **Interaction priorities:** drag-connect; validate before publish; test-run with live trace; branch config.

### 7.6 Execution trace / run detail — [`examples/run-detail.html`](examples/run-detail.html)
- **Layout:** header (run id, agent, status, duration, prompt version) + tabs *Output · Trace · Sources
  · Metrics*. Trace tab = vertical stepper + `.ods-log`.
- **Components:** stepper, event log, tool chips, streaming/final response, citations, diff, metrics tiles.
- **Hierarchy:** outcome first (Output tab); full lineage a tab away (Trace).
- **Interaction priorities:** audit & reproduce — replay, re-run with edits, export trace, jump to any step.

---

## 8. CSS variable layer

The concrete, production token block (primitives → semantic → light + dark themes → density) is
[`css/tokens.css`](css/tokens.css). Load order for any app or generated page:

```html
<link rel="stylesheet" href="css/tokens.css">   <!-- 1. tokens (required first) -->
<link rel="stylesheet" href="css/base.css">      <!-- 2. reset + typography -->
<link rel="stylesheet" href="css/components.css"><!-- 3. component library -->
```

Theme is set with `data-theme="light|dark"` on `<html>`; density with `data-density="comfortable|compact"`.

---

## 9. Component markup examples

Copy-paste HTML for the representative subset (primary button, prompt composer, agent run card,
streaming response, approval action bar, event log row, citation block) is in
[§9 of the README](README.md#component-markup) and rendered live in [`examples/`](examples/).
See [`examples/components.html`](examples/components.html) for a gallery of every component in both themes.

---

## 10. Design rules for coding agents

The deterministic, LLM-followable ruleset lives in [`AGENTS.md`](AGENTS.md). Summary of the
non-negotiables:

1. **Consume semantic tokens only.** Never a raw hex, never a raw px for spacing. Missing token → add a
   semantic one to `tokens.css`; don't inline.
2. **Follow the naming grammar:** `.ods-<component>` · `.-<variant>` · `.is-<state>`.
3. **One `-primary` per region.** Encode action hierarchy with variants, not size hacks.
4. **Spacing discipline:** only `--space-*` on the 4px grid; vertical rhythm `--space-12` between cards.
5. **Color = meaning.** Status hues for outcomes; agent (iris) for AI identity/activity; automation
   (teal) for tool steps; human-review (amber) for HITL. Never repurpose a hue.
6. **New AI state?** Compose from existing agent/status semantics; add a `--color-agent-*` /
   `--color-*` token before introducing a new hue. Pair every state color with text/icon (never color alone).
7. **Motion is feedback only.** Continuous motion is reserved for live-system signals (caret, thinking,
   pulse). Respect `prefers-reduced-motion`.
8. **A11y is a gate, not a polish pass:** WCAG AA contrast, visible focus (`--shadow-focus`), full
   keyboard paths, labeled controls, state announced to SR (`role`/`aria-*`), not color alone.
9. **Anti-patterns to reject:** gradients-as-decoration, neon "AI" glow, glassmorphism (unless a
   justified elevation case), decorative shadows on flat surfaces, prose in mono / logs in sans,
   confidence shown as color only, agent actions without attribution, dead-end error states.
10. **Both themes, always.** Anything you add must resolve in light *and* dark via semantic tokens.
