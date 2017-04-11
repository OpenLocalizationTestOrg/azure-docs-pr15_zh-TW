<properties 
    pageTitle="使用資源管理員範本資料工廠 |Microsoft Azure" 
    description="瞭解如何建立及使用 Azure 資源管理員範本來建立資料工廠實體。" 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor=""/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/24/2016" 
    ms.author="shlo"/>

# <a name="use-templates-to-create-azure-data-factory-entities"></a>使用範本建立 Azure 資料工廠項目

## <a name="overview"></a>概觀
時使用您的資料整合的需求，您可能會發現自己 Azure 資料工廠重複相同的模式使用過的不同環境或執行相同的方案中的重複相同的工作。 範本可協助您可實作與管理這些案例簡單的方式。 Azure 資料工廠範本非常適合案例涉及重複使用性和重複項目。
 
請考慮這種情況的組織有 10 製造植物世界的位置。 從每個植物記錄會儲存在個別的內部部署的 SQL Server 資料庫。 公司想要建立臨機操作分析雲端中的單一資料倉庫。 也要有相同的邏輯，但開發、 測試和生產環境中的不同設定。 

在此情況下，任務必須在每個製造廠房為 10 的資料工廠重複相同的環境，但有不同的值。 實際上，**重複項目**會出現。 範本可讓此一般流程 （也就是管線中每個資料工廠有相同的活動） 的抽象，但是使用不同的參數檔案的每個製造廠房。

此外，為組織想要將這些 10 資料工廠多次部署不同環境，範本可以利用開發、 測試和生產環境中的另一個參數檔案使用此**重複使用**。

