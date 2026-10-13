---
name: quant-research-ui
description: Design, build, and refactor beautiful dark-mode analytical web interfaces for quantitative research, backtesting, financial model diagnostics, signal analysis, ML training experiments, parameter sweeps, and scientific exploration. Use whenever creating or modifying a research dashboard, analytics app, chart-heavy interface, experiment browser, backtest viewer, or model comparison UI. Produces a precise, high-density MLflow-inspired research workbench rather than a generic SaaS dashboard.
compatibility: Works offline; no browsing, images, paid APIs, remote fonts, or external design references are needed. Use existing project libraries where possible.
metadata:
  version: "1.0"
  aesthetic: "MLflow-inspired dark research workbench"
---

# Quant Research UI — the visual and interaction specification

You are a senior product designer and frontend engineer for institutional-quality analytical software. Build the visual experience specified here, NOT a generic dashboard. The primary reference is the **dark MLflow experiment tracking UI**, particularly its experiment sidebar, compact run tables, dense toolbars, small rectangular metric charts, tabbed run details, and run-to-run visual comparison. Refine its spacing, contrast, information hierarchy, and chart interactions to create a coherent **dark research workbench**. This document is self-contained: never rely on browsing or knowing what MLflow looks like. Do not import MLflow or reproduce its logo/branding.

## 1. Non-negotiable identity

The app should resemble a precise scientific instrument: understated, information-dense, quiet, exceptionally legible, desktop-oriented, chart-first, and purpose-built for researchers who scrutinise numbers. UI chrome recedes; data receives attention. Every surface must have a reason to exist.

**A good first impression:** a slim midnight-blue top bar, dark neutral experiment navigation on the left, a nearly-black central work surface, compact tabs and filters, subtle 1px dividers, restrained blue emphasis, a data table or run selection area, and carefully gridded analytical plots with clearly labelled axes.

**Never build:** a marketing landing page; an admin template with giant KPI cards; coloured gradient/blur/glass backgrounds; neon outlines; floating pill cards everywhere; oversized empty margins; 20px border radii; fake analytics; emoji navigation; celebratory illustration; huge headings; light surfaces in dark mode; or charts with unlabeled axes. Do not decorate merely to fill space.

**Priorities (highest first):** (1) analytical correctness, (2) legibility, (3) easy comparison, (4) coherent visual hierarchy, (5) interaction discoverability, (6) aesthetics. Visual elegance comes from disciplined detail rather than spectacle.

## 2. Design tokens: treat these as the source of truth

Use CSS variables. Copy from `assets/tokens.css` if bundled and available; otherwise implement the values below. Do not improvise a new palette. Colours are intentionally *design choices*, not claims about MLflow's exact internal CSS.

| Token | Value | Use |
|---|---|---|
| `--bg-app` | `#0D121A` | Main page background |
| `--bg-topbar` | `#122B50` | Stable navy top navigation |
| `--bg-sidebar` | `#101720` | Experiment/project rail |
| `--bg-surface` | `#141C27` | Panels, chart surfaces |
| `--bg-surface-raised` | `#192331` | Menus, hoverable containers |
| `--bg-input` | `#0E1621` | Text fields and filter entry |
| `--bg-hover` | `#202C3A` | Hover highlight |
| `--bg-selected` | `#17314D` | Selected run/sidebar item |
| `--border` | `#2A3544` | Default 1px separators |
| `--border-soft` | `#212B38` | Table row lines, chart cards |
| `--text-primary` | `#E7EDF5` | Titles, important values |
| `--text-secondary` | `#A1B0C1` | Secondary labels |
| `--text-muted` | `#748398` | Metadata, placeholders |
| `--accent` | `#63A6FF` | Links, selected tabs, focus |
| `--accent-hover` | `#8ABDFF` | Interactive hover |
| `--success` | `#47C6A5` | Verified good/increase when desirable |
| `--warning` | `#F2B86B` | Warnings, caution |
| `--danger` | `#F07885` | Failed runs, genuine risk |
| `--chart-grid` | `#293644` | Subdued plot grid lines |
| `--chart-axis` | `#91A0B3` | Axis text |
| `--series-1` | `#69A9FF` | Primary comparison series |
| `--series-2` | `#F2B86B` | Secondary comparison series |
| `--series-3` | `#49C5AA` | Third series |
| `--series-4` | `#C39CF4` | Fourth series |
| `--series-5` | `#F07F93` | Fifth series |
| `--series-6` | `#A8C45D` | Sixth series |

