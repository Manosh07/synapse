## Exercise 3: Read data with Azure Databricks

Estimated Time: 30 minutes

Individual exercise

The main tasks for this exercise are as follows:

1. Confirm that the Databricks cluster has been created.

2. Collect the Azure Data Lake Store Gen2 account name

3. Enable your Databricks instance to access the Data Lake Gen2 Store.

4. Create a Databricks Notebook and connect to a Data Lake Store.

5. Read data in Azure Databricks.

### Task 1: Confirm the creation of the Databricks cluster

1. Return back to Microsoft Edge, under **Interactive Clusters** confirm that the state column is set to **Running** for the cluster named **awdbclstudxx**, where **xx** are your initials.

### Task 2: Collect the Azure Data Lake Store Gen2 account name

1. In Microsoft Edge, click on the  Azure portal tab, click **Resource groups**, and then click **awrgstudxx**, and then click on **awdlsstudxx**, where **xx** are your initials.

2. In the **awdlsstudxx** screen, under settings, click on **Access keys**, and then click on the copy icon next to the **Storage account name**, and paste it into Notepad.

    ![Accessing Data Lake Storage account name in the Azure portal](Linked_Image_Files/M03-E03-T02-img01.png)

### Task 3: Enable your Databricks instance to access the Data Lake Gen2 Store.

1. In the Azure portal, Click the **Home** hyperlink, and then click the **Azure Active Directory** icon.

2. In the **Microsoft - Overview** screen, click on **App registrations**.

3. In the **Microsoft - App registrations** screen, click on the **+ New registration** button.

4. In the register an application screen, provide the **name** of **DLAccess** and under the **Redirect URI (optional)** section, ensure **Web** is selected and type **http://localhost**for the application value. After setting the values.

    ![Registering an application in the Azure portal](Linked_Image_Files/M03-E03-T03-img01.png)

5. Click **Register**. The DLAccess screen will appear.

6. In the **DLAccess** registered app screen, copy the **Application  (client) ID** and **Directory (tenant) ID** and paste both into Notepad.

7. In the **DLAccess** registered app screen, click on **Certificates and Secrets**, and the click **+ New Client Secret**

8. In the Add a client secret screen. type a **description** of **DL Access Key**, and a **Expires** of **12 months** for the key. When done, click **Add**.

    ![Adding a client secret in the Azure portal](Linked_Image_Files/M03-E03-T03-img02.png)

    >**Important**: When you click on **Add**, the key will appear as shown in the graphic below. Make sure you copy the **Value** and the **Secret ID** and paste it in a notepad. 

    ![Location of the DLAccess Key](Linked_Image_Files/M03-E03-T03-img03.png)

9. Assign the Storage Blob Data Contributor permission to your resource group. In the Azure portal, click on the **Home** hyperlink, and then the **Resource groups** icon, click on the resource group **awrgstudxx**, where **xx** are your initials.

10. In the **awrgstudxx** screen, click on **Access Control (IAM)** 

11. Click on the **Role assignments** tab. 

12. Click **+ Add**, and click **Add role assignment**

13. In the **Add role assignment** blade, under Role, select **Storage Blob Data Contributor**.

14. In the **Add role assignment** blade, under **Select**, select **DLAccess**, and then click **Save**.

15. In the Azure portal, click the **Home** hyperlink, and then click the **Azure Active Directory** icon, Note **your role**. If you have the User role, you must make sure that non-administrators can register applications.

16. Click **Users**, and then click **User settings**. Check the **App registrations** setting. This value can only be set by an administrator. If set to **Yes**, any user in the Azure AD tenant can register an app. 

17. Close down the **Users - All users** screen.

18. In the Azure Active Directory blade, click **Properties**.

19. Click on the Copy icon next to the **Tenant ID** to get your tenant ID and paste this into notepad.

20. Save the notepad document in the folder **Allfiles\Labfiles\Starter\DP-200.3** as **DatabricksDetails.txt**

### Task 4: Create a Databricks Notebook and connect to a Data Lake Store.

1. In Microsoft Edge, click on the tab **Clusters - Databricks**

    > **Note**: You will see the Clusters page.

