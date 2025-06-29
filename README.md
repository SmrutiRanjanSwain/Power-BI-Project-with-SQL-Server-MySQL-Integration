# Power-BI-Project-with-SQL-Server-MySQL-Integration
In this project focused on working with data from Microsoft SQL Server and MySQL databases in a Power BI environment. The main goals are:
1.Understanding Data Sources:
SQL Server and MySQL are used as back-end databases.
2.Real-time scenario: shifting a Power BI report from a test environment to a production environment.
Another scenario includes transitioning data sources from SQL Server to MySQL, considering differences in SQL syntax.
3.Data Overview:
Demand and Availability Table:
Columns: Order Date, Product ID, Availability, and Demand.
Highlights supply-demand matching issues like shortages.
Products Table:
Contains Product ID, Product Name, and Unit Price.
KPI Requirements for Power BI Report:
Page 1:
Average Demand Per Day
Average Availability Per Day
Total Supply Shortage
Page 2:
Total Loss
Total Profit
Average Daily Loss
4.Tools Needed:
SQL Server Management Studio (SSMS)
MySQL Workbench
====================================================================================================

Creating a New Database:
A new database named test_env (Test Environment) is created using SQL commands.
The USE command is then executed to start working within this database.
Importing Data Using the Import Wizard:
Import Flat File Wizard is used to load data into SQL Server:
Test Environment Inventory Dataset is imported first.
Products Table is imported next.
The import is completed successfully for both tables.
Verifying Imported Data:
The database is refreshed, and both tables (dbo.test_environment_inventory_dataset and dbo.products) are confirmed under the Tables section.
SQL queries like SELECT * FROM dbo.products and SELECT * FROM dbo.test_environment_inventory_dataset are used to verify the data.
Observations:
20 products are available in the products table.
In the inventory table, the date format appears altered after import, but the issue will be handled later in Power BI.
============================================================================================

1.Data Exploration in SQL Server
Products Table:
Contains 20 distinct product IDs, corresponding product names, and unit prices.
Data is clean with no nulls or missing values.
Test Environment Inventory Dataset:
Contains 99 records with columns: Order Date, Product ID, Availability, and Demand.
No nulls or blanks; 93 distinct order dates and 22 unique availability values were found.
A few entries have zero availability, indicating unmet demand (shortage).
Distinct Value Checks:
The instructor demonstrates how to use SELECT DISTINCT to analyze unique values for columns like Order Date, Availability, and Demand.

2. Data Joining Using SQL (Left Join)
Goal: Combine both tables so the resulting data can be directly used in Power BI.
A LEFT JOIN is applied between:
Test Environment Inventory Dataset (alias A)
Products table (alias B)
Join condition: A.Product_ID = B.Product_ID
A refined query is created to select only required columns, avoiding duplication of Product_ID.

3. Creating a New Combined Table
Instead of directly using the join query in Power BI, the instructor:
Uses the SQL statement SELECT ... INTO Combine_table to create a new table called Combine_table.
This table stores the combined result of the joined data from the two original tables.
Query confirms that 99 rows were inserted successfully.
The new table is now ready for import into Power BI for visualization and KPI reporting.
===================================================================================================

1. Connecting Power BI to SQL Server
Power BI Desktop is opened.
Previous SQL Server connections are cleared from Data Source Settings to avoid conflicts.
A new SQL Server connection is made using:
Server name from SSMS (e.g., "Lenovo")
Database: test_env
Query: SELECT * FROM new_table

2. Transforming and Preparing Data
Power Query Editor is used to inspect and fix column data types:
Order Date: Converted from Date/Time to Date.
Product ID, Availability, Demand: Set to Whole Number.
Unit Price: Kept as Decimal.
Product Name: Set as Text.
Changes are applied and data is loaded into the model (99 records).

3. Renaming & Organizing
The loaded query (default name Query1) is renamed to Demand/Availability Data for clarity.
The model is now ready to be used for visual reporting.

