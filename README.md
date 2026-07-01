# Enterprise Retail Analytics Pipeline (E-RAP)

[![Enterprise EDW Pipeline CI](https://github.com)](https://github.com)
[![License: MIT](https://shields.io)](https://opensource.org)

**Enterprise Retail Analytics Pipeline (E-RAP)** is a cloud-native ELT project built with **Python**, **SQL (dbt)**, and **Terraform**. It processes transactional streams, runs custom **chaos-engineering** scripts to perform destructive data testing, and isolates anomalies into a quarantine layer before loading optimized star schemas into **Google BigQuery** via CI/CD.

---

## 🏗️ Architecture & Data Flow

```text
 [ Upstream Analytical Stream ]
               │
               ▼ (Ingestion Pipeline)
     [ python_and_chaos.py ]
               │
      ┌────────┴────────┐
      │ (Destructive Testing Firewall)
      ▼                 ▼
[ Clean Data ]   [ Quarantine Zone ] 
  (Valid Data)     (Negative Price/Null Keys)
      │
      ▼ (IaC Landing Layer)
 [ Google Cloud Storage ]
               │
               ▼ (Modern DW Transformation)
  [ dbt Semantic Models ] ──► [ Google BigQuery ]
                                 ├── dim_customers
                                 ├── dim_products
                                 └── fact_sales
```

---

## ⚡ Core Features (Role Alignment)

* **60% Execution - Destructive Testing:** Implements custom automated chaos scripts (`pipeline_and_chaos.py`) to systematically test pipeline resiliency against negative financial payloads, duplicate primary keys, and orphaned foreign identifiers.
* **Data Governance & Integrity:** Deploys an enterprise firewall architecture. Instead of breaking production or failing silently, anomalous rows are systematically split, aggregated into operational metric payloads, and isolated inside an analytical quarantine layer.
* **Modern Semantic Layer Schema:** Models a standard data warehouse dimensional schema (Star Schema) via **dbt** into transactional Fact and historical Dimension models inside **Google BigQuery**.
* **Infrastructure as Code (IaC):** Utilizes **Terraform** scripts to provision cloud data warehousing storage assets, eliminating manual browser mutations and guaranteeing repeatable developer environments.
* **Continuous Integration (CI/CD):** Configures automated operational checks via **GitHub Actions** to guarantee programmatic validations run cleanly before merges into `main`.

---

## 📂 Repository Structure

```text
E-RAP/
├── .github/workflows/
│   └── ci-cd.yml            # CI Automation Pipeline
├── dbt_models/              # Warehouse Semantic Layer
│   ├── dim_customers.sql    # Customer Grain Dimension
│   ├── dim_products.sql     # Product Domain Dimension
│   ├── fact_sales.sql       # High-Volume Atomic Fact Schema
│   └── staging_transactions.sql
├── src/                     # Core Ingestion Processing
│   ├── pipeline_and_chaos.py# Chaos Engine Firewall
│   └── requirements.txt     # Python Dependencies
├── terraform/               # Infrastructure Code
│   ├── main.tf              # GCS & BigQuery Definition
│   └── variables.tf
├── .gitignore
├── LICENSE                  # MIT License
└── README.md
```

---

## 🚀 Local Quickstart

### Prerequisites
* Python 3.11+
* Terraform v1.0+
* A Google Cloud Platform free-tier account

### Setup Environment
1. Clone the enterprise architecture locally:
   ```bash
   git clone https://github.com
   cd E-RAP
   ```

2. Establish Python pipeline libraries:
   ```bash
   pip install -r src/requirements.txt
   ```

3. Provision your cloud assets using Terraform:
   ```bash
   cd terraform
   terraform init
   terraform apply -auto-approve
   cd ..
   ```

4. Trigger the stream ingestion engine and destructive testing validation:
   ```bash
   python src/pipeline_and_chaos.py
   ```

---

## 📊 Pipeline Observability Metrics Sample

When executing `pipeline_and_chaos.py`, the firewall engine captures transaction streams and generates structured telemetry tracking pipeline health:

```text
--- Starting Pipeline Processing & Auditing ---

[Observability Metrics Generated]:
 - Total Records Received: 1001
 - Duplicate Ids Found: 1
 - Null Keys Found: 3
 - Negative Prices Found: 3
 - Records Quarantined: 7
 - Records Successfully Loaded: 994

[Success] Clean staging tables prepared for data warehouse ingestion.
```

---

## 📄 License

Distributed under the MIT License. See `LICENSE` for more structural information.
