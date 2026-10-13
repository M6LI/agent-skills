# MLflow fidelity source audit — 13 October 2026

This file records what the skill can truthfully reproduce offline, and what requires a screenshot comparison or the actual vendored component implementation. It is **not** a claim that all applications built with this skill are pixel-perfect reproductions.

## Source repository

- MLflow frontend instructions: https://github.com/mlflow/mlflow/blob/master/mlflow/server/js/CLAUDE.md
- MLflow app dependencies: https://github.com/mlflow/mlflow/blob/master/mlflow/server/js/package.json
- DuBois vendored package: https://github.com/mlflow/mlflow/blob/master/mlflow/server/js/vendor/design-system/package.json
- DuBois token LESS: https://github.com/mlflow/mlflow/blob/master/mlflow/server/js/vendor/design-system/dist/dubois-colors.less
- DuBois dark stylesheet: https://github.com/mlflow/mlflow/blob/master/mlflow/server/js/vendor/design-system/dist/index-dark.css
- MLflow open source project: https://github.com/mlflow/mlflow

All paths point to `master`, which changes over time. For strict reproducibility, pin a Git commit SHA and verify the screenshots for the intended version. Exact original proprietary branding should not be used in independent work apps.

## Source-grounded findings

1. MLflow frontend code uses `@databricks/design-system` as its component family and instructs developers not to invent UI components when DuBois ones exist.
2. `package.json` resolves `@databricks/design-system` via `file:./vendor/design-system`, not necessarily via a public npm package.
3. Verified DuBois dark colour values (source `dubois-colors.less`): `backgroundPrimary #11171C`, `backgroundSecondary #1F272D`, `textPrimary #E8ECF0`, `textSecondary #92A4B3`, `actionPrimaryBackgroundDefault #4299E0`, `actionPrimaryBackgroundHover #8ACAFF`, `actionDefaultBorderDefault #37444F`, status colours `#3BA65E` / `#DE7921` / `#E65B77`; row hover and select alphas given in the tokens CSS.
4. Vendored compiled dark CSS includes `.du-bois-dark-btn` at 40px high, 16px inline padding, 13px font, 4px radius; `.du-bois-dark-btn-sm` at 32px high, 16px inline padding, 13px font, 4px radius. CSS also contains legacy button-specific colours that can differ from semantic theme tokens; actual shipped component takes precedence.
5. Compiled dark CSS shows single-select selector height 40px, horizontal padding 12px, and tab primitive 13px font with `4px 0` padding.
6. Compiled dark CSS includes system UI font stack headed `-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, 'Noto Sans'` in select text fields. Applying that app-wide is a high-fidelity approximation unless verified against the specific MLflow page.
7. The vendored TypeScript icon declarations list 451 generated icon names. Selected verified exports: `SearchIcon`, `FilterIcon`, `DownloadIcon`, `PlusIcon`, `CloseIcon`, `ChevronDownIcon`, `ChevronRightIcon`, `ChartLineIcon`, `BarChartIcon`, `TableIcon`, `FolderIcon`, `RunIcon`, `PlayIcon`, `PauseIcon`, `ClockIcon`, `CalendarIcon`, `CheckIcon`, `WarningIcon`, `InfoIcon`, `CopyIcon`, `TrashIcon`.

## Fidelity boundaries

- Dark source colour tokens: **verified**.
- Some base button, select and tabs rules: **verified at CSS source level**. Actual resolved CSS cascade may differ.
- App-wide font face and weight: **partially verified**; system stack observed in vendor CSS, not a computed browser screenshot.
- DuBois icon names: **verified**, but this package does not redistribute the actual SVG path data. `lucide-react` will not match precisely.
- MLflow navbar/sidebar widths, row heights, per-page layout and chart margins: **adaptive recommendations**. No single screenshot/version has been pixel-audited.
- Whole-app 'identical MLflow': **not guaranteed** until original bundled components or exact paths are used and screenshot comparisons pass at common viewports.

## Offline agent protocol

1. Check the local repository for `@databricks/design-system` or licensed vendored DuBois assets. If present, use the matching version directly.
2. If not present, import `assets/tokens.css` and follow the MLflow source-grounded rules above.
3. For icons, prefer actual DuBois assets from local dependencies. If absent, use simple local SVG stand-ins and mark those differences, rather than making a false exact-match claim.
4. Prefer the supplied theme colours to changing tokens based on taste. Preserve dark mode.
5. Compare screen captures side-by-side when possible. Record unresolved differences: fonts, icon paths, component border/radius, row heights, chart spacing.
6. Don't claim all metrics/plots resemble MLflow solely because the chrome does; domain-specific plotting remains the application's own responsibility.
