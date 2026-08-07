# Lakehouse Retail Analytics: Databricks Hands-On Project

Welcome to your custom Databricks Data Engineering preparation project! This repository is designed to help you practice and solidify the concepts you learn in the Databricks Certified Data Engineer Associate course at your own pace.

Rather than copying and pasting course solutions, you will apply each chapter's concepts to design and build a real-world **E-Commerce & Retail Sales Analytics Lakehouse**.

---

## 📌 Project Overview: The Retail Analytics Domain
Imagine you are the Lead Data Engineer for a global retail chain, **"Aura Retail"**. Aura sells electronics, apparel, and home goods across multiple stores. The company wants to transition from a legacy database system to a modern Lakehouse architecture on Databricks to handle high-frequency sales transactions, store inventories, and customer behavior logs.

As you progress through the preparation course, you will build and refine Aura's data pipelines, optimize tables, manage incremental updates, orchestrate production runs, and configure data governance.

---

## 🛠️ Part 1: Databricks Lakehouse Platform (Chapter 1)
Your objective for this chapter is to set up your environment, establish database architecture, create initial tables, manipulate and travel through data history, perform performance optimization, and implement access control via views.

> [!IMPORTANT]
> **Rules of the Game:**
> - Implement these tasks using SQL and Spark Python in your Databricks Workspace.
> - Do not copy-paste code. Try to write queries from memory or reference the Databricks official documentation to learn syntax.
> - **Folder & Git Integration:** You can link this repository directly to your Databricks workspace using Databricks Git Folders (formerly Repos) to maintain file and folder structures directly inside Databricks.
> - You can check your progress against the checkpoint details in [AGENTS.md](file:///Users/nicolasalonso/Desktop/code/projects/databricks-prep/da-prep/nico-project/AGENTS.md).

> [!NOTE]
> **Metastore & Storage Disclaimer:**
> - **Legacy Hive Metastore:** Part 1 of this project is explicitly designed around the **Legacy Hive Metastore** (using the `hive_metastore` catalog). We do this so you can practice working with custom storage locations (`LOCATION` parameter) and standard DBFS paths without needing cloud credential setup. We will transition to modern **Unity Catalog (UC)** governance in **Part 5**.
> - **Managed Tables Production Warning:** In a legacy Hive Metastore setup, storing managed tables directly in the default hive warehouse (DBFS root) is **not recommended** for production environments because it lacks granular security controls and dropping a table deletes all data permanently. We practice it here strictly to observe table behaviors. In Unity Catalog, managed tables are the recommended best practice because they use secure, catalog-defined managed storage.

---

### Task 1: Compute and Workspace Initialization
1. In your Databricks workspace, navigate to the **Compute** page.
2. Create a **Single Node** development cluster:
   - **Name:** `Aura-Dev-Cluster`
   - **Databricks Runtime (DBR):** Choose an LTS version (e.g., `13.3 LTS` or `14.3 LTS`).
   - **Worker Type / Node Type:** Select the smallest available instance (e.g., 4 cores).
   - **Auto Termination:** Configure it to terminate after **20 or 30 minutes** of inactivity to save costs.
3. In your Databricks Workspace (inside your Git Folder or personal workspace folder), create a folder named `practice/01_lakehouse_platform/01_setup/`.
4. Inside that folder, create a new SQL notebook named `01_setup_and_schemas` (which will be exported as `01_setup_and_schemas.sql`). Attach it to your cluster.

### Task 2: Schema & Database Architecture (Legacy Hive Metastore)
Define where and how your retail data is stored by setting up isolated schemas (databases) in the legacy Hive Metastore.
1. Open the `01_setup_and_schemas` notebook you created in Task 1.
2. Create a schema called `hive_metastore.aura_raw_external` with a custom location pointing to DBFS (`dbfs:/mnt/aura/raw/`). This schema will simulate the landing zone for external data files.
3. Create another schema called `hive_metastore.aura_gold_managed` without specifying a custom location. This schema will hold default managed tables for business-level reporting.
4. Verify the details of both schemas using Spark SQL commands. Confirm which one maps to the default hive warehouse path and which one points to your custom DBFS location.

### Task 3: Delta Table Creation & Exploration
Establish your primary tables and examine their under-the-hood physical structure.
1. In your Databricks Workspace, create a new folder named `practice/01_lakehouse_platform/02_ingestion/`.
2. Inside that folder, create a new SQL notebook named `02_delta_table_basics` (which will be exported as `02_delta_table_basics.sql`).
3. Within your raw schema (`hive_metastore.aura_raw_external`), create a table named `store_sales` containing fields for:
   - `sale_id` (Integer)
   - `store_id` (String)
   - `product_category` (String)
   - `quantity` (Integer)
   - `amount` (Double)
   - `sale_timestamp` (Timestamp)
4. Insert a mock batch of at least 8-10 sales records covering multiple product categories (`Electronics`, `Apparel`, `Home`) and different store locations (`Store_East`, `Store_West`, `Store_North`).
5. Describe the table details and location.
6. Run filesystem utilities (`%fs` or `dbutils.fs.ls`) to look inside the physical directory backing this table. Find the parquet data files and look inside the `_delta_log` directory. Identify the first JSON commit file.

### Task 4: Table Mutations & History (Delta Lake Basics)
Understand how Delta handles transactions and record updates.
1. Open the `02_delta_table_basics` notebook inside `practice/01_lakehouse_platform/02_ingestion/` that you created in Task 3.
2. Perform an update: Increase the price (amount) of all sales in the `Electronics` category by 5%.
3. Delete some test records (e.g., where sales amount is less than a certain threshold).
4. Query the table history to inspect the commit log. Note the versions, timestamps, and types of operations (e.g., `WRITE`, `UPDATE`, `DELETE`) recorded in the history.
5. Read the corresponding JSON transaction file in the `_delta_log` using `%fs head` to see how Delta specifies which files were added and which were removed.

### Task 5: Time Travel & Table Restore
Practice recovering from accidental operations.
1. In your Databricks Workspace, create a new folder named `practice/01_lakehouse_platform/03_time_travel/`.
2. Inside that folder, create a new SQL notebook named `03_time_travel_and_restore` (which will be exported as `03_time_travel_and_restore.sql`).
3. Perform a query to view the `store_sales` table exactly as it was *before* you ran the updates and deletes in Task 4 (using time travel syntax).
4. Run a query that deletes *all* records in the `store_sales` table (simulating a catastrophic user error).
5. Verify the table is empty.
6. Use the restore command to bring your table back to its fully populated state just before the deletion. Confirm that the data is completely recovered and examine the history again.

### Task 6: Compaction & Optimization
Organize files for maximum query performance.
1. In your Databricks Workspace, create a new folder named `practice/01_lakehouse_platform/04_optimization/`.
2. Inside that folder, create a new SQL notebook named `04_optimization_and_cleanup` (which will be exported as `04_optimization_and_cleanup.sql`).
3. Clean up file fragmentation: Compact the table files using Delta's built-in optimization.
4. Co-locate data by clustering: Apply optimization on the `store_sales` table, using multi-dimensional clustering (`ZORDER`) on `product_category` and `store_id`.
5. Check the table detail and history metadata again to see the effect of the optimize step on the number of files and transaction logs.

### Task 7: Data Cleanup & Vacuuming
Manage storage costs by pruning stale historical files.
1. Open the `04_optimization_and_cleanup` notebook inside `practice/01_lakehouse_platform/04_optimization/` that you created in Task 6.
2. Run a vacuum command on your sales table to clean up files that are no longer needed for current versions.
3. Change the Spark session retention checks to allow immediate deletion (`RETAIN 0 HOURS`) and run vacuum again to see it in action.
4. Test time travel to your very first version. What error do you get? Why?

### Task 8: Managed vs. External Tables
Observe what happens when you drop tables.
> [!WARNING]
> **Production Disclaimer:** Remember that in a legacy Hive Metastore setup, using managed tables on the DBFS root is not recommended for production. Dropping a managed table deletes both its metadata and its underlying physical data files. In Unity Catalog, managed tables are the standard because they are written to a managed, secure storage area rather than a shared root directory.
1. In your Databricks Workspace, create a new folder named `practice/01_lakehouse_platform/05_managed_vs_external/`.
2. Inside that folder, create a new SQL notebook named `05_managed_vs_external` (which will be exported as `05_managed_vs_external.sql`).
3. In `hive_metastore.aura_gold_managed` (the default database/schema), create a managed table named `sales_summary` by selecting aggregated metrics from `store_sales`.
4. Compare the storage paths and metadata of `store_sales` (external) vs `sales_summary` (managed) using `DESCRIBE EXTENDED`.
5. Drop both tables.
6. Use `%fs ls` to inspect both backing directories. What happened to the physical parquet files for the managed table? What happened to the files for the external table?

### Task 9: Stored, Temporary, and Global Temporary Views
Analyze how to share and limit access to data using views.
1. In your Databricks Workspace, create a new folder named `practice/01_lakehouse_platform/06_views/`.
2. Inside that folder, create a new SQL notebook named `06_views` (which will be exported as `06_views.sql`).
3. Re-create the `store_sales` table and populate it.
4. Create a **Standard View** called `view_high_value_sales` displaying only sales transactions exceeding $100. Check if it appears when running `SHOW TABLES`.
5. Create a **Temporary View** called `temp_view_store_electronics` that queries only electronics sales.
6. Create a **Global Temporary View** called `global_temp_view_sales_summary` summarizing sales totals per store.
7. Query the temporary views.
8. Open a new notebook/session (or simulate one in Databricks) and verify which views are still visible and queries are valid. (Hint: Pay attention to the namespace prefix for the global temporary view).
9. Clean up your environment by dropping the views.


---

## 🏁 Checkpoint Verification
Once you complete all tasks in Part 1, update the checkpoint status in [AGENTS.md](file:///Users/nicolasalonso/Desktop/code/projects/databricks-prep/da-prep/nico-project/AGENTS.md) and let your AI assistant know. We will review your SQL scripts inside the `practice/01_lakehouse_platform/` subdirectory, run check queries to verify correctness, and if everything looks solid, we will unlock **Part 2 (ELT with Spark SQL and Python)**!
