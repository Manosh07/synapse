# Lab - Incrementally load data from multiple tables

## Overview

### Here are the important steps to create this solution:

## 1. Select the watermark column.

Select one column for each table in the source data store, which can be used to identify the new or updated records for every run. Normally, the data in this selected column (for example, last_modify_time or ID) keeps increasing when rows are created or updated. The maximum value in this column is used as a watermark.

### 2. Prepare a data store to store the watermark value.

In this tutorial, you store the watermark value in a SQL database.

### 3. Create a pipeline with the following activities:

a. Create a ForEach activity that iterates through a list of source table names that is passed as a parameter to the pipeline. For each source table, it invokes the following activities to perform delta loading for that table.

b. Create two lookup activities. Use the first Lookup activity to retrieve the last watermark value. Use the second Lookup activity to retrieve the new watermark value. These watermark values are passed to the Copy activity.

c. Create a Copy activity that copies rows from the source data store with the value of the watermark column greater than the old watermark value and less than the new watermark value. Then, it copies the delta data from the source data store to Azure Blob storage as a new file.

d. Create a StoredProcedure activity that updates the watermark value for the pipeline that runs next time.

### Here is the high-level solution diagram:
![Select Standard](img/high-level-solution-diagram.png)

Reference: [Micrsoft Pages](https://docs.microsoft.com/en-us/azure/data-factory/tutorial-incremental-copy-multiple-tables-portal)

In this excercise you will be peforming following task:
- Task 1: Create and configure a SQL Database instance.
- Task 2: Create tables and procedure
- Task 3: Create a pipeline

## Task 1 : Create and configure a SQL Database instance.

1. In the Azure portal, navigate to the **+ Create a resource** blade.

2. In the New screen, click the **Search services and marketplace** text box, and type the word **SQL Database**. Click **SQL Database** in the list that appears.

3. In the **SQL Database** screen, click **Create**.

4. From the **Create SQL Database** screen, create an Azure SQL Database with the following settings:

    - In the Project details section, type in the following information
    
        - **Subscription**: the name of the subscription you are using in this lab

        - **Resource group**: **synapse-xxxxx**, 'xxxxx' is some unique suffix
    
    - In the Database details section, type in the following information
    
        - Database name: type in **AdventureworksLT**
     
        - Server: Create a new server by clicking **Create new** with the following settings and click on **OK**:
            - **Server name**: **sqlservicexxxxx**, where **xxxxx** are your unique suffix
            - **Location**: choose a **location** near as your **data-engineering-synapse-xxxxx** resource group.
            - **Server admin login**: **sqladmin**
            - **Password**: **Pa55w.rd**
            - **Confirm Password**: **Pa55w.rd**
            - click on **OK**
         
         - Want to use SQL elastic pool?:  **No**
         
         - Workload environment: **Development**
        
        - **Cmpute + Storage**: Select Standard Service Tier
        
        Step 1:
        
        ![Select Standard](img/SQL_DB_COMP_STORAGE.png)
        
        Step 2:
        
        ![Creating a server instance in the Azure portal](img/SQL_DB_SERV_TIER.png)
        
        Step 3:
        
        ![Creating a server instance in the Azure portal](img/SQL_DB_SERV_TIER_DTU.png)

	- In the **Networking tab**

		![Click on Compute + Storage configure option](img/SQL_DB_NW.png)
	
    - Click on the  **Additional setting** tab, click **Sample** . The AdventureworksLT sample database is selected automatically. 
    
		![Click on Compute + Storage configure option](img/SQL_DB_ADD_SETTING.png)

5. In the **Create SQL Database** blade, click **Review + create**.


6. After the validation of the **Create SQL Database*** blade, click **Create**.

   > **Note**: The provision will takes approximately 4 minutes.

> **Result**: After you completed this task, you have an Azure SQL Database instance


## Task 2: Create table and procedures

### Create source tables in your SQL database

1. Open SQL Database, and connect to your SQL Server database.
  
  ![Open SQL Database](img/SQL_DB_QUERY_EDITOR_OPEN.png)
   
2. Run the following SQL command against your database to create tables named customer_table and project_table:.
  
  ![Open SQL Database](img/SQL_DB_QUERY_EDITOR_RUN_QUERY.png)
  
  ```
create table customer_table
(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

create table project_table
(
    Project varchar(255),
    Creationtime datetime
);

INSERT INTO customer_table
(PersonID, Name, LastModifytime)
VALUES
(1, 'John','9/1/2017 12:56:00 AM'),
(2, 'Mike','9/2/2017 5:23:00 AM'),
(3, 'Alice','9/3/2017 2:36:00 AM'),
(4, 'Andy','9/4/2017 3:21:00 AM'),
(5, 'Anny','9/5/2017 8:06:00 AM');

INSERT INTO project_table
(Project, Creationtime)
VALUES
('project1','1/1/2015 0:00:00 AM'),
('project2','2/2/2016 1:23:00 AM'),
('project3','3/4/2017 5:16:00 AM');
  ```

### Create destination tables in synpase dedicated sql pool

1. In develop hub create a sql script.

   ![Run Query](img/SYNPASE_POOL_SQL_SCRIPT.png)
   
2. Run the following SQL command against your Dedicated SQL Pool to create tables named customer_table and project_table:.
   
   ![Run Query](img/SYNPASE_POOL_EXECUTE_SQL_SCRIPT.png)
   
```
create table customer_table
(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);
GO

create table project_table
(
    Project varchar(255),
    Creationtime datetime
);
GO

create table table_list
(
    TableName varchar(255),
    WaterMarkColumn varchar(255),
	UpsertColumn varchar(255)
);
GO

INSERT INTO table_list (TableName, WaterMarkColumn, UpsertColumn) VALUES ('customer_table', 'LastModifytime', 'PersonID')
GO

INSERT INTO table_list (TableName, WaterMarkColumn, UpsertColumn) VALUES ('project_table','Creationtime', 'Project')
GO
```

### Create a stored procedure in your database

1. Run the following command to create a stored procedure in your Dedicated SQL Pool. This stored procedure updates the watermark value after every pipeline run
```
create table watermarktable
(

    TableName varchar(255),
    WatermarkValue datetime
);
```
3. Insert initial watermark values for both source tables into the watermark table.
```
INSERT INTO watermarktable VALUES ('customer_table','1/1/2010 12:00:00 AM')
INSERT INTO watermarktable VALUES ('project_table','1/1/2010 12:00:00 AM')
```

### Create a stored procedure in your Dedicated SQL Pool

Run the following command to create a stored procedure in your Dedicated SQL Pool. This stored procedure updates the watermark value after every pipeline run.
```
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

UPDATE watermarktable
SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END
```

## Task 3 : Create and configure a pipeline.

### Create SQL Database linked service

1. Under manage hub -> Linked Service -> + New -> Search for SQL Database -> Select SQL Database -> Click Continue

   ![SYNPASE_POOL_SQL_DB_Linked_SERVICE](img/SYNPASE_POOL_SQL_DB_Linked_SERVICE.png)
   
2. Provide the required configuration details as mentioned

   ![SYNPASE_POOL_SQL_DB_Linked_SERVICE_CONFIG_1](img/SYNPASE_POOL_SQL_DB_Linked_SERVICE_1.png)
   ![SYNPASE_POOL_SQL_DB_Linked_SERVICE_CONFIG_2](img/SYNPASE_POOL_SQL_DB_Linked_SERVICE_2.png)
   

### Create a Pipeline

1. In the left pane, click + (plus), and click Pipeline.

2. In the General panel under Properties, specify IncrementalCopyPipeline for Name. Then collapse the panel by clicking the Properties icon in the top-right corner.

3. In the Activities toolbox, expand Iteration & Conditionals, and drag-drop the ForEach activity to the pipeline designer surface. In the General tab of the Properties window, enter LookupTableList.

4. Switch to the Settings tab, click + New to create a dataset as shown below
  
   ![PIPELINE_TABLE_LIST_LOOKUP_ACTIVITY_SETTING_NEW_DATASET](img/PIPELINE_TABLE_LIST_LOOKUP_ACTIVITY_SETTING_NEW_DATASET.png)
	
  - Select Azure Synapse Analytics
  
    ![PIPELINE_TABLE_LIST_LOOKUP_ACTIVITY_SETTING_NEW_DATASET_SYNAPSE](img/PIPELINE_TABLE_LIST_LOOKUP_ACTIVITY_SETTING_NEW_DATASET_SYNAPSE.png)
  
  - Set properties
    - Dataset Name: ```AzureSynapseAnalyticsTableList```
    - Select Linked Service: **sqlpool01**
    - Select Table Name: **table_list**

    ![PIPELINE_TABLE_LIST_LOOKUP_ACTIVITY_SETTING_NEW_DATASET_PROP](img/PIPELINE_TABLE_LIST_LOOKUP_ACTIVITY_SETTING_NEW_DATASET_PROP.png)

  - Click: **Ok**
  
  - Under setting tab of Lookup activity
    - Select **Frist Row only**: Unchecked
    - Use query: **Table**
 
      ![PIPELINE_TABLE_LIST_LOOKUP_ACTIVITY_SETTING_FIRSTROW](img/PIPELINE_TABLE_LIST_LOOKUP_ACTIVITY_SETTING_FIRSTROW.png)
