# 📈 Fraud Detection Dashboard — Power BI

An interactive Power BI dashboard that analyzes ~10,000 simulated mobile-money transactions to help an analyst spot where fraud is concentrated, how much money is at risk, and what behavioural signals (like unusual logins) actually separate fraudulent transactions from safe ones.

---

## 📋 Overview

In this project : every page shares the same KPI band and filter panel, so a viewer never gets lost, while each page still answers one specific question (geography, payment method, account type, login behaviour).

The dashboard is built on a single, cleaned transaction table and uses custom bookmark-based navigation instead of default Power BI page tabs, so it feels closer to a lightweight web app than a report.

## 📸 Screenshots

Dashboard
![Image not loaded.](screenshots\pj2.1.png)

Fraud
![Image not loaded.](screenshots\pj2.2.png)

Customers
![Image not loaded.](screenshots\pj2.3.png)

Device
![Image not loaded.](screenshots\pj2.4.png)

Methods
![Image not loaded.](screenshots\pj2.5.png)

Accounts
![Image not loaded.](screenshots\pj2.6.png)

## Dataset

- **Source**: A PaySim-style synthetic mobile-money transaction dataset (`Fraud Dataset.csv`), 10,127 rows, dated 2–25 Jan 2018.
- **Fields**: transaction amount, sender/receiver IDs, account balances before/after, account type (Savings/Current), branch (country), payment type (PAYMENT/CASH_IN/CASH_OUT/TRANSFER/DEBIT), time of day, an `unusuallogin` anomaly score, and a fraud label.
- **Class balance**: Safe — 10,057 · Fraud — 68 · Not reviewed — 2. Fraud is intentionally rare (~0.67%), which is realistic for this domain and shaped several design choices below (e.g., using averages instead of raw sums so the small fraud group isn't visually erased by the much larger safe group).

## 📊 Data preparation (Power Query)

1. Loaded and promoted headers from the raw CSV, set correct data types.
2. Parsed `Date of transaction` into a proper date field.
3. Sorted rows chronologically by transaction step.
4. Duplicated the numeric `isFraud` flag to preserve it for calculations.
5. Recoded the primary `isFraud` column from `0 / 1 / null` into readable labels: **Safe / Fraud / Not reviewed** — nulls are explicitly labelled rather than silently dropped.

## 📝 Report structure

| Page | Focus |
|---|---|
| **Dashboard** | Landing page — KPI band, transaction trend, average amount at risk, status breakdown, geographic hotspot map |
| **Fraud** | Decomposition tree for click-to-drill root-cause analysis, fraud by weekday × account type |
| **Customers** | Row-level transaction/customer detail table |
| **Device** | Daily fraud count trend |
| **Methods** | Fraud and volume broken down by payment type |
| **Accounts** | Deepest page — fraud vs. safe comparison across account type, and a clustered bar comparing average `unusuallogin` score between fraudulent and safe transactions |

Every page carries the same nine slicers (date, account type, branch, payment type, step, unusual login, amount, time of day, sender ID), so filtering is consistent no matter which page you're on.

## 🧭 Navigation

Instead of default page tabs, navigation uses hidden slide-out menu and filter panels toggled by **24 bookmarks**, wired to on-page action buttons — every page can reach every other page directly.


## Key design decisions

- **Average, not sum, for the "amount at risk" chart** — with fraud at under 1% of volume, a sum-based chart would make fraud invisible; averages keep it visible and comparable.
- **No star schema / no explicit DAX measures** — the model is a single flat table, so all metrics use Power BI's built-in implicit aggregations (Sum, Average, Median, Variance, Count Distinct). This keeps the model simple and fast to build; a multi-table production version would use explicit DAX measures and proper relationships instead.
- **Decomposition tree over static bar charts on the Fraud page** — gives the viewer an actual investigative tool rather than a fixed narrative.

## ⁉️ Known limitations 

- `isFlaggedFraud`, the dataset's built-in auto-flag, is 0 for every row in this sample and isn't currently used.
- A handful of rows have blank `type` / `Acct type` / `Time of day` values that could be cleaned further.
- The `branch` column actually holds country names — a rename to `Country` would reduce confusion on the map visual.
- Next iteration: add explicit DAX measures for fraud rate (%) and average balance change, and test a proper star schema if additional lookup tables (branch metadata, account holder details) are added.

## 👩‍💻 Tech stack

- **Power BI Desktop** — data modeling, DAX/implicit measures, report design
- **Power Query (M)** — data cleaning and transformation
- **Bookmarks & Selection pane** — custom in-report navigation

## 🔎 How to use

1. Clone this repo and open `PowerBI_Dashboard-Fraud_Detection.pbix` in Power BI Desktop.
2. Use the slicers on any page to filter by date, branch, account type, payment method, or login-anomaly score.
3. Start on the **Dashboard** page for the overview, then use the nav menu to jump into **Fraud** for root-cause drill-down or **Accounts** for the deepest behavioural comparison.

## 👩‍🎓 Author

Built by Diksha Jharotiya ,as part of a data analytics / business intelligence portfolio, targeting decision analytics and BI roles.