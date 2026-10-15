# Quantifying the Airline "Monopoly Tax": Market Concentration and Pricing Dynamics in U.S. Domestic Aviation

[![Python](https://img.shields.io/badge/Python-3.9%2B-blue.svg)](https://www.python.org/)
[![Data Source: DOT](https://img.shields.io/badge/Data-DOT%20Airfare%20API-orange.svg)](https://data.transportation.gov)
[![Data Source: EIA](https://img.shields.io/badge/Data-EIA%20Jet%20Fuel%20API-green.svg)](https://www.eia.gov/opendata/)
[![Format: Parquet](https://img.shields.io/badge/Format-Apache%20Parquet-red.svg)](https://parquet.apache.org/)

---

## 📌 Project Overview & Motivation

Commercial air travel is a critical backbone of the U.S. economy, generating billions of dollars annually from millions of passengers traveling for work, family, and leisure. While travelers theoretically have multiple airlines to choose from nationwide, airline competition operates on a **route-by-route** basis. 

When a single airline captures dominant market share on a specific city-pair route, passengers lose competitive leverage. This project investigates and quantifies the **"Monopoly Tax"**—the premium travelers pay driven by market dominance rather than legitimate operating costs or organic supply and demand.

### Core Objectives
1. **Calculate the Price Markup:** Measure the direct dollar and percentage premium passengers pay when one airline dominates a city-pair flight path.
2. **Analyze Distance Dynamics:** Determine whether monopoly power is leveraged more heavily on short-haul flights (competing with ground transit) or long-haul flights (where flying is the sole viable option).
3. **Build a Fair-Price Forecast:** Train a predictive model to establish baseline fair ticket prices, identifying the most overcharged routes in U.S. domestic aviation.

---

## 👥 Authors & Team
* **Ben An**
* **Dan Mosu**
* **Kai Clancy**
* **Kate Bloom**
* **Priyanka Govani**

*Course Project for MIST 6380.*

---

## 🏗️ Architecture & Pipeline

```text
[ DOT Socrata API ] ──┐
                      ├──> [ Stage 1: Ingestion ] ──> [ Stage 2: Processing & Cleaning ]
[ EIA Fuel Spot API ] ┘                                          │
                                                                 ▼
[ Stage 5: Dashboards & Insights ] <── [ Stage 4: Analytics ] <── [ Stage 3: Parquet Storage ]
```

---

## 🔄 Project Stages

### Stage 1: Data Ingestion (`Capture`)
Automated extraction via Python `requests` targeting two primary APIs:
* **U.S. DOT Domestic Airline Consumer Airfare Report (Socrata API):** Quarterly route records including origin/destination cities (`city1`, `city2`), non-stop flight distance (`nsmiles`), passenger volume (`passengers`), average fare (`fare`), lead carrier (`carrier_lg`), lead carrier market share (`large_ms`), and lead carrier average fare (`fare_lg`).
* **U.S. Energy Information Administration (EIA) API:** Monthly jet fuel spot prices aligned with corresponding quarters to isolate true pricing power from operating cost fluctuations.

### Stage 2: Data Cleaning & Processing (`Process`)
* **Standardization:** Currency strings stripped and converted to numeric floats; `large_ms` converted to standard decimals; city/state identifiers normalized from metropolitan area tags.
* **Filtering:** Pruning incomplete records and low-volume routes ($<10$ passengers/day) to eliminate statistical noise.
* **Feature Engineering:**
  * **Airline Fare Premium (Markup):** 
    $$\text{Dollar Markup} = \text{fare\_lg} - \text{fare}$$
  * **Cost-Per-Mile:** 
    $$\text{CPM} = \frac{\text{fare}}{\text{nsmiles}}$$
  * **Competition Tiers:**
    * **Competitive:** Lead carrier market share $< 40\%$
    * **Concentrated:** Lead carrier market share between $40\%$ and $65\%$
    * **Monopolized:** Lead carrier market share $> 65\%$

### Stage 3: Storage & Data Management (`Store`)
* **Format:** Serialized to **Apache Parquet** for typed columns, high-compression ratios, and fast parallel reads.
* **Partitioning:** Partitioned chronologically by year and quarter:
  ```text
  data/
  └── processed/
      └── year=YYYY/
          └── quarter=QX/
              └── routes.parquet
  ```

### Stage 4: Analytics & Statistical Modeling (`Analyze`)
* **Insight 1: Monopoly Surcharge Analysis:** Difference-of-means tests (ANOVA / independent $t$-tests) across competition tiers, controlling for flight distance.
* **Insight 2: Distance vs. Pricing Power:** Correlation and scatter regression evaluating the relationship between distance (`nsmiles`) and pricing markup, evaluating price elasticity on routes $<400$ miles versus coast-to-coast flights.
* **Insight 3: Predictive Fair-Price Modeling:** Multiple Linear Regression and Random Forest models trained on `nsmiles`, `large_ms`, quarter seasonality, passenger volume, and jet fuel prices to calculate an expected fair market ticket price and isolate predatory pricing anomalies.

### Stage 5: Reporting & Communication (`Communicate`)
* **Comparative Box Plots:** Ticket prices across Competitive, Concentrated, and Monopolized routes.
* **Distance Scatter Plot:** Fare vs. distance color-coded by dominant carrier.
* **Top-10 Gouged Routes:** Ranked table of city pairs exhibiting the highest monopoly markups.
* **Executive Summary:** Strategic guidance for individual travelers, enterprise travel planners, and transportation policy makers.

---

## 📁 Repository Structure

```text
├── data/
│   ├── raw/                  # Staged raw API JSON responses
│   └── processed/            # Partitioned Parquet data files
├── notebooks/                # Exploratory data analysis & prototyping
├── src/
│   ├── ingest.py             # API fetching scripts (DOT & EIA)
│   ├── transform.py          # Data cleaning and feature engineering
│   └── models.py             # Fair-price regression & evaluation models
├── visuals/                  # Generated figures, plots, and tables
├── requirements.txt          # Python dependencies
└── README.md                 # Project documentation
```

---

## 🚀 Getting Started

### Prerequisites
* Python 3.9+
* API keys for [Data.gov](https://data.transportation.gov) and [EIA Open Data](https://www.eia.gov/opendata/)

### Installation
1. Clone this repository:
   ```bash
   git clone https://github.com/<your-username>/airline-monopoly-tax.git
   cd airline-monopoly-tax
   ```

2. Set up a virtual environment:
   ```bash
   python -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. Install required packages:
   ```bash
   pip install -r requirements.txt
   ```

4. Configure your environment variables by creating a `.env` file in the root directory:
   ```env
   DOT_API_KEY=your_dot_api_key_here
   EIA_API_KEY=your_eia_api_key_here
   ```

---

## 📜 License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