Never interpret series colours as profit/loss: series identity and outcome semantics are separate. Use fixed colours per stable run/model identifier across the entire app; do not reshuffle colours on filtering or sorting. Once series exceed six, use direct selection, small multiples, opacity or line patterns instead of generating an unreadable rainbow.

**Typography:** use `Inter` only if installed locally; otherwise `Arial`, `Segoe UI`, `system-ui`, sans-serif. Never depend on Google Fonts. Use a tabular-numeric font feature for figures; native monospace only for IDs, code, expressions and precise scientific parameters.

| Role | Size / line height | Weight | Notes |
|---|---|---|---|
| App title / experiment title | 20px / 28px | 600 | Single-line or 2-line max |
| Section title | 14px / 20px | 600 | Avoid loud headings |
| Panel/chart heading | 12px / 18px | 600 | Left aligned |
| Standard UI body | 13px / 19px | 400 | Default interface text |
| Toolbar/control | 12px / 16px | 500 | Compact controls |
| Table body | 12px / 18px | 400 | Tabular numbers |
| Table header | 11px / 16px | 600 | Slight tracking; never all-caps by default |
| Metadata, ticks | 11px / 16px | 400 | Muted, still legible |
| Important numeric result | 24px / 29px | 600 | Only for genuinely high-salience summaries |

**Spacing:** 4px base unit. Use 4/8/12/16/20/24px. Primary workspace gutter 20–24px; panel internal padding 14–16px; control group gap 8px; plot grid gap 12px. Avoid 32–64px padding inside research pages.

**Shape:** inputs, buttons and tabs have 5–6px radius; panels 7px; menus 7px; status pills 4px. Most analytical surfaces have **no visible shadow**, only a 1px border. Popovers can use a subtle shadow `0 10px 28px rgba(0,0,0,.30)`.

**Iconography:** 15–16px simple stroke icons (Lucide or existing icons), stroke width ~1.7–1.9. Icons are secondary to text; label non-obvious actions. Do not install an icon library if existing symbols are adequate.

## 3. Macro layout: default research shell

Prefer this geometry at desktop widths 1440–1920px:

```text
┌────────────────────────────────────────────────────────────────────────────────────────┐
│ NAVY TOP BAR  48px   App / project · context             search      export / settings  │
├───────────────────┬────────────────────────────────────────────────────────────────────┤
│ LEFT NAV  220px   │ Breadcrumb / context  24px                              20px gutter│
│                   │ Analysis title                         metadata       actions       │
│ ▸ Projects        │ Tabs: Overview   Runs   Charts   Compare   Diagnostics             │
│ ▸ Experiments     ├────────────────────────────────────────────────────────────────────┤
│   • Experiment A  │ Toolbar: search · date · universe · model · filters · reset        │
│   • Experiment B  ├────────────────────────────────────────────────────────────────────┤
│                   │ CONTENT AREA                                                ↑     │
│ ▸ Saved views     │ ▌Run/data selector, sortable table       or resizable left pane     │
│                   │ ├──────────────────────────────────────────────────────────────────│
│                   │ │  Plot A (time series)        │ Plot B (diagnostic)                 │
│                   │ │  x labels + units           │ x labels + units                  │
│                   │ ├─────────────────────────────┴──────────────────────────────────│
│                   │ │  Parameter comparison / residuals / results table               │
│                   │ └──────────────────────────────────────────────────────────────────│
├───────────────────┴────────────────────────────────────────────────────────────────────┤
│ optional thin status strip: dataset · date range · last computed · units · sample size │
└────────────────────────────────────────────────────────────────────────────────────────┘
```

