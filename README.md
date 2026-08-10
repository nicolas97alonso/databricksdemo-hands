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
> - **Reference Material:** If you get stuck, compare your approach with the teacher's official course repository located at `/Users/nicolasalonso/Desktop/code/projects/databricks-prep/da-prep/Databricks-Certified-Data-Engineer-Associate`.
> - You can check your progress against the checkpoint details in [AGENTS.md](file:///Users/nicolasalonso/Desktop/code/projects/databricks-prep/da-prep/nico-project/AGENTS.md).

> [!WARNING]
> **Course vs. Workspace Translation Guide (Please Read!)**
> The Chapter 1 videos use the legacy `hive_metastore` catalog to teach the basics. However, your modern Databricks workspace disables this in favor of **Unity Catalog** (which the exam actually tests!). 
>
> To follow the videos without getting blocked by permissions, use this mapping:
>
> **1. The Catalog (The top-level container)**
> - *Video says:* `USE CATALOG hive_metastore;`
> - *You type:* `USE CATALOG main;`
> - *Why:* `main` is just the default Unity Catalog box provided by Databricks. Treat it the same as `hive_metastore` for now.
>
> **2. External Locations (`dbfs:/mnt/...`)**
> - *Video says:* `CREATE SCHEMA ... LOCATION 'dbfs:/mnt/aura/raw/'`
> - *You type:* `CREATE SCHEMA aura_raw;` *(Just omit the LOCATION part entirely!)*
> - *Why:* In Unity Catalog, Databricks handles the storage path securely behind the scenes. Setting up custom locations requires cloud admin setups covered in Chapter 5.
>
> **3. Managed vs. External Tables**
> - *Video teaches:* Dropping a managed table deletes the files, but dropping an external table keeps the files.
> - *You do:* Understand this theory for the exam! However, for Chapter 1 exercises, you will just build **Managed Tables**. We will build Unity Catalog external tables in Chapter 5. You can still practice Time Travel, Optimization, and Vacuuming perfectly on managed tables.

---

### Task 1: Compute and Workspace Initialization
1. In your Databricks workspace, navigate to the **Compute** page.
2. Create a **Single Node** development cluster:
   - **Name:** `Aura-Dev-Cluster`
   - **Databricks Runtime (DBR):** Choose an LTS version (e.g., `13.3 LTS` or `14.3 LTS`).
   - **Worker Type / Node Type:** Select the smallest available instance (e.g., 4 cores).
   - **Auto Termination:** Configure it to terminate after **20 or 30 minutes** of inactivity to save costs.
3. In your Databricks Workspace (inside your Git Folder or personal workspace folder), create a folder named `01_lakehouse_platform/01_setup/`.
4. Inside that folder, create a new SQL notebook named `01_setup_and_schemas` (which will be exported as `01_setup_and_schemas.sql`). Attach it to your cluster.

### Task 2: Schema & Database Architecture
1. Open the `01_setup_and_schemas` notebook you created in Task 1.
2. Ensure you are using the active default catalog: `USE CATALOG main;`
3. Create a schema called `aura_raw` (omit the custom location clause). This schema will simulate the landing zone.
4. Create another schema called `aura_gold` (omit the custom location clause). This schema will hold tables for business-level reporting.
5. Verify the details of both schemas using `DESCRIBE SCHEMA EXTENDED aura_raw;`

### Task 3: Delta Table Creation & Exploration
Establish your primary tables.
1. In your Databricks Workspace, create a new folder named `01_lakehouse_platform/02_ingestion/`.
2. Inside that folder, create a new SQL notebook named `02_delta_table_basics` (which will be exported as `02_delta_table_basics.sql`).
3. Within your raw schema (`aura_raw`), create a managed table named `store_sales` containing fields for:
   - `sale_id` (Integer)
   - `store_id` (String)
   - `product_category` (String)
   - `quantity` (Integer)
   - `amount` (Double)
   - `sale_timestamp` (Timestamp)
4. Insert a mock batch of at least 8-10 sales records covering multiple product categories (`Electronics`, `Apparel`, `Home`) and different store locations (`Store_East`, `Store_West`, `Store_North`).
5. Describe the table details and location using `DESCRIBE EXTENDED`.

### Task 4: Table Mutations & History (Delta Lake Basics)
Understand how Delta handles transactions and record updates.
1. Open the `02_delta_table_basics` notebook.
2. Perform an update: Increase the price (amount) of all sales in the `Electronics` category by 5%.
3. Delete some test records (e.g., where sales amount is less than a certain threshold).
4. Query the table history (`DESCRIBE HISTORY`) to inspect the commit log. Note the versions, timestamps, and types of operations.

### Task 5: Time Travel & Table Restore
Practice recovering from accidental operations.
1. In your Databricks Workspace, create a new folder named `01_lakehouse_platform/03_time_travel/`.
2. Inside that folder, create a new SQL notebook named `03_time_travel_and_restore`.
3. Perform a query to view the `store_sales` table exactly as it was *before* you ran the updates and deletes in Task 4 (using time travel syntax like `VERSION AS OF` or `TIMESTAMP AS OF`).
4. Run a query that deletes *all* records in the `store_sales` table (simulating a catastrophic user error).
5. Verify the table is empty.
6. Use the `RESTORE` command to bring your table back to its fully populated state just before the deletion. Confirm that the data is completely recovered.

### Task 6: Compaction & Optimization
Organize files for maximum query performance.
1. In your Databricks Workspace, create a new folder named `01_lakehouse_platform/04_optimization/`.
2. Inside that folder, create a new SQL notebook named `04_optimization_and_cleanup`.
3. Clean up file fragmentation: Compact the table files using the `OPTIMIZE` command.
4. Co-locate data by clustering: Apply optimization on the `store_sales` table, using multi-dimensional clustering (`ZORDER`) on `product_category` and `store_id`.
5. Check the table detail and history metadata again to see the effect of the optimize step on the transaction logs.

### Task 7: Data Cleanup & Vacuuming
Manage storage costs by pruning stale historical files.
1. Open the `04_optimization_and_cleanup` notebook.
2. Run a `VACUUM` command on your sales table to clean up files that are no longer needed for current versions.
3. Change the Spark session retention checks to allow immediate deletion (`SET spark.databricks.delta.retentionDurationCheck.enabled = false;`) and run vacuum again specifying `RETAIN 0 HOURS`.
4. Test time travel to your very first version. What error do you get? Why?

### Task 8: Table Drops & Unity Catalog Security
1. In your Databricks Workspace, create a new folder named `01_lakehouse_platform/05_managed_vs_external/`.
2. Inside that folder, create a new SQL notebook named `05_managed_vs_external`.
3. In `aura_gold`, create a managed table named `sales_summary` by selecting aggregated metrics from `store_sales`.
4. Drop the `sales_summary` table. (Recall from the videos: dropping a managed table permanently deletes the underlying files).
5. (Optional): Try running `%fs ls '<path_from_describe_extended>'` on a Unity Catalog table. You will notice it throws a security error! This is because Unity Catalog natively protects managed data paths, unlike the old Hive Metastore.

### Task 9: Stored, Temporary, and Global Temporary Views
Analyze how to share and limit access to data using views.
1. In your Databricks Workspace, create a new folder named `01_lakehouse_platform/06_views/`.
2. Inside that folder, create a new SQL notebook named `06_views`.
3. Re-create the `store_sales` table and populate it.
4. Create a **Standard View** called `view_high_value_sales` displaying only sales transactions exceeding $100. Check if it appears when running `SHOW TABLES`.
5. Create a **Temporary View** called `temp_view_store_electronics` that queries only electronics sales.
6. Create a **Global Temporary View** called `global_temp_view_sales_summary` summarizing sales totals per store.
7. Query the temporary views.
8. Open a new notebook/session (or simulate one in Databricks) and verify which views are still visible and queries are valid. (Hint: Pay attention to the namespace prefix for the global temporary view).
9. Clean up your environment by dropping the views.


---

## 🏁 Checkpoint Verification
Once you complete all tasks in Part 1, update the checkpoint status in [AGENTS.md](file:///Users/nicolasalonso/Desktop/code/projects/databricks-prep/da-prep/nico-project/AGENTS.md) and let your AI assistant know. We will review your SQL scripts inside the `01_lakehouse_platform/` subdirectory, run check queries to verify correctness, and if everything looks solid, we will unlock **Part 2 (ELT with Spark SQL and Python)**!
