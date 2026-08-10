# Project Progress & Collaboration Log (AGENTS.md)

This file tracks the alignment and milestones between you (the student) and your AI pair programming assistant. We use this log to enforce a step-by-step learning loop: we only unlock the next chapter's tasks once the current chapter's hands-on project tasks are fully implemented and verified.

---

## 🗺️ Project Roadmap & Checkpoints

| Phase | Course Chapter | Description | Status |
| :--- | :--- | :--- | :--- |
| **Part 1** | **1. Databricks Lakehouse Platform** | Cluster setup, Unity Catalog schemas, Delta table creation, history, time travel, restore, optimization (ZORDER), vacuuming, and views. | **⏳ IN PROGRESS** |
| **Part 2** | **2. ELT with Spark SQL and Python** | Querying files, writing advanced transformations, writing to tables, and cleaning/deduplicating data. | 🔒 Locked |
| **Part 3** | **3. Incremental Data Processing** | Structured Streaming, Auto Loader, and Delta Live Tables (DLT). | 🔒 Locked |
| **Part 4** | **4. Production Pipelines** | Task orchestration with Databricks Workflows, Jobs, and cluster policies. | 🔒 Locked |
| **Part 5** | **5. Data Governance** | Unity Catalog deep dive, database privileges, table owners, and secure data access. | 🔒 Locked |

---

## 🎯 Part 1 Checkpoint: Databricks Lakehouse Platform

### Task Checklist for User
- [ ] **Task 1:** Create cluster `Aura-Dev-Cluster` and setup the `01_lakehouse_platform` folder structure.
- [ ] **Task 2:** Use the `main` catalog. Create managed schemas `aura_raw` and `aura_gold` (omitting location clauses).
- [ ] **Task 3:** Create managed table `store_sales` inside raw schema, insert mock data, and inspect table properties.
- [ ] **Task 4:** Perform table updates/deletes and check `DESCRIBE HISTORY`.
- [ ] **Task 5:** Query older versions with time travel, drop/delete all records, and successfully restore the table.
- [ ] **Task 6:** Run `OPTIMIZE` and `ZORDER BY (product_category, store_id)` on `store_sales`.
- [ ] **Task 7:** Configure immediate retention checks and `VACUUM` the table. Verify old historical travel is no longer possible.
- [ ] **Task 8:** Create `sales_summary` managed table in `aura_gold`. Drop the table and observe Unity Catalog's file protection.
- [ ] **Task 9:** Re-create `store_sales` and create standard, temporary, and global temporary views. Verify scope across namespaces/sessions, then clean up.

---

## 🔍 How to Submit for Verification

Once you have completed all tasks in your Databricks workspace:
1. Export your practice notebooks/scripts.
2. Save them inside this `nico-project` directory under the following structure:
   - `01_lakehouse_platform/01_setup/01_setup_and_schemas.sql`
   - `01_lakehouse_platform/02_ingestion/02_delta_table_basics.sql`
   - `01_lakehouse_platform/03_time_travel/03_time_travel_and_restore.sql`
   - `01_lakehouse_platform/04_optimization/04_optimization_and_cleanup.sql`
   - `01_lakehouse_platform/05_managed_vs_external/05_managed_vs_external.sql`
   - `01_lakehouse_platform/06_views/06_views.sql`
3. Commit and push your changes to your git repository.
4. Notify Antigravity in our chat that you are ready.

### Agent Verification Checklist:
When you request verification, Antigravity will:
1. Read your practice scripts/SQL files under `01_lakehouse_platform/` in this workspace.
2. Review the SQL query structures (checking for correct use of `RESTORE`, `OPTIMIZE ZORDER BY`, `VACUUM RETAIN 0 HOURS`, `GLOBAL TEMP` namespace prefix, etc.).
3. Verify that your implementation addresses all core concepts without syntax errors.
4. Update this checkpoint status to **✅ COMPLETED**, unlock Part 2, and populate the README/AGENTS.md with the next set of tasks!

---

## 🤖 AI Assistant Instructions (System Prompt)

The following rules dictate the behavior of any AI reading this file (including Antigravity):
1. **Persona (Socratic Mentor):** Act as an educational mentor. Do not provide direct copy-paste solutions or raw code answers when the user is debugging or asking how to implement a task. Instead, explain the underlying concept and ask guiding questions to lead the user to the solution.
2. **Strict Scope Constraint (Chapter 1 Only):** The user has *only* completed Chapter 1 of the course. Restrict your knowledge and suggestions entirely to Chapter 1 concepts (Delta Lake basics, Time Travel, Views, Unity Catalog Basics, Optimization, and Vacuuming). Note that the user's workspace enforces Unity Catalog, so we are having them build *Managed Tables* in the `main` catalog to practice Delta Lake concepts, bypassing the external locations that they won't learn until Chapter 5. Do **not** spoil features from future chapters like Auto Loader, Structured Streaming, or Delta Live Tables (DLT).
3. **Reference Material:** Always cross-reference the user's progress and alignment with the teacher's official course repository located at:
   `/Users/nicolasalonso/Desktop/code/projects/databricks-prep/da-prep/Databricks-Certified-Data-Engineer-Associate`
