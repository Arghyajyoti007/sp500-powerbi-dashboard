# 📊 Chart Data Mappings — S&P 500 Power BI Dashboard
> Complete X-axis, Y-axis, legend, filter, and formatting specs for every visual across all 3 pages.

---

## 🗂️ Table of Contents
- [Page 1 — Executive Overview](#page-1--executive-overview)
- [Page 2 — Stock Deep Dive](#page-2--stock-deep-dive)
- [Page 3 — Risk & Portfolio Intelligence](#page-3--risk--portfolio-intelligence)

---

## Page 1 — Executive Overview

---

### Chart 1: Avg Close Price Trend — 2014–2017
**Visual Type:** Line Chart (with Area fill)

| Field Slot | Value | Notes |
|------------|-------|-------|
| X Axis | `'Stock Prices'[date]` | Set hierarchy to: Year > Quarter > Month |
| Y Axis (Primary) | `[Avg Close Price]` = `AVERAGE('Stock Prices'[close])` | Navy line (#1E2B8E), area fill with 85% transparency |
| Y Axis (Secondary) | `[MA 30 Days]` | Gold dashed line (#F5A623), secondary axis on right side |
| Legend | None | Two series auto-labelled in legend: "AVG Close" and "MA 30 Days" |
| Filters | Year slicer (page-level) | Responds to 2014 / 2015 / 2016 / 2017 tile buttons |
| Tooltip | Date, Avg Close Price, MA 30 Days | All 3 show on hover |

**Format Settings:**
```
Lines > AVG Close  → Color: #1E2B8E, Width: 2px, Style: Solid
Lines > MA 30 Days → Color: #F5A623, Width: 1.5px, Style: Dashed
Area fill          → Enabled, Transparency: 85%
Y Axis (left)      → Title: "Average Close", Format: $0
Y Axis (right)     → Title: "MA 30 Days", Format: $0
X Axis             → Title: Off, Grid lines: Light gray
```

---

### Chart 2: Return by Year
**Visual Type:** Clustered Column Chart

| Field Slot | Value | Notes |
|------------|-------|-------|
| X Axis | `'Stock Prices'[Year]` | Power Query column — NOT the date column |
| Y Axis | `[Yearly Return %]` | Jan-to-Dec return per year |
| Legend | None | No legend needed |
| Color | Conditional — see below | Positive = Navy, Negative = Red |
| Data Labels | On | Format: `+0.0%;-0.0%` — shown above each bar |
| Sort | X Axis ascending | Year 2014 → 2017 left to right |

**Conditional Color Rule:**
```
Format > Data colors > fx (conditional formatting)
  Rule 1: If value >= 0   → Color: #1E2B8E (Navy)
  Rule 2: If value < 0    → Color: #E74C3C (Red)
  Rule 3: 2016 bar        → Color: #F5A623 (Gold) — optional accent
```

**Format Settings:**
```
Column width       → 60%
Border radius      → 4px (rounded top)
Data labels        → Position: Outside End, Font: 9px, Color: match bar
Y Axis             → Format: 0%, Grid lines: Light gray
X Axis             → No title
```

---

### Chart 3: Volume vs Price (Monthly)
**Visual Type:** Combo Chart (Clustered Column + Line)

| Field Slot | Value | Notes |
|------------|-------|-------|
| X Axis | `'Stock Prices'[MonthName]` | MUST be sorted by MonthNumber column |
| Column Y Axis (Primary) | `[AVG Volumn]` = `AVERAGE('Stock Prices'[volume])` | Navy bars (#1E2B8E) |
| Line Y Axis (Secondary) | `[Avg Close Price]` = `AVERAGE('Stock Prices'[close])` | Gold dashed line |
| Secondary Axis | Enabled for Line series | Right-side axis for Avg Close |
| Legend | Auto | Shows "AVG Volumn" and "AVG Close" |
| Filters | Symbol slicer | Both series filter together |

**Format Settings:**
```
Columns → Color: #1E2B8E, Border radius: 2px
Line    → Color: #F5A623, Width: 1.5px, Style: Dashed
Y Axis (left)  → Format: 0,,M (shows 5M instead of 5000000)
Y Axis (right) → Format: $0
X Axis         → Rotate labels 45° if needed
```

**Month Sort Fix (do this or months show alphabetically):**
```
Data view → click MonthName column
Column Tools ribbon → Sort by Column → MonthNumber
```

---

### Chart 4: Top Sector by Return
**Visual Type:** Donut Chart

| Field Slot | Value | Notes |
|------------|-------|-------|
| Legend | `'Stock Prices'[Sector]` | Calculated column using SWITCH on symbol |
| Values | `[Total Return %]` | Aggregated per sector |
| Detail Labels | Category + Percentage of total | e.g. "118.4% (29.29%)" |
| Filters | Year slicer, Sector slicer | Both apply |

**Color Assignments (set manually):**
```
Tech        → #1E2B8E  (Dark Navy)
Industrial  → #3B52C4  (Mid Navy)
Finance     → #F5A623  (Gold)
Consumer    → #27AE60  (Green)
Health      → #1D9E75  (Teal)
Other       → #888780  (Gray)
Energy      → #E74C3C  (Red)
```

**Format Settings:**
```
Inner radius       → 55% (makes it a donut, not pie)
Detail labels      → On, Inside + Outside
Legend position    → Bottom
Slice spacing      → 2px
```

---

### Chart 5: Rank Bucket Returns
**Visual Type:** Horizontal Bar Chart

| Field Slot | Value | Notes |
|------------|-------|-------|
| Y Axis (Categories) | `RankBuckets[Bucket]` | Static table: Top 10, Top 25, Mid 50, Bot 25, Bot 10 |
| X Axis (Values) | `[Bucket Return]` | SWITCH measure — see core_measures.dax |
| Sort | `RankBuckets[SortOrder]` ascending | 1=Top10, 2=Top25, 3=Mid50, 4=Bot25, 5=Bot10 |
| Data Labels | On | Format: `+0.0;-0.0`, position: Outside End |
| Color | Per-bar conditional — see below | |

**Static Table to Create (Home > Enter Data):**
```
Bucket   | SortOrder | ReturnValue
---------|-----------|------------
Top 10   |     1     |    3.3
Top 25   |     2     |    2.2
Mid 50   |     3     |    1.3
Bot 25   |     4     |   -0.8
Bot 10   |     5     |   -1.8
```

**Per-bar Color (set manually in Format > Data colors):**
```
Top 10  → #1E2B8E  (Dark Navy)
Top 25  → #3B52C4  (Mid Navy)
Mid 50  → #F5A623  (Gold)
Bot 25  → #E07B20  (Orange)
Bot 10  → #E74C3C  (Red)
```

---

## Page 2 — Stock Deep Dive

---

### Chart 1: Price Movement (OHLC)
**Visual Type:** Line Chart (3 series) OR Candlestick (OKViz custom visual)

#### Option A — Built-in Line Chart

| Field Slot | Value | Notes |
|------------|-------|-------|
| X Axis | `'Stock Prices'[date]` | Day level — do NOT group to month |
| Y Axis — Series 1 | `[Avg High]` = `AVERAGE('Stock Prices'[high])` | Green dashed (#27AE60), 1px |
| Y Axis — Series 2 | `[Avg Low]` = `AVERAGE('Stock Prices'[low])` | Red dashed (#E74C3C), 1px |
| Y Axis — Series 3 | `[Avg Close Price]` = `AVERAGE('Stock Prices'[close])` | Navy solid (#1E2B8E), 2px |
| Legend | Auto | Labels: "Avg High", "Avg Low", "AVG Close" |
| Filters | Symbol slicer (required) | Shows one stock at a time |

**Format Settings:**
```
Avg High  → Color: #27AE60, Width: 1px, Style: Dashed, Markers: Off
Avg Low   → Color: #E74C3C, Width: 1px, Style: Dashed, Markers: Off
AVG Close → Color: #1E2B8E, Width: 2px, Style: Solid,  Markers: Off
```

#### Option B — OKViz Candlestick (Recommended)
```
Install: Power BI > ... > Get more visuals > search "Candlestick" > OKViz
Fields:
  Date  → 'Stock Prices'[date]
  Open  → AVERAGE('Stock Prices'[open])
  High  → AVERAGE('Stock Prices'[high])
  Low   → AVERAGE('Stock Prices'[low])
  Close → AVERAGE('Stock Prices'[close])
Colors:
  Up candle   → #1E2B8E (Navy)
  Down candle → #E74C3C (Red)
```

---

### Chart 2: Daily Volume with AVG Line
**Visual Type:** Clustered Column Chart with Constant Line

| Field Slot | Value | Notes |
|------------|-------|-------|
| X Axis | `'Stock Prices'[MonthName]` | Sorted by MonthNumber |
| Y Axis | `[Daily Volume]` = `SUM('Stock Prices'[volume])` | Navy bars |
| Constant Line | `[AVG Volumn]` value | Added via Format > Average line |
| Tooltip | `[Vol vs Avg %]` | Shows % difference from average on hover |
| Filters | Symbol slicer, Year slicer | Both apply |

**Constant Line Setup:**
```
Format pane > Analytics > Average line > ON
  Label   → "Avg Volume"
  Color   → #F5A623 (Gold)
  Style   → Dashed
  Width   → 2px
  Data label → On (shows the value)
```

**Data Labels on bars:**
```
Format: 0,,\"bn\"  → shows 196bn instead of 196,000,000
Position: Inside end or Outside end
Color: White (for bars) or Navy (outside)
```

---

### Chart 3: Risk-Return Scatter
**Visual Type:** Scatter Chart

| Field Slot | Value | Notes |
|------------|-------|-------|
| X Axis | `[Volatility %]` | Risk dimension — higher = riskier |
| Y Axis | `[Total Return %]` | Return dimension |
| Size | `[AVG Volumn]` | Larger bubble = more liquid stock |
| Details | `'Stock Prices'[symbol]` | One bubble per stock ticker |
| Legend | `'Stock Prices'[Sector]` | Color-codes by sector |
| Filters | Year slicer, Sector slicer | Both apply |

**Quadrant Reference Lines:**
```
Format > X Axis > Constant line:
  Value → ~22 (approximate avg volatility — check your data)
  Color → #888780 (Gray), Style: Dashed, Width: 1px

Format > Y Axis > Constant line:
  Value → ~49 (approximate avg return — check your data)
  Color → #888780 (Gray), Style: Dashed, Width: 1px
```

**Quadrant Text Boxes (Insert > Text Box):**
```
Top-Left     → "Low Risk / High Return ★"  Font: Navy, Bold, 9px
Top-Right    → "High Risk / High Return"   Font: Gold, Bold, 9px
Bottom-Left  → "Low Risk / Low Return"     Font: Gray, 9px
Bottom-Right → "High Risk / Low Return ✕"  Font: Red, Bold, 9px
```

**Format Settings:**
```
Bubble size range → Min: 3px, Max: 12px
Bubble opacity    → 80%
Marker shape      → Circle
```

---

### Chart 4: Monthly Return % by Sector
**Visual Type:** Line Chart (Multi-series)

| Field Slot | Value | Notes |
|------------|-------|-------|
| X Axis | `'Stock Prices'[MonthName]` | Sorted by MonthNumber — critical |
| Y Axis | `[Monthly Return %]` | Month-start to month-end return |
| Legend | `'Stock Prices'[Sector]` | One colored line per sector |
| Filters | Year slicer | Changing year changes all lines |

**Line Colors per Sector:**
```
Tech        → #1E2B8E  (Navy)
Finance     → #F5A623  (Gold)
Health      → #27AE60  (Green)
Energy      → #E74C3C  (Red)
Consumer    → #8B5CF6  (Purple)
Industrial  → #1D9E75  (Teal)
Other       → #888780  (Gray)
```

**Format Settings:**
```
Line width     → 1.5px for all series
Markers        → Off
Shaded area    → Off
Y Axis         → Format: 0.0%, Title: "Monthly Return %"
X Axis         → Rotate labels if needed
```

---

### Chart 5: Top 10 Stocks Table
**Visual Type:** Table Visual

| Column | Field/Measure | Formatting |
|--------|--------------|------------|
| Stock Rank | `[Stock Rank]` | Plain number, sort ascending |
| Symbol | `'Stock Prices'[symbol]` | Bold, Navy text |
| Total Return % | `[Total Return %]` | Data bars: Navy gradient, White font |
| AVG Volumn | `[AVG Volumn]` | Format: `0.0,,M` → shows 10.8M |
| Volatility % | `[Volatility %]` | Icon set — see below |
| Return YTD | `[Return YTD]` | Format: `+0.0%;-0.0%`, Green/Red |

**Top N Filter:**
```
Filters pane > This visual > drag [Stock Rank]
  Filter type: Top N
  Show items: Top 10
  By value: [Total Return %]
  Click: Apply filter
```

**Conditional Formatting — Total Return % (Data Bars):**
```
Click column header > Format pane > Cell elements > Data bars > ON
  Positive bar color → #1E2B8E (Navy)
  Negative bar color → #E74C3C (Red)
  Bar direction      → Left to right
  Min value          → Lowest (auto)
  Max value          → Highest (auto)
```

**Conditional Formatting — Volatility % (Icons):**
```
Click column header > Format pane > Cell elements > Icons > ON
  Format style: Rules
  Icon layout: Left of data
  Rule 1: If value > 30   → Red circle    ●
  Rule 2: If value > 20   → Orange triangle ▲
  Rule 3: If value <= 20  → Green circle  ●
```

**Table Style:**
```
Style preset        → Minimal
Header background   → #1E2B8E (Navy)
Header font color   → White
Header font size    → 10px
Row padding         → 8px
Alt row color       → #E8ECF8 (Light Blue)
Sort by Stock Rank  → Ascending
```

---

## Page 3 — Risk & Portfolio Intelligence

---

### Chart 1: Risk-Return Quadrant Map
**Visual Type:** Scatter Chart (full-page version of P2 Chart 3)

| Field Slot | Value | Notes |
|------------|-------|-------|
| X Axis | `[Volatility %]` | Volatility = risk |
| Y Axis | `[Total Return %]` | Return = reward |
| Details | `'Stock Prices'[symbol]` | One dot per stock |
| Color Saturation | `[Total Return %]` | Diverging scale: Navy (high) → Red (negative) |
| Size | `[AVG Volumn]` | Optional: bubble size = volume |
| Filters | Year slicer, Sector slicer | Both apply |

**Quadrant Lines:**
```
X Constant line → AVERAGEX(ALL('Stock Prices'[symbol]), [Volatility %])
Y Constant line → AVERAGEX(ALL('Stock Prices'[symbol]), [Total Return %])
Both lines      → Gray dashed, 1px width
```

**Quadrant Labels (Text Boxes):**
```
Top-Left     → "STAR ★"           Navy Bold — High Return, Low Risk
Top-Right    → "AGGRESSIVE"       Gold Bold — High Return, High Risk
Bottom-Left  → "STABLE"           Gray      — Low Return, Low Risk
Bottom-Right → "AVOID ✕"         Red Bold  — Low Return, High Risk
```

---

### Chart 2: Volatility % KPI Tiles
**Visual Type:** Multi-Row Card (one per top stock)

| Field Slot | Value | Notes |
|------------|-------|-------|
| Fields | `[Volatility %]` | One card per stock |
| Category label | `'Stock Prices'[symbol]` | Shows stock name |
| Filter | Symbol IN {NVDA, NFLX, AMZN, AAPL, GOOG, MSFT} | Visual-level filter |

**Conditional Background Color Rules:**
```
Format > Cell elements > Background color > Rules
  Rule 1: If value > 30   → #E74C3C (Red)    — High Risk
  Rule 2: If value > 25   → #E07B20 (Orange) — Medium-High
  Rule 3: If value > 20   → #F5A623 (Gold)   — Medium
  Rule 4: If value > 15   → #1E2B8E (Navy)   — Normal
  Rule 5: If value <= 15  → #27AE60 (Green)  — Low Risk

Font color for all rules → White
Format values as         → 0.0%
```

---

### Chart 3: MA 30 Days vs Close Price
**Visual Type:** Line Chart (Dual Series)

| Field Slot | Value | Notes |
|------------|-------|-------|
| X Axis | `'Stock Prices'[date]` | Month level |
| Y Axis — Series 1 | `[Avg Close Price]` | Navy, thin 1px line |
| Y Axis — Series 2 | `[MA 30 Days]` | Gold, bold 2px line |
| Legend | Auto | "Close Price" and "MA 30 Days" |
| Filters | Symbol slicer, Year slicer | Meaningful per stock |

**Format Settings:**
```
Close Price → Color: rgba(30,43,142,0.65), Width: 1px, Style: Solid
MA 30 Days  → Color: #F5A623, Width: 2px, Style: Solid
Fill below  → Enable for one series to shade the gap area
Markers     → Off for both
Y Axis      → Format: $0, Title: "Price ($)"
```

---

### Chart 4: Monthly Return Heatmap
**Visual Type:** Matrix Visual

| Field Slot | Value | Notes |
|------------|-------|-------|
| Rows | `'Stock Prices'[Year]` | 2014, 2015, 2016, 2017 |
| Columns | `'Stock Prices'[MonthName]` | Jan–Dec, sorted by MonthNumber |
| Values | `[Monthly Return %]` | Calculated at Year+Month intersection |

**Conditional Formatting (Background Color):**
```
Click [Monthly Return %] in Values
Format pane > Cell elements > Background color > Gradient
  Minimum → #E74C3C (Red)    — most negative month
  Center  → #B4B2A9 (Gray)   — near zero
  Maximum → #1E2B8E (Navy)   — most positive month
  
Apply to: Values only (not totals)
```

**Font Color:**
```
All cells → White (#FFFFFF) — for contrast on colored background
Font size → 8px
Font      → Bold
```

**Month Sort Fix (Critical):**
```
Data view → MonthName column
Column Tools → Sort by Column → MonthNumber (1–12)
```

**Format Settings:**
```
Row subtotals    → Off
Column subtotals → Off
Row headers      → Bold, Navy background, White font
Column headers   → Bold, Navy background, White font
Cell padding     → 6px
Value format     → +0.0%;-0.0%
```

---

## 📌 Global Settings (Apply to All Pages)

### Slicer Sync (View > Sync Slicers)
```
Year Slicer   → Sync ON: Page 1, Page 2, Page 3
Symbol Slicer → Sync ON: Page 1, Page 2, Page 3
Sector Slicer → Sync ON: Page 1, Page 3 (optional on Page 2)
```

### Drillthrough Setup
```
Page 2 > Visualizations pane > Add drillthrough fields:
  → Drag 'Stock Prices'[symbol] here

Effect: Right-clicking any visual on Page 1 that has symbol context
        shows "Drill through → Stock Deep Dive"
```

### Theme Colors (Custom Theme)
```json
{
  "name": "Motilal Oswal",
  "dataColors": ["#1E2B8E","#F5A623","#27AE60","#E74C3C","#3B52C4","#8B5CF6","#1D9E75","#888780"],
  "background": "#FFFFFF",
  "foreground": "#1E2B8E",
  "tableAccent": "#F5A623"
}
```
*Save as `.json` and load via View > Themes > Browse for themes*

### Page Canvas Settings
```
All pages:
  Canvas size → 1280 × 720 (16:9 Widescreen)
  Background  → White (#FFFFFF)
  Wallpaper   → None
```

---

*Last updated: Page 1 ✅ | Page 2 ✅ | Page 3 🔄 In Progress*
