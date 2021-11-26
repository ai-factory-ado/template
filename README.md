# Overview
This project template provides a simplified, reusable MLOps template for Azure Machine Learning and Azure DevOps.

Please follow the steps below to use it:

## 1. Create a new Azure DevOps project

* Navigate to https://azuredevopsdemogenerator.azurewebsites.net/environment/createproject
* Click on "Choose Template" and click on the "Private" tab
* Choose the "Github" option and enter the following url: https://github.com/ai-factory-ado/template/blob/main/files/ai-factory-template.zip
* Enter the information on the next page to create a new project

## 2. Update Project Information

In the `project-template-file` repo, replace the string `ai-factory` in the repositories section with the name of your Azure DevOps org in the files:
* `controller/devops-pipelines/deploy-model-training-pipeline.yml`
* `controller/devops-pipelines/deploy-model-batch-scoring.yml`
* `controller/devops-pipelines/deploy-model-to-aks.yml`

## 3. Change Organization and Project Settings

Navigate to Project settings and change the following settings under the Pipeline section:

![image](https://user-images.githubusercontent.com/26466075/143133733-4eb5e7b7-f78d-40b5-9f4e-0dc8f8860562.png)

*Note: If this setting is greyed out, then this setting will first need to be changed in the Github organisation's settings and then at project level. If this is the case, then navigate to the Azure DevOps Organization's settings and change the following 3 settings under the Pipeline section:

![image](https://user-images.githubusercontent.com/26466075/143460635-f1986c97-4c18-4ca3-9d2b-cf014e942676.png)

## 4. Set up Azure Key Vault

This repo requires a number of secrets to create various artefacts in Azure, Azure DevOps and GitHub. In order to keep the secrets secure, an Azure Key Vault with the following secrets needs to be created:

| Secret Name            | Required Value           | Short description                                                                                                           |
| ------------------------ | ------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| AZURE-RM-SERVICE-PRINCIPAL-ID-DEV                 | [Application (client) ID]                 | Client ID of the [Service Principal](https://docs.microsoft.com/en-us/azure/active-directory/develop/quickstart-register-app#register-an-application). This service principal should have enough rights to create a new resource group and provision new Azure Services                           |
| AZURE-RM-SERVICE-PRINCIPAL-KEY-DEV           | [Client Secret]                  | [Client Secret](https://docs.microsoft.com/en-us/azure/active-directory/develop/quickstart-register-app#add-a-client-secret) of the service principal                                                                                                  |
| AZURE-RM-SUBSCRIPTION-ID-DEV           | [Subscription Id]              | Azure Subscription Id                                                                                                     |
| AZURE-RM-SUBSCRIPTION-NAME-DEV  | [Subscription name] | Azure Subscription name |
| AZURE-RM-TENANT-ID | [Subscription Tenand Id]  | [Tenant Id](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/active-directory-how-to-find-tenant) of your Azure subscription               |

After adding all the settings, your Key Vault Secrets should look like this:
![image](https://user-images.githubusercontent.com/525867/135234077-af139012-55fc-4bc7-83a3-9aff2d727478.png)

NOTE: If your subscription name has a space in it, put quotes `""` around your subscription name for the AZURE-RM-SUBSCRIPTION-NAME-DEV secret. Else, the pipeline will fail.

## 5. Set up for the bootstrap Pipeline

* Create a variable group named **`bootstrap-variables-kv`** (under `Pipelines` --> `Library`). The YAML pipeline definitions in this repository refer to this variable group by name.

* The "Link secrets from an Azure Key Vault as variables" button should be set and the variable group should be linked to the Azure Key Vault created in step 1. Once it it has been linked, all the secrets created above should be added as variables in this variable group.

* Go to the Azure DevOps organization's settings and grant "Create new projects" permission to the Project Collection Build Service account. The name of this account should be "Project Collection Build Service"
 
![image](https://user-images.githubusercontent.com/26466075/143461344-36b80b1f-a4f1-432a-91eb-32a8aa53a358.png)
![image](https://user-images.githubusercontent.com/26466075/143461221-9cef8156-ce28-4eee-b592-6c2cf4083361.png)


## 6. Update parameters for the pipeline

Go to the file named `/azuredevops/pipelines/project-variables.yml` in the `bootstrap` repo in the project and update the variable values as per your requirements. The parameters in this file are used to provision an Azure DevOps project and related artefacts for the new project.

The most important parameter for in this file is the path for the project template. This is the path for the project template repo, which is one of the repos that created in the first step. 

![image](https://user-images.githubusercontent.com/26466075/143450936-08db7633-f727-4c39-b78c-34f1481776fd.png)

## 7. Pipeline Run

Once you commit the `project-variables.yml` file, it should run the bootstrap pipeline. Please note that the first run of the pipeline will request for permission to access the Azure Subscription in order to access the Key Vault. 


![image](https://user-images.githubusercontent.com/26466075/143451462-acd83e32-378d-4f62-b368-388340a1de36.png)


Some notes:

* It is hard to get all the permissions right in the first go, so it'll be very surprising if the pipeline succeeds in the first go.
* If not, then please look for the errors and fix them. The pipeline will eventually run once everything is in place.

