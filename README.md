# 🏢 End-to-End ERP System for Revenue Management & Business Intelligence

> A full-stack enterprise solution built on **Odoo 17**, integrating custom Python modules, PostgreSQL data modeling, ETL pipelines, and Power BI dashboards to deliver real-time financial KPIs and revenue analytics.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Tech Stack](#tech-stack)
- [Module: `ventes_ayoub`](#module-ventes_ayoub)
- [Project Structure](#project-structure)
- [Installation & Setup](#installation--setup)
- [ETL Pipeline](#etl-pipeline)
- [Power BI Dashboards](#power-bi-dashboards)
- [Screenshots](#screenshots)
- [License](#license)

---

## Overview

This project delivers an **end-to-end ERP system** designed for revenue management and business intelligence. It covers the full data lifecycle — from transactional data capture inside Odoo, through ETL extraction via ODBC, to interactive Power BI dashboards for real-time decision-making.

### Key Achievements

- ✅ **Custom Odoo Module** — Built a sales tracking module (`ventes_ayoub`) in Python that computes real-time revenue (Chiffre d'Affaires) across all transactions
- ✅ **Relational Data Modeling** — Designed PostgreSQL schemas on a Linux VM to support complex multi-departmental analytics
- ✅ **ETL Pipeline** — Architected a reliable data extraction pipeline using ODBC protocols to bridge Odoo/PostgreSQL with Power BI
- ✅ **Business Intelligence** — Created interactive dashboards in Power BI to monitor financial KPIs and revenue performance metrics

---

## Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│                        ARCHITECTURE                              │
├──────────────┬──────────────┬────────────────┬───────────────────┤
│  ODOO 17     │ PostgreSQL   │  ETL Layer     │  Power BI         │
│  (ERP)       │ (Database)   │  (ODBC)        │  (BI/Reporting)   │
│              │              │                │                   │
│  Custom      │  Relational  │  Data          │  Interactive      │
│  Module:     │  Schemas     │  Extraction &  │  Dashboards       │
│  ventes_     │  for Sales,  │  Transformation│  for KPIs &       │
│  ayoub       │  Partners,   │  Pipeline      │  Revenue          │
│              │  Revenue     │                │  Analytics        │
│              │              │                │                   │
│  ┌────────┐  │  ┌────────┐  │  ┌──────────┐  │  ┌─────────────┐ │
│  │ Python │──│──│ PgSQL  │──│──│  ODBC    │──│──│  Power BI   │ │
│  │ Models │  │  │ Tables │  │  │  Driver  │  │  │  Desktop    │ │
│  └────────┘  │  └────────┘  │  └──────────┘  │  └─────────────┘ │
└──────────────┴──────────────┴────────────────┴───────────────────┘

Infrastructure: Linux VM (VirtualBox) ──── WinSCP (File Transfer)
```

---

## Tech Stack

| Layer              | Technology       | Purpose                                           |
| ------------------ | ---------------- | ------------------------------------------------- |
| **ERP Platform**   | Odoo 17          | Enterprise Resource Planning & business workflows  |
| **Backend**        | Python 3         | Custom module development & business logic         |
| **Database**       | PostgreSQL 15    | Relational data storage & schema modeling          |
| **OS**             | Linux (VM)       | Server environment for Odoo & PostgreSQL           |
| **File Transfer**  | WinSCP           | SFTP access to the Addons directory on the VM      |
| **IDE**            | PyCharm          | Python development environment                     |
| **ETL**            | ODBC Protocol    | Data extraction from PostgreSQL to Power BI        |
| **BI/Reporting**   | Power BI Desktop | Interactive dashboards & financial KPI monitoring  |

---

## Module: `ventes_ayoub`

A custom Odoo module for **sales tracking and real-time revenue computation**.

### Features

- **Sales Records** — Track individual sales with client, date, and amount
- **Auto-computed Revenue** — The total Chiffre d'Affaires (CA) is calculated automatically across all sales records
- **Partner Integration** — Links each sale to an Odoo `res.partner` (client) record via `Many2one` relationship
- **List & Form Views** — Full CRUD interface with list view and detailed form view
- **Access Control** — Proper security configuration via `ir.model.access.csv`

### Data Model

| Field               | Type       | Description                                    |
| ------------------- | ---------- | ---------------------------------------------- |
| `name`              | Many2one   | Link to client (`res.partner`)                 |
| `date_facture`      | Date       | Invoice / request date (defaults to today)     |
| `montant`           | Float      | Sale amount                                    |
| `chiffredaffaires`  | Float      | Computed total revenue across all sales (CA)   |

---

## Project Structure

```
End-to-End ERP System for Revenue Management & Business Intelligence/
│
├── README.md                          # This file
├── LICENSE                            # MIT License
├── .gitignore                         # Git ignore rules
├── project_details.txt                # Project summary (resume description)
│
├── docs/
│   └── architecture.md                # Detailed architecture documentation
│
└── ventes_ayoub/                      # Custom Odoo Module
    ├── __init__.py                    # Module initialization
    ├── __manifest__.py                # Odoo module manifest
    ├── models.py                      # Python models (Ventes class)
    ├── views.xml                      # XML views (list + form)
    └── security/
        └── ir.model.access.csv        # Access control list
```

---

## Installation & Setup

### Prerequisites

- **Odoo 17** running on a Linux VM (e.g., Ubuntu on VirtualBox)
- **PostgreSQL 15** configured as the Odoo database backend
- **WinSCP** (or any SFTP client) for file transfer to the VM
- **Power BI Desktop** (Windows) for dashboard visualization
- **ODBC Driver for PostgreSQL** installed on Windows

### 1. Deploy the Odoo Module

```bash
# 1. Connect to your Linux VM via WinSCP (SFTP)

# 2. Copy the ventes_ayoub/ folder to the Odoo addons directory:
cp -r ventes_ayoub /usr/lib/python3/dist-packages/odoo/addons/

# 3. Set proper permissions
sudo chmod -R 755 /usr/lib/python3/dist-packages/odoo/addons/ventes_ayoub
sudo chown -R odoo:odoo /usr/lib/python3/dist-packages/odoo/addons/ventes_ayoub

# 4. Restart the Odoo service
sudo systemctl restart odoo
```

### 2. Activate the Module in Odoo

1. Log into Odoo at `http://<your-vm-ip>:8069`
2. Go to **Apps** → Click **Update Apps List**
3. Search for **"Compte CA"**
4. Click **Install**
5. A new **"CA"** menu item will appear in the top navigation

### 3. Configure the ETL Pipeline (ODBC)

```
# On Windows, configure an ODBC Data Source:
# 1. Open ODBC Data Source Administrator (64-bit)
# 2. Add a new System DSN using the PostgreSQL ODBC driver
# 3. Configure:
#    - Server: <VM IP address>
#    - Port: 5432
#    - Database: <your Odoo database name>
#    - Username: odoo
#    - Password: <your password>
```

### 4. Connect Power BI

1. Open **Power BI Desktop**
2. **Get Data** → **ODBC** → Select your configured DSN
3. Import the `ventes_ayoub_ventes_ayoub` table
4. Build dashboards on top of the imported data

---

## ETL Pipeline

The ETL (Extract, Transform, Load) pipeline bridges the Odoo/PostgreSQL environment with Power BI:

```
  Odoo ERP (Sales Module)
         │
         ▼
  PostgreSQL Database
  (ventes_ayoub_ventes_ayoub table)
         │
         ▼
  ODBC Driver (Data Extraction)
         │
         ▼
  Power BI Desktop (Visualization)
```

- **Extract**: ODBC driver connects to PostgreSQL on the Linux VM
- **Transform**: Power BI Power Query handles data cleansing and shaping
- **Load**: Data is loaded into the Power BI data model for visualization

---

## Power BI Dashboards

The Power BI layer provides real-time business intelligence with:

- 📊 **Revenue Overview** — Total Chiffre d'Affaires with trend analysis
- 📈 **Sales Performance** — Per-client and per-period breakdowns
- 🎯 **KPI Monitoring** — Key financial indicators with targets
- 📅 **Temporal Analysis** — Date-based filtering and drill-down capabilities

---

## Screenshots

> _Add screenshots of your Odoo module and Power BI dashboards here._
>
> Example:
> ```
> ![Odoo Sales List View](docs/screenshots/odoo_list_view.png)
> ![Power BI Dashboard](docs/screenshots/powerbi_dashboard.png)
> ```

---

## License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.

---

## Author

**Ayoub** — ERP Developer & Data Analyst

---

<p align="center">
  <i>Built with Odoo 17 · Python · PostgreSQL · Power BI</i>
</p>
