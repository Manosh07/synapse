### Task : Create and configure a SQL Database instance.

1. In the Azure portal, navigate to the **+ Create a resource** blade.

2. In the New screen, click the **Search services and marketplace** text box, and type the word **SQL Database**. Click **SQL Database** in the list that appears.

3. In the **SQL Database** screen, click **Create**.

4. From the **Create SQL Database** screen, create an Azure SQL Database with the following settings:

    - In the Project details section, type in the following information
    
        - **Subscription**: the name of the subscription you are using in this lab

        - **Resource group**: **synapse-xxxxx**, 'xxxxx' is some random suffix
    
    - In the Database details section, type in the following information
    
        - Database name: type in **AdventureworksLT**
     
        - Server: Create a new server by clicking **Create new** with the following settings and click on **OK**:
            - **Server name**: **sqlservicexxxxx**, where **xxxxx** are your unique suffix
            - **Server admin login**: **sqladmin**
            - **Password**: **Pa55w.rd**
            - **Confirm Password**: **Pa55w.rd**
            - **Location**: choose a **location** near as your **data-engineering-synapse-xxxxx** resource group.
            - click on **OK**
            - Leave the remaining settings to their defaults, and then click on **OK**
        
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

> **Result**: After you completed this exercise, you have an Azure SQL Database instance
