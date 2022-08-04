# Exercise Read data with Azure Databricks

The main tasks for this exercise are as follows:

1. Collect the Azure Data Lake Store Gen2 account name

2. Enable your Databricks instance to access the Data Lake Gen2 Store.

3. Create a Databricks Notebook and connect to a Data Lake Store.

4. Read data in Azure Databricks.

### Task 1: Collect the Azure Data Lake Store Gen2 account name

1. In Microsoft Edge, click on the  Azure portal tab, click **Resource groups**, and then click **data-engineering-synpase-xxxxxx**, and then click on **asadatalakexxxxxx**, where **xxxxxx** is your uniquw suffix.

2. In the **asadatalakexxxxxx** screen, under settings, click on **Access keys**, and then click on the copy icon next to the **Storage account name**, and paste it into Notepad.

### Task 2: Enable your Databricks instance to access the Data Lake Gen2 Store.

1. In the Azure portal, Click the **Home** hyperlink, and then click the **Azure Active Directory** icon.

2. In the **Microsoft - Overview** screen, click on **App registrations**.

3. In the **Microsoft - App registrations** screen, click on the **+ New registration** button.

4. In the register an application screen, provide the **name** of **DLAccess** and under the **Redirect URI (optional)** section, ensure **Web** is selected and type **http://localhost** for the application value. After setting the values.

5. Click **Register**. The DLAccess screen will appear.

6. In the **DLAccess** registered app screen, copy the **Application  (client) ID** and **Directory (tenant) ID** and paste both into Notepad.

7. In the **DLAccess** registered app screen, click on **Certificates and Secrets**, and the click **+ New Client Secret**

8. In the Add a client secret screen. type a **description** of **DL Access Key**, and a **Expires** of **12 months** for the key. When done, click **Add**.

    >**Important**: When you click on **Add**, the key will appear as shown in the graphic below. Make sure you copy the **Value** and the **Secret ID** and paste it in a notepad. 

9. Assign the Storage Blob Data Contributor permission to your resource group. In the Azure portal, click on the **Home** hyperlink, and then the **Resource groups** icon, click on the resource group **asadatalakexxxxxx**, where **xxxxxx** is your unique suffix.

10. In the **asadatalakexxxxxx** screen, click on **Access Control (IAM)** 

11. Click on the **Role assignments** tab. 

12. Click **+ Add**, and click **Add role assignment**

13. In the **Add role assignment** blade, under Role, select **Storage Blob Data Contributor**.

14. In the **Add role assignment** blade, under **Select**, select **DLAccess**, and then click **Save**.

15. In the Azure Active Directory blade, click **Properties**.

16. Click on the Copy icon next to the **Tenant ID** to get your tenant ID and paste this into notepad.

17. Save the notepad document as **DatabricksDetails.txt**

### Task 3: Create a Databricks Notebook and connect to a Data Lake Store.

1. Opwn your databricks workspace

2. In the Azure Databricks blade on the left, click on **Workspace**, click on the **Users**, and click on the dropdown next to **Users**. Then click on the drop down next to your username (with house icon) and point to **Create** and then click on **Notebook**.

3. In the **Create Notebook** screen, next to Name type **My Notebook**.

4. Next to the **Default Language** drop down list, select **Python**.

5. Ensure that the Cluster states the name of the cluster that you have created earlier, click on **Create**

    > **Note**: This will open up a Notebook with the title My Notebook (Scala).

6. In the Notebook, in the cell  **Cmd 1**, copy the following code and paste it into the cell:

    ```scala
    //Connect to Azure Data Lake Storage Gen2 account
    configs={
        "fs.azure.account.auth.type": "OAuth",
        "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
        "fs.azure.account.oauth2.client.id": "<application-id>",
        "fs.azure.account.oauth2.client.secret": "<Secret Value>",
        "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant-id>/oauth2/token"}
	
    // Optionally, you can add <directory-name> to the source URI of your mount point.
    dbutils.fs.mount(
        source = "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/",
        mount_point = "/mnt/<mount-name>",
        extra_configs = configs)
    ```

7. In this code block, replace the **application-id**, **Secret Value**, **tenant-id**, **storage-account-name**, **container-name**, and **mount-name** placeholder values in this code block with the values that you collected earlier and are held in notepad.

    - **application-id**: the application id you've created under Microsoft Azure Active Directory -> App Registrations -> DLAccess app-> Application (client) ID

    - **Secret Value**: when you created the client secret, it is the the key that will appear as **Value**

    - **tenant-id**: this ID you should have saved but can be find through Microsoft_AAD_IAM -> ActiveDirectoryMenuBlade -> Properties
    
    - **container-name**: `wwi-02`
    
    - **storage-account-name**: **asadatalakexxxxxx**, where **xxxxxx** is your usinque suffix.
    
    - **mount-name**: `data`


8. In the Notebook, in the cell under **Cmd 1**, click on the **Run** icon and click on **Run Cell**. 

### Task 4: Read data in Azure Databricks.

1. In the Notebook, hover your mouse over the code cell you just created, and click on the **+** icon. A new cell will appear named **Cmd2**.

2. In the Notebook, in the cell  **Cmd 2**, copy the following code and paste it into the cell:

    ```python
    //Read JSON data in Azure Data Lake Storage Gen2 file system

    sourcepath = "/mnt/data/sale-poc/sale-20170501.csv"

    df = spark.read.option("header",True).option("inferSchema", True).csv(sourcepath)
    
    ```

3. In the Notebook, in the cell under **Cmd 2**, click on the **Run** icon and click on **Run Cell**. 

4. In the Notebook, hover your mouse over the code cell you just created, and click on the **+** icon. A new cell will appear named **Cmd3**. 

5. In the Notebook, in the cell  **Cmd 3**, copy the following code and paste it into the cell:

    ```scala
    //Show result of reading the CSV file
  
    df.show()
    ```

6. In the Notebook, in the cell under **Cmd 3**, click on the **Run** icon and click on **Run Cell**.

7. Leave the Azure Databricks Notebook open

>**Result** In this exercise, you have performed the necessary steps that setup up the permission for Azure Databricks to access data in an Azure Data Lake Store Gen2. You then used python to connect up to a Data Lake Store and you read data.
