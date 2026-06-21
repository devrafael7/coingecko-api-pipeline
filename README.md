# 🚀 CoinGecko API Pipeline

## 📖 Overview

Este projeto implementa um pipeline de Engenharia de Dados utilizando a API da CoinGecko para ingestão, tratamento e disponibilização de dados de criptomoedas seguindo a arquitetura Medalhão (Bronze, Silver e Gold).

O pipeline foi desenvolvido utilizando Databricks, PySpark e SQL, permitindo a coleta automatizada de dados do mercado de criptomoedas e sua transformação em informações prontas para análise.

---

## 🏗️ Architecture

```text
CoinGecko API
       │
       ▼
  00_setup
       │
       ▼
  01_bronze
       │
       ▼
  02_silver
       │
       ▼
  03_gold
```

### Layers

| Layer  | Objective                                      |
| ------ | ---------------------------------------------- |
| Bronze | Raw ingestion from CoinGecko API               |
| Silver | Data cleansing, standardization and validation |
| Gold   | Business metrics and analytical datasets       |

---

## ⚙️ Technologies

* Databricks
* Apache Spark
* PySpark
* SQL
* Delta Lake
* CoinGecko API
* Databricks Secrets

---

## 📂 Project Structure

```text
coingecko-api-pipeline/
│
├── 00_setup
├── 01_bronze
├── 02_silver
├── 03_gold
└── README.md
```

---

# 🔐 Setup Layer

The setup notebook is responsible for:

* Loading CoinGecko API credentials from Databricks Secrets.
* Creating the project catalog.
* Creating Bronze, Silver and Gold schemas.
* Centralizing reusable project configurations.

Example:

```python
api_key = dbutils.secrets.get(
    scope="coingecko",
    key="api-key"
)
```

Schemas created:

```text
coingecko.bronze
coingecko.silver
coingecko.gold
```

---

# 🥉 Bronze Layer

## Objective

Store raw data collected from the CoinGecko API with minimal transformations.

## Data Source

Endpoint:

```text
https://api.coingecko.com/api/v3/coins/markets
```

Parameters:

```text
vs_currency = usd
per_page = 100
page = 1
```

## Fields Collected

| Column                      |
| --------------------------- |
| id                          |
| symbol                      |
| name                        |
| current_price               |
| total_volume                |
| market_cap                  |
| market_cap_rank             |
| high_24h                    |
| low_24h                     |
| price_change_24h            |
| price_change_percentage_24h |
| atl_date                    |
| atl                         |
| ath                         |
| ath_change_percentage       |
| last_updated                |
| ingestion_date              |

## Bronze Table

```text
coingecko.bronze.crypto_markets_raw
```

## Characteristics

* Raw API ingestion
* Explicit schema definition
* Historical ingestion date tracking
* Secure API authentication using Databricks Secrets

---

# 🥈 Silver Layer

## Objective

Transform raw data into trusted and standardized datasets.

## Transformations Applied

### Data Type Standardization

* DOUBLE
* BIGINT
* INTEGER
* TIMESTAMP
* DATE

### Data Quality

* Removal of duplicate records
* Validation of mandatory fields
* Standardization of date formats
* Data enrichment with processing timestamp

### Examples

```sql
CAST(current_price AS DOUBLE)

TO_TIMESTAMP(last_updated)

CAST(ingestion_date AS DATE)
```

## Silver Table

```text
coingecko.silver.crypto_markets
```

---

# 🥇 Gold Layer

## Objective

Provide business-ready datasets for analytics and dashboards.

---

## Gold Table 1

### market_overview

Global cryptocurrency market summary.

Table:

```text
coingecko.gold.market_overview
```

Metrics:

* Total cryptocurrencies
* Total market capitalization
* Total traded volume
* Average cryptocurrency price
* Maximum cryptocurrency price
* Minimum cryptocurrency price

Business Purpose:

Provides an executive overview of the market.

---

## Gold Table 2

### top_10_by_market_cap

Top cryptocurrencies ranked by market capitalization.

Table:

```text
coingecko.gold.top_10_by_market_cap
```

Metrics:

* Market cap ranking
* Current price
* Market capitalization
* Volume traded
* 24h performance

Business Purpose:

Track the largest cryptocurrencies in the market.

---

## Gold Table 3

### price_performance_24h

24-hour cryptocurrency performance analysis.

Table:

```text
coingecko.gold.price_performance_24h
```

Metrics:

* Price change
* Percentage change
* Market cap
* Trading volume
* Performance classification

Classification:

```sql
positive
negative
neutral
```

Business Purpose:

Identify market gainers and losers.

---

# 📊 Example Pipeline Flow

```text
CoinGecko API
       │
       ▼
100 Cryptocurrencies
       │
       ▼
Bronze Layer
(Raw Data)
       │
       ▼
Silver Layer
(Data Quality & Standardization)
       │
       ▼
Gold Layer
(Business Metrics)
       │
       ├── market_overview
       ├── top_10_by_market_cap
       └── price_performance_24h
```

---

# 🔒 Security

Sensitive credentials are not stored in source code.

The pipeline uses Databricks Secrets for secure API key management.

```python
dbutils.secrets.get(
    scope="coingecko",
    key="api-key"
)
```

---

# 🎯 Learning Objectives

This project demonstrates practical knowledge of:

* API Consumption
* Databricks
* PySpark
* SQL Transformations
* Delta Lake
* Medallion Architecture
* Data Engineering Best Practices
* Secure Credential Management
* ETL Pipeline Development

---

# 📈 Future Improvements

* Incremental loads
* API pagination
* Historical price tracking
* Delta Live Tables
* Data quality framework
* Workflow automation with Databricks Jobs
* Dashboard integration with Power BI
* Real-time cryptocurrency monitoring