4. Report Page Design and Custom Backgrounds
Background images (designed in Canva) are added to each report page:
Page 1 KPIs:
Average Demand per Day
Average Availability per Day
Total Supply Shortage
Page 2 KPIs:
Total Profit
Total Loss
Average Daily Loss
Steps followed:
Insert image under Canvas Background.
Set image fit to Fit and transparency to 0%.
Change wallpaper color to black.
These visual templates help maintain consistency and aesthetic design across report pages.

5. Saving the Report
The Power BI report is saved as:
📁 prod_power_bi.pbix
File path: Projects > Power BI
Although it's based on test environment data, it is named for the production version (as the report will eventually shift to production).
===============================================================================================================================================

1. Setting Up for Measures
A new table named Measures Table was created using Enter Data (blank table used to store DAX measures).

2. Creating DAX Measures
Basic Measures:
Total Number of Days:
Total Number of Days = DISTINCTCOUNT('Demand/Availability Data'[Order Date])
Total Demand:
Total Demand = SUM('Demand/Availability Data'[Demand])
Total Availability:
Total Availability = SUM('Demand/Availability Data'[Availability])
KPI Measures:
Average Demand per Day:
Average Demand per Day = DIVIDE([Total Demand], [Total Number of Days])
Average Availability per Day:
Average Availability per Day = DIVIDE([Total Availability], [Total Number of Days])
Total Supply Shortage:
Total Supply Shortage = [Total Demand] - [Total Availability]

3. Visualizing KPIs with Card Visuals
Three card visuals were added to the report layout (on Page 1) to display:
Average Demand per Day
Average Availability per Day
Total Supply Shortage
Card formatting:
Category labels disabled
Font: bold, italic, white
Positioned according to background design (from Canva)

4. Enabling Dynamic Filtering
To allow user interaction:
Filters pane enabled via the View tab.
Fields added to "Filters on this page":
Product Name
Order Date
Users can now filter KPIs by product or date.
Filters Pane Formatting:
Background color: black
Text color: white
Input box and card background: black
Ensures consistency with the dark report theme.
=============================================================================================================================

🎯 KPIs Covered (Page 2)
Total Profit
Total Loss
Average Daily Loss

1. Create a New Calculated Column
A new column was created in the original data table:
Loss/Profit = 'Demand/Availability Data'[Availability] - 'Demand/Availability Data'[Demand]
Positive result → Profit

Negative result → Loss

2. Create DAX Measures in Measures Table
✅ Total Profit
Total Profit = 
SUMX(
    FILTER('Demand/Availability Data', 'Demand/Availability Data'[Loss/Profit] > 0),
    'Demand/Availability Data'[Loss/Profit] * 'Demand/Availability Data'[Unit Price]
)
✅ Total Loss
Total Loss = 
SUMX(
    FILTER('Demand/Availability Data', 'Demand/Availability Data'[Loss/Profit] < 0),
    'Demand/Availability Data'[Loss/Profit] * 'Demand/Availability Data'[Unit Price]
)
✅ Average Loss Per Day
Average Loss Per Day = 
DIVIDE([Total Loss], [Total Number of Days])
=======================================================================================================================

Finalizing Test Report & Setting Up Production Environment
🎯 Objective
Final polish of visuals on Page 2
Begin setup of Production environment in SQL Server
Understand how to transition report from test to production efficiently

🛠️ Fixes in Page 2 Report
🔁 Converting Negative "Loss" Values to Positive
Loss values (e.g., -300) were shown along with the word "Loss" in the UI, causing redundancy.

Fix: Multiplied the following measures by -1 to make them positive:

Total Loss = 
SUMX(
    FILTER('Demand/Availability Data', 'Demand/Availability Data'[Loss/Profit] < 0),
    'Demand/Availability Data'[Loss/Profit] * 'Demand/Availability Data'[Unit Price]
) * -1
Average Loss Per Day


🧱 Setting Up the Production Database
✅ Steps in SQL Server Management Studio (SSMS)
Create Production DB:
CREATE DATABASE Prod;
USE Prod;
Import Production Datasets:

Used Import Flat File Wizard:
✅ Prod Environment Inventory Dataset
✅ Products Table

