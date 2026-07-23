# Architecture Documentation

## System Overview

This document describes the full architecture of the End-to-End ERP System for Revenue Management & Business Intelligence.

## Layer 1: Odoo ERP (Application Layer)

- **Platform**: Odoo 17 (Community Edition)
- **Deployment**: Linux VM (VirtualBox)
- **Custom Module**: `ventes_ayoub` — Sales tracking with auto-computed revenue

### Module Architecture

```
ventes_ayoub/
├── __init__.py          → Registers the models package
├── __manifest__.py      → Module metadata, dependencies, data files
├── models.py            → ORM model: Ventes (sales + computed CA)
├── views.xml            → UI definitions: list view, form view, menu items
└── security/
    └── ir.model.access.csv → RBAC permissions for base users
```

### ORM Model: `Ventes`

| Attribute          | Type                | Notes                                      |
| ------------------ | ------------------- | ------------------------------------------ |
| `_name`            | `ventes_ayoub.ventes_ayoub` | Odoo technical model name            |
| `name`             | `Many2one → res.partner`    | Links sale to a client record        |
| `date_facture`     | `Date`              | Defaults to `fields.Date.today`            |
| `montant`          | `Float`             | Individual sale amount                     |
| `chiffredaffaires` | `Float` (computed)  | Sum of all `montant` records (not stored)  |

## Layer 2: PostgreSQL (Data Layer)

- **Version**: PostgreSQL 15
- **Hosting**: Same Linux VM as Odoo
- **Role**: Relational backend for Odoo ORM; stores all transactional data

### Key Table

The Odoo ORM auto-generates the table `ventes_ayoub_ventes_ayoub` with columns:

| Column          | PostgreSQL Type | Maps To               |
| --------------- | --------------- | ---------------------- |
| `id`            | `integer`       | Auto-increment PK      |
| `name`          | `integer`       | FK → `res_partner(id)` |
| `date_facture`  | `date`          | Invoice date           |
| `montant`       | `double precision` | Sale amount         |
| `create_uid`    | `integer`       | FK → `res_users(id)`   |
| `write_uid`     | `integer`       | FK → `res_users(id)`   |
| `create_date`   | `timestamp`     | Record creation time   |
| `write_date`    | `timestamp`     | Last modification time |

## Layer 3: ETL Pipeline (Integration Layer)

- **Protocol**: ODBC (Open Database Connectivity)
- **Driver**: PostgreSQL ODBC Driver (psqlODBC) on Windows
- **Connection**: Windows host → Linux VM PostgreSQL (port 5432)

### Data Flow

```
PostgreSQL (Linux VM)
       │
       │  TCP/IP port 5432
       ▼
ODBC Data Source (Windows)
       │
       │  System DSN
       ▼
Power BI (Power Query)
       │
       │  Import / DirectQuery
       ▼
Power BI Data Model
```

## Layer 4: Power BI (Presentation Layer)

- **Tool**: Power BI Desktop
- **Connection Mode**: Import (scheduled) or DirectQuery (live)
- **Data Source**: ODBC DSN pointing to PostgreSQL

### Dashboard Components

1. **Revenue Card** — Total CA (Chiffre d'Affaires)
2. **Sales Table** — Detailed transaction list
3. **Time Series** — Revenue trend over time
4. **Client Breakdown** — Revenue per client (bar/pie chart)

## Infrastructure

| Component  | Tool/Platform | Purpose                                     |
| ---------- | ------------- | ------------------------------------------- |
| Server     | VirtualBox    | Hosts the Linux VM running Odoo + PostgreSQL |
| Transfer   | WinSCP        | SFTP file transfer to VM addons directory    |
| IDE        | PyCharm       | Python development for Odoo modules          |
| BI Tool    | Power BI      | Dashboard creation and KPI monitoring        |
