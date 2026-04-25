# exportaciones-colombia-ue

**Latent Risk and Regulatory Persistence: European Environmental TBTs as a Structural Threat to Colombian Manufacturing Exports, 2019–2026**

> Data pipeline and analysis scripts for the working paper by Carlos Iván Lemus Serna  
> Universidad EAN · Semillero Inglomark / LEMON Research Group · 2026

---

## Overview

This repository contains the full data pipeline used to construct the **Risk Persistence Index (RPI)**, a custom indicator that classifies 72 Colombian manufacturing export lines (HS Chapters 39–96) according to their simultaneous exposure to:

- **Historical regulatory pressure** — EU environmental Technical Barriers to Trade (TBT) notifications registered in the WTO I-TIP Goods database (2019–2023)
- **Prospective regulatory threat** — active EU TBT alerts published on the WTO ePing platform (2024–2026)

The pipeline integrates three international databases at the HS-4 digit level and produces a master file used for the Power BI dashboard and the quantitative analysis of the working paper.

---

## Repository Structure

```
exportaciones-colombia-ue/
│
├── scripts/
│   ├── 01_Unification_of_Colombian_exports_to_the_EU.py   # Export data unification and filtering
│   ├── 02_Homologation_and_organization_of_ePing_notifications_(2024-2026).py          # ePing alert normalization (ICS → HS)
│   └── 03_Master_File-Data_Unification.py             # Master file construction + RPI calculation
│
├── data/
│   └── processed/                        # Cleaned output files (raw data not included)
│
├── dashboard/
│   └── vulnerabilidad_regulatoria.pbix   # Power BI dashboard (3 synchronized tabs)
│
├── requirements.txt
└── README.md
```

---

## Data Sources

| Source | Description | Coverage |
|--------|-------------|----------|
| [WTO I-TIP Goods](https://i-tip.wto.org/goods) | Environmental TBT notifications by the EU | 2019–2023 · 88 notifications |
| [WTO ePing](https://epingalert.org) | Active regulatory alerts before entry into force | 2024–2026 · 75 relevant alerts |
| [ODEB](https://observatorio.desarrolloeconomico.gov.co) | Colombian exports to the EU by HS code | 2019–2025 · 6,129 records |

> **Note:** Raw data files are not included in this repository due to size and licensing constraints. All three sources are publicly accessible via the links above.

---

## Scripts — Execution Order

Run the scripts sequentially (1 → 2 → 3). Each script produces the input required by the next.

### Script 1 — `01_Unification_of_Colombian_exports_to_the_EU.py`
Unifies annual ODEB export files into a single dataset. Filters HS Chapters 39–96 (manufactured goods and diversified consumer products) and retains only trade flows destined for the 27 EU member states.

**Output:** `data/processed/exportaciones_colombia_ue_2019_2025.csv`

---

### Script 2 — `02_Homologation_and_organization_of_ePing_notifications_(2024-2026).py`
Processes the 158 EU ePing notifications (2024–2026). Separates multiple product codes per row into individual records, prioritizes HS codes over ICS codes, and applies an ICS-to-HS equivalence dictionary for notifications without a direct tariff code. Removes unidentifiable entries.

**Output:** `data/processed/eping_homologado_limpio.csv` (109 records, no null values)

---

### Script 3 — `03_Master_File-Data_Unification.py`
Integrates all three sources at the HS-4 digit level. Builds a bridge table that maps ePing chapter ranges to specific tariff headings with verified trade flows. Calculates the **Risk Persistence Index (RPI)** for each of the 72 export lines:

| RPI Category | Definition | Score |
|---|---|---|
| Extreme Risk | Present in both I-TIP and ePing | 4 |
| Chronic Risk | Present in I-TIP only | 3 |
| Emerging Risk | Present in ePing only | 2 |
| No Identified Risk | Absent from both sources | 1 |

**Output:** `data/processed/archivo_maestro_ipr.xlsx`

---

## Requirements

```
Python 3.12+
pandas
openpyxl
numpy
```

Install all dependencies:

```bash
pip install -r requirements.txt
```

---

## Power BI Dashboard

The file `dashboard/vulnerabilidad_regulatoria.pbix` requires **Microsoft Power BI Desktop 2024** or later. The dashboard contains three synchronized tabs:

1. **Historical Vulnerability Matrix** — regulatory intensity vs. export value (2019–2023)
2. **Prospective Radar** — temporal and sectoral distribution of ePing alerts (2024–2026)
3. **Master Cross** — bubble chart where bubble size represents export value at risk; filters are synchronized across all three tabs

---

## Key Findings

- **81.9%** of the export portfolio (59 of 72 tariff headings) is classified as **Extreme Risk**
- Over **USD 99.2 million** in exports face simultaneous historical and prospective regulatory pressure
- **Chapter 85** (electrical and electronic equipment) is the most vulnerable sector: 22 historical measures + 10 active prospective alerts
- **Chapter 87** (vehicles): exports grew **+562%** while accumulating new regulatory alerts — highest combination of growing exposure and regulatory risk
- Central paradox: despite high regulatory pressure, several product lines continue growing, suggesting that Colombian exporters are not yet incorporating these regulatory signals into their decisions

---

## Citation

If you use this repository or any of its scripts in your research, please cite as:

> Lemus Serna, C. I. (2026). *exportaciones-colombia-ue* [Source code repository]. GitHub. https://github.com/clemuss14430/exportaciones-colombia-ue

---

## Author

**Carlos Iván Lemus Serna**  
International Business · Universidad EAN  
Semillero Inglomark · LEMON Research Group (Laboratorio de Emprendimiento, Oportunidades y Negocios)  
Bogotá, Colombia

---

## License

This repository is shared for academic and research replication purposes.  
Please contact the author before using the scripts or data in commercial applications.