## <a name="templating-with-azure-resource-manager"></a>範本與 Azure 資源管理員
[Azure 資源管理員範本](../azure-resource-manager/resource-group-overview.md#template-deployment)是以達到範本的 Azure 資料工廠的絕佳方式。 資源管理員範本透過 JSON 檔案中定義的基礎結構和 Azure 方案的設定。 Azure 資源管理員範本處理所有/最 Azure 服務，因為它廣泛可輕鬆地管理您的 Azure 資產的所有資源。 請參閱若要進一步瞭解資源管理員範本一般[撰寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)。 

## <a name="tutorials"></a>教學課程
如需逐步指示，若要使用資源管理員範本建立資料工廠實體下列教學課程，請參閱︰

- [教學課程︰ 建立使用 Azure 資源管理員範本複製資料的管線](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [教學課程︰ 使用 Azure 資源管理員範本來建立程序資料的管線](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Github 資料工廠範本
請查看下列 Azure 快速入門範本 Github 上︰ 

- [建立資料工廠，將資料複製到 Azure SQL 資料庫 Azure Blob 儲存體](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
- [使用登錄區活動 Azure HDInsight 叢集上建立的資料工廠](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
- [建立資料，將資料複製到 Azure Blob Salesforce 工廠](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)

請隨意共用 Azure 資料工廠範本在[Azure 快速入門](https://azure.microsoft.com/documentation/templates/)。 請參閱[比重指南](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE)時開發可以透過這個存放庫共用的範本。 

下列各節提供在資源管理員範本中定義資料工廠資源相關的詳細資訊。 

## <a name="defining-data-factory-resources-in-templates"></a>在 [範本] 中定義資料工廠資源
定義資料工廠的最上層範本是︰

    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
    {
        "name": "[parameters('dataFactoryName')]",
        "apiVersion": "[variables('apiVersion')]",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "westus",
        "resources": [
        { "type": "linkedservices",
            ...
        },
        {"type": "datasets",
            ...
        },
        {"type": "dataPipelines",
            ...
        }
    }

### <a name="define-data-factory"></a>定義資料工廠

下列範例所示，您可以定義資源管理員範本中的資料工廠︰

    "resources": [
    {
        "name": "[variables('<mydataFactoryName>')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "East US"
    }

DataFactoryName 「 變數 」 中定義為︰

    "dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",

### <a name="define-linked-services"></a>定義連結的服務 
    
    "type": "linkedservices",
    "name": "[variables('<LinkedServiceName>')]",
    "apiVersion": "2015-10-01",
    "dependsOn": [ "[variables('<dataFactoryName>')]" ],
    "properties": {
        ...
    }


您想要部署的特定連結服務 JSON 屬性的詳細資料，請參閱[儲存連結服務](data-factory-azure-blob-connector.md#azure-storage-linked-service)或[計算連結的服務](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)。 「 DependsOn 」 參數會指定的對應資料工廠的名稱。 定義 Azure 儲存體的連結的服務的範例是以下列 JSON 定義所示︰

### <a name="define-datasets"></a>定義資料集

    "type": "datasets",
    "name": "[variables('<myDatasetName>')]",
    "dependsOn": [
        "[variables('<dataFactoryName>')]",
        "[variables('<myDatasetLinkedServiceName>')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        ...
    }

請參閱[支援的資料存放區](data-factory-data-movement-activities.md#supported-data-stores-and-formats)取得您想要部署的特定的資料集類型 JSON 屬性的詳細資料。 附註的 「 dependsOn 」 參數會指定名稱的對應資料工廠及儲存連結服務。 定義資料集 Azure blob 儲存體類型的範例是以下列 JSON 定義所示︰

    "type": "datasets",
    "name": "[variables('storageDataset')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('storageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "[variables('storageLinkedServiceName')]",
    "typeProperties": {
        "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
        "fileName": "[parameters('sourceBlobName')]",
        "format": {
            "type": "TextFormat"
        }
    },
    "availability": {
        "frequency": "Hour",
        "interval": 1
    }

### <a name="define-pipelines"></a>定義管線

    "type": "dataPipelines",
    "name": "[variables('<mypipelineName>')]",
    "dependsOn": [
        "[variables('<dataFactoryName>')]",
        "[variables('<inputDatasetLinkedServiceName>')]",
        "[variables('<outputDatasetLinkedServiceName>')]",
        "[variables('<inputDataset>')]",
        "[variables('<outputDataset>')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        activities: {
            ...
        }
    }

請參閱[定義管線](data-factory-create-pipelines.md#pipeline-json)的詳細資料 JSON 屬性的定義的特定的管線和您想要部署的活動。 請注意的 「 dependsOn 」 參數會指定的資料工廠名稱與任何對應連結的服務或資料集。 將資料從 Azure Blob 儲存體到 Azure SQL 資料庫的管線範例所示下列 JSON 程式碼片段︰

    "type": "datapipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]",
        "[variables('azureSqlLinkedServiceName')]",
        "[variables('blobInputDatasetName')]",
        "[variables('sqlOutputDatasetName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "activities": [
        {
            "name": "CopyFromAzureBlobToAzureSQL",
            "description": "Copy data frm Azure blob to Azure SQL",
            "type": "Copy",
            "inputs": [
                {
                    "name": "[variables('blobInputDatasetName')]"
                }
            ],
            "outputs": [
                {
                    "name": "[variables('sqlOutputDatasetName')]"
                }
            ],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "SqlSink",
                    "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
                },
                "translator": {
                    "type": "TabularTranslator",
                    "columnMappings": "Column0:FirstName,Column1:LastName"
                }
            },
            "Policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 3,
                "timeout": "01:00:00"
            }
        }
        ],
        "start": "2016-10-03T00:00:00Z",
        "end": "2016-10-04T00:00:00Z"

## <a name="parameterizing-data-factory-template"></a>參數化資料工廠範本
在參數化，請參閱[最佳作法建立 Azure 資源管理員範本的](../resource-manager-template-best-practices.md#parameters)文件的最佳作法。 一般而言，參數用法應該會最小化，尤其是如果變數您可以使用。 僅提供下列情況中的參數︰

- 依環境的設定而定 (範例︰ 開發、 測試和生產)
- 機密資料 （例如密碼）

如果您要部署 Azure 資料工廠實體使用的範本時，從[Azure 金鑰保存庫](../key-vault/key-vault-get-started.md)提取機密資料，請，指定**主要保存庫**和**私人的名稱**，如下列範例所示︰

    "parameters": {
        "storageAccountKey": { 
            "reference": {
                "keyVault": {
                    "id":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.KeyVault/vaults/<keyVaultName>",
                },
                "secretName": "<secretName>"
            }, 
        },
        ...
    }

> [AZURE.NOTE] 雖然匯出的現有資料工廠範本目前尚未支援，但卻中。 


