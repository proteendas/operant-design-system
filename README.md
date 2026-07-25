# Operant

**A self-contained design system for AI-assisted workflows and agentic interfaces.**
Copilots · workflow automation · incident triage · research assistants · prompt orchestration ·
approval/review pipelines · human-in-the-loop dashboards.

> Personality: **intelligent · operational · trustworthy · efficient · calm under complexity.**
> Built to feel like Linear / GitHub / Vercel / Stripe Dashboard — an operations console for
> intelligent systems, not a marketing site.

---

## What's in here

```
design-system/
├── index.html              # ⭐ Start here — navigation hub, designed with the system itself
├── DESIGN-SYSTEM.md        # The full spec (philosophy → tokens → components → AI patterns → screens)
├── AGENTS.md               # Deterministic rules for AI coding agents (the executable contract)
├── README.md               # This file — quickstart + component markup
├── css/
│   ├── tokens.css          # Design tokens: primitives → semantic → light/dark → density
│   ├── base.css            # Reset, typography roles, mono sub-system, utilities
│   └── components.css      # The component library (.ods-* classes)
└── examples/
    ├── dashboard.html      # AI Operations Dashboard (dense)
    ├── approval-inbox.html # Approval Inbox (review / human-in-the-loop)
    ├── run-detail.html     # Execution trace / run detail
    └── components.html     # Live gallery of every component, both themes
```

## Quickstart

Load the three stylesheets in order and set theme + density on `<html>`:

```html
<!doctype html>
<html lang="en" data-theme="dark" data-density="comfortable">
<head>
  <link rel="stylesheet" href="css/tokens.css">
  <link rel="stylesheet" href="css/base.css">
  <link rel="stylesheet" href="css/components.css">
</head>
<body>
  <!-- compose with .ods-* components -->
</body>
</html>
```

- **Theme:** `data-theme="light"` or `"dark"`.
- **Density:** `data-density="comfortable"` (default) or `"compact"` — set on `<html>` or any subtree.
- **Fonts:** the stack falls back to system fonts; add Inter + JetBrains Mono for the intended look.

## For coding agents / LLMs

Read [`AGENTS.md`](AGENTS.md) first. The five rules that prevent 90% of drift:
1. Consume **semantic tokens only** — no raw hex, no raw px. Missing token → add one to `tokens.css`.
2. Naming grammar: **`.ods-<component>` · `.-<variant>` · `.is-<state>`**.
3. **Color = meaning:** status = outcomes, iris `agent` = AI activity, teal `automation` = tool steps,
   amber `human-review` = HITL. Never repurpose a hue; never color-only.
4. **One `-primary` per region.** Motion only for live-system feedback.
5. **Both themes + a11y (AA contrast, focus rings, keyboard) are a gate, not polish.**

---

<a id="component-markup"></a>
## Component markup examples

Representative subset. All values come from tokens; every snippet works in both themes.

### Primary button
```html
<button class="ods-btn -primary">
  <svg viewBox="0 0 16 16" aria-hidden="true"><!-- icon --></svg>
  Run workflow
</button>
<button class="ods-btn -secondary">Cancel</button>
<button class="ods-btn -danger-ghost">Reject</button>
<button class="ods-iconbtn" aria-label="More actions">⋯</button>
```

### Prompt composer
```html
<form class="ods-composer" aria-label="Prompt">
  <textarea class="ods-composer-input" rows="1"
    placeholder="Describe the task, or ask the agent…" aria-label="Prompt input"></textarea>
  <div class="ods-composer-toolbar">
    <span class="ods-composer-chip">
      <span class="ods-dot -automation"></span> claude-opus-4-8
    </span>
    <span class="ods-composer-chip">3 files attached</span>
    <span class="grow"></span>
    <span class="ods-composer-meta tabular">312 / 8k tokens</span>
    <button class="ods-btn -primary -sm" type="submit">Send ⌘↵</button>
  </div>
</form>
```

