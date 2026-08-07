# Project Progress & Collaboration Log (AGENTS.md)

This file tracks the alignment and milestones between you (the student) and your AI pair programming assistant. We use this log to enforce a step-by-step learning loop: we only unlock the next chapter's tasks once the current chapter's hands-on project tasks are fully implemented and verified.

---

## 🗺️ Project Roadmap & Checkpoints

| Phase | Course Chapter | Description | Status |
| :--- | :--- | :--- | :--- |
| **Part 1** | **1. Databricks Lakehouse Platform** | Cluster setup, external/managed schemas, Delta table creation, history, time travel, restore, optimization (ZORDER), vacuuming, and views. | **⏳ IN PROGRESS** |
| **Part 2** | **2. ELT with Spark SQL and Python** | Querying files, writing advanced transformations, writing to tables, and cleaning/deduplicating data. | 🔒 Locked |
| **Part 3** | **3. Incremental Data Processing** | Structured Streaming, Auto Loader, and Delta Live Tables (DLT). | 🔒 Locked |
| **Part 4** | **4. Production Pipelines** | Task orchestration with Databricks Workflows, Jobs, and cluster policies. | 🔒 Locked |
| **Part 5** | **5. Data Governance** | Unity Catalog, database privileges, table owners, and secure data access. | 🔒 Locked |

---

## 🎯 Part 1 Checkpoint: Databricks Lakehouse Platform

### Task Checklist for User
- [ ] **Task 1:** Create cluster `Aura-Dev-Cluster` and setup the `01_lakehouse_platform` folder structure.
- [ ] **Task 2:** Create schemas `hive_metastore.aura_raw_external` (external location) and `hive_metastore.aura_gold_managed` (managed).
- [ ] **Task 3:** Create external table `store_sales` inside raw schema, insert mock data, and inspect the physical parquet files/JSON logs in DBFS.
- [ ] **Task 4:** Perform table updates/deletes, check `DESCRIBE HISTORY`, and read the `_delta_log` JSON files.
- [ ] **Task 5:** Query older versions with time travel, drop/delete all records, and successfully restore the table.
- [ ] **Task 6:** Run `OPTIMIZE` and `ZORDER BY (product_category, store_id)` on `store_sales`.
- [ ] **Task 7:** Configure immediate retention checks and `VACUUM` the table. Verify old historical travel is no longer possible.
- [ ] **Task 8:** Create `sales_summary` managed table in `hive_metastore.aura_gold_managed`. Compare paths, drop both tables, and verify which files are deleted from storage.
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
2. Review the SQL query structures (checking for correct use of `LOCATION`, `RESTORE`, `OPTIMIZE ZORDER BY`, `VACUUM RETAIN 0 HOURS`, `GLOBAL TEMP` namespace prefix, etc.).
3. Verify that your implementation addresses all core concepts without syntax errors.
4. Update this checkpoint status to **✅ COMPLETED**, unlock Part 2, and populate the README/AGENTS.md with the next set of tasks!
