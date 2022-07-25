### Task : Create and configure a SQL Database instance.

1. In the Azure portal, navigate to the **+ Create a resource** blade.

2. In the New screen, click the **Search services and marketplace** text box, and type the word **SQL Database**. Click **SQL Database** in the list that appears.

3. In the **SQL Database** screen, click **Create**.

4. From the **Create SQL Database** screen, create an Azure SQL Database with the following settings:

    - In the Project details section, type in the following information
    
        - **Subscription**: the name of the subscription you are using in this lab

        - **Resource group**: **data-engineering-synapse-xxxxx**.
    
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
        
        ![Click on Compute + Storage configure option](../data/images/CreateSQLDatabase-MicrosoftAzure-000123.png)
        
        Step 2:
        
        ![Select Standard](../data/images/CreateSQLDatabase-MicrosoftAzure000124.png)
        
        Step 3:
        
        ![Creating a server instance in the Azure portal](../data/images/ConfigureMicrosoftAzure-000125.png)
        
        Step 4:
        
        ![Creating a server instance in the Azure portal](../data/images/Configure-MicrosoftAzure-000126.png)

	- In the **Networking tab**

		![Click on Compute + Storage configure option](../data/images/Window-000129.png)
	
    - Click on the  **Additional setting** tab, click **Sample** . The AdventureworksLT sample database is selected automatically. 
    
		![Click on Compute + Storage configure option](../data/images/Window-000130.png)

5. In the **Create SQL Database** blade, click **Review + create**.


6. After the validation of the **Create SQL Database*** blade, click **Create**.

   > **Note**: The provision will takes approximately 4 minutes.

> **Result**: After you completed this exercise, you have an Azure SQL Database instance