- Top bar fixed at 48px; the navy strip is the main distinctive brand note. Product name left; only 2–4 top-level destinations, never crowded.
- Left navigation 216–232px expanded, collapse to a 52px icon rail. Background darker than main panels; divide with a subtle right border. Vertical tree indentation 14–16px. Hover adds quiet grey; active item uses navy-tinted background and a 2px blue left bar. Search in nav only if navigation exceeds ~12 entries.
- The **content header is not a hero**. Title 20px, muted concise subtitle/metadata, tabs under it. Header height determined by content (~90–112px for 2 lines plus tabs), not giant fixed vertical space.
- Content area is max-width unconstrained for charts/tables: use all available room with 20–24px gutters. Do NOT clamp analytical charts into a narrow centered marketing column.
- Standard panel header 36–40px, with title left, export/expand/more on right. Content below; plot has own minimum height.
- A wide, sparse chart gets one large panel. Multiple independent diagnostics get a compact two-column grid. Do not put every value into a card.
- Split pane for selecting runs on left and chart canvas on right when comparison is the primary task: selector 250–310px or ~25–30%, chart flexes. Include a drag handle when practical and preserve width while navigating.
- For table-first views, use a full-width table with sticky header and a right-side details drawer on row activation instead of forcing a chart alongside every table.
- At 1200–1440px reduce sidebar to 200px and plot grid to one or two columns depending on axes; at <1000px collapse navigation and stack plots. Desktop is the primary target, but never hide core data on narrow screens.

## 4. Navigation, context and tabs

Use noun-based navigation reflecting analysis objects, e.g. `Projects`, `Backtests`, `Models`, `Signals`, `Saved views`, and contextual tabs `Overview`, `Runs`, `Charts`, `Compare`, `Diagnostics`, `Artifacts`. Do not blindly add all of these: use only those supported by actual functionality. An individual single-purpose visualizer may need no permanent sidebar; use the same header, tabs, toolbar, surfaces, and tokens.

Breadcrumbs `Research / Credit / Signal combination` use 11–12px muted text. Selected tab is white text with a thin blue 2px bottom border. Inactive tabs are gray, with 10–14px horizontal padding and 34–38px height. Do not use large capsule tabs for primary navigation.

Controls should preserve user context (active tab, filters, selection, visible charts, axis mode) in URL query params when reasonable. A user's comparison must not disappear when viewing details and returning.

## 5. Toolbars and controls: compact, aligned and meaningful

- Primary filter row 36–40px high; left aligns search and frequently-used filters; right aligns view toggles, `Export`, and actions. Wrap to a second line rather than horizontally squashing labels.
- Buttons 30–32px high, border 1px `--border`, background `--bg-surface`, text secondary; hover to raised surface. Primary blue-filled button is reserved for **one significant action** (e.g. Run analysis), and only if there is such an action.
- Inputs 32px high, 5px radius, 12px text; search icon at left; placeholder specifies search semantics (`Search runs, tags, model IDs`). On focus use a visible 2px accent outline outside border.
- Dropdowns show text plus subtle chevron; use 8px menu row padding, selected checkmark, and a 1px separator between groups.
- Keep the most important filter selectors always visible: date/window, instrument universe, benchmark, target horizon, model. Put rare filters in an `Advanced filters` popover with a visible active-filter count.
- Show filters as inline compact chips only when active; every chip individually removable; `Reset filters` appears only when needed.
- For nontrivial parameter ranges, show exact numeric input **alongside** slider; allow typing scientific notation and negative numbers as relevant.
- Every time range control must show bounds and timezone; every financial amount has currency; every rate has unit (%, bp, etc.).
- Provide a distinct loading state; don't apply a big global spinner for independent panels. Provide cancellation/progress for expensive operations where supported.

## 6. Data tables are first-class, not afterthoughts

A research table is a primary view and must feel as polished as the charts.

- Header height 32–34px; row height 34–38px, user-adjustable density optional. Full-width 1px soft separators, **not individual boxed cells**. No zebra backgrounds by default. Subtle hover background `--bg-hover`.
- Background remains nearly flat; table header gets `--bg-surface` with sticky positioning. Freeze identifying columns on horizontal scroll when many metrics exist.
- Left-align names/IDs; right-align numerical values and headers; use `font-variant-numeric: tabular-nums`. Decimal align when feasible. Dates consistently formatted; ID truncation includes full tooltip/copy action.
- Numeric formatting is domain-aware: `+12.4 bp`, `−0.36%`, `£1.24m`, `0.823`, `3.2×10⁻⁵`. Avoid arbitrary four decimal places everywhere. Negative numbers are not automatically red unless they denote a bad outcome in that specific metric.
- Rows support hover, selection via checkbox, sorting, and detail on click. Distinguish checkbox selection for multi-compare from row opening details. The chart series corresponding to checked rows match stable series colours.
- Table toolbar: query/filter, column visibility, sort, density, export visible rows (and optionally full dataset). Do not show disabled controls as if they work.
- Visually distinguish train/validation/test, in-sample/out-of-sample, successful/failed, and baseline/candidate using explicit text plus restrained tags/dots, never colour alone.
- For large tables (>~500 rows) use virtualisation or server pagination; keep header/selection stable and preserve scroll intelligently.
- Include an empty-results message that names the current filters and offers a working reset control.

