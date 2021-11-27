# Workshop Day Modernizacao Aplicacoes para o Azure

Mentoria Arquiteto Cloud - Hands-on Lab

## Application Modernization

Modernize a Legacy On-premises Application (Aplication, Database and Serverless)

**Parts Unlimited's E-Commerce website**

   ![Screenshot of the App Modernization](/AllFiles/Images/PartsUnlimited.png)

## Requirements Hands-on lab

- An active Microsoft Azure subscription in preference be Pay-as-you-go or MSDN.
  - **Important:** To complete this lab, you must have sufficient rights within your Azure AD tenant to register resource providers in your Azure Subscription.
- An active [GitHub Account](https://github.com) in Free or Pay plans.

1. In the [Azure portal](https://portal.azure.com), select **Resource groups** from the Azure services list.

2. On the Resource groups blade, select **+Add**.

3. On the Create a resource group **Basics** tab, enter the following:

   - **Subscription (1)**: Select the subscription you are using for this hands-on lab.
   - **Resource group (2)**: Enter **RG-MAC-AppModern** as the name of the new resource group.
   - **Region (3)**: Select the region you are using for this hands-on lab.

4. Select **Review + Create (4)**.

5. On the **Review + create** tab, ensure the Validation passed message is displayed and then select **Create**.

1. Register the `Microsoft.DataMigration` resource provider within your Azure subscription. These resource providers allow Azure Database Migration Service to be provisioned within your subscription.

1. In the [Azure portal](https://portal.azure.com), select **Subscriptions** from the Azure services list.

2. Select the subscription you are using for this hands-on lab from the list, select **Resource providers**, enter "migration" into the filter box, select **Microsoft.DataMigration**, and then select **Register**.

   > If the resource provider is already registered, there is no further action required.

3. It can take a couple of minutes for the registration to complete. Make sure you see the status of **Registered** before moving on. You may need to select **Refresh** to see the updated status.

## Deploy Application Architecture (30 minutes)

1. To run the ARM template deployment, select the **Deploy to Azure** button below, which opens a custom deployment screen in the Azure portal. The resources created by the ARM template include:

- Azure Storage account
- A Windows Server 2019 Web virtual machine (VM) with
  - Parts Unlimited E-Commerce site
  - App Service Migration Assistant
  - Edge
  - .NET Core 2.2
  - .NET Core 3.1 SDK
  - SQL Server Management Studio
  - Git Tools
  - Visual Studio Code installed and configured
- A SQL Server 2008 R2 VM with the Microsoft Data Migration Assistant (DMA).
- Azure SQL Database
- Azure Database Migration Service (DMS)
- Azure App Service Plan
- Function App
- Virtual Network

   <a href ="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2FMCW-App-modernization%2Fmain%2FHands-on%20lab%2Flab-files%2FARM-template%2Fazure-deploy.json" target="_blank" title="Deploy to Azure">
      <img src="http://azuredeploy.net/deploybutton.png"/>
   </a>

2. On the custom deployment screen in the Azure portal, enter the following:

   - **Subscription (1)**: Select the subscription you are using for this hands-on lab.
   - **Resource group (2)**: Select the **RG-MAC-AppModern** resource group from the dropdown list.
   - **SQL Server Name**: Accept the default value, **parts**.

    > **Note**: The actual name must be globally unique, so a unique string is generated from your Resource Group Id and appended to the name during provisioning.

    - **Admin Username**: Accept the default value, **demouser**.
    - **Admin Password (3)**: Type in your custom password you will be using for the rest of the lab. The default password is `Password.1!!`.

3. Select **Review + create** and select **Create** on the next step to start provisioning the lab resources.

   > **Note**: The deployment of the custom ARM template takes about 30 minutes to complete. If the error indicates an issue with quotas in the region you selected for the resource group, you can delete the resource group, create a new resource group in a different region, and re-run the ARM template, or you can request a quota increase and then manually provision the missing resources.

4. You can monitor the progress of the deployment by navigating to the **RG-MAC-AppModern** resource group in the Azure portal and then selecting **Deployments** from the left-hand menu. The deployment should be named **Microsoft.Template**. Select the deployment item to view the progress of each individual component in the template.

## Project - Application Modernization

## Solution architecture

![This solution diagram includes a high-level overview of the architecture](AllFiles/Images/Architecture-Diagram.png "Solution architecture diagram")

> **Note:** The solution provided is only one of many possible, viable approaches.

## Application Modernization (30 minutes)

### Setting up Azure Migrate

1. In the [Azure portal](https://portal.azure.com), navigate to your lab resource group. Select **Create** to add a new resource.

2. On the Create a resource screen, search for **Azure Migrate** and select it from the list of suggestions.

3. Select **Create** to continue.

4. As part of our migration project for Parts Unlimited, we will first assess and migrate their Web Application living on IIS, on a VM. Select **Web Apps** to continue.

5. Select **Create project**.

6. Type **partsunlimitedweb** as your project name. Select **Create** to continue.

7. Once your project is created **Azure Migrate** will show you default **Web App Assessment** and **Web App Migration** tools (You might need to refresh your browser). For the Parts Unlimited web site, **App Service Migration Assistant** is the one we have to use. Download links are presented on Azure Migrate's Web Apps page. In our case, our lab environment comes with App Service Migration Assistant pre-installed on Parts Unlimited's web server.

8. Another aspect of our migration project will be the database for Parts Unlimited's website. We will have to assess the database's compatibility and migrate to Azure SQL Database. Let's switch to the **SQL Server (only)** section in Azure Migrate. Select **Click here** hyperlink for Assessment tools.

9. We will use **Azure Migrate: Database Assessment** to assess Parts Unlimited's database hosted on a SQL Server 2008 R2 server. Pick **Azure Migrate: Database Assessment (1)** and select **Add tool (2)**.

10. **This step is for informational purposes only.**  Now, we can see a download link for the **Data Migration Assessment (1)** tool under assessment tools in Azure Migrate. In our case, our lab environment comes with the Data Migration Assessment pre-installed on Parts Unlimited's database server. Select **Click here (2)** under the **Migration Tools** section to continue.

11. We will use **Azure Migrate: Database Migration** to migrate Parts Unlimited's database to an Azure SQL Database. Pick **Azure Migrate: Database Assessment (1)** and select **Add tool (2)**.

12. Now, we have all the assessment and migration tools/services we need for Parts Unlimited ready to go under the Azure Migrate umbrella.

### Migrate Application with App Service Migration Assistant

1. In the [Azure portal](https://portal.azure.com), navigate to your **WebVM** VM by selecting **Resource groups** from Azure services list, selecting the **RG-MAC-AppModern** resource group, and selecting the **WebVM** VM from the list of resources.

2. Go back to the Azure Portal. On the WebVM Virtual Machine's **Overview** blade, select **Connect (1)** and **RDP (2)** on the top menu.

3. Select **Download RDP File** on the next page, and open the downloaded file.

    > **Note**: The first time you connect to the WebVM Virtual Machine, you will see a blue pop-up terminal dialog taking you through a couple of software installs. Don't be alarmed, and wait until the installs are complete.

4. Select **Connect** on the Remote Desktop Connection dialog.

5. Enter the following credentials with your password when prompted, and then select **OK**:

   - **Username**: demouser
   - **Password**: {YOUR-ADMIN-PASSWORD}

    > **Note**: default password is `Password.1!!`

6. Select **Yes** to connect, if prompted that the identity of the remote computer cannot be verified.

7. Once logged into the WebVM VM, a script will execute to install the various items needed for the remaining lab steps.
    >**Warning:** You must wait for all of the scripts to finish installing and configuring the software when you first log in. DO NOT shutdown the PowerShell script.  The PowerShell script creates the database and seeds the tables with data. Failing to wait for the script to complete will prevent you from finishing the rest of the lab.

8. Once the PowerSell script has completed, the web server should be configured now. Return to the Azure Portal, on the WebVM Virtual Machine's **Overview (1)** blade, copy the **Public IP address (2)**.

9. Open a new browser window and navigate to the IP Address you copied.

    >**Note:** If you cannot see the web page properly display, then your web server configuration may be incorrect.

10. Once the script completes, open **AppServiceMigrationAssistant** that is located on the desktop.

11. Once App Service Migration Assistant discovers the websites available on the server, choose **Default Web Site (1)** for migration and select **Next (2)** to start the assessment.

12. Observe the result of the assessment report. In our case, our application has successfully passed 13 tests **(1)** with no additional actions needed. Now that our assessment is complete, select **Next (2)** to proceed to the migration.

   > For the details of the readiness checks, see [App Service Migration Assistant documentation](https://github.com/Azure/App-Service-Migration-Assistant/wiki/Readiness-Checks).

#### Migrate the Web application to Azure App Service

1. In order to continue with the migration of our website Azure App Service Migration Assistant needs access to our Azure Subscription. Select **Copy Code & Open Browser** button to be redirected to the Azure Portal.

2. At its first launch, you will be asked to choose a browser. Select **Microsoft Edge (1)**  and check **Always use this app (2)** checkbox. Select **OK (2)** to move to the next step.

3. Right-click the text box and select **Paste (1)** to paste your login code. Select **Next** to give subscription access to App Service Migration Assistant.

4. Continue the login process with your Azure Subscription credentials. When you see the message that says **You have signed in to the Azure App Service Migration Assistant application on your device** close the browser window and return to the App Service Migration Assistant.

5. Select the Azure Migrate project we created **(1)** in the previous exercise to submit our migration results. Select **Next** to continue.
    >**Note:** If you do not see your Azure Migrate Project, you may have switch user directory (subscriptions) to the directory where the project was created.

6. In order to migrate Parts Unlimited website, we have to create an App Service Plan. The Azure App Service Migration Assistant will take care of all the requirements needed. Select **Use existing (1)** and select the lab resource group as your deployment target. App Service requires a globally unique Site Name. We suggest using a pattern that matches `partsunlimited-web-{uniquesuffix}` **(2)**. Select **Migrate** to start the migration process.

7. Verify your migration. Select **Go to your website**.

    > **Warning:** If your migration fails with a **WindowsWorkersNotAllowedInLinuxResourceGroup (1)** try the migration process again, but this time selecting a different Resource Group for your deployment. If that is not possible, select a different Region.  

8. We have just completed the Parts Unlimited website's migration from IIS on a Virtual Machine to Azure App Service. Congratulations. Let's go back to the Azure Portal and look into Azure Migrate. Search for `migrate` **(1)** on the Azure Portal and select **Azure Migrate (2)**.

9. Verify your migrations.
    - Select the **Web Apps** section. View the number of discovered web servers, assessed websites.
    - Select the **Refresh** button.
    - View the assessed and migrated websites. Keep in mind that you might need to wait for 5 to 10 minutes for results to show up.

## Modernization Database (90 minutes)

### Perform Assessment for Migration to Azure SQL Database

1. In the [Azure portal](https://portal.azure.com), navigate to your **SqlServer2008** VM by selecting **Resource groups** from Azure services list, selecting the **RG-MAC-AppModern** resource group, and selecting the **SqlServer2008** VM from the list of resources.

2. Launch DMA from the Windows Start menu by typing "data migration" into the search bar and then selecting **Microsoft Data Migration Assistant** in the search results.

    > **Note**: If you do not see the migration assistant, install it from the `c:\DataMigrationAssistant.msi` file.

    > **Note**: There is a known issue with screen resolution when using an RDP connection to Windows Server 2008 R2, which may affect some users. This issue presents itself as very small, hard-to-read text on the screen. The workaround for this is to use a second monitor for the RDP display, allowing you to scale up the resolution to make the text larger.

3. In the DMA dialog, select **+** from the left-hand menu to create a new project.

4. In the New project pane, set the name of the project **(1)** and make sure the following value are selected:

   - **Project type**: Select Assessment.
   - **Project name (1)**: Enter **Assessment**.
   - **Assessment type**: Select Database Engine.
   - **Source server type**: Select SQL Server.
   - **Target server type**: Select Azure SQL Database.

5. Select **Create (2)**.

6. On the **Options** screen, ensure **Check database compatibility (1)** and **Check feature parity (1)** are both checked, and then select **Next (2)**.

7. On the **Sources** screen, select **Add sources**.
8. 
9. Enter the following into the **Connect to a server** dialog that appears on the right-hand side:

    - **Server name (1)**: Enter **SQLSERVER2008**.
    - **Authentication type (2)**: Select **SQL Server Authentication**.
    - **Username (3)**: Enter **PUWebSite**
    - **Password (4)**: Enter **{YOUR-ADMIN-PASSWORD}** `Password.1!!`
    - **Encrypt connection**: Check this box if not checked.
    - **Trust server certificate (5)**: Check this box.

9. Select **Connect (6)**.

10. On the **Add sources** dialog that appears next, check the box for `PartsUnlimited` **(1)** and select **Add (2)**.

11. Select **Start Assessment**.

12. Take a moment to review the assessment for migrating to Azure SQL DB. The SQL Server feature parity report **(1)** shows that Analysis Services and SQL Server Reporting Services are unsupported **(2)**, but these do not affect any objects in the `PartsUnlimited` database, so they won't block a migration.

13. Now, select **Compatibility issues (1)** so you can review that report as well.

    The DMA assessment for migrating the `PartsUnlimited` database to a target platform of Azure SQL DB reveals that no issues or features are preventing Parts Unlimited from migrating their database to Azure SQL DB.

14. Select **Upload to Azure Migrate** to upload assessment results to Azure.

15. Select the right Azure environment **(1)** your subscription lives. Select **Connect (2)** to proceed to the Azure login screen.

16. Select your subscription **(2)** and the `partsunlimited` Azure Migrate project **(3)**. Select **Upload (4)** to start the upload to Azure.

    > **Note**: If you encounter **Failed to fetch subscription list from Azure, Strong Authentication required (1)** you might not see some of your subscriptions because of MFA limitations. You should still be able to see your lab subscription.

17. Once the upload is complete select **OK** and navigate to the Azure Migrate page on the Azure Portal.

18. Select the **Databases (1)** page on Azure Migrate. Observe the number of assessed database instances **(2)** and the number of databases ready for Azure SQL DB **(2)**. Keep in mind that you might need to wait for 5 to 10 minutes for results to show up. You can use the **Refresh** button on the page to see the latest status.

### Retrieve Connection information for SQL Databases

1. In the [Azure portal](https://portal.azure.com), navigate to your **SqlServer2008-ip** resource by selecting **Resource groups** from the Azure services list, selecting the **RG-MAC-AppModern** resource group, and selecting the **SqlServer2008-ip** Public IP address from the list of resources.

2. In the **Overview** blade, select **Copy** to copy the public IP address and paste the value into a text editor, such as Notepad.exe, for later reference.

3. Go back to the resource list and navigate to your **SQL database** resource by selecting the **parts** SQL database resource from the resources list.

    > **Note**: If you do not see the database resource, navigate to the SQL Server resource, then select **SQL Databases** and then the **parts** database.

4. On the Overview blade of your SQL database, copy the **Server name** and paste the value into a text editor, such as Notepad.exe, for later reference.

### Migrate the database schema using the Data Migration Assistant

1. On the SqlServer2008 VM, return to the Data Migration Assistant and select the New **(+)** icon in the left-hand menu.

2. In the New project dialog, enter the following:

   - **Project type (1)**: Select Migration.
   - **Project name (2)**: Enter Migration.
   - **Source server type**: Select SQL Server.
   - **Target server type**: Select Azure SQL Database.
   - **Migration scope (3)**: Select Schema only.

3. Select **Create (4)**.

4. On the **Select source** tab, enter the following:

   - **Server name (1)**: Enter **SQLSERVER2008**.
   - **Authentication type (2)**: Select **SQL Server Authentication**.
   - **Username (3)**: Enter **PUWebSite**
   - **Password (4)**: Enter **{YOUR-ADMIN-PASSWORD}** `Password.1!!`
   - **Encrypt connection**: Check this box.
   - **Trust server certificate (5)**: Check this box.
   - Select **Connect (6)**, and then ensure the `PartsUnlimited` database is selected **(7)** from the list of databases.

5. Select **Next (7)**.

6. On the **Select target** tab, enter the following:

   - **Server name (1)**: Paste the server name of your Azure SQL Database you copied into a text editor in the previous task.
   - **Authentication type (2)**: Select SQL Server Authentication.
   - **Username (3)**: Enter **demouser**
   - **Password (4)**: Enter **{YOUR-ADMIN-PASSWORD}** `Password.1!!`
   - **Encrypt connection**: Check this box.
   - **Trust server certificate (5)**: Check this box.
   - Select **Connect (6)**, and then ensure the `parts` database is selected **(7)** from the list of databases.
 
7. Select **Next (8)**.

8. On the **Select objects** tab, leave all the objects checked **(1)**, and select **Generate SQL script (2)**.

9. On the **Script & deploy schema** tab, review the script. Notice the view also provides a note that there are no blocking issues **(1)**.

10. Select **Deploy schema (2)**.

11. After the schema is deployed, review the deployment results, and ensure there were no errors.

12. Launch SQL Server Management Studio (SSMS) on the SqlServer2008 VM from the Windows Start menu by typing "sql server management" **(1)** into the search bar and then selecting **SQL Server Management Studio 17 (2)** in the search results.

13. Connect to your Azure SQL Database by selecting **Connect->Database Engine** in the Object Explorer and then entering the following into the Connect to server dialog:

    - **Server name (1)**: Paste the server name of your Azure SQL Database you copied above.
    - **Authentication type (2)**: Select SQL Server Authentication.
    - **Username (3)**: Enter **demouser**
    - **Password (4)**: Enter **{YOUR-ADMIN-PASSWORD}** `Password.1!!`
    - **Remember password (5)**: Check this box.

14. Select **Connect (6)**.

15. Once connected, expand **Databases**, and expand **parts**, then expand **Tables**, and observe the schema has been created **(1)**. Expand **Security > Users** to observe that the database user is migrated as well **(2)**.

### Migrate the Database using the Azure Database Migration Service

1. In the [Azure portal](https://portal.azure.com), navigate to your Azure Database Migration Service by selecting **Resource groups** from the Azure services list, selecting the **RG-MAC-AppModern** resource group, and then selecting the **contoso-dms-UniqueId** Azure Database Migration Service in the list of resources.

2. On the Azure Database Migration Service blade, select **+New Migration Project**.

3. On the New migration project blade, enter the following:

   - **Project name (1)**: Enter DataMigration.
   - **Source server type**: Select SQL Server.
   - **Target server type**: Select Azure SQL Database.
   - **Choose type of activity**: Select **Offline data migration** and select **Save**.

4. Select **Create and run activity (2)**.

5. On the Migration Wizard **Select source** blade, enter the following:

   - **Source SQL Server instance name (1)**: Enter the IP address of your SqlServer2008 VM that you copied into a text editor in the previous task.
   - **Authentication type (2)**: Select SQL Authentication.
   - **Username (3)**: Enter **PUWebSite**
   - **Password (4)**: Enter **{YOUR-ADMIN-PASSWORD}** `Password.1!!`
   - **Connection properties (5)**: Check both Encrypt connection and Trust server certificate.

6. Select **Next: Select databases >> (6)**.

7. PartsUnlimited database comes preselected. Select **Next: Select target >>** to continue.

8. On the Migration Wizard **Select target** blade, enter the following:

   - **Target server name (1)**: Enter the `fullyQualifiedDomainName` value of your Azure SQL Database (e.g., parts-xwn4o7fy6bcbg.database.windows.net), which you copied in the previous task.
   - **Authentication type (2)**: Select SQL Authentication.
   - **Username (3)**: Enter **demouser**
   - **Password (4)**: Enter **{YOUR-ADMIN-PASSWORD}** `Password.1!!`
   - **Connection properties**: Check Encrypt connection.

9. Select **Next: Map to target databases >> (5)**.

10. On the Migration Wizard **Map to target databases** blade, confirm that **PartsUnlimited (1)** is checked as the source database, and **parts (2)** is the target database on the same line, then select **Next: Configuration migration settings >> (3)**.

11. On the Migration Wizard **Configure migration settings** blade, expand the **PartsUnlimited (1)** database and verify all the tables are selected **(2)**.

12. Select **Next: Summary >> (3)**.

13. On the Migration Wizard **Summary** blade, enter the following:

    - **Activity name**: Enter PartsUnlimitedDataMigration.

14. Select **Start migration**.

15. Monitor the migration on the status screen that appears. Select the refresh icon in the toolbar to retrieve the latest status.

    > The migration takes approximately 2 - 3 minutes to complete.

16. When the migration is complete, you should see the status as **Completed**.

17. When the migration is complete, select the **PartsUnlimited** migration item.

18. Review the database migration details.

19. If you received a status of "Warning" for your migration, you can find more details by selecting **Download report** from the ContosoDataMigration screen.

    > **Note**: The **Download report** button will be disabled if the migration is completed without warnings or errors.

20. The reason for the warning can be found in the Validation Summary section. In the report below, you can see that a storage object schema difference triggered a warning. However, the report also reveals that everything was migrated successfully.

### Configure the application connection to SQL Azure Database

1. In the [Azure portal](https://portal.azure.com), navigate to your `parts` SQL Database resource by selecting **Resource groups** from Azure services list, selecting the **RG-MAC-AppModern** resource group, and selecting the `parts` SQL Database from the list of resources.

2. Switch to the **Connection strings (1)** blade, and copy the connection string by selecting the copy button **(2)**.

3. Paste the value into a text editor, such as Notepad.exe, to replace the Password placeholder. Replace the `{your_password}` section with your admin password. Copy the entire connection string with the replaced password for later use.

4. Go back to the resource list, navigate to your `partsunlimited-web-{uniquesuffix}` **(2)** App Service resource. You can search for `partsunlimited-web` **(1)** to find your Web App and App Service Plan.

5. Switch to the **Configuration (1)** blade, and select **+New connection string (2)**.

6. On the **Add/Edit connection string** panel, enter the following:

   - **Name(1)**: Enter `DefaultConnectionString`.
   - **Value**: Enter SQL Connection String you copied in Step 3.
   - **Type (3)**: Select **SQLAzure**
   - **Deployment slot setting (4)**: Check this option to make sure connection strings stick to a deployment slot. This will be helpful when we add additional deployment slots during the next exercises.

7. Select **OK (5)**.

8. Select **Save** and **Continue** for the following confirmation dialog.

9. Switch to the **Overview (1)** blade, and select **URL (2)** to navigate to the Parts Unlimited website hosted in our Azure App Service using Azure SQL Database.

## Deploy CI/CD Pipeline with Github Actions for the Application (60 minutes)

### Moving the codebase to a GitHub repo

1. Log in to [GitHub](https://github.com) with your account. Select the New button positioned on top of the repositories list. As an alternative, you can [navigate to the new repository site here](https://github.com/new).

2. Type in `partsunlimited` **(1)** as your repository name. Select **Private (2)** to prevent public access to the repository. Select **Create repository (3)** to continue.

3. Select the **clipboard copy command** to copy the Git endpoint for your repository and paste the value into a text editor, such as Notepad.exe, for later reference.

    So far, we have used the WebVM virtual machine to simulate Parts Unlimited's On-Premises IIS server. Now that we are done with the migration of Parts Unlimited's website. We will use the VM to execute some development tasks.

4. Connect to your WebVM VM with RDP.

5. Right-click on the Windows Start Menu and select **Windows PowerShell (Admin)** to launch a terminal window.

6. The Parts Unlimited website's source code is already copied into the VM as part of the **Before the hands-on lab exercises**. Run the command below to navigate to the source code folder.

    ```powershell
    cd "C:\MCW\MCW-App-modernization-main\Hands-on lab\lab-files\src"
    ```

7. Run the following command to initialize a local Git repository.

    ```powershell
    git init
    ```

8. Next, we will define the remote endpoint as an origin to our local repository. Replace `{YourEndpointURL}` with the endpoint URL you copied previously from GitHub. Run the final command in your PowerShell terminal.

    ```powershell
    git remote add origin {YourEndpointURL}
    ```
9. Run the following commands to rename the current branch to **Main** and stage all the files for a git commit.

    ```powershell
    git branch -M main
    git add .
    ```

10. Before we commit our changes, we have to identify our git user name and e-mail. In the following command, replace `John Doe` with your name and `johndoe@example.com` with your e-mail address. Once ready, run the command in your PowerShell terminal.

    ```powershell
    git config --global user.name "John Doe"
    git config --global user.email johndoe@example.com
    ```

11. We are ready to commit the source code to our local Git repository. Run the following command to continue.

    ```powershell
    git commit -m "Initial Commit"
    ```

12. Let's push our code to GitHub. Run the following command in your PowerShell terminal.

    ```powershell
    git push -u origin main
    ```

13. GitHub authentication screen will pop up. Select **Sign in with your browser (2)**. A new browser pop-up will appear with the GitHub login page.

14. Fill-in your GitHub account credentials on the browser window to Sign-In.

15. On the **Authorize Git Credential Manager** screen, select **Authorize GotCredentialManager**. This will give your local environment permission to push the code to GitHub.

16. Close the browser.

17. Go back to GitHub and observe the repository with the source code uploaded.

### Creating a Staging Deployment slot

1. Go back to your lab resource group, navigate to your `partsunlimited-web-{uniquesuffix}` **(2)** App Service resource. You can search for `partsunlimited-web` **(1)** to find your Web App and App Service Plan

2. Switch to the **Deployment slots (1)** tab and select **Add Slot (2)**.

3. Type in **staging** as the name **(1)** of the new slot. Select your app service name from the **Clone settings from (2)** dropdown list. This will ensure our website configurations for the production environment are copied over to the staging environment as a starting point. Select **Add (3)** to add the new slot.

4. Once you receive the success message, close **(1)** the panel. Observe **(2)** the two environments we have for the App Service in the deployment slots list.

### Setting up CI/CD with GitHub Actions

1. Select your staging slot from the list of deployment slots.

2. From the toolbar menu, select **Get publish profile**. The publish profile is used to authenticate between GitHub and Azure.

3. Open the downloaded file in a text editor. Keep this editor open.

4. In a web browser, return to the GitHub repository for this lab, and select the **Settings** tab.

5. From the left menu, select **Secrets**. Then, select **New repository secret**.

6. Enter the following values in the **New secret** form, then select **Add secret**.

    | Field | Value |
    |-------|-------|
    | Name  | AZURE_WEBAPP_PUBLISH_PROFILE |
    | Value | Copy and paste the contents of the downloaded publish profile open in the text editor. |

7. Select the **Actions** tab.

8. On the **Get started with GitHub Actions** screen, select the **set up a workflow yourself** link.

9. On the workflow editor screen, provide the file name **stagingdeploy.yml**. Commit your changes by selecting the Start Commit button.

10. Return the Web VM. Open a Command window. Execute the following code to pull your newly created GitHub stagingdeploy.yml file locally. You are going to update the file content with the template stagingdeploy.yml.

    ```cmd
        cd C:\MCW\MCW-App-modernization-main\Hands-on lab\lab-files\src
        git pull
    ```

11. Open Windows Explorer. Copy the solution template yml file `C:\MCW\MCW-App-modernization-main\Hands-on lab\lab-files\workflow\stagingdeploy.yml` to `C:\MCW\MCW-App-modernization-main\Hands-on lab\lab-files\src\.github\workflows\stagingdeploy.yml`.  You are going to replace the default GitHub workflow yml content.

12. Open the `stagingdeploy.yml` in Visual Studio Code. Replace the suffix value on lines 7 and 11 to match your lab.

13. You have just updated the `partsunlimited` GitHub project. It is time to save and push your changes. Execute these commands in a Web VM Command window.

    ```cmd
        cd C:\MCW\MCW-App-modernization-main\Hands-on lab\lab-files\src
        git add .
        git commit -am "Updated the stagingdeploy.yml with my changes"
        git push 
    ```

14. In GitHub, select **Actions**. The workflow will display as in progress.

15. Go back to your lab resource group on the Azure Portal, navigate to your `staging (partsunlimited-web-{uniquesuffix}/staging)` **(2)** App Service resource. You can search for `staging` **(1)** to find your App Service (Slot) for staging.

16. Notice the dedicated web link for your staging slot. Select to navigate to the website to see the result of your successful deployment through the CI/CD pipeline.

### Pushing code changes to Staging and Production

1. Connect to your WebVM VM with RDP.

2. Select the Start menu and search for **Visual Studio Code**. Select **Visual Studio Code** to run it.

3. Open the **File (1)** menu and select **Open Folder... (2)**.

4. Navigate to `C:\MCW\MCW-App-modernization-main\Hands-on lab\lab-files\src` and select **Select Folder (1)**.

5. We are going to introduce a brand new change to Parts Unlimited's website. In the Explorer window navigate to **src > PartsUnlimitedWebSite > Views > Home** and select **Index.cshtml (4)** for editing. Change the Title of the page **(5)** and save the file by using going to the File menu and selecting **Save**. Notice the underlying git repository detecting a change (6) in the codebase.

6. Select **Source Control (1)** tab in Visual Studio Code. Since we worked on the codebase in our repo in the virtual machine, the codebase in the repo on GitHub has changed. Open the **Views and more actions... (2)** menu and select **Pull (3)** to get the latest from the remote repository.

7. Select **Stage Changes (1)**. Type in a commit message **(2)** for the changes. Select **Commit (3)**.

8. Open the **Views and more actions... (1)** menu and select **Push (2)** to push the changes to GitHub.

9. Open the GitHub repository and observe the Actions page for the latest execution of the CI/CD Pipeline.

10. Navigate to the staging environment endpoint in your browser and observe the Title change.

### Swap Deployment slots to move changes in Staging to Production

1. Go back to your lab resource group, navigate to your `partsunlimited-web-{uniquesuffix}` **(2)** App Service resource. You can search for `partsunlimited-web` **(1)** to find your app service.

2. Switch to the **Deployment slots (1)** tab and select **Swap (2)**.

3. Select the **Swap** button to swap the staging slot with the production slow.

4. Once you receive the success message, close the swap panel.

5. Visit both production and staging slot endpoints and observe how the Title change is moved to production.

    > Once you move your staging slot to production, your production slot is moved to staging as well. This means that your current staging slot does not have the latest changes you have pushed to the repo. You can trigger a manual CI/CD workflow execution to push the latest changes to staging.
    >
    > To run a CI/CD workflow manually, go to GitHub actions page **(1)** in your repository. Select the workflow **(2)** to run. Open the **Run workflow (3)** menu and select **Run workflow (4)**.
    >

## Deploy Serverless to Proceded orders (60 minutes)

1. Connect to your WebVM VM with RDP.

2. Select the Start menu and search for **Visual Studio Code**. Select **Visual Studio Code** to run it.

3. Open the **File (1)** menu and select **Open Folder... (2)**.

4. Navigate to `C:\MCW\MCW-App-modernization-main\Hands-on lab\lab-files\src-invoicing-functions\FunctionApp` and select **Select Folder**.

5. Select **Install** to install extensions required for your Azure Functions project. This will install C# for Visual Studio Code and Azure Functions Extension for Visual Studio Code.

6. Once install is **Finished (1)** select **Restore (2)** to download dependencies for the project.

7. When restore is complete close the tabs titled **Extension (1) (2)** and the **welcome tab (3)**. Select **Azure (4)** from the left menu and select **Sign into Azure (5)**. Select **Edge** as your browser if requested.

8. Enter your Azure credentials and Sign In.

9. Close the browser window once your sign-in is complete.

10. Drill down **(1)** the resource in your subscription. Right-click on your Azure Function named **parts-func-{uniquesuffix} (2)** and select **Deploy to Azure Function App... (3)**.

11. Select **Deploy**.

### Connecting Function App and App Service

1. In the [Azure portal](https://portal.azure.com), navigate to your `parts` Storage Account resource by selecting **Resource groups** from Azure services list, selecting the **RG-MAC-AppModern** resource group, and selecting the `parts{uniquesuffix}` Storage Account from the list of resources.

2. Switch to the **Access keys (1)** blade, and select **Show keys (2)**. Select the copy button for the first connection string **(3)**. Paste the value into a text editor, such as Notepad.exe, for later reference.

3. Go back to the resource list and navigate to your `partsunlimited-web-{uniquesuffix}` **(2)** App Service resource. You can search for `partsunlimited-web` **(1)** to find your Web App and App Service Plan

4. Switch to the **Configuration (1)** blade, and select **+New connection string (2)**.

5. On the **Add/Edit connection string** panel, enter the following:

   - **Name(1)**: Enter `StorageConnectionString`.
   - **Value**: Enter Storage Connection String you copied in Step 2.
   - **Type (3)**: Select **Custom**
   - **Deployment slot setting (4)**: Check this option to make sure connection strings stick to a deployment slot. This will make sure you can have different settings for staging and production.

6. Select **OK (5)**.

7. Select **Save** and **Continue** for the following confirmation dialog.

8. Go back to the resource list and navigate to your `parts-func-{uniquesuffix}` **(2)** Function App resource. You can search for `func` **(1)** to find your function app.

9. Switch to the **Configuration (1)** blade, and select **+New application setting (2)**.

10. On the **Add/Edit connection string** panel, enter the following:

    - **Name(1)**: Enter `DefaultConnection`.
    - **Value**: Enter SQL Connection String you copied in above.

11. Select **OK (3)**.

12. Select **Save** and **Continue** for the following confirmation dialog.

### Testing serverless order processing

1. Go back to the resource list and navigate to your `partsunlimited-web-{uniquesuffix}` **(2)** App Service resource. You can search for `partsunlimited-web` **(1)** to find your Web App and App Service Plan.

2. Select **URL** and navigate to the Parts Unlimited website hosted in Azure App Service.

3. Select **Login (1)** and select **Register as a new user? (2)**.

4. Type in `test@test.com` for the email **(1)** and `Password.1!!` **(2)** for the password. Select **Register (3)**.

5. On the next screen, select **Click here to confirm your e-mail** to confirm your e-mail.

6. Select **Login (1)** and type the credentials listed below.

    - **Email (2):** test@test.com
    - **Password (3):** Password.1!!

7. Select **Login (4)**.

8. Select a product from the home page, and select **Add to Card** once you are on the product detail page.

9. Select **Checkout** on the next screen.

10. Fill in sample shipping information **(1)** for testing purposes. Use coupon code **FREE (2)** and select **Submit Order (3)**.

11. Once checkout is complete select **view your order** to see order details.

12. Observe the invoice field. Right now, your order is flagged as in processing. An order job is submitted to the Azure Storage Queue to be processed by Azure Functions. Refresh the page every 15 seconds to see if anything changes about your order.

13. Once your order has been processed, an invoice will be created, and a download link will appear. Select the download link to download the PDF invoice created for your order by Azure Functions.

### Enable Application Insights on the Function App

1. In the [Azure portal](https://portal.azure.com), navigate to your **Function App** by selecting **Resource groups** from Azure services list, selecting the **RG-MAC-AppModern** resource group, and selecting the **parts-func-{uniquesuffix}** App service from the list of resources.

2. On the Function App blade, select **Application Insights (1)** under Settings from the left-hand menu. On the Application Insights blade, select **Turn on Application Insights (2)**.

3. On the Application Insights blade, select **Create new resource (1)**, accept the default name provided, and then select **Apply (2)**. Select **Yes (3)** when prompted about restarting the Function App to apply monitoring settings.

4. After the Function App restarts, select **View Application Insights data**.

5. On the Application Insights blade, select **Live Metrics Stream (1)** from the left-hand menu.

   > While having Live Metric up, try submitting a new order on the Parts Unlimited website. You will see access to blob storage in the telemetry to upload the PDF **(2)** and execution count on the graph **(3)**.

## After the Hands-on Lab

1. Delete all Azure resources created in support of this Hands-on lab.

1. End of **Workshop Day**

1. Continue in the **Mentoria Arquiteto Cloud**.









