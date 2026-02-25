# Medicare Part D Spend Drivers — Power BI Portfolio Project

**Author:** Gregory Brookfield  
**Tools:** Power BI Desktop, Power Query (M), DAX  
**Dataset:** CMS Medicare Part D Drug Spending (public)  
**Focus of analysis:** Dynamically selects the latest two years available in the dataset (current source data spans 2016–2023)

---

## Overview
This project analyzes Medicare Part D prescription drug spending to identify what drove year-over-year (YoY) changes in spend.  
The report is built to answer:
- Which **therapeutic categories** drove overall spend change?
- Which **drugs** drove those changes?
- Which **manufacturers** were the biggest winners/losers?
- Is spend change driven more by **utilization (claims)** or **cost intensity ($/claim)**?

---

## Report Highlights
- **Driver decomposition:** YoY spend split into **Volume Effect** vs **Price Effect**
- **Utilization vs Cost quadrant:** scatter views showing utilization change vs cost/claim change
- **Manufacturer winners/losers:** YoY change and concentration metrics (Top 1 / Top 5 share)
- **Interactive drillthrough:** right-click to move from category/drug drivers to detail pages

---

## Screenshots

### 1) Overview
![Overview page](images/overview.png)

### 2) Therapeutic Growth Drivers
![Therapeutic drivers page](images/therapeutic_drivers.png)

### 3) Drug Growth Drivers
![Drug drivers page](images/drug_drivers.png)

### 4) Utilization vs Cost Intensity
![Utilization vs Cost intensity page](images/util_vs_cost.png)

### 5) Manufacturer Winners & Losers
![Manufacturer page](images/manufacturer_view.png)

---

## Data Sources
**Primary:** CMS (Centers for Medicare & Medicaid Services) Medicare Part D Drug Spending dataset (accessed via API).  
**Years available in source:** 2016–2023  
**Years analyzed in report visuals:** 2022–2023 (designed to refresh as CMS publishes new years)

**Therapeutic classification (ATC):**
- ATC therapeutic class fields were sourced from CMS-provided reference fields and WHO ATC/DDD reference data.
- Where ATC mappings were missing, I resolved mappings using the NIH RxNorm API based on generic drug names.
- The final ATC mapping is stored as a local mapping table in the model to keep the Power BI report self-contained and refreshable.

---

## Data Model (Star Schema)
Key tables:
- `fct_PartD` (Fact): spending, claims, beneficiaries, average spend per claim
- `dim_Drug`: generic name, brand name, therapeutic class
- `dim_Manufacturer`: manufacturer name
- `dim_Calendar`: date/year table

Relationships:
- `fct_PartD` → `dim_Drug` (many-to-one)
- `fct_PartD` → `dim_Manufacturer` (many-to-one)
- `fct_PartD` → `dim_Calendar` (many-to-one)

---

## Methods (High Level)
- **YoY change** metrics ($ and %) comparing 2022 vs 2023.
- **Spend decomposition:** splits YoY spend into **Volume Effect** (claims change at prior-year cost/claim) and **Price Effect** (cost/claim change at current-year claims).
- **Utilization vs Cost Intensity:** quadrant scatter comparing YoY Claims % vs YoY Avg $/Claim %.
- **Top-N and drillthrough** patterns used to keep visuals readable while enabling detail exploration.

---

## Data Prep (Power Query)
The CMS annual Part D dataset is delivered in a wide, metric-by-year structure. To support clean YoY analysis, I normalized and reconstructed the data into a consistent fact table.

Key transformations:
- **API ingestion:** pulled JSON-formatted data from CMS REST endpoints using `Web.Contents`, with pagination via `List.Generate`.
- **Selective import:** requested only required columns through API query parameters to reduce data volume and improve refresh performance.
- **Metric normalization:** created separate staging queries per metric family (Spend, Claims, Beneficiaries, Avg Spend/Claim), removed non-relevant fields, and unpivoted year columns into a long format.
- **Scaffold grain:** built a scaffold table of unique Drug–Manufacturer–Year combinations by appending key-only interim tables and removing duplicates to enforce a consistent fact grain.
- **Metric alignment:** merged each metric table into the scaffold to ensure correct year-to-year alignment and prevent mismatched grains across metrics.
- **Model hygiene:** disabled "Enable Load" for intermediate/staging queries and retained only final model tables.

---

## Report Navigation
Pages included:
1. **Overview**
2. **Therapeutic Growth Drivers**
3. **Therapeutic Drillthrough (detail)**
4. **Drug Growth Drivers**
5. **Drug Drillthrough (detail)**
6. **Utilization vs Cost Intensity**
7. **Manufacturer Winners & Losers**

Drillthrough usage:
- Right-click a bar/bubble → **Drill through** → detail page

---

## How to Use
- Use slicers to filter by therapeutic class, manufacturer, or other dimensions
- Hover visuals to view tooltips for key definitions and supporting metrics
- Use drillthrough for drug/manufacturer-level detail behind major drivers

---

## Repository Contents
- `/images/` — report screenshots
- `README.md` — project overview and documentation
- `LICENSE` — usage terms

(Optional, if you include files)
- `report.pdf` — exported pages for quick viewing
- `model_notes.md` — deeper technical notes (M query, DAX patterns)

---

## Report File (PBIX)
To reduce the risk of copying and to keep this portfolio project view-only, the `.pbix` file is not published in this public repository.  
**PBIX available upon request.**

---

## License
© 2026 Gregory Brookfield. See `LICENSE` for details.

---

## Contact
- Email: Gregorybrookfield@outlook.com
- LinkedIn: [www.linkedin.com/in/gregory-brookfield]
