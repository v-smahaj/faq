## Prerequisites

To begin, you will need: 

* Two Azure subscriptions attached to two different Office 365 Tenants:
  * Azure Commerical Subscription attached to an Office 365 GCC tenant (user directory)
  	* Bot channels registration
	* Application Insights
  * Azure Government (MAG) Subscription attached to Office 365 GCC High tenant (all other services)
	* App service
	* App service plan
	* Azure storage account
	* Azure search
	* Azure function
	* QnA Maker cognitive service
	* Application Insights  
* A team in Microsoft Teams GCC with your group of experts. (You can add and remove team members later!)
* A copy of the FAQ Plus app repo (this directory)
* A reasonable set of Question and Answer pairs to set up the knowledge base for the bot.
* A copy of the FAQ Plus app GitHub repo (https://github.com/OfficeDev/microsoft-teams-apps-faqplus-hybrid)
* Latest version of the [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows?tabs=azure-cli)


## Step 1: Register Azure AD applications

Register two Azure AD applications in your GCC tenant's directory: one for the bot, and another for the configuration app.

1. Log in to the Azure Portal for your Azure Commerical subscription, and go to the "App registrations" blade [here](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).

2. Click on "New registration", and create an Azure AD application.
	1. **Name**: The name of your Teams app - if you are following the template for a default deployment, we recommend "FAQ Plus".
	2. **Supported account types**: Select "Accounts in any organizational directory"
	3. Leave the "Redirect URL" field blank.

![Azure registration page](https://github.com/OfficeDev/microsoft-teams-apps-faqplus/wiki/Images/multitenant_app_creation.png)

3. Click on the "Register" button.

4. When the app is registered, you'll be taken to the app's "Overview" page. Copy the **Application (client) ID** and **Directory (tenant) ID**; we will need it later. Verify that the "Supported account types" is set to **Multiple organizations**.

![Azure overview page](https://github.com/OfficeDev/microsoft-teams-apps-faqplus/wiki/Images/multitenant_app_overview.png)

5. On the side rail in the Manage section, navigate to the "Certificates & secrets" section. In the Client secrets section, click on "+ New client secret". Add a description of the secret and select an expiry time. Click "Add".

![Azure AD overview page](https://github.com/OfficeDev/microsoft-teams-apps-faqplus/wiki/Images/multitenant_app_secret.png)

6. Once the client secret is created, copy its **Value**; we will need it later.

7. Go back to “App registrations”, then repeat steps 2-3 to create another Azure AD application for the configuration app.
	1. **Name**: The name of your configuration app. We advise appending “Configuration” to the name of this app; for example, “FAQ Plus Configuration”.
	2. **Supported account types**: Select "Account in this organizational directory only"
	3. Leave the "Redirect URL" field blank for now.

At this point you have 4 unique values:

* Application (botclientId) ID for the bot
* Client secret for the bot
* Application (configAppClientId) ID for the configuration app
* Directory (tenant) ID, which is the same for both apps

We recommend that you copy these values into a text file, using an application like Notepad. We will need these values later.

![Configuration step 3](https://github.com/OfficeDev/microsoft-teams-apps-faqplus/wiki/Images/azure-config-app-step3.png)

## Step 2: Deploy bots to your Commerical Azure subscription
1. Click on the "Deploy to Azure" button below.

[![Deploy To Azure Commercial](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FOfficeDev%2Fmicrosoft-teams-apps-faqplus-hybrid%2Fmaster%2FDeployment%2Fazuredeploy.json)

2. When prompted, log in to your commercial Azure subscription.
3. Azure will create a "Custom deployment" based on the ARM template and ask you to fill in the template parameters.
4. Select a subscription and resource group.
	- We recommend creating a new resource group.
	- The resource group location MUST be in a data center that supports:
	     - Application Insights
		 - Azure Search
		 - QnA Maker.
    - For an up-to-date list, click [here](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=logic-apps%2Ccognitive-services%2Csearch%2Cmonitor). , and select a region where the following services are available:
	     - Application Insights
		 - Azure Search
		 - QnA Maker.
5. Enter a "Base Resource Name", which the template uses to generate names for the other resources.
	- The app service names [Base Resource Name], [Base Resource Name]-config, and [Base Resource Name]-qnamaker must be available. For example, if you select contosofaqplus as the base name, the names contosofaqplus, contosofaqplus-config, and contosofaqplus-qnamaker must be available (not taken); otherwise, the deployment will fail with a conflict error.
	- Remember the base resource name that you selected. We will need it later.
6. Fill in the various IDs in the template:
    - Bot Client ID: The application (client) ID of the Microsoft Teams Bot app
	     - Make sure that the values are copied as-is, with no extra spaces. The template checks that GUIDs are exactly 36 characters.
7. If you wish to change the app name, description, and icon from the defaults, modify the corresponding template parameters.
8. Click on "Review+Create" to start the deployment. It will validate the parameters provided in the template .Once the validation is passed, click on create to start the deployment.
9. Wait for the deployment to finish. You can check the progress of the deployment from the "Notifications" pane of the Azure Portal. It can take more than 10 minutes for the deployment to finish.
10. Once the deployment has finished, you would be directed to a page that has the following fields:
    - botId - This is the Microsoft Application ID for the FAQ Plus bot.
	- appDomain - This is the base domain for the FAQ Plus Bot.
	- configurationAppUrl - This is the URL for the configuration web application.


## Step 3: Deploy remaining resources to your Azure Government subscription
1. Click on the "Deploy to Azure" button below.

[![Deploy To Azure Gov](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FOfficeDev%2Fmicrosoft-teams-apps-faqplus-hybrid%2Fmaster%2FDeployment%2Fazuredeploy-gcc-h.json) 

2. When prompted, log in to your GCC High + Azure Government subscription.
3. Azure will create a "Custom deployment" based on the ARM template and ask you to fill in the template parameters.
4. Select a subscription and resource group.
	- We recommend creating a new resource group.
	- The resource group location MUST be in a data center that supports Azure Search service.
5. Enter the same Base Resource Name, that you gave while bots deployment in your Azure commercial.
	- The app service names [Base Resource Name], must be available. For example, if you select contosoworkplaceawards as the base name, the names contosoworkplaceawards must be available (not taken); otherwise, the deployment will fail with a Conflict error.
	- Remember the base resource name that you selected. We will need it later.
6. Fill in the various IDs in the template:
	- botClientId - The application (client) ID registered in Step 1.
	- botClientSecret - The client secret registered in Step 1.
	- isGCCHigh - True if deployment tenant is GCC government tenant.
	- tenantId - The tenant ID registered in Step 1. If your Microsoft Teams tenant is same as Azure subscription tenant, then we would recommend to keep the default values.
	- botAppInsightsKey - Navigate to App Insights created during Azure commercial deployment. Under left menu, select Properties under Configure. Copy the instrumentation key. 
	- configAppClientId - The application (client) ID of the configuration app
	- configAdminUPNList - a semicolon-delimited list of users who will be allowed to access the configuration app.
		- For example, to allow Megan Bowen (meganb@contoso.com) and Adele Vance (adelev@contoso.com) to access the configuration app, set this parameter to `meganb@contoso.com;adelv@contoso.com`.
        * You can change this list later by going to the configuration app service's "Configuration" blade.
	-  Make sure that the values are copied as-is, with no extra spaces.The template checks that GUIDs are exactly 36 characters. 
7. If you wish to change the Git repo Url from the defaults, modify the corresponding template parameters.
8. Click on "Review+Create" to start the deployment.It will validate the parameters provided in the template. Once the validation is passed,click on create to start the deployment.
9. Wait for the deployment to finish. You can check the progress of the deployment from the "Notifications" pane of the Azure Portal.It can take more than 20 minutes for the deployment to finish.


## Step 4: Set up authentication for the configuration app

1. Note the location of the configuration app that you deployed, which is `https://[BaseResourceName]-config.azurewebsites.us`. For example, if you chose "contosofaqplus" as the base name, the configuration app will be at `https://contosofaqplus-config.azurewebsites.us`

2. Go back to the "App Registrations" page [here](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview).

3. Click on the configuration app in the application list. Under "Manage", click on "Authentication" to bring up authentication settings.

4. Click on Add a platform, select Web.

![Adding Redirect URI1](https://github.com/OfficeDev/microsoft-teams-apps-faqplus/wiki/Images/AuthenticationImage1.png)

5. Add new entry to "Redirect URIs":
	If your configuration app's URL is https://contosofaqplus-config.azurewebsites.us, then add the following entry as the Redirect URIs:
	- https://contosofaqplus-config.azurewebsites.us

	Note: Please refer to Step 4.1 for more details about the URL. 

6. Under "Implicit grant", check "ID tokens" and "Access tokens". The reason to check "ID tokens" is because you are using only the accounts on your current Azure tenant and using that to authenticate yourself in the configuration app. Click configure.

![Adding Redirect URI2](https://github.com/OfficeDev/microsoft-teams-apps-faqplus/wiki/Images/AuthenticationImage2.png)

7. Add new entries to "Redirect URIs":
	If your configuration app's URL is https://contosofaqplus-config.azurewebsites.us, then add the following entry as the Redirect URIs:
	- https://contosofaqplus-config.azurewebsites.us/signin
	- https://contosofaqplus-config.azurewebsites.us/configuration

![Adding Redirect URI3](https://github.com/OfficeDev/microsoft-teams-apps-faqplus/wiki/Images/AuthenticationImage3.png)

8. Click "Save" to commit your changes.

## Step 5: Create the QnA Maker knowledge base

Create a knowledge base on the [QnA Maker Azure Gov portal](https://www.qnamaker.azure.us/Create), following the instructions in the QnA Maker documentation [QnA Maker documentation](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/tutorials/create-publish-query-in-portal#create-a-knowledge-base).

Skip the step, "Create a QnA service in Microsoft Azure", because the ARM template that you deployed in Step 3 "Deploy to your Azure subscription" already created the QnA service. Proceed directly to the next step, "Connect your QnA service to your KB".

Use the following values when connecting to the QnA service:

* **Microsoft Azure Directory ID**: The tenant associated with the Azure subscription used for Step 3.
* **Azure subscription name**: The Azure subscription to which the ARM template was deployed in Step 3.
* **Azure QnA service**: The QnA service was created during the deployment. This is the same as the "Base resource name"; for example, if you chose "contosofaqplus" as the base name, the QnA Maker service will be named `contosofaqplus`.

![Screenshot of settings](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

### Multi-Turn Enablement
With the new updates to the FAQ Plus app template, the knowledge base can now support multi-turn conversations. To understand the basics of multi-turn conversations, navigate to the [QnA Maker documentation](https://docs.microsoft.com/en-us/azure/cognitive-services/QnAMaker/how-to/multiturn-conversation#what-is-a-multi-turn-conversation) to understand about multi-turn conversations. To enable multi-turn on the newly created knowledge base, go to this [link](https://docs.microsoft.com/en-us/azure/cognitive-services/QnAMaker/how-to/multiturn-conversation#create-a-multi-turn-conversation-from-a-documents-structure) to enable multi-turn extraction. 

* Note: For best practices with regards to formatting and document structure, please follow the guidelines [here](https://docs.microsoft.com/en-us/azure/cognitive-services/QnAMaker/how-to/multiturn-conversation#building-your-own-multi-turn-document).

After [publishing the knowledge base](https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/tutorials/create-publish-query-in-portal#publish-to-get-knowledge-base-endpoints), note the knowledge base ID (see screenshot).

![Screenshot of the publishing page](https://github.com/OfficeDev/microsoft-teams-apps-faqplus/wiki/Images/kb_publishing.png)

Remember the knowledge base ID: we will need it in the next step.

## Step 6: Finish configuring the FAQ Plus app

1. Go to the configuration app, which is at `https://[BaseResourceName]-config.azurewebsites.us`. For example, if you chose “contosofaqplus” as the base name, the configuration app will be at `https://contosofaqplus-config.azurewebsites.us`.

2. You will be prompted to log in with your credentials. Make sure that you log in with an account that is in the list of users allowed to access the configuration app.

![Config web app page](https://github.com/OfficeDev/microsoft-teams-apps-faqplus/wiki/Images/config-web-app-login.png)

3. Get the link to the team with your experts from the Teams client. To do so, open Microsoft Teams, and navigate to the team. Click on the "..." next to the team name, then select "Get link to team".

![Get link to Team](https://github.com/OfficeDev/microsoft-teams-apps-faqplus/wiki/Images/get-link-to-Team.png)

Click on "Copy" to copy the link to the clipboard.

![Link to team](https://github.com/OfficeDev/microsoft-teams-apps-faqplus/wiki/Images/link-to-team.png)

4. Paste the copied link into the "Team Id" field, then press "OK".

![Add team link form](https://github.com/OfficeDev/microsoft-teams-apps-faqplus/wiki/Images/fill-in-team-link.png)

5. Enter the QnA Maker knowledge base ID into the "Knowledge base ID" field, then press "OK".

6. Customize the "Welcome message" that's sent to your End-users when they install the app. This message supports basic markdown, such as bold, italics, bulleted lists, numbered lists, and hyperlinks. See [here](https://docs.microsoft.com/en-us/adaptive-cards/authoring-cards/text-features#markdown) for complete details on what Markdown features are supported.

### Notes

Remember to click on "OK" after changing a setting. To edit the setting later, click on "Edit" to make the text box editable.

## Step 7: Create the Teams app packages

Create two Teams app packages: one for end-users to install personally, and one to be installed to the experts' team.

1. Open the `Manifest\manifest_enduser.json` file in a text editor.

2. Change the placeholder fields in the manifest to values appropriate for your organization.

* `developer.name` ([What's this?](https://docs.microsoft.com/en-us/microsoftteams/platform/resources/schema/manifest-schema#developer))

* `developer.websiteUrl`

* `developer.privacyUrl`

* `developer.termsOfUseUrl`

3. Replace all the occurrences of `<<botId>>` placeholder to your Azure AD application's ID from above. This is the same GUID that you entered in the template under "Bot Client ID".

4. In the "validDomains" section, replace all the occurrences of `<<appDomain>>` with your Bot App Service's domain. This will be `[BaseResourceName].azurewebsites.us`. For example, if you chose "contosofaqplus" as the base name, change the placeholder to `contosofaqplus.azurewebsites.us`.

5. Save and Rename `manifest_enduser.json` file to a file named `manifest.json`.

6. Create a ZIP package with the `manifest.json`,`color.png`, and `outline.png`. The two image files are the icons for your app in Teams.
* Name this package `faqplus-enduser.zip`, so you know that this is the app for end-users.
* Make sure that the 3 files are the _top level_ of the ZIP package, with no nested folders.
![File Explorer](https://github.com/OfficeDev/microsoft-teams-apps-faqplus/wiki/Images/file-explorer.png)

7. Rename the `manifest.json` file to `manifest_enduser.json` for reusing the file.

8.  Open the `Manifest\manifest_sme.json` file in a text editor.

9. Repeat the steps from 2 to 4 to replace all the placeholders in the file.

10. Save and Rename `manifest_sme.json` file to a file named `manifest.json`.

11. Create a ZIP package with the `manifest.json`,`color.png`, and `outline.png`. The two image files are the icons for your app in Teams.
* Name this package `faqplus-experts.zip`, so you know that this is the app for sme/experts.
* Make sure that the 3 files are the _top level_ of the ZIP package, with no nested folders.
![File Explorer](https://github.com/OfficeDev/microsoft-teams-apps-faqplus/wiki/Images/file-explorer.png)

12. Rename the `manifest.json` file to `manifest_sme.json` for reusing the file.

## Step 8: Run the apps in Microsoft Teams

1. If your tenant has sideloading apps enabled, you can install your app by following the instructions [here](https://docs.microsoft.com/en-us/microsoftteams/platform/concepts/apps/apps-upload#load-your-package-into-teams)

2. You can also upload it to your tenant's app catalog so that it can be available for everyone in your tenant to install. See [here](https://docs.microsoft.com/en-us/microsoftteams/tenant-apps-catalog-teams)

3. Install the experts' app (the `faqplus-experts.zip` package) to your team of subject-matter experts. This **MUST** be the same team that you selected in Step 6.3 above.

    **NOTE:** Do NOT use app permission policies to restrict the experts' app to the members of the subject matter experts team. Teams does not support applying different policies to the same bot via two different app packages. If you do this, you may find that the end-user app does not respond to some users.

4. Install the end-user app (the `faqplus-enduser.zip` package) to your users.

## Troubleshooting

Please see our [Troubleshooting](https://github.com/OfficeDev/microsoft-teams-apps-faqplus/wiki/Troubleshooting) page.