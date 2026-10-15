
# Quantifying the Airline "Monopoly Tax": Market Concentration and Pricing Dynamics in U.S. Domestic Aviation

[![Python](https://img.shields.io/badge/Python-3.9%2B-blue.svg)](https://www.python.org/)
[![Data Source: DOT](https://img.shields.io/badge/Data-DOT%20Airfare%20API-orange.svg)](https://data.transportation.gov)
[![Data Source: EIA](https://img.shields.io/badge/Data-EIA%20Jet%20Fuel%20API-green.svg)](https://www.eia.gov/opendata/)
[![Format: Parquet](https://img.shields.io/badge/Format-Apache%20Parquet-red.svg)](https://parquet.apache.org/)

**Authors:** Ben An, Dan Mosu, Kai Clancy, Kate Bloom, Priyanka Govani[cite: 1]  
**Course:** MIST 6380[cite: 1]  
**Deliverables:** Jupyter Notebook (`.ipynb`) & Presentation Slides (`.pdf` / `.pptx`)

---

## 📌 Project Overview & Motivation

Commercial aviation is an essential driver of the U.S. economy, yet competition rarely plays out on a truly national level—it happens route-by-route[cite: 1]. While travelers may enjoy numerous carrier choices nationally, individual city-pair markets are frequently dominated by one or two airlines[cite: 1]. 

When a carrier captures a dominant market share on a specific route, passengers lose alternative options, allowing airlines to extract a **"Monopoly Tax"**—an artificial pricing premium driven by market dominance rather than operational flight costs or standard supply/demand[cite: 1].

This project designs and executes an end-to-end data analytics pipeline to:
1. **Quantify the Markup:** Measure the exact dollar and percentage premium passengers pay on single-carrier-dominated routes[cite: 1, 2].
2. **Evaluate Distance Effects:** Compare carrier pricing power across short-haul routes (where ground transit offers substitution) versus long-haul routes (where flying is unavoidable)[cite: 1, 3].
3. **Model Fair Fares:** Train an intuitive pricing model that benchmarks expected ticket prices and pinpoints the most gouged domestic routes[cite: 1, 3].

---

## 🏗️ Analytics Pipeline Architecture

The pipeline follows a five-stage structure:

[ Data Capture ] ──> [ Data Processing ] ──> [ Data Storage ] ──> [ Analysis & Modeling ] ──> [ Communication ]

DOT API            - Cleaning & Typing     - Local Parquet      - ANOVA / T-Tests           - Visualizations

EIA Fuel API       - Feature Engineering   - Partitioned        - Regressions / ML          - Slide Deck


### 1. Capture
* **Primary Source (DOT Airfare API):** Extracted via the Data.gov Socrata Open Data API (`data.transportation.gov`) from the U.S. Bureau of Transportation Statistics Domestic Airline Consumer Airfare Report[cite: 1]. Captures city pairs (`city1`, `city2`), route distance (`nsmiles`), passenger volume (`passengers`), average market fare (`fare`), dominant carrier (`carrier_lg`), dominant market share (`large_ms`), and dominant carrier fare (`fare_lg`)[cite: 1, 2].
* **Enrichment Source (EIA Jet Fuel API):** Monthly jet fuel spot prices from the U.S. Energy Information Administration (EIA) to control for macro fuel cost shocks across analyzed quarters[cite: 2].

### 2. Process
* **Cleaning & Formatting:** Cleans text formatting, strips non-numeric characters from currency fields, normalizes market share to standard decimal percentages, and removes routes with fewer than 10 passengers per day to eliminate statistical noise[cite: 2].
* **Feature Engineering:**
  * **Markup Surcharge:** Measures absolute dollar and percentage differences between dominant carrier fares and aggregate market fares (`fare_lg - fare`)[cite: 2, 3].
  * **Competition Tiers:** Categorizes routes into **Competitive** (<40% lead carrier share), **Concentrated** (40%–65%), and **Monopolized** (>65%)[cite: 2].
  * **Normalized Pricing:** Calculates Cost-per-Mile (`fare / nsmiles`) to standardize comparisons across varied route lengths[cite: 2].

### 3. Store
* Cleaned and enriched records are serialized into **Apache Parquet** format to provide high-performance columnar compression and type enforcement[cite: 2].
* Files are partitioned logically by year and quarter (`/data/processed/year=YYYY/quarter=Q/`) to enable modular queries without reprocessing historical data[cite: 2].

### 4. Analyze
* **Monopoly Surcharge Assessment:** Statistical difference-of-means tests (ANOVA / independent $t$-tests) across competition tiers while controlling for distance[cite: 2, 3].
* **Distance Elasticity:** Correlation and regression analysis measuring dominant carrier pricing power on short-haul flights (<400 miles) versus transcontinental routes[cite: 3].
* **Fair-Fare Estimation & Anomaly Detection:** Predictive models (Multiple Linear Regression / Random Forest) trained on flight distance, market concentration, passenger volume, quarter seasonality, and fuel indices to flag the most overpriced flight paths[cite: 3].

### 5. Communicate
* Distribution box plots of airfares segmented by competition tier[cite: 3].
* Distance-versus-fare scatter plots colored by dominant carrier to highlight market anomalies[cite: 3].
* Top-10 summary table of the most overpriced U.S. domestic routes[cite: 3].

---

## 📂 Repository Structure

```text
├── data/
│   ├── raw/                   # Staged raw API JSON responses
│   └── processed/             # Cleaned Parquet partitions (year=YYYY/quarter=Q/)
├── notebooks/
│   └── airline_monopoly_analysis.ipynb  # End-to-end pipeline, code, and annotations
├── presentation/
│   └── MIST6380_Final_Presentation.pdf  # 10-minute presentation slide deck
├── src/
│   ├── capture.py             # DOT and EIA API ingestion scripts
│   ├── process.py             # Data transformation & feature engineering
│   └── models.py              # Statistical tests and pricing models
├── README.md
└── requirements.txt
---

## 📜 License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