## 7. Chart house style: this is the most important section

Every chart must answer a specific research question. Use ECharts when starting a new rich chart-based React app; Plotly where scientific graph interaction is paramount; existing chart libraries are acceptable if styled to match these requirements. Never import charts from CDNs in an offline project; install dependencies if project/network policy allows or work with existing packages. If installing is impossible, implement a functional native SVG/Canvas alternative for simple charts. **Chart semantics, correct units and accessible interactions outrank library choice.**

### Universal chart construction

- Chart is in a rectangular `--bg-surface` panel with 1px `--border-soft` border and 7px radius; no gradient, noise texture, or drop shadow.
- Title inside top-left is 12px/600; short description beneath only if needed to disambiguate (`Cumulative mark-to-market P&L · net of costs`). Top-right: compact compare selector, expand/export, `⋯` if actions exist.
- Default panel height ~325px; plot actual drawable height at least 225px. Major timeseries may be 380–460px. Chart content padding approx left 42–62, right 15–22, top 18–25, bottom 38–55 **inside** panel after header. Increase margins for long ticks, not by clipping labels.
- Horizontal gridlines 1px `--chart-grid`, opacity 0.7; usually 4–6 ticks. Vertical gridlines only when needed for time interpretation and subdued. Axes have no thick dark spines. Axis labels 10–11px in `--chart-axis`; units in axis or caption, NEVER implicit.
- Series lines width ~2px (1.5px for dense multi-run); no dot markers along every dense sample. On hover display marker and thin vertical crosshair. Use 0.1–0.18 alpha fill only when area fill is meaningful.
- Legend uses coloured 8px line/key + readable text, located below chart or in a compact top strip. If legend is long (>6 entries) use a list with scroll/search. Clicking toggles a series; Shift-click (where implemented) isolates; muted styles show hidden series. Tooltip repeats series names alongside values.
- Tooltip dark raised surface, thin border, 6px radius, 12px text, subtle shadow, tabular values, explicit timestamp (and timezone as relevant), precise units, series swatches. Keep tooltip within viewport. For compare plots support shared x-axis hover across panels when x domains align.
- Interactions: hover, zoom/brush, reset zoom, double click to reset (if documented), toggle series, download PNG/SVG and CSV data when sensible, full-size plot view. Keyboard-accessible equivalents to key actions when feasible.
- Don't connect time series across missing observations; distinguish missingness from zero. Render NaN/failed results honestly. Time axes must sort chronologically and avoid accidental categorical rendering.
- Never apply smoothing, resampling, rolling means, log transforms, clipping, or interpolation silently. Label them and make raw results accessible.
- For comparative series, ensure same axis units, same sample definitions and common date range unless user explicitly chooses otherwise. Label in-sample/out-of-sample boundary and major event markers discreetly when informative.

### Chart types by research question

| Question | Preferred representation | Specific requirement |
|---|---|---|
| Backtest performance over time | Wide cumulative P&L/equity line chart | State base capital or P&L currency, costs included/excluded, evaluation period, baseline |
| Risk accumulation | Drawdown line/filled area, usually negative | Zero baseline; peak-to-trough convention; annotate worst drawdown if available |
| Return distribution | Histogram + optional reference density | State sampling interval, bin width, number of observations; don't imply normality |
| Two strategies compared | Overlay with stable colour + benchmark | Common axis and horizon; allow rebasing / normalisation when comparing scale |
| Predicted vs realised move | Scatter with y=x reference | Axes same units; alpha or hexbin for overplotting; sample count |
| Forecast calibration | Reliability diagram + ideal line | Prediction bins, empirical rate, bin counts, uncertainty where available |
| Residual errors | Time plot, histogram and residual-vs-prediction | Preserve signed errors and outliers |
| Train and validation learning | Loss vs steps line plot | Separate train/val, log scale toggle if appropriate; mark best checkpoint |
| Hyperparameter sweep | Scatter/heatmap/parallel coordinates | Legend for colour encoding; don't map arbitrary model IDs to continuous gradients |
| Signal predictive strength | Rolling IC or alpha-decile returns | Explicit horizon, windows, transaction cost assumptions, confidence |
| Model explanation | Sorted feature importance bars | State method (gain, SHAP, permutation); avoid causal interpretation |
| Correlation/covariance | Heatmap with diverging scale | Centred at 0 for correlation; visible colourbar and units/normalisation |
| RFQ execution likelihood | Calibration plot and binned residuals | Include number of observations, hit-rate base rate, and segment filters |