### Agent run card
```html
<article class="ods-run -active">
  <header class="ods-run-header">
    <span class="ods-run-avatar" aria-hidden="true">
      <svg viewBox="0 0 16 16"><!-- agent glyph --></svg>
    </span>
    <div class="grow">
      <div class="ods-run-name">Triage Agent · Incident #4821</div>
      <div class="ods-run-sub">Started 2m ago · prompt <code>v7</code></div>
    </div>
    <span class="ods-status">
      <span class="ods-dot -active -pulse"></span> Running
    </span>
  </header>
  <div class="ods-run-body">
    <div class="ods-thinking">
      <span class="ods-thinking-dots"><i></i><i></i><i></i></span>
      Correlating logs across 3 services…
    </div>
  </div>
  <footer class="ods-run-footer">
    <span class="ods-tool"><svg viewBox="0 0 16 16"></svg><span class="name">query_logs</span></span>
    <span class="ods-confidence -med"><span class="ods-confidence-track"><span class="ods-confidence-fill"></span></span>
      <span class="ods-confidence-label">Confidence 58%</span></span>
    <span class="grow"></span>
    <button class="ods-btn -ghost -sm">Stop</button>
  </footer>
</article>
```

### Streaming response panel
```html
<section class="ods-response -streaming" role="status" aria-live="polite">
  <div class="ods-response-prose">
    <p>The elevated error rate on <code>checkout-svc</code> began at 14:02 UTC, correlating with a
    deploy of <code>v2.31.0</code>.<sup class="ods-cite-ref">1</sup> Rolling back is recommended.<span class="ods-caret"></span></p>
  </div>
</section>
```

### Approval action bar
```html
<div class="ods-approval-bar" role="group" aria-label="Review decision">
  <div class="grow">
    <div class="ods-run-name">Approve rollback of checkout-svc to v2.30.4?</div>
    <div class="ods-approval-context">Proposed by Triage Agent · affects production · reversible</div>
  </div>
  <button class="ods-btn -danger-ghost" accesskey="r">Reject</button>
  <button class="ods-btn -secondary" accesskey="e">Edit</button>
  <button class="ods-btn -secondary">Retry</button>
  <button class="ods-btn -primary" accesskey="a">Approve</button>
</div>
```

### Event log row
```html
<div class="ods-log">
  <div class="ods-log-row">
    <span class="ods-log-time tabular">14:02:11</span>
    <span class="ods-log-level -tool">TOOL</span>
    <span class="ods-log-msg"><span class="k">query_logs</span>(service="checkout-svc", window="15m")</span>
    <span class="ods-log-dur tabular">412ms</span>
  </div>
  <div class="ods-log-row">
    <span class="ods-log-time tabular">14:02:12</span>
    <span class="ods-log-level -ok">OK</span>
    <span class="ods-log-msg">Retrieved 1,204 log lines · 37 errors</span>
    <span class="ods-log-dur tabular"></span>
  </div>
  <div class="ods-log-row">
    <span class="ods-log-time tabular">14:02:13</span>
    <span class="ods-log-level -warn">WARN</span>
    <span class="ods-log-msg">Confidence below threshold — escalating to human review</span>
    <span class="ods-log-dur tabular"></span>
  </div>
</div>
```

### Citation block
```html
<div class="ods-citation">
  <span class="ods-citation-index">1</span>
  <div class="grow">
    <div class="ods-citation-title">Deploy log: checkout-svc v2.31.0</div>
    <div class="ods-citation-src">ci/cd · deploy #9931 · 14:01 UTC · relevance 0.94</div>
  </div>
  <a class="ods-btn -ghost -sm" href="#">Open</a>
</div>
```

---

## License / usage
Drop-in reference system. Use it as production CSS or as a style guide for code generation. Extend it
by following [`AGENTS.md`](AGENTS.md) so the language stays coherent.
