# Agent Notes — v3 Design Port

## What was done

### Chunk 1: CSS tokens
- Replaced `:root` with full v3 warm off-white token set (ink, bg, surface,
  border, accent, sidebar-bg, nav-active, topbar-bg, spacing scale, radius
  scale, shadow scale, semantic success/warning/danger colors)
- Updated `[data-theme="dark"]` with full v3 dark token set
- Added dark sidebar gradient rule

### Chunk 2: Topbar + sidebar
- `.topbar`: frosted glass (`backdrop-filter: saturate(180%) blur(14px)`),
  `color-mix` topbar-bg, 64px height, border-bottom
- `.sidebar`: dark gradient background, updated nav-item gap/font-size/weight
- `.btn`, `.btn-primary`, `.btn-ghost`: pill shape, shadow, hover lift
- `.card`, `.card-head`, `.card-title`: updated radius, shadow, typography
- Updated `.main` and `#dash-period-bar` top offsets for 64px topbar

### Chunk 3: Dashboard HTML restructure
- Replaced flat dashboard layout with 5 `<section class="dash-section">` blocks:
  Overview (bento hero + stats), Drilldown (by-cat breakdown), Commitments,
  Cash Flow, and Charts
- Added bento grid CSS (`.dash-overview`, `.dash-overview-hero`,
  `.dash-overview-stats`), `.dash-section*`, `.dash-drilldown`, full
  `.heat-*` heatmap CSS, `.heat-view-toggle`
- Preserved all existing element IDs that JS references
- Welcome banner preserved at the top of the dashboard view

### Chunk 4: Heatmap table JS
- Added `chartDisplayMode` and `chartCatMode` state variables (lt_ prefix)
- Added `setChartDisplay()` and `setChartCatMode()` functions
- Updated `renderChartSection()` with:
  - chartCatMode top5/all/custom filtering
  - Graph/Table toggle sync
  - Early-return path: hide canvas, show `#timeTable`, call `renderHeatmapTable`
- Added full `_heat*` helper suite: `_heatFmt`, `_heatIsMobile`,
  `_heatBudgetClass`, `_heatMonthLabel`, `_heatGotoMonthCat`,
  `_heatGotoMonthKind`, `_heatExpandedKinds`, `_heatToggleKindExpand`
- Added `renderHeatmapTable()` — Needs/Wants rows with inline category expand,
  budget shading, negative-cell green, compact $1.2k mobile format

### Chunk 5: Currency selector
- Added `dash-currency-wrap` pill to `#dash-period-bar` (between YTD and
  Push to Drive) with `dash-currency-menu` dropdown
- Added `CURRENCIES` object (12 currencies: CAD, USD, GBP, EUR, AUD, NZD,
  CHF, JPY, HKD, SGD, INR, MXN)
- Added `globalCurrency` state (lt_ prefix), `setGlobalCurrency()`,
  `toggleCurrencyDropdown()`, `renderCurrencyDropdown()`
- Updated `fmt()` to use `CURRENCIES` config and accept optional currency arg
- Updated `_heatFmt()` to use `CURRENCIES[globalCurrency]` symbol + decimals
- `renderCurrencyDropdown()` called on app init

### Chunk 6: Edit transaction modal
- Added `add-modal-title`, `add-type-row`, `add-nw-field`, `add-modal-confirm-btn`
  IDs to the existing add modal so it doubles as the edit modal
- Added `_editTxnId` state variable
- Added `_setAddNwLabel()`, `toggleAddNw()` for Needs/Wants override button
- Updated `openAddModal()` to reset state and seed modal for add mode
- Added `openEditModal(id)` — fills modal with transaction data, hides type
  row for protected (income/savings) txns, shows nw override for plain expenses
- Updated `closeAddModal()` to clear `_editTxnId`
- Added `saveEditTxn()` — edits in place, preserves income/savings linkage,
  handles type changes for unprotected transactions
- Updated `confirmAdd()` to route to `saveEditTxn()` when `_editTxnId` is set
- Added pencil (✎) `del-btn` to each transaction row in `renderTxns`

### Chunk 7: Polish
- Added `filterTxnsByKind(kind)` — called by Needs/Wants dashboard tiles,
  navigates to Transactions filtered by needs/wants for the current month
- Needs/Wants pill filter (`txnNeedsFilter`, `setTxnNeedsPill`) was already
  present in the target; `.tcp-needs` CSS with orange accent was already there

## What was preserved (not changed)
- `lt_` localStorage prefix throughout (NOT `l3_` from reference)
- Welcome banner HTML and all its JS (`loadSampleData`, `clearLocalData`,
  `_wipeAllLocalState`, `dismissWelcomeBanner`, `showWelcomeBanner`)
- Google Drive sync code (not replaced with Apps Script)
- Debt page (route `mortgages`)
- All existing element IDs that JS references

## Known gaps / TODO
- Per-account currency on savings accounts (savings modal `as-currency` field,
  `balanceEquiv` display) — not ported; `setGlobalCurrency` already triggers
  `renderSavings()` so the global currency is respected, but per-account
  foreign-currency display is not implemented
- `chartCatMode` is not persisted through the `onChartCatCheckChange` callback
  (it correctly sets `chartCatMode = 'custom'` via the reference pattern but
  that function was not updated to set localStorage — minor, resets on reload)