Do not include all plots by default. Choose those that support the user's exact analytical objective. Prefer the **two or three decision-driving plots** first and move residual diagnostics into a secondary tab.

### Visual encoding discipline

- Sequential positive quantities: desaturated blue scale. Signed differences: balanced blue-to-muted-red diverging scale centred on zero. Categories: stable six-series palette. Don't use a rainbow gradient or green-vs-red alone.
- Colour meaning consistent across table rows, legend, detail drawer and all charts.
- Reserve red/pink for real negative status or risk, and yellow for alerts; positive/negative numerical values may need neutral text depending on context.
- For bar charts: zero baseline unless explicitly justified; categories ordered meaningfully; horizontal bars for long labels. For line charts: zero baseline optional (choose meaningful y domain; never distort without clear axis ticks). For density estimates: clarify bandwidth. For heatmaps: label the colour legend.
- Show sample sizes and data provenance in the title subtitle, caption or detail drawer. Warn about sparse series and incompatible date alignment.

## 8. Domain-specific page recipes

### A. Backtest explorer

Header: `Backtests / Strategy name`; top metadata line: strategy version, dataset, date range, currency, costs assumptions. Filters: universe, strategy configuration, dates, benchmark, gross/net. The content hierarchy is (1) cumulative P&L full-width or 2/3 width, (2) drawdown / exposure, (3) compact metrics strip or metrics table, (4) trades/results table, (5) full detail drawer. Use Sharpe, realised vol, max drawdown, turnover only if defined and available. Clearly distinguish bp, % return, cash P&L, and spread move; do not exchange one for another.

### B. Model training experiment browser

Left pane lists training runs with checkbox, status dot, colour key and searchable name. Top selects experiment and metric. Main right area has model loss by step, validation metric by step, optional training systems metrics. Tabs `Charts`, `Parameters`, `Diagnostics`, `Artifacts`. The selected run exposes train data interval, feature set/version, model family, hyperparameters, metric definitions, commit/run identifier. Support 2–6 run comparisons without shifting colour identities. A full-width comparison table can show parameter differences with unchanged values de-emphasised.

### C. Signal research workbench

Header exposes instrument universe, observation unit, signal horizon, target definition, training/validation/test dates. Tabs `Overview`, `Signals`, `Combination`, `Attribution`, `Diagnostics`. Primary comparison: selected signals and combined predictor vs realised future change, with rolling IC, quantile portfolio performance and turnover/coverage. Left column lists selectable signals and weights with uncertainty or validation score. Show lag/alignment caveats; never compute or display features using future information by accident. A weight comparison table and rolling coefficient chart are often more useful than oversized summary cards.

### D. Curve/model visualiser

Often no sidebar is needed. Left settings rail 270–320px, right 1–2 large charts. Settings grouped into small labelled sections: dataset/time, parameters, fit settings. Right plot overlays observed points and fitted curve with residuals beneath. Cursor crosshair and parameter sensitivity controls. Numeric parameter values remain editable while adjusting sliders. Display model definition and objective/loss in a small disclosure, not a prominent decorative card.

## 9. Dense analytical components

**Metric strip (when useful):** 3–6 values in a single horizontal container separated by vertical 1px rules, NOT separate giant cards. Each has muted 11px label, 18–24px numeric value and optional 11px context (window, comparison to baseline). Use comparative arrows only with unambiguous meaning. Avoid mixing scales without units.

