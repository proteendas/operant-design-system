# AGENTS.md — Deterministic rules for coding agents

You are extending the **Operant** design system (an AI-workflow UI language). Follow these rules
exactly when generating or modifying any UI. They are ordered; when two rules conflict, the
lower-numbered rule wins. Read [`DESIGN-SYSTEM.md`](DESIGN-SYSTEM.md) for rationale; this file is the
executable contract.

## 0. Load & setup (always)
```html
<html data-theme="dark" data-density="comfortable">
<link rel="stylesheet" href="css/tokens.css">
<link rel="stylesheet" href="css/base.css">
<link rel="stylesheet" href="css/components.css">
```
- `data-theme`: `light` | `dark`. `data-density`: `comfortable` | `compact` (settable per-subtree).
- Every UI you produce MUST resolve correctly in both themes. If you can only verify one, you are not done.

## 1. Tokens (hard rules)
- **NEVER** write a raw hex, rgb, hsl, or named color in a component. Use a `--color-*` semantic token.
- **NEVER** write a raw px/rem for spacing, sizing, radius, or z-index. Use `--space-*`, `--radius-*`,
  `--size-icon-*`, `--z-*`, `--w-*`, `--h-*`, `--control-h`, `--row-h`.
- **NEVER** consume a primitive (`--slate-500`, `--blue-600`, …) in a component. Primitives feed
  semantic tokens only.
- If the semantic token you need does not exist: **add it to `tokens.css`** (in BOTH light and dark
  blocks) with a meaningful name, then use it. Do not inline the value.
- Font: `--font-sans` for human prose/UI, `--font-mono` for machine text (prompts, logs, traces, tool
  calls, JSON, diffs, ids, citations). This mapping is semantic and non-negotiable.

## 2. Naming grammar
- Component: `.ods-<component>` (kebab). Variant modifier: `.-<variant>` (leading dash).
  State: `.is-<state>`. Example: `<button class="ods-btn -primary is-disabled">`.
- New components follow the same grammar. New variants are `-adjective` (`.-danger`, `.-compact`).
- Do not invent parallel class systems, utility frameworks, or inline `style=` for anything a token
  or component class already covers.

## 3. Component selection (decision table)
| Need | Use | Not |
|---|---|---|
| Commit an action | `.ods-btn` (`-primary` once/region) | multiple primaries |
| Toolbar/row action | `.ods-iconbtn` (+ `aria-label`) | tiny text button |
| Switch views in one context | `.ods-tabs` | stepper |
| Sequential workflow progress | `.ods-stepper` | tabs |
| Mutually-exclusive ≤4 options | `.ods-segmented` | radios |
| Freeform AI instruction | `.ods-composer` | bare `.ods-textarea` |
| Global action/nav | `.ods-command` (`⌘K`) | mega menu |
| Dense records | `.ods-table` (+ `data-density`) | stacked cards |
| A unit of agent work | `.ods-run` | generic card |
| Live/complete AI output | `.ods-response` (`-streaming`) | plain `<p>` |
| Machine event stream | `.ods-log` | table with prose |
| Human decision on AI output | `.ods-approval-bar` + `.ods-diff`/candidates | toast |
| Evidence for a claim | `.ods-citation` + `.ods-cite-ref` | footnote text |
| Blocking confirm (irreversible) | `.ods-modal` | inline |
| Non-blocking detail | `.ods-context` / `.ods-drawer` | modal |

## 4. Color = meaning (never repurpose a hue)
- **Outcomes** → status tokens: `success` (green), `warning` (amber), `danger` (red), `info` (blue).
- **AI identity & activity** → `--color-agent-active` (iris): thinking, running, streaming.
- **Tool / deterministic steps** → `--color-automation-step` (teal).
- **Needs a human** → `--color-human-review` (amber semantic, HITL).
- **Confidence** → `--color-confidence-high|med|low`, always with a numeric/text label.
- A colored state MUST also carry text or an icon. Color alone is never the only signal (color-blind + SR).

