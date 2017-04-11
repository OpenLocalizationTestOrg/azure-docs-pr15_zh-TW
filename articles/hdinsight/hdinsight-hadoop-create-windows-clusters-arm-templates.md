<properties
   pageTitle="HDInsight 使用 Azure 資源管理員範本中建立 Windows 型 Hadoop 叢集 |Microsoft Azure"
    description="瞭解如何建立使用 Azure 資源管理員範本 Azure HDInsight 叢集。"
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/19/2016"
   ms.author="jgao"/>

# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-resource-manager-templates"></a>建立 Windows 型 Hadoop 叢集 HDInsight 使用 Azure 資源管理員範本

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

瞭解如何建立 HDInsight 叢集使用 Azure 資源管理員範本。 如需詳細資訊，請參閱[部署具有 Azure 資源管理員範本的應用程式](../resource-group-template-deploy.md)。 其他叢集建立的工具和功能按一下此頁面上方的索引標籤上選取，或請參閱[叢集建立方法](hdinsight-provision-clusters.md#cluster-creation-methods)。

##<a name="prerequisites"></a>先決條件︰

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


本文中的指示進行之前，您必須具備下列項目︰

- [Azure 訂閱](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- Azure PowerShell 或 Azure CLI

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)] 

### <a name="access-control-requirements"></a>存取控制需求

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="resource-manager-templates"></a>資源管理員範本

資源管理員範本，可以輕鬆地建立 HDInsight 叢集、 其相依資源 （例如預設儲存的帳戶） 及其他資源 （例如，若要使用 Apache Sqoop Azure SQL 資料庫），您的應用程式，在單一、 協同作業。 在範本中，您可以定義所需的應用程式的資源，並指定部署參數以輸入不同環境中的值。 範本包含 JSON 和可用來建構您的部署中的值的運算式。