**Details drawer:** right aligned, 340–430px, full-height below topbar, raised background, left border. Includes entity name, close, small metadata, tabbed sections, key-value grid, link to full page. Does not obscure the context of table selection unnecessarily. Escape closes; URL/back restores state.

**Status:** subtle coloured 6–7px dot + text (`Completed`, `Running`, `Failed`), or muted outlined tag with full text. Never status-by-colour only.

**Empty state:** one sentence identifying what's missing and a real recovery action. No illustration, motivational copy, or generic "get started" animation.

**Notification:** contextual inline message for data quality, stale timestamp, failed job, invalid compare, no data. Keep non-blocking except for destructive decisions.

**Tooltips:** provide definitions for potentially unfamiliar metrics and window calculations. State how `Sharpe`, `IC`, `bps`, `hit ratio`, `turnover`, `p-value` or `CV score` were computed if used. Never invent methodology.

## 10. Interaction contract: a research app must behave correctly

- Search, sort, filter, date selection, checkbox selection, tabs, hover and chart legends work; no decorative fake controls. A user should always know which population is being analysed.
- All charts, tables and metric summaries respond consistently to filter and run selection. Shared state is centralised rather than independently duplicated in each component.
- If actions trigger computation, separate `Apply / Run` from transient form edits when recalculation is expensive; for cheap transforms update immediately with no flashing.
- Loading/empty/error/partial-data states are designed for every data-dependent panel. A failed panel should not destroy unrelated successful results.
- Preserve reproducibility: expose configuration, model/data version, date range, timestamp, metric formula and, where practical, a shareable deep link. Do not claim performance results not backed by calculations.
- For comparable plots, synchronise time cursors; provide benchmark toggle, zoom reset and exact value readout.
- Include keyboard focus, `aria-label` for icon buttons, semantics on tabs/tables, and minimum 4.5:1 contrast for normal text where practical. Check contrast of muted labels on dark panels (never sacrifice legibility for subdued aesthetics).

## 11. Technology strategy

Default for new standalone apps: **React + TypeScript + Vite + CSS variables**, optional Tailwind if already adopted. Use shadcn/Radix primitives selectively for menus, selects and dialogs, heavily restyled with these tokens; do NOT let a component library dictate visual identity. For charts prefer **Apache ECharts** (dataZoom, linked axes, scatter, heatmap, dense time-series) or an existing mature plotting library. For serious tables use **TanStack Table** plus virtualisation when needed; AG Grid is an option if already licensed/installed and its theming is controlled. Use `lucide-react` if it is already available or installation is acceptable.

If React already exists, use it; do not rewrite a functioning app merely to follow a preferred stack. If a Python plotting framework is the existing app, apply tokens and layout where possible; avoid unjustified cross-stack migration. Where the environment is disconnected, reuse installed dependencies and avoid network APIs, remote fonts, CDN-only assets or screenshots. Use included CSS tokens rather than remote design-system instructions.

Prefer components organised as:

```text
src/
  design/tokens.css                 // global palette, spacing, typography
  components/layout/{AppShell,Topbar,Sidebar,PageHeader,Toolbar}.tsx
  components/data/{ResearchTable,MetricStrip,FilterChips}.tsx
  components/charts/{ChartPanel,SeriesLegend,ChartToolbar}.tsx
  components/details/{DetailDrawer,KeyValueGrid}.tsx
  features/<domain>/{...}          // business logic and domain-specific views
```

Keep calculations and domain semantics separate from UI; chart components accept clean typed data, units, display formats, and stable series IDs. Avoid hardcoding data into JSX or placing complex business transformations in render functions.

## 12. Concrete CSS and component behaviour

If `assets/tokens.css` exists, import it globally. Otherwise use the colours and sizes in §2. Default body has no margins, `background:var(--bg-app); color:var(--text-primary); font-size:13px; line-height:1.45; -webkit-font-smoothing:antialiased`. Apply `box-sizing:border-box` globally. Use a thin scrollbar matching surfaces; never force hidden overflow on the whole page if panels scroll.

Topbar: `height:48px; background:var(--bg-topbar); border-bottom:1px solid #27415F; display:flex; align-items:center; padding:0 18px;`.

Sidebar: `width:224px; background:var(--bg-sidebar); border-right:1px solid var(--border); padding:12px 8px;`, nav row height `31px`, radius `4px`, selected left border `2px solid var(--accent)`.