2. In the Azure Databricks blade on the left of Microsoft Edge, click on **Workspace**, click on the drop down next to **Workspace**, and click on the dropdown next to **Users**. Then click on the drop down next to your username and point to **Create** and then click on **Notebook**.

3. In the **Create Notebook** screen, next to Name type **My Notebook**.

4. Next to the **Default Language** drop down list, select **Scala**.

5. Ensure that the Cluster states the name of the cluster that you have created earlier, click on **Create**

    ![Creating a Notebook in Azure Databricks](Linked_Image_Files/M03-E03-T04-img01.png)

     > **Note**: This will open up a Notebook with the title My Notebook (Scala).

6. In the Notebook, in the cell  **Cmd 1**, copy the following code and paste it into the cell:

    ```scala
    //Connect to Azure Data Lake Storage Gen2 account

    spark.conf.set("fs.azure.account.auth.type", "OAuth")
    spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
    spark.conf.set("fs.azure.account.oauth2.client.id.<storage-account-name>.dfs.core.windows.net", "<application-id>")
    spark.conf.set("fs.azure.account.oauth2.client.secret.<storage-account-name>.dfs.core.windows.net", "<authentication-key>")
    spark.conf.set("fs.azure.account.oauth2.client.endpoint.<storage-account-name>.dfs.core.windows.net", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
    ```

7. In this code block, replace the **application-id**, **authentication-key**, **tenant-id**, and **storage-account-name** placeholder values in this code block* with the values that you collected earlier and are held in notepad.

    - **storage-account-name**: **awdlsstudxx**, where **xx** are your initials.

    - **application-id**: the application id you've created under Microsoft Azure Active Directory -> App Registrations -> DLAccess app-> Application (client) ID

    - **authentication-key**: when you created the client secret, it is the the key that will appear as **Value**  (see step 8 in task 3)

    - **tenant-id**: this ID you should have saved but can be find through Microsoft_AAD_IAM -> ActiveDirectoryMenuBlade -> Properties


8. In the Notebook, in the cell under **Cmd 1**, click on the **Run** icon and click on **Run Cell** as highlighted in the following graphic. 

    ![Running cvode in a Notebook in Azure Databricks](Linked_Image_Files/M03-E03-T04-img02.png)

    >**Note** A message will be returned at the bottom of the cell that states "Command took 0.0X seconds -- by person at 4/4/2019, 2:46:48 PM on awdbclstudxx"

### Task 5: Read data in Azure Databricks.

1. In the Notebook, hover your mouse over the code cell you just created, and click on the **+** icon. A new cell will appear named **Cmd2**.

2. In the Notebook, in the cell  **Cmd 2**, copy the following code and paste it into the cell:

    ```scala
    //Read JSON data in Azure Data Lake Storage Gen2 file system

    val df = spark.read.json("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/preferences.json")
    ```

3. In this code block, replace the **file-system-name** with the word **logs** and **storage-account-name** placeholder values in this code block with the value that you collected earlier and are held in notepad.

4. In the Notebook, in the cell under **Cmd 2**, click on the **Run** icon and click on **Run Cell**. 

    >**Note** A message will be returned at the bottom of the cell that states that a Spark job has executed and "Command took 0.0X seconds -- by person at 4/4/2019, 2:46:48 PM on awdbclstudxx"

5. In the Notebook, hover your mouse over the code cell you just created, and click on the **+** icon. A new cell will appear named **Cmd3**. 

6. In the Notebook, in the cell  **Cmd 3**, copy the following code and paste it into the cell:

    ```scala
    //Show result of reading the JSON file
  
    df.show()
    ```

    ![Running results in a Notebook in Azure Databricks](Linked_Image_Files/M03-E03-T04-img04.png)

7. In the Notebook, in the cell under **Cmd 3**, click on the **Run** icon and click on **Run Cell**.

    >**Note**  A message will be returned at the bottom of the cell that states that a Spark job has executed, a table of results are returned and "Command took 0.0X seconds -- by person at 4/4/2019, 2:46:48 PM on awdbclstudxx"

8. Leave the Azure Databricks Notebook open

>**Result** In this exercise, you have performed the necessary steps that setup up the permission for Azure Databricks to access data in an Azure Data Lake Store Gen2. You then used scala to connect up to a Data Lake Store and you read data and created a table output showing the preferences of people.
