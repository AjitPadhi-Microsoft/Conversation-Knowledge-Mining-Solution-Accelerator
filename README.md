# Conversation knowledge mining solution accelerator

MENU: [**USER STORY**](#user-story) \| [**ONE-CLICK DEPLOY**](#one-click-deploy)  \| [**SUPPORTING DOCUMENTS**](#supporting-documents) \|
[**CUSTOMER TRUTH**](#customer-truth)

<h2><img src="/images/readMe/userStory.png" width="64">
<br/>
User story
</h2>

**Solution accelerator overview**

This is a solution accelerator built on top of Azure Cognitive Search
Service and Azure OpenAI Service that leverages LLM to synthesize
post-contact center transcripts for intelligent contact center scenarios. It
shows how raw transcripts are converted into simplified customer call
summaries to extract valuable insights around product and service
performance.

**Scenario**

This scenario shows how a data professional within a travel company contact
center can use AI to quickly analyze call logs and analytics to identify areas 
for improvement.

**Key features**

Azure Cognitive Search and Azure OpenAI can enable new and 
innovative ways to run contact center operations. Post call insights 
can inform data driven actions to drive decisions around staffing and operations.

- **Conversation Summarization and Key Phrase Extraction:** Summarize long conversations into a short paragraph and pull out key phrases that are relevant to the conversation.
- **Batch speech-to-text using Azure Speech:** Transcribe large amounts of audio files asynchronously including speaker diarization and is typically used in post-call analytics scenarios. Diarizations the process of recognizing and separating speakers in mono channel audio data.
- **Sensitive information extraction and redaction:** Identify, categorize, and redact sensitive information in conversation transcription
- **Sentiment analysis and opinion mining:** Analyze transcriptions and associate positive, neutral, or negative sentiment at the utterance and conversation-level.

**Below is an image of the solution accelerator.**\
\
![image](/images/readMe/image2.png)

<h2><img src="/images/readMe/oneClickDeploy.png" width="64">
<br/>
One-click deploy
</h2>

### Prerequisites

These requirements must be met before the solution accelerator is installed.

-   If you would like to use an **existing** OpenAI resource in your Azure tenant (instead of opting to create a new one), you'll need an Azure OpenAI resource with a deployed model that has the following
    settings:

    -   Model: gpt-35-turbo

    -   Model Version: 0613

    -   Tokens per Minute: 22K

### Products used/licenses required

-   Azure Cognitive Search

-   Azure OpenAI

-   Azure storage account

-   The user deploying the template must have permission to create
    resources and resource groups.


### Deploy

The Azure portal displays a pane that allows you to easily provide parameter values. The parameters are pre-filled with the default values from the template.

Once the deployment is completed, start processing your audio files by adding them to the "audio-input" container in the "storage account" in your "Resource Group". 
To navigate the Web UI, check the "App Service" resource in your "Resource Group".


The template builds on top of the [Ingestion Client for Speech service](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/ingestion-client).
Please check here for detailed parameters explanations: [Getting started with the Ingestion Client](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/ingestion/ingestion-client/Setup/guide.md)

Learn more on how to configure your [Azure OpenAI prompt here](#integrate-your-openai-prompt)

### Solution accelerator architecture
![image](/images/readMe/image4.png)

### **How to install/deploy**

1. Click the following deployment button to create the required resources for this accelerator directly in your Azure Subscription.

   [![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fbrittneek%2Customer-Service-Conversational-Insights-with-Azure-OpenAI-Services%2Fckm-v2-dev%2FDeployment%2Fbicep%2Fmain.json)

   1.  Most fields will have a default name set already. You will need to update the following Azure OpenAI settings:

       -  Region - the region where the resoruces will be created in.

       -  Solution Prefix - provide a 6 alphanumeric value that will be used to prefix resources
      
       -  Location - location of resources, by default it will use the resource group's location
           
2.  Create Fabric Workspace
    1.  Navigate to ([Fabric Workspace](https://app.fabric.microsoft.com/))
    2.  Click on Workspaces from left Navigation
        1.  Provide Name of Workspace 
        2.  Provie Description of Workspace (optional)
        3.  Click Apply
    3.  Open Workspace
    4.  Retrieve Workspace ID from URL, refer to documentation additional assistance ([here](https://learn.microsoft.com/en-us/fabric/admin/portal-workspace#identify-your-workspace-id))

3.   Deploy Fabric resources and artifacts
     1.   Navigate to ([Azure Portal](https://portal.azure.com/))
     2.   Click on Azure Cloud Shell in the top right of navigation Menu (add image)
     3.   Run the run the following command: 
          1.   az login
          2.   rm -rf ./ckm
          3.   git clone https://github.com/microsoft/Customer-Service-Conversational-Insights-with-Azure-OpenAI-Services/blob/ckm-v2-dev/
          4.   cd ./ckm-v2-bk/Deployment/scripts/fabric_scripts
          5.   sh ./run_fabric_items_scripts.sh keyvault_param workspaceid_param solutionprefix_param
               1.   keyvault_param - the name of the keyvault that was created in Step 1
               2.   workspaceid_param - the workspaceid created in Step 2
               3.   solutionprefix_param - prefix used to append to lakehouse upon creation
     4.  Get Fabric Lakehouse Connection details:
         1.   Once deployment is complete, navigate to Fabric Workspace
         2.   Find Lakehouse in workspace (ex.lakehouse_*solutionprefix_param*), click on the SQL Analytics Endpoint
         3.   Click on Settings icon
         4.   In right panel, click copy icon for SQL connection String (needed for next step)
         5.   Copy the Lakehouse name from workspace (needed for next steps)
    1.   Wait 10-15 minutes to allow the data pipelines to finish processing then proceed to next step.
4.   Deploy Power BI Report
     1.   Download the .pbix file from repo (link).
     2.   Open Power BI report in Power BI Dashboard
     3.   Click on Transform Data menu option from the Task Bar
     4.   Click Data source settings
     5.   Click Change Source...
     6.   Input the Server link (from Fabric Workspace)
     7.   Input Database name (from Fabric Workspace)
     8.   Click OK
     9.   Click Edit Permissions
     10.  If not signed in, sign in your credentials and proceed to click OK
     11.  Click Close
     12.  Report should refresh with need connection.

### Integrate your OpenAI Prompt
You can add your Azure OpenAI prompt to extract specific entities in the template parameter OPENAI_PROMPT.
<br>
The defined keys have to be added in the OPENAI_PROMPT_KEYS parameter as well, to enable the data Push to the Azure Cognitive Search index.
<br>
Please be sure to set up both parameters accordingly to your entities name.

| Environment variable | Default value | Note |
|--|--|--|
|OPENAI_PROMPT | Execute these tasks:<br>&#8226; Summarize the conversation, key: summary<br>&#8226; Is the customer satisfied with the interaction with the agent, key: satisfied<br> Answer in JSON machine-readable format, using the keys from above.<br> Format the ouput as JSON object called 'results'. Pretty print the JSON and make sure that is properly closed at the end.<br>| The prompt to be used with OpenAI, please define the keys in the setting below as well |
|OPENAI_PROMPT_KEYS | summary:Edm.String:False,satisfied:Edm.String:True|The prompt keys to use for the OpenAI API. Format: key,SearchType,Facetable e.g. key1:Edm.String:False,key2:Edm.String:True,key3:Edm.String:True | 

### Modify the prompt after deployment

You can modify the Azure OpenAI prompt after the deployment by modifying the Azure Function application settings ("OPENAI_PROMPT", "OPENAI_PROMPT_KEYS") and creating the required field in the Azure Cognitive Search index.

<h2><img src="/images/readMe/supportingDocuments.png" width="64">
<br/>
Supporting documents
</h2>

Launch the application by navigating to your Azure resource group,
choosing the app service resource, and clicking on the default domain.
On the first launch, your application will not show any summaries. You
will need to upload your data files in the correct format (instructions available below the following image) to see summarized results of conversations.

![image](/images/readMe/image12.png)

-   To upload files, click the \'Upload file\' button. Here you can
    upload .wav files of conversations, or conversations that have been
    formatted to the specified JSON format. After uploading, allow 10
    minutes for the files to fully process and appear on the Call
    summaries page.

    -   Files can also be uploaded directly to the blob storage. Audio
        files should be uploaded to the audio-input folder, JSON files
        should be uploaded to the conversationkm-full folder. When uploading
        please follow this [format](ConversationalDataFormat.md).

    -   If 10 minutes have passed and you still do not see results,
        empty your cache and do a hard refresh on your browser.

-   To filter the conversations, use the filters on the left side of the
    screen. Alternatively, you can filter the conversations by clicking
    a keyphrase on a summary card.

-   To search for a specific topic, type what you want to find into the
    search bar, for example, if you want to find conversations where the
    customer is staying at the Ritz, you can type \"ritz\" or \"ritz
    hotel\" and the relevant conversations will surface.

-   To view a full conversation, click anywhere on the summary card.
    This will bring up the timeline of the conversation. You will also
    be able to view the full transcription and the meta data for the
    conversation. In this view you can search through this specific
    conversation using the search bar and by clicking on the keyphrases.

-   To view the relationships between topics, click the \'View entity
    map\' button. Here you will choose a Facet and number of nodes and
    be able to view the relationships between the phrases.

### How to customize the UI

Using the options on the customize page, you can upload your own logo
and set the background and text colors for the navigation bar. For
information on how to make other customizations, refer to the UI
documentation.

-   From the home page, click on the customize button located in the upper right hand corner of the page.

![image](/images/readMe/image10.png)

![image](/images/readMe/image14.png)

-   Select the rectangle next to background color to use the color
    picker to select a new background color for the header.

-   Select the rectangle next to text color to use the color picker to
    select a new footer text color.

![image](/images/readMe/image15.png)

-   Select the choose file button to upload a new image.

![image](/images/readMe/image16.png)

Click save to apply the updates.
<br>
<br/>
<br>
<br/>

**Troubleshooting**
-   [Troubleshooting documentation](Troubleshooting.md)
  
**Extensibility**

-   [Future extensibility documentation](Extensibility.md)

**More info**
-   [Solution
    architecture](SolutionArchitecture.md)

-   [Resource group walkthrough](SolutionArchitecture.md#resource-group-walkthrough)

-   [Updating the UI](GBB.ConversationalKM.WebUI)

<br/>
<br>
<h2><img src="/images/readMe/customerTruth.png" width="64">
</br>
Customer truth
</h2>
Customer stories coming soon. For early access, contact: nfelton@microsoft.com

<br/>
<br/>
<br/>

---

## Disclaimers

This Software requires the use of third-party components which are governed by separate proprietary or open-source licenses as identified below, and you must comply with the terms of each applicable license in order to use the Software. You acknowledge and agree that this license does not grant you a license or other right to use any such third-party proprietary or open-source components.  

To the extent that the Software includes components or code used in or derived from Microsoft products or services, including without limitation Microsoft Azure Services (collectively, “Microsoft Products and Services”), you must also comply with the Product Terms applicable to such Microsoft Products and Services. You acknowledge and agree that the license governing the Software does not grant you a license or other right to use Microsoft Products and Services. Nothing in the license or this ReadMe file will serve to supersede, amend, terminate or modify any terms in the Product Terms for any Microsoft Products and Services. 

You must also comply with all domestic and international export laws and regulations that apply to the Software, which include restrictions on destinations, end users, and end use. For further information on export restrictions, visit https://aka.ms/exporting. 

You acknowledge that the Software and Microsoft Products and Services (1) are not designed, intended or made available as a medical device(s), and (2) are not designed or intended to be a substitute for professional medical advice, diagnosis, treatment, or judgment and should not be used to replace or as a substitute for professional medical advice, diagnosis, treatment, or judgment. Customer is solely responsible for displaying and/or obtaining appropriate consents, warnings, disclaimers, and acknowledgements to end users of Customer’s implementation of the Online Services. 

You acknowledge the Software is not subject to SOC 1 and SOC 2 compliance audits. No Microsoft technology, nor any of its component technologies, including the Software, is intended or made available as a substitute for the professional advice, opinion, or judgement of a certified financial services professional. Do not use the Software to replace, substitute, or provide professional financial advice or judgment.  

BY ACCESSING OR USING THE SOFTWARE, YOU ACKNOWLEDGE THAT THE SOFTWARE IS NOT DESIGNED OR INTENDED TO SUPPORT ANY USE IN WHICH A SERVICE INTERRUPTION, DEFECT, ERROR, OR OTHER FAILURE OF THE SOFTWARE COULD RESULT IN THE DEATH OR SERIOUS BODILY INJURY OF ANY PERSON OR IN PHYSICAL OR ENVIRONMENTAL DAMAGE (COLLECTIVELY, “HIGH-RISK USE”), AND THAT YOU WILL ENSURE THAT, IN THE EVENT OF ANY INTERRUPTION, DEFECT, ERROR, OR OTHER FAILURE OF THE SOFTWARE, THE SAFETY OF PEOPLE, PROPERTY, AND THE ENVIRONMENT ARE NOT REDUCED BELOW A LEVEL THAT IS REASONABLY, APPROPRIATE, AND LEGAL, WHETHER IN GENERAL OR IN A SPECIFIC INDUSTRY. BY ACCESSING THE SOFTWARE, YOU FURTHER ACKNOWLEDGE THAT YOUR HIGH-RISK USE OF THE SOFTWARE IS AT YOUR OWN RISK.  