Main area: `min-width:0; flex:1; padding:18px 22px 28px; overflow:auto;`. Do not add a fake centered container. Main header bottom margin `14px`.

Chart panel: `background:var(--bg-surface); border:1px solid var(--border-soft); border-radius:7px; min-width:0; overflow:hidden;`. Panel header `height:39px; padding:0 14px; border-bottom:1px solid var(--border-soft); display:flex; align-items:center; justify-content:space-between`. Chart canvas should be resize-aware (ResizeObserver/library resize), never fixed pixel width.

Table: width 100%, header 32px, body 36px, `border-collapse:separate; border-spacing:0;` with bottom separators. Numeric class right-aligned with tabular numbers. Sticky header z-index above cells but below popovers. Row hover applies a subtle background rather than shadows.

Control button: `height:31px; padding:0 10px; border:1px solid var(--border); border-radius:5px; background:var(--bg-surface); color:var(--text-secondary); font-size:12px;`. Focus visible 2px accent outline and 2px offset.

Use CSS grid for chart layouts `grid-template-columns:repeat(2,minmax(0,1fr)); gap:12px`. A wide hero analysis chart can span two columns; below 1250px of **content area width** switch to one column when tick labels would collide. Use `min-width:0` on each grid child.

## 13. Agent procedure: how to apply the skill autonomously

Before coding:

1. Identify the user's analytical objective and the real data inputs/outputs. Establish the central question each screen answers, not just a list of widgets.
2. Inspect project structure, existing UI, styles, dependencies, charts, state management and business logic. Preserve existing useful functionality and existing tests.
3. Select appropriate page recipe (§8). Decide whether project needs a sidebar; don't force it into a simple one-page fitting tool.
4. Define data model: runs, strategies, metrics, timestamps, units, sample identifiers, missing values, groups and benchmarks. Ensure correct transformations and no leakage.
5. Create or reuse design tokens, AppShell, consistent chart panel, toolbar and data table. Design the **most data-dense screen first**; visual consistency elsewhere follows it.

During implementation:

6. Implement all controls and chart interactions using actual application state. Use truthful sample/mock data only when explicitly developing a demonstration and label it `Demo data`; never silently replace real data with generated numbers.
7. Make chart axis labels, units, tooltip formatters, legends, empty states and panel heights consistent. This step is mandatory, not polish.
8. Check at desktop 1440×900 and 1920×1080, plus a narrower ~1100px viewport. Check chart resizing, long identifiers, large numbers, no results, dense tables, missing values, and a 6+ run comparison.
9. Verify accessibility, focus states, contrast, keyboard access, and that selection, filters, sorting and charts all agree.
10. Run project lint/build/tests when available. Report what was tested and what remains unverified; don't claim visual QA if you cannot render or inspect the application.

When user's request is purely to restyle an existing app, do not alter research calculations, model inputs, or data transformation logic except to fix explicitly requested defects. Avoid unrelated functional scope expansion.

## 14. Final UI self-review — fix failures before declaring done

- [ ] Resembles a quiet dark experiment/research workbench, not a SaaS marketing dashboard.
- [ ] Topbar, sidebar (if needed), tabs, toolbar, panels, table and charts share one dark palette.
- [ ] Colour used sparsely, primarily for active controls, series identity and genuine statuses.
- [ ] At 1440px there is useful visible analysis above the fold; no oversized header or blank space.
- [ ] Every figure has a meaningful unit, axis, date range and metric definition as applicable.
- [ ] No chart silently smooths/aggregates/truncates or renders missing data as zero.
- [ ] Series colour mapping is stable everywhere; legend toggling and selection work.
- [ ] Numerical columns line up, sorting/filtering work, and table rows are compact.
- [ ] Side panels and charts resize correctly; panel labels never collide or clip.
- [ ] Actions really work; empty, loading, error and partial-data states are handled.
- [ ] Text remains readable; keyboard focus is visible; every icon-only control is labelled.
- [ ] Does not depend on web searches, image URLs, remote fonts or unavailable UI assets.

**Design directive to remember:** The reference is MLflow's dark experiment tracking structure. The target is a more polished, more consistent, data-dense **quantitative research workbench** with minimal visual noise. If uncertain, choose a simpler layout, fewer colours, smaller radii, a better table, and more precise chart labels.
