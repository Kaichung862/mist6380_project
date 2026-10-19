# Quantifying the Airline "Monopoly Tax"

An end-to-end data analytics and econometric pipeline investigating localized pricing power, operational cost decoupling, and America's most price-gouged domestic flight corridors.

---

## 📌 Project Overview
While national airline competition appears stable, carrier fortress hubs and slot constraints create localized route monopolies across direct domestic city pairs. This project isolates and quantifies the economic "monopoly tax"—the unearned price markup carriers extract when route-level competition is absent. 

By integrating U.S. Department of Transportation (DOT) origin-destination ticket pricing with weekly U.S. Energy Information Administration (EIA) Gulf Coast kerosene spot prices, our model tests whether high fares reflect operational cost pass-through or structural rent extraction.

---

## 🏗️ Architecture & Pipeline Flow
The project implements an automated ETL pipeline that ingests, cleans, harmonizes, and models multi-source time-series panel data:

┌─────────────────────────────────┐       ┌─────────────────────────────────┐
│     DOT DB1B / BTS Airfare      │       │     EIA Jet Fuel Spot Price     │
│ (10% Ticket Sample, Quarterly)  │       │  (U.S. Gulf Coast Kerosene, W)  │
└────────────────┬────────────────┘       └────────────────┬────────────────┘
│                                         │
└───────────────────┬─────────────────────┘
▼
┌─────────────────────────────────┐
│  Data Cleaning & Preprocessing  │
│ - O&D Airport Standardization   │
│ - Exclude Non-Commercial Fares  │
│ - Impute Stage Length (Miles)   │
└────────────────┬────────────────┘
▼
┌─────────────────────────────────┐
│       Feature Engineering       │
│ - Carrier Share & Route HHI     │
│ - Market Regimes (Monopoly/Comp)│
│ - Fare Yield (¢ / Non-Stop Mile)│
│ - Temporal Alignment (Quarter)  │
└────────────────┬────────────────┘
▼
┌─────────────────────────────────┐
│    Econometric Modeling & FE    │
│ - OLS Pooled Regressions        │
│ - Carrier & Time Fixed Effects  │
│ - Cost Decoupling Verification  │
└─────────────────────────────────┘


---

## 🔑 Key Empirical Findings

### 1. Route Concentration Markups (Yield Spread)
* Normalized by distance (**cents per non-stop mile**), monopoly routes sustain a median fare yield exceeding **40¢/mile**, compared to roughly **28–30¢/mile** in highly concentrated markets and **~20¢/mile** in competitive corridors.
* Fares on single-carrier routes exhibit heavy positive skewness and expanded interquartile ranges (IQR), reflecting unconstrained pricing discretion.

### 2. Operational Cost Decoupling ("Rockets and Feathers")
* Competitive multi-carrier routes track fluctuations in kerosene spot prices with high elasticity.
* On monopoly corridors, ticket yields decouple from marginal fuel costs: carrier markups persist or expand during fuel price declines rather than passing operational savings down to consumers.

### 3. America's Top 10 Marked-Up Corridors
* The top 10 domestically monopolized routes command fare yields between **60¢ and 95¢ per non-stop mile**—representing markups exceeding **200% above competitive domestic cost baselines**.
* Identified corridors are heavily concentrated around fortress hub-and-spoke routes with zero non-stop direct flight alternatives.

---

## 💻 Tech Stack & Dependencies

* **Language:** Python 3.10+
* **Environment:** JupyterLab / Google Colab
* **Libraries:**
  * **Data Manipulation:** `pandas`, `numpy`
  * **Econometrics & Statistics:** `statsmodels`, `scipy`
  * **Data Visualization:** `matplotlib`, `seaborn`

To install required dependencies:
```bash
pip install -r requirements.txt
🚀 Execution & Quickstart
Clone the repository:

Bash
git clone [https://github.com/your-username/airline-monopoly-tax.git](https://github.com/your-username/airline-monopoly-tax.git)
cd airline-monopoly-tax
Data Directory Setup:
Ensure raw DOT DB1B data (.csv) and EIA kerosene spot price tables are placed inside the /data/raw/ directory.

Run Pipeline Notebook:
Launch JupyterLab and run the primary notebook sequentially:

Bash
jupyter lab airline_pricing_pipeline.ipynb
Section 2.1 – 2.3: Ingestion, O&D cleaning, and HHI feature engineering.

Section 2.4: Exploratory data visualizations (yield box plots, time-series decoupling, HHI scatter).

Section 3.0: Multivariate fixed-effects panel regression.

👥 Authors & Academic Context
Authors: Ben An, Dan Mosu, Kai Clancy, Kate Bloom, Priyanka Govani

Course: MIST 6380

Date: October 2026
