# LeafSync — BookLeaf Admin Dashboard & AI Automation Pipeline

**Author:** Shreyansh Dwivedi

LeafSync is a lightweight, highly secure operations dashboard and event-driven automation pipeline built for **BookLeaf Publishing**. Designed to streamline the royalty payout workflow, this architecture connects a secure frontend database directly to an asynchronous AI triage and email dispatch system. 

Instead of relying on heavy frontend plugins that freeze the UI while waiting for LLM processing, LeafSync utilizes a decoupled microservice approach. The Bubble frontend handles strict data access and fires a lightweight webhook, while an n8n backend worker asynchronously processes the LLM prompt via Google Gemini and routes the final communication through Gmail.

---

## Live Deployment & Access Links

- **Live Admin Dashboard (Frontend):** [https://leafsync-dashboard.bubbleapps.io/version-test/admin_dashboard](https://leafsync-dashboard.bubbleapps.io/version-test/admin_dashboard)
- **Bubble Editor (Database & Architecture View):** [https://bubble.io/page?id=leafsync-dashboard&tab=Design&name=admin_dashboard&type=page&subtab=General&elements=bTGQs](https://bubble.io/page?id=leafsync-dashboard&tab=Design&name=admin_dashboard&type=page&subtab=General&elements=bTGQs)
- **GitHub Repository (n8n Workflow Code & Docs):** [https://github.com/Shreyansh3108/Leafsync2](https://github.com/Shreyansh3108/Leafsync2)

> **Note on Evaluation:** The Bubble Editor has been set to "Everyone can view" so you can inspect the Data Privacy Rules and API Connector webhook configurations. 

---

## Core System Architecture

### 1. Frontend & Database (Bubble)
- **Data Modeling:** Built custom Data Types (`Author`, `Book`, `Payout`, `NotificationLog`) and Option Sets (`UserRole`, `PayoutStatus`) to manage relational catalog data.
- **Zero-Cost Data Seeding:** Successfully mapped and linked 10 canonical authors and 18 books using relational unique IDs to satisfy assignment parameters without requiring paid CSV-upload plugin tiers.
- **Admin Dashboard UI:** Developed a responsive repeating group interface that dynamically pulls author metadata (Name, Email, Pending Royalty) and injects it directly into action elements.

### 2. Strict Data Security & Privacy Rules
Security was prioritized at the database level rather than just hiding elements on the UI:
- **Role-Based Access Control (RBAC):** Implemented strict `Is Admin` privacy rules to ensure sensitive financial data is only accessible to authorized operations staff.
- **Author Isolation:** Applied `Is Self` rules to prevent horizontal data leakage between author accounts.
- **Public Lockdown:** Completely blanked out all fields under the `Everyone else` privacy role, ensuring that unauthorized API scraping or unauthenticated sessions return zero records. *(Note: Read access is temporarily enabled for "Everyone else" specifically for evaluator testing without requiring a login).*

### 3. Decoupled AI Pipeline (n8n + Gemini + Gmail)
- **Event-Driven Webhook Integration:** The Bubble `Generate & Send` button is wired to a configured API Connector `POST` request. This passes a structured JSON payload (`name`, `email`, `pending_royalty`) to a production n8n webhook.
- **Asynchronous AI Drafting:** n8n catches the webhook payload and routes it to **Google Gemini**. Gemini utilizes a predefined system prompt to merge the dynamic variables (author name, exact royalty amount) into a professional, empathetic email draft.
- **Automated Dispatch:** The generated text is passed directly into a Gmail node, which automatically dispatches the final email to the author's registered address, completing the cycle with zero human bottleneck.

---

## Repository Structure & Setup

This repository contains the exported backend configuration required to replicate the n8n automation pipeline.

```text
BookLeaf-Admin-Dashboard/
├── README.md                           # Architecture overview and live links
└── n8n-workflow/
    └── workflow-export.json            # Exported n8n node configuration