Check Data:
SELECT * FROM dbo.[Prod Environment Inventory Dataset];
=====================================================================================================================

Shifting Power BI Report from Test to Production Environment
🔄 Data Loaded into Production (SQL Server)
✅ Verified:
Data from dbo.Prod Environment Inventory Dataset contains 1043 records
Test environment only had 99 records

📅 Column Quality Check:
✅ OrderDate: No nulls or blanks

✅ ProductID: 22 unique values found in fact table, but only 20 in Products table

⚠️ Data Quality Issue Identified
Product IDs 21 and 22 existed in the fact table but not in the product dimension table

✅ Temporary Fix Implemented via SQL UPDATE

UPDATE dbo.[Prod Environment Inventory Dataset]
SET ProductID = 7
WHERE ProductID = 21;

UPDATE dbo.[Prod Environment Inventory Dataset]
SET ProductID = 11
WHERE ProductID = 22;
After fix:

SELECT DISTINCT ProductID shows values from 1 to 20 only ✅

🧼 Data Profiling Checks
Checked columns for null or blank values:
✅ Availability — 121 distinct values, no nulls/blanks
✅ Demand — 121 distinct values, no nulls/blanks

Used SQL:
SELECT DISTINCT [ColumnName] FROM [TableName];
-- or
SELECT * FROM [TableName] WHERE [ColumnName] IS NULL OR [ColumnName] = '';
🛠️ Recreated Cleaned Production Table
Copied logic from test environment (using LEFT JOIN with product table) to create:
SELECT *
INTO [New Table]
FROM dbo.[Prod Environment Inventory Dataset] x
LEFT JOIN dbo.Products y
ON x.ProductID = y.ProductID;
✅ Created New Table in Prod environment
📌 Naming consistency maintained with test environment to avoid DAX breakage

📊 Shifting Power BI Report Source: Test ➝ Production
Steps:
Open Power BI Report

Select old test environment DB

Update to: prod

New data (1043 records) loaded ✔️
KPIs on both Page 1 and Page 2 updated automatically with new DAX logic

🔁 Switched Back (Production ➝ Test ➝ Production)
Verified smooth back-and-forth switching
DAX measures recalculated automatically
===============================================================================================================

1: Setup and Import in MySQL Workbench
Created a new schema prod.
Imported Excel data (Production Environment Inventory Dataset) using Data Import Wizard.
Refreshed schemas and validated table import.

2: Update Statements (From SQL Server to MySQL)
Converted SQL Server update statements to MySQL syntax.
Adjusted column names (e.g., Product ID with spaces).
Handled "safe update mode" in MySQL Workbench Preferences.

3: Importing the Products Table
Imported second dataset (Products Table) into prod schema.
Refreshed schema and previewed data.

4: Creating New Table via Left Join in MySQL
Rewrote SQL Server's SELECT INTO logic using MySQL’s CREATE TABLE new_table AS SELECT...JOIN.
Aliased all columns to match SQL Server naming convention.
Executed and created new_table with consistent schema for Power BI.

5: Creating Workspaces in Power BI Service
Created 2 workspaces in Power BI:
SQL Server Data Source
MySQL Database Data Source
Published SQL Server-based report to workspace #1 as backup.

6: Connect Power BI Desktop to MySQL
Chose Get Data > MySQL and entered connection details (localhost, prod).
Used query: SELECT * FROM new_table
Successfully imported data into Power BI as Query1.

7: Transitioning Report Source
Used Advanced Editor in Power Query Editor.
Copied connection string from MySQL query.
Replaced the M-code in SQL Server query.
Applied changes, removed temporary MySQL table from model.

8: Validation
Compared reports:
SQL Server version (Power BI Service)
MySQL version (Power BI Desktop + Service)
All KPIs matched:
Average Demand per Day
Total Profit
Average Daily Loss, etc.
Confirmed successful transition.

9: Final Publishing to MySQL Workspace

========================================================================================================

📌 Conclusion

This project demonstrates how to successfully Integration a Power BI report's backend from Microsoft SQL Server to MySQL while preserving the report's business logic, visuals, and DAX measures.
