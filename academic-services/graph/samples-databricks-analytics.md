---
title: PySpark samples for Microsoft Academic Graph
description: Perform analytics for Microsoft Academic Graph using PySpark on Azure Databricks
services: microsoft-academic-services
ms.topic: tutorial
ms.service: microsoft-academic-services
ms.date: 9/23/2020
---
# PySpark analytics samples for Microsoft Academic Graph

Illustrates how to perform analytics for Microsoft Academic Graph using PySpark on Azure Databricks.

## Sample projects

* [Extract affiliation ID for an affiliation](https://github.com/Azure-Samples/microsoft-academic-graph-pyspark-samples/blob/master/src/Lab1_ExtractAffiliation.py)
* [Join conferences and journals as venues](https://github.com/Azure-Samples/microsoft-academic-graph-pyspark-samples/blob/master/src/Lab2_UnionVenues.py)
* [Get publications from an affiliation](https://github.com/Azure-Samples/microsoft-academic-graph-pyspark-samples/blob/master/src/Lab3_JoinPaperAuthorAffiliation.py)
* [Get authors from an affiliation and the publication details](https://github.com/Azure-Samples/microsoft-academic-graph-pyspark-samples/blob/master/src/Lab4_CreateTable_Extract.py)
* [Get fields of studiy for an affiliation](https://github.com/Azure-Samples/microsoft-academic-graph-pyspark-samples/blob/master/src/Lab5_CreateTableByTvf.py)
* [Get collaborated affiliations of an affiliation using its publications](https://github.com/Azure-Samples/microsoft-academic-graph-pyspark-samples/blob/master/src/Lab6_GetPartnerData.py)
* [Get publication and citation counts by year](https://github.com/Azure-Samples/microsoft-academic-graph-pyspark-samples/blob/master/src/Lab7_GroupByYear.py)

## Prerequisites

Before running these examples, you need to complete the following setups:

* Set up provisioning of Microsoft Academic Graph to an Azure blob storage account. See [Get Microsoft Academic Graph on Azure storage](get-started-setup-provisioning.md).

* Set up an Azure Databricks service. See [Set up Azure Databricks](get-started-setup-databricks.md).

* Download or clone the [samples repository](https://github.com/Azure-Samples/microsoft-academic-graph-pyspark-samples)

## Gather the information that you need

Before you begin, you should have these items of information:

   :heavy_check_mark:  The name of your Azure Storage (AS) account containing MAG dataset from [Get Microsoft Academic Graph on Azure storage](get-started-setup-provisioning.md#note-azure-storage-account-name-and-primary-key).

   :heavy_check_mark:  The access key of your Azure Storage (AS) account from [Get Microsoft Academic Graph on Azure storage](get-started-setup-provisioning.md#note-azure-storage-account-name-and-primary-key).

   :heavy_check_mark:  The name of the container in your Azure Storage (AS) account containing MAG dataset.

   :heavy_check_mark:  The name of the output container in your Azure Storage (AS) account.

## Import MagClass notebook

In this section, you will import the MagClass.py notebook to Azure Databricks workspace. You will run this utility notebook from other notebooks later.

Follow instructions in [Import MagClass Notebook](import-MagClass.md).

## Create a new notebook

In this section, you create a new notebook in the Azure Databricks workspace.

1. On the left, select **Workspace**. From the **Workspace** drop-down, select **Create** > **Notebook**. Optionally, you could create this notebook in **Users** level.

    ![Create a notebook in Databricks](media/databricks/databricks-create-notebook.png "Create notebook in Databricks")

1. In the **Create Notebook** dialog box, enter a name for the notebook. Select **Python** as the language.

    ![Provide details for a notebook in Databricks](media/databricks/create-notebook.png "Provide details for a notebook in Databricks")

1. Select **Create**.

## Create first notebook cell

In this section, you create the first notebook cell to run the MagClass notebook.

1. Copy and paste following code block into the first cell.

   ```python
   %run "./MagClass"
   ```

1. Press the **SHIFT + ENTER** keys to run the code in this block. It defines MicrosoftAcademicGraph class.

## Define configration variables

In this section, you add a new notebook cell and define configration variables.

1. Copy and paste following code block into the first cell.

   ```python
   # Define configration variables
   AzureStorageAccount = '<AzureStorageAccount>'     # Azure Storage (AS) account containing MAG dataset
   AzureStorageAccessKey = '<AzureStorageAccessKey>' # Access Key of the Azure Storage (AS) account
   MagContainer = '<MagContainer>'                   # The container name in Azure Storage (AS) account containing MAG dataset, usually in forms of mag-yyyy-mm-dd
   OutputContainer = '<OutputContainer>'             # The container name in Azure Storage (AS) account where the output goes to
   ```

1. In this code block, replace `<AzureStorageAccount>`, `<AzureStorageAccessKey>`, and `<MagContainer>` placeholder values with the values that you collected while completing the prerequisites of this sample.

   |Value  |Description  |
   |---------|---------|
   |**`<AzureStorageAccount>`** | The name of your Azure Storage account. |
   |**`<AzureStorageAccessKey>`** | The access key of your Azure Storage account. |
   |**`<MagContainer>`** | The container name in Azure Storage account containing MAG dataset, usually in the form of **mag-yyyy-mm-dd**. |
   |**`<OutputContainer>`** | The container name in Azure Storage (AS) account where the output goes to |

1. Press the **SHIFT + ENTER** keys to run the code in this block.

## Create MicrosoftAcademicGraph and AzureStorageUtil instances

In this section, you create a MicrosoftAcademicGraph instance to access MAG dataset, and an AzureStorageUtil instance to access other Azure Storage files.

1. Copy and paste the following code block in a new cell.

   ```python
   # Create a MicrosoftAcademicGraph instance to access MAG dataset
   mag = MicrosoftAcademicGraph(container=MagContainer, account=AzureStorageAccount, key=AzureStorageAccessKey)

   # Create a AzureStorageUtil instance to access other Azure Storage files
   asu = AzureStorageUtil(container=OutputContainer, account=AzureStorageAccount, key=AzureStorageAccessKey)
   ```

1. Press the **SHIFT + ENTER** keys to run the code in this block.

## Run scripts in the repository

1. Copy content in a script and paste into a new cell.

1. Press the **SHIFT + ENTER** keys to run the code in this cell. Please note that some scripts take more than 10 minutes to complete.

## View results with Microsoft Azure Storage Explorer

![View result with Microsoft Azure Storage Explorer](media/samples-view-pyspark-script-results.png "View result with Microsoft Azure Storage Explorer")

## Resources

* [Create an Azure Databricks service](https://azure.microsoft.com/services/databricks/)
* [Create a cluster for the Azure Databricks service](https://docs.azuredatabricks.net/user-guide/clusters/create.html)
* [Import a Databrick notebook](https://docs.databricks.com/user-guide/notebooks/notebook-manage.html#import-a-notebook)
* [Get started with Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
