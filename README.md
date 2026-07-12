# Camile Thai Kitchen — CRM Automation & Analytics

A Business Intelligence and Business Analytics project built for **Camile Thai Kitchen**, a 20-branch Thai restaurant chain in Ireland, addressing a real operational gap: customer complaints, refunds, and cancellations were being handled manually, with no priority logic, no ownership, and no escalation when requests went unresolved.

This project implements a **dual-layer solution**:

- **HubSpot CRM** as the operational layer — automated workflows that classify, prioritise, assign, and escalate every incoming service request.
- **Power BI** as the analytical layer — three dashboards giving management real-time visibility into revenue, complaints/CSAT, and staff/branch efficiency.



---

## The Problem

Across 20 branches and ~500 active customers, service requests were handled ad hoc:

- No differentiation between a complaint, a refund, and a cancellation
- No guaranteed follow-up — requests could sit indefinitely
- No accountability — nobody was explicitly assigned ownership
- No escalation mechanism if something went unresolved
- No management-level visibility into complaint volume, SLA performance, or staff efficiency

## The Solution

### 1. HubSpot Workflows

Three automated workflows, triggered on deal creation:

| Workflow | Trigger | Logic |
|---|---|---|
| **Refund Automation** | Deal name contains "Refund" | Priority → Medium, owner assigned, task created, 24h delay, reminder email if task incomplete |
| **Cancellation Automation** | Deal name contains "Cancel" | Priority → Low, same task/reminder structure as Refund |
| **Complaint Handling** | Deal name contains "Complaint" | Priority → High immediately, owner set, deal renamed to "Complaint – Urgent Case", instant email alert (no delay — complaints need immediate action) |

Early versions used exact-match filters on deal names, which failed on real-world (imperfectly typed) data. Switching to **"contains" filter logic** fixed trigger reliability.

### 2. Power BI Dashboards

Built on data modeled through PostgreSQL, exported to Power BI:

- **Sales & Revenue** — revenue by branch/channel/dish category, monthly trend, top-performing branches
- **Customer & CRM** — complaint types, pipeline status, CSAT trend, customer spend by loyalty tier, SLA compliance
- **Operations & Staff** — labour cost vs revenue by branch, staff efficiency, role/nationality diversity, Google ratings by branch

### 3. GDPR Compliance

A dedicated GDPR-blocked-contacts table (18 contacts with withdrawn consent) is maintained and excluded from all automated communications.

---

## Repo Structure

```
camile-thai-crm-analytics/
├── README.md
├── .gitignore
├── LICENSE
├── data/
│   ├── raw/                   # Source CSVs used to build the PostgreSQL data model
│   └── hubspot_exports/       # Data exported in HubSpot-import-ready format
├── scripts/
│   ├── import_to_postgres.py  # Loads raw CSVs → PostgreSQL → exports HubSpot CSVs
│   ├── requirements.txt
│   └── .env.example           # Copy to .env and fill in your own DB credentials
├── powerbi/
│   └── biba.pbix               # Power BI file — all three dashboards
├── presentation/
│   └── Camile_Thai_CRM_Presentation.pptx
└── screenshots/
    ├── workflow-refund-cancel-trigger-branch.png
    ├── workflow-refund-cancel-task-delay-reminder.png
    ├── workflow-complaint-trigger-priority-owner.png
    ├── workflow-complaint-rename-notify-end.png
    ├── dashboard-sales-revenue.png
    ├── dashboard-customer-crm.png
    ├── dashboard-operations-staff.png
    ├── email-refund-request-received.jpeg
    ├── email-refund-task-pending.jpeg
    ├── email-complaint-alert.jpeg
    ├── hubspot-workflow-list-overview.jpeg
    ├── hubspot-deals-pipeline-view.jpeg
    └── hubspot-contacts-view.jpeg
```

---

## Data

All datasets are **mock data**, generated with [Mockaroo](https://www.mockaroo.com) and custom Python scripts to reflect realistic restaurant-chain operations. No real customer, staff, or business data is used.

| Dataset | Records | Description |
|---|---|---|
| Branches | 20 | Branch details, revenue, seating, Google ratings |
| Customers | 500 | Contact info, loyalty tier, consent status |
| Complaints | 200 | Type, severity, SLA breach, CSAT, resolution time |
| GDPR Blocked Contacts | 18 | Consent-withdrawn contacts excluded from marketing |
| Orders | 2,000 | Channel, dish category, revenue, rating |
| Staff | 150 | Role, branch, pay, hours, nationality |

## Screenshots

### Workflows
| Refund/Cancel — Trigger & Branch | Refund/Cancel — Task, Delay & Reminder |
|---|---|
| ![](screenshots/workflow-refund-cancel-trigger-branch.png) | ![](screenshots/workflow-refund-cancel-task-delay-reminder.png) |

| Complaint — Trigger, Priority & Owner | Complaint — Rename, Notify & End |
|---|---|
| ![](screenshots/workflow-complaint-trigger-priority-owner.png) | ![](screenshots/workflow-complaint-rename-notify-end.png) |

### Dashboards
![Sales & Revenue](screenshots/dashboard-sales-revenue.png)
![Customer & CRM](screenshots/dashboard-customer-crm.png)
![Operations & Staff](screenshots/dashboard-operations-staff.png)

### Email Automation Evidence
| New Refund Request | Refund Task Pending (24h reminder) | New Complaint Alert |
|---|---|---|
| ![](screenshots/email-refund-request-received.jpeg) | ![](screenshots/email-refund-task-pending.jpeg) | ![](screenshots/email-complaint-alert.jpeg) |

---

## Running the Data Pipeline

```bash
cd scripts
pip install -r requirements.txt
cp .env.example .env   # then fill in your local PostgreSQL credentials
python import_to_postgres.py
```

This loads the six raw CSVs into a local PostgreSQL database and exports Contacts, Companies, and Deals in the format used for HubSpot import.

## Opening the Dashboards

Open `powerbi/biba.pbix` in Power BI Desktop. If prompted for a data source, point it at your local PostgreSQL instance populated by the script above, or the CSVs in `data/raw/` directly.

---

## Tech Stack

`HubSpot CRM` · `Power BI` · `PostgreSQL` · `Python (pandas, SQLAlchemy)` · `DAX`

## Key Results

- **34%** of complaints traced to "Wrong Order" — flagging an order-accuracy issue
- **14.5%** SLA breach rate identified and made measurable for the first time
- Full automation of request classification, prioritisation, ownership, and escalation — closing the accountability gap identified in the original process audit

## Future Work

- Live PostgreSQL → HubSpot API sync (replacing manual CSV import)
- Power BI DirectQuery for real-time dashboard refresh
- ML-based complaint routing based on historical type/severity/resolution data
- Automated customer-facing acknowledgement emails
- Dedicated SLA-breach early-warning workflow
- Loyalty-tier upgrade automation