## 5. Spacing & layout discipline
- Vertical rhythm between stacked cards/sections: `--space-12`. Inside cards: `--space-8`/`--space-10`.
- Control gaps: `--space-4`–`--space-6`. Page padding: `--space-12`/`--space-16`.
- Use the shell grid (`.ods-shell`) for full pages; `.ods-split` for multi-pane; `.ods-page` +
  `.ods-prose` (`--w-reading-max`) to cap AI prose line length.
- Respect region widths (`--w-nav`, `--w-context`) and the responsive collapse rules already in
  `components.css`. Don't hardcode panel widths.

## 6. Styling new AI-specific states
When a new agent/model state appears (e.g. "rate-limited", "awaiting-tool-approval"):
1. Map it to an existing semantic family first (idle? review? error? automation?).
2. If genuinely new, add a `--color-agent-<state>` (+ `-subtle`) token in both themes — reuse an
   existing hue family; do not introduce a new base hue without explicit instruction.
3. Represent it with: a `.ods-dot.-<state>` + label, a `.ods-badge`, and (if it blocks) an action path.
4. Live/continuous states may use `.ods-dot.-pulse`, `.ods-caret`, or `.ods-thinking-dots`. Static
   states must NOT animate.

## 7. Motion rules
- Transitions: `--dur-fast`/`--dur-base` with `--ease-out`. No bounce, no >260ms UI transitions.
- Continuous animation is allowed ONLY for live-system feedback: streaming caret, thinking dots,
  active/streaming pulse, skeleton shimmer. Everything else is static.
- Never animate layout in a way that shifts content during streaming (reserve space).
- Honor `prefers-reduced-motion` (tokens already zero durations; don't re-introduce animation inline).

## 8. Accessibility gate (must pass, not optional)
- **Contrast:** body/text on its surface ≥ WCAG AA (4.5:1 normal, 3:1 large/UI). Muted text stays ≥ 4.5:1
  on its background; `text-faint` is for non-essential meta only.
- **Focus:** every interactive element shows `--shadow-focus` on `:focus-visible`. Never remove outlines
  without replacing them.
- **Keyboard:** full operability — composer (`⌘/Ctrl+Enter` send, `Esc` stop), palette (`⌘K`, arrows,
  `↵`, `Esc`), approval bar (`A/R/E`), list nav (`J/K`), tabs (arrows). Logical tab order; trap focus in
  modals and restore on close.
- **Semantics:** real elements (`<button>`, `<table>`, `<nav>`, `<dialog>`); `aria-label` on icon-only;
  `aria-expanded`/`-selected`/`-current`/`-invalid`/`-busy` where applicable.
- **Status communication:** streaming/thinking/complete announced via `role="status"`; errors via
  `role="alert"`; never rely on color or motion alone to convey state.

## 9. Anti-patterns (reject / do not generate)
- Gradients or glows as decoration; neon "AI" aesthetics; glassmorphism (unless a justified true-elevation
  case with a fallback).
- Decorative shadows on flat/non-elevated surfaces; shadow used for structure (use borders).
- Prose rendered in mono, or logs/traces/JSON rendered in sans.
- Confidence shown as color only, or presented as certainty.
- Agent actions, human overrides, or tool calls without attribution/trace.
- Dead-end error states (an error with no recovery action).
- More than one `-primary` button per region; buttons used for navigation.
- Per-component theme overrides that bypass semantic tokens; hardcoded light-only colors.

## 10. Definition of done (self-check before returning UI)
- [ ] No raw hex/px in component code; only tokens.
- [ ] Naming grammar `.ods-*` / `.-*` / `.is-*` followed.
- [ ] Renders correctly in `data-theme="dark"` AND `light`.
- [ ] Every state has text/icon, not just color.
- [ ] Focus-visible rings present; keyboard paths work.
- [ ] AI states (thinking/streaming/tool/uncertain/review/error) use the correct semantic family.
- [ ] Any AI factual claim has a citation; any agent action is attributable.
- [ ] No anti-pattern from §9 is present.
