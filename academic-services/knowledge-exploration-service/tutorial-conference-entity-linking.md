---
title: Link private data with MAG entities using MAKES
description: Step by step tutorial for linking prviate data with MAG entities using MAKES
ms.topic: tutorial
ms.date: 09/27/2020
---

# Link private data with MAG entities using MAKES

This is the third part of building an knowledge application for KDD conference. 

In this tutorial, we'll focus on linking outside data sources (KDD 2019 oral presentations) with MAG entities to enrich the search experience. After linking the oral presentation data using MAKES, users should be able to search for presentations by fields of study, abstract, authors, and affiliations. 

We will start with leveraging the default MAKES grammar for entity linking. Next, we will build a MAKES index that supports oral presentation search. Finally, we'll create a UI for users to find oral presentations.

## Prerequisite

- [Microsoft Academic Knowledge Service (MAKES) subscription](get-started-setup-provisioning.md)
- Follow [Create API instance](get-started-create-api-instances.md) to deploy a MAKES instance
- Understand how to design, build, deploy a custom index, and create a corresponding UI by following [Create a filterable paper list using MAKES](tutorial-conference-filterable-paperlist.md) 
- [Install Powershel 7](https://docs.microsoft.com/powershell/scripting/install/installing-powershell?view=powershell-7)

## Download and unzip tutorial resources

1. Download and unzip the MAKES management tool (kesm.exe) from your latest MAKES release.
    (You can find this in your MAKES storage account under:
    **https://<makes_storage_account>.blob.core.windows.net/makes/<makes_release>/tools/kesm.zip**)

1. Download and unzip tutorial resources from [here](TutorialResources.zip).

## Leverage Interpret API to Link data with MAG entities

MAKES's Interpret API interprets a natural language query using a garmmar. The default grammar MAKES API is designed for general paper entity search. The grammar can parse out title, author, affiliation, fields of study, etc in a natural language query to find papers. Since the Interpret API provides a confidence score(in log probability form) with each interpretation, we can set a minimum confidence score and link MAG entity using the default grammar. To learn more about the default MAKES grammar, see [MAKES example grammar](how-to-grammar.md#example-grammar).

### Determine entity linking strategy

Inspect the 2019 KDD oral presentation data from **<tutorial_resource_root>/kddOralPresentation2019Data.json**. These oral presentation data are drived from the [Official 2019 KDD oral presentation agenda](https://www.kdd.org/kdd2019/docs/Applied_Data_Science_and_Research_Track_Paper_Oral_Presentations.pdf). We see that the presentation titles/paper titles are nicely seperated as a indiviual attribute. We can call Interpret API for each presentation and conduct a "paper title" search to link oral presentations with paper entities in MAG.

Since we'll be linking data by presentations, we can transform the oral presentation data from session entity based data to oral presentation entity based data for easier data manipulation. We have done this step for the transformed data is avaliable at **tutorial_resource_root>/kddOralPresentation2019Data.flat.json**. Below is an example of the transformed oral presentation entity:

```json
{
    "Title": "Auto-Keras: An Efficient Neural Architecture Search System",
    "Presenters": "Haifeng Jin (Texas A&M University); Qingquan Song (Texas A&M University); Xia Hu (Texas A&M University)",
    "Session": {
      "Title": "Applied Data Science Track Session ADS1: Auto-ML and Development Frameworks",
      "Location": "Summit 1, Ground Level, Egan Center",
      "Chair": "Gabor Melli (Sony PlayStation)",
      "StartDateTime": "Tuesday, August 6th, 2019 10:00AM",
      "EndDateTime": "Tuesday, August 6th, 2019 12:00AM"
    }
}
```

### Use default MAKES grammar to link oral presentation data via paper title match

We've created a sample powershell script to link the 2019 KDD oral presentation data with MAG entities using a MAKES instance. To run the script:

    1. Follow [Create API instance](get-started-create-api-instances.md) to deploy a MAKES instance if you haven't already
    1. Open **<tutorial_resource_root>/linkKddOralPresentationData.ps1** and modify the **makesEndpoint** variable and set it to the URL of the MAKES instance you deployed
    1. Open up a terminal and execute the script

You should see console output similar to below:

![linkKddOralPresentation script console output](linkKddOralPresentation-script-console-output.png)

The first column represents the confidence score (in log probability form) for each entity linking attempt via title match. The script has a preset minimum confidence score for linking. You can modify the **minLogProbForLinking** variable to change the linking behavior.

### Leverage MAG entities to enrich oral presentation data

After linking a oral presentation with a paper entity sucessfully, we can then leverage the MAG paper entity attributes to enrich information of the linked oral presentation. 

In the entity linking script provided, we've added various MAG paper entity attributes such as fields of study, normalized title, abstract for search and display. To merge these additional information, see **Merge-MagEntity(oralPresentationEntity, magPaperEntity)** function in **<tutorial_resource_root>/linkKddOralPresentationData.ps1**

## Build and host index with linked oral presentation data

After linking and enriching oral presentation data with MAG paper entities, we are ready to build a MAKES index to power presentation filtering and search. 

### Create index schema for linked oral presentation entities

The first step of building MAKES index is to create a index schema. We've included a sample schema that models the linked oral presentation entities for search and filtering. See **<tutorial_resource_root>/kddOralPresentationSchema.json** for the sample schema.

### Build index

1. Open up a commandline console, change directory to the root of the tutorial resource folder, and build the index with the following command:

    ```cmd
    kesm.exe BuildIndexLocal --SchemaFilePath <tutorial_resource_root>/kddOralPresentationSchema.json --EntitiesFilePath kddOralPresentation2019Data.flat.linked.json --OutputIndexFilePath <tutorial_resource_root>/kdd2019OralPresentations.kes --IndexDescription "KDD 2019 Oral Presentations"
    ```
    ## Deploy MAKES API Host with a custom index

We are now ready to set up a MAKES API instance with a custom index.

1. Upload the built, custom index to your MAKES storage account. You can do so by using following [Blob Upload from Azure Portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). If you use cloud index build, you may skip this step.

1. Run CreateHostResources to create a MAKES hosting virtual machine image.

    ```cmd
    kesm.exe CreateHostResources --MakesPackage https://<makes_storage_account_name>.blob.core.windows.net/makes/<makes_release_version> --HostResourceName <makes_host_resource_name>
    ```
>[!NOTE]
> BuildIndexLocal command is only avaliable on win-x64 version of kesm


### Deploy MAKES API Host with a custom index

We are now ready to set up a MAKES API instance with a custom index.

1. Upload the built, custom index to your MAKES storage account. You can do so by using following [Blob Upload from Azure Portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). If you use cloud index build, you may skip this step.

1. Run CreateHostResources to create a MAKES hosting virtual machine image.

    ```cmd
    kesm.exe CreateHostResources --MakesPackage https://<makes_storage_account_name>.blob.core.windows.net/makes/<makes_release_version> --HostResourceName <makes_host_resource_name>
    ```

> [!NOTE]
> If your account is connected to multiple Azure Directories or Azure Subscriptions, you'll also have to specify the **--AzureActiveDirectoryDomainName** and/or **--AzureSubscriptionId** parameters. See [Command Line Tool Reference](reference-makes-command-line-tool.md#common-azure-authentication-parameters) for more details.

1. Run DeployHost command and use the "--MakesIndex" parameter to load the custom 2019 KDD oral presentation index we've built.

    ```cmd
     kesm.exe DeployHost --HostName "<makes_host_instance_name>" --MakesPackage "https://<makes_storage_account_name>.blob.core.windows.net/makes/<makes_release_version>/"  --MakesHostImageId "<id_from_previous_command_output>" --MakesIndex "<custom_index_url>"
    ```

For more detailed deployment instructions, See [Create API Instances](get-started-create-api-instances.md#create-makes-hosting-resources)

> [!NOTE]
> Since the index we're hosting is relatively small, you can reduce Azure consumption for the tutorial MAKES host instance by using the "--HostMachineSku" parameter and set the SKU to "Standard_D2_V2".

## Create filterable oral presentation list UI

We now have a backend API to serve our KDD oral presentation data. The last step is to create the client application to showcase the filterable and search capability. The client application will support search, smart filter generation, and data retrival via Interpret, Histogram, and Evaluate APIs. For more information on building knowledge application client for search and filtering, see [Create a filterable paper list using MAKES](tutorial-conference-filterable-paperlist.md) for more details.

### Use sample UI code to see them in action

We've created a sample client app written in javascript. You should be able to see the conference application with a filterable paper list by wiring up the MAKES host URL to your deployed MAKES instance. For more to run the client app information, see **<tutorial_resource_root>/ConferenceApp_FilterableOralPresentation/README.md**