資源管理員範本來建立 HDInsight 叢集和相依 Azure 儲存體帳戶，請參閱[附錄 A](#appx-a-arm-template)。 若要將範本儲存至您工作站上的檔案中使用文字編輯器。 您將學習如何呼叫使用各種不同的工具的範本。

如需有關資源管理員範本的詳細資訊，請參閱

- [作者 Azure 資源管理員範本](../resource-group-authoring-templates.md)
- [部署 Azure 資源管理員範本與應用程式](../resource-group-template-deploy.md)


## <a name="deploy-with-powershell"></a>使用 PowerShell 部署

下列程序所建立的 HDInsight 叢集。

**若要部署使用資源管理員範本**

1. 儲存您的工作站[附錄 A](#appx-a-arm-template) json 檔案。
2. 如有需要請設定參數。
3. 執行使用下列 PowerShell 指令碼的範本︰

        ####################################
        # Set these variables
        ####################################
        #region - used for creating Azure service names
        $nameToken = "<Enter an Alias>" 
        $templateFile = "C:\HDITutorials-ARM\hdinsight-arm-template.json"
        #endregion

        ####################################
        # Service names and varialbes
        ####################################
        #region - service names
        $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

        $resourceGroupName = $namePrefix + "rg"
        $hdinsightClusterName = $namePrefix + "hdi"
        $defaultStorageAccountName = $namePrefix + "store"
        $defaultBlobContainerName = $hdinsightClusterName

        $location = "East US 2"

        $armDeploymentName = $namePrefix
        #endregion

        ####################################
        # Connect to Azure
        ####################################
        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #endregion

        # Create a resource group
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $Location

        # Create cluster and the dependent storage accounge
        $parameters = @{clusterName="$hdinsightClusterName";clusterStorageAccountName="$defaultStorageAccountName"}

        New-AzureRmResourceGroupDeployment `
            -Name $armDeploymentName `
            -ResourceGroupName $resourceGroupName `
            -TemplateFile $templateFile `
            -TemplateParameterObject $parameters

        # List cluster
        Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName 

    PowerShell 指令碼只設定叢集名稱和儲存體帳戶名稱。  資源管理員範本中，您可以設定其他值。 
    
如需詳細資訊，請參閱[使用 PowerShell 進行部署](../resource-group-template-deploy.md#deploy-with-powershell)。

## <a name="deploy-with-azure-cli"></a>透過 Azure CLI 部署

下列範例會建立叢集其從屬參照儲存帳戶和容器呼叫資源管理員範本︰

    azure login
    azure config mode arm
    azure group create -n hdi1229rg -l "East US 2"
    azure group deployment create "hdi1229rg" "hdi1229" --template-file "C:\HDITutorials-ARM\hdinsight-arm-template.json" -p "{\"clusterName\":{\"value\":\"hdi1229win\"},\"clusterStorageAccountName\":{\"value\":\"hdi1229store\"},\"location\":{\"value\":\"East US 2\"},\"clusterLoginPassword\":{\"value\":\"Pass@word1\"}}"





## <a name="deploy-with-rest-api"></a>部署以 REST API

請參閱[以 REST API 部署](../resource-group-template-deploy.md#deploy-with-the-rest-api)。

## <a name="deploy-with-visual-studio"></a>使用 Visual Studio 中部署

使用 Visual Studio 中，您可以建立資源群組專案，並將其部署到 Azure 中透過使用者介面。 選取要包含在專案中的資源類型，這些資源會自動新增到資源管理員範本。 專案也會提供的 PowerShell 指令碼部署範本。

使用 Visual Studio 與資源群組簡介資訊，請參閱[建立及部署 Visual Studio 透過 Azure 資源群組](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)。

##<a name="next-steps"></a>後續步驟
本文中，您已經學會多種方式可以建立 HDInsight 叢集。 若要深入瞭解，請參閱下列文章︰


- 部署資源的.NET 用戶端文件庫的範例，請參閱[部署資源使用.NET 文件庫和範本](../virtual-machines/virtual-machines-windows-csharp-template.md)。
- 如需在您採取進階的部署應用程式範例，請參閱[佈建及部署 Azure 中預測 microservices](../app-service-web/app-service-deploy-complex-application-predictably.md)。
- 在不同環境中部署您的方案，請參閱[開發和 Microsoft Azure 中的測試環境](../solution-dev-test-environments.md)。
- 若要瞭解 Azure 資源管理員範本的節，請參閱[撰寫範本](../resource-group-authoring-templates.md)。
- 您可以在 Azure 資源管理員範本中使用的函數的清單，請參閱[範本函數](../resource-group-template-functions.md)。



##<a name="appx-a-resource-manager-template"></a>Appx a︰ 資源管理員範本

下列 Azure 資源管理員範本建立 Windows 型 Hadoop 叢集相依 Azure 儲存體帳戶。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
        "type": "string",
        "defaultValue": "East US 2",
        "allowedValues": [
            "Central US",
            "North Europe",
            "East US",
            "East US 2",
            "North Central US",
            "South Central US",
            "West US",
            "North Europe",
            "West Europe",
            "East Asia",
            "Southeast Asia",
            "Japan East",
            "Japan West",
            "Brizil South",
            "Australia East",
            "Australia Southeast",
            "Central India"
        ],
        "metadata": {
            "description": "The location where all azure resources will be deployed."
        }
        },
        "clusterName": {
        "type": "string",
        "metadata": {
            "description": "The name of the HDInsight cluster to create."
        }
        },
        "clusterLoginUserName": {
        "type": "string",
        "defaultValue": "admin",
        "metadata": {
            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
        }
        },
        "clusterLoginPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password for the cluster login."
        }
        },
        "clusterStorageAccountName": {
        "type": "string",
        "metadata": {
            "description": "The name of the storage account to be created and be used as the cluster's storage."
        }
        },
        "clusterStorageType": {
        "type": "string",
        "defaultValue": "Standard_LRS",
        "allowedValues": [
            "Standard_LRS",
            "Standard_GRS",
            "Standard_ZRS"
        ]
        },
        "clusterWorkerNodeCount": {
        "type": "int",
        "defaultValue": 4,
        "metadata": {
            "description": "The number of nodes in the HDInsight cluster."
        }
        }
    },
        "variables": {},
        "resources": [
            {
            "name": "[parameters('clusterStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2015-05-01-preview",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "accountType": "[parameters('clusterStorageType')]"
            }
            },
            {
            "name": "[parameters('clusterName')]",
            "type": "Microsoft.HDInsight/clusters",
            "location": "[parameters('location')]",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"
            ],
            "tags": {},
            "properties": {
                "clusterVersion": "3.2",
                "osType": "Windows",
                "clusterDefinition": {
                "kind": "hadoop",
                "configurations": {
                    "gateway": {
                    "restAuthCredential.isEnabled": true,
                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                    }
                }
                },
                "storageProfile": {
                "storageaccounts": [
                    {
                    "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                    "isDefault": true,
                    "container": "[parameters('clusterName')]",
                    "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
                    }
                ]
                },
                "computeProfile": {
                "roles": [
                    {
                    "name": "headnode",
                    "targetInstanceCount": "1",
                    "hardwareProfile": {
                        "vmSize": "Large"
                    }
                    },
                    {
                    "name": "workernode",
                    "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                    "hardwareProfile": {
                        "vmSize": "Large"
                    }
                    }
                ]
                }
            }
            }
        ],
        "outputs": {
            "cluster": {
            "type": "object",
            "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
            }
        }
    }

