<properties
    pageTitle="教學課程︰ 建立使用資源管理員範本的管線 |Microsoft Azure"
    description="在本教學課程中，您建立 Azure 資料工廠管線複製活動與使用 Azure 資源管理員範本。"
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/10/2016"
    ms.author="spelluru"/>

# <a name="tutorial-create-a-pipeline-with-copy-activity-using-azure-resource-manager-template"></a>教學課程︰ 使用 Azure 資源管理員範本複製活動建立管線
> [AZURE.SELECTOR]
- [概觀與先決條件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [複製精靈](data-factory-copy-data-wizard-tutorial.md)
- [Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Azure 資源管理員範本](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)


本教學課程教您如何建立和監視使用 Azure 資源管理員範本 Azure 資料工廠。 資料工廠管線會將資料從 Azure Blob 儲存體到 Azure SQL 資料庫。

## <a name="prerequisites"></a>必要條件
- [教學課程概觀與先決條件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)瀏覽並完成的**必要**步驟。
- 依照您的電腦上安裝最新版本的 PowerShell 的 Azure[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)文件中的指示進行。 在本教學課程中，您可以使用 PowerShell 部署資料工廠項目。 
- （選用）請參閱[撰寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)，若要深入瞭解 Azure 資源管理員範本。


## <a name="in-this-tutorial"></a>在本教學課程

在本教學課程中，您可以建立資料工廠下列資料工廠實體︰

實體 | 描述  
------ | ----------- 
Azure 連結的儲存服務 | 連結資料 factory Azure 儲存體帳戶。 Azure 儲存體來源的資料存放區，而 Azure SQL 資料庫會複製活動接收資料存放區在教學課程。 它會指定包含複製活動的輸入的資料的儲存空間帳戶。 
Azure SQL 資料庫連結服務| 連結資料 factory Azure SQL 資料庫。 它會指定保留複製活動的輸出資料 Azure SQL 資料庫。 
Azure Blob 輸入資料集 | 參照 Azure 儲存體連結服務。 連結的服務參照 Azure 儲存體帳戶和 Azure Blob 資料集指定容器、 資料夾及檔案名稱中含有輸入的資料的儲存空間。 
Azure SQL 輸出資料集 | 參照 Azure SQL 連結服務。 Azure SQL 連結服務會參照到 Azure SQL server，Azure SQL 資料集指定存放輸出資料的資料表的名稱。 
資料管線 | 管線具有一個活動類型的複製採用 Azure blob 資料集的輸入與輸出 Azure SQL 資料集的。 複製活動會將資料從 Azure blob Azure SQL 資料庫中的表格。  

資料工廠多可以有一或多個管線。 管線可以有一或多個活動。 有兩種類型的活動︰[資料移動活動](data-factory-data-movement-activities.md)」 和 「[資料轉換的活動](data-factory-data-transformation-activities.md)。 在本教學課程中，您可以建立的管線含一個活動 （複製活動）。

![Azure Blob 複製到 Azure SQL 資料庫](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/CopyBlob2SqlDiagram.png) 

下一節提供完整的資源管理員範本定義資料工廠項目，好讓您可以快速執行教學課程和測試範本。 若要瞭解如何每個資料工廠實體的定義，請參閱[在範本中的資料工廠實體](#data-factory-entities-in-the-template)一節。

## <a name="data-factory-json-template"></a>資料工廠 JSON 範本
定義資料工廠的最上層資源管理員範本是︰ 

    {
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
                    { ... },
                    { ... },
                    { ... },
                    { ... }
                ]
            }
        ]
    }

建立一個名為**ADFCopyTutorialARM.json**下列內容**C:\ADFGetStarted**資料夾中的 JSON 檔案︰


    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {
          "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the data to be copied." } },
          "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
          "sourceBlobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
          "sourceBlobName": { "type": "string", "metadata": { "description": "Name of the blob in the container that has the data to be copied to Azure SQL Database table" } },
          "sqlServerName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Server that will hold the output/copied data." } },
          "databaseName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Database in the Azure SQL server." } },
          "sqlServerUserName": { "type": "string", "metadata": { "description": "Name of the user that has access to the Azure SQL server." } },
          "sqlServerPassword": { "type": "securestring", "metadata": { "description": "Password for the user." } },
          "targetSQLTable": { "type": "string", "metadata": { "description": "Table in the Azure SQL Database that will hold the copied data." } 
          } 
        },
        "variables": {
          "dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]",
          "azureSqlLinkedServiceName": "AzureSqlLinkedService",
          "azureStorageLinkedServiceName": "AzureStorageLinkedService",
          "blobInputDatasetName": "BlobInputDataset",
          "sqlOutputDatasetName": "SQLOutputDataset",
          "pipelineName": "Blob2SQLPipeline"
        },
        "resources": [
          {
            "name": "[variables('dataFactoryName')]",
            "apiVersion": "2015-10-01",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "West US",
            "resources": [
              {
                "type": "linkedservices",
                "name": "[variables('azureStorageLinkedServiceName')]",
                "dependsOn": [
                  "[variables('dataFactoryName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                  "type": "AzureStorage",
                  "description": "Azure Storage linked service",
                  "typeProperties": {
                    "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
                  }
                }
              },
              {
                "type": "linkedservices",
                "name": "[variables('azureSqlLinkedServiceName')]",
                "dependsOn": [
                  "[variables('dataFactoryName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                  "type": "AzureSqlDatabase",
                  "description": "Azure SQL linked service",
                  "typeProperties": {
                    "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
                  }
                }
              },
              {
                "type": "datasets",
                "name": "[variables('blobInputDatasetName')]",
                "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                  "type": "AzureBlob",
                  "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                  "structure": [
                    {
                      "name": "Column0",
                      "type": "String"
                    },
                    {
                      "name": "Column1",
                      "type": "String"
                    }
                  ],
                  "typeProperties": {
                    "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
                    "fileName": "[parameters('sourceBlobName')]",
                    "format": {
                      "type": "TextFormat",
                      "columnDelimiter": ","
                    }
                  },
                  "availability": {
                    "frequency": "Day",
                    "interval": 1
                  },
                  "external": true
                }
              },
              {
                "type": "datasets",
                "name": "[variables('sqlOutputDatasetName')]",
                "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureSqlLinkedServiceName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                  "type": "AzureSqlTable",
                  "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
                  "structure": [
                    {
                      "name": "FirstName",
                      "type": "String"
                    },
                    {
                      "name": "LastName",
                      "type": "String"
                    }
                  ],
                  "typeProperties": {
                    "tableName": "[parameters('targetSQLTable')]"
                  },
                  "availability": {
                    "frequency": "Day",
                    "interval": 1
                  }
                }
              },
              {
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
                  "start": "2016-10-02T00:00:00Z",
                  "end": "2016-10-03T00:00:00Z"
                }
              }
            ]
          }
        ]
      }

## <a name="parameters-json"></a>參數 JSON 
建立一個名為**ADFCopyTutorialARM Parameters.json**包含 Azure 資源管理員範本參數的 JSON 檔案。 

> [AZURE.IMPORTANT] 指定 [名稱] 和 [索引鍵**storageAccountName**和**storageAccountKey**參數的 Azure 儲存體帳戶。  

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": { 
            "storageAccountName": { "value": "<Name of the Azure storage account>"  },
            "storageAccountKey": {
                "value": "<Key for the Azure storage account>"
            },
            "sourceBlobContainer": { "value": "adftutorial" },
            "sourceBlobName": { "value": "emp.txt" },
            "sqlServerName": { "value": "<Name of the Azure SQL server>" },
            "databaseName": { "value": "<Name of the Azure SQL database>" },
            "sqlServerUserName": { "value": "<Name of the user who has access to the Azure SQL database>" },
            "sqlServerPassword": { "value": "<password for the user>" },
            "targetSQLTable": { "value": "emp" }
        }
    }

> [AZURE.IMPORTANT] 您可能需要開發、 測試和生產環境中，您可以使用相同的資料工廠 JSON 範本的另一個參數 JSON 檔案。 使用 Power Shell 指令碼，您可以自動化這些環境中的部署資料工廠項目。  

## <a name="create-data-factory"></a>建立資料工廠
1. 啟動**Azure PowerShell** ，並執行下列命令︰
    - 執行`Login-AzureRmAccount`輸入使用者名稱和密碼，您用於登入 Azure 入口網站。  
    - 執行`Get-AzureRmSubscription`檢視此帳戶的所有訂閱。
    - 執行`Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext`以選取您想要使用的訂閱。 
2. 執行下列命令以部署使用資源管理員範本的資料工廠實體您在步驟 1 中建立。

        New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFCopyTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFCopyTutorialARM-Parameters.json

## <a name="monitor-pipeline"></a>監視器管線
1. [Azure 入口網站](https://portal.azure.com)使用 Azure 帳戶登入。
2. 按一下左側功能表上的 [**資料工廠**（或） 按一下 [**更多服務**並按一下**資料工廠**下**智慧 + 分析**的類別。

    ![資料工廠] 功能表](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factories-menu.png)
3. 在 [**資料工廠**] 頁面搜尋，並尋找您的資料工廠。 

    ![搜尋資料工廠](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/search-for-data-factory.png)  
4. 按一下您 Azure 資料工廠。 您會看到資料工廠首頁。

    ![資料工廠首頁](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factory-home-page.png)  
5. 按一下以查看您的資料工廠的圖表檢視]**圖**磚。

    ![工廠資料的圖表檢視](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factory-diagram-view.png)
6. 在 [圖表] 檢視中，按兩下 [ **SQLOutputDataset**資料集。 您看到的扇形區的狀態。 複製作業完成時，您的狀態設定為 [**可**。

    ![準備好狀態的輸出扇形區](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/output-slice-ready.png)
7. **準備好**狀態扇形區時，請確認要將資料複製到**emp**資料表中 Azure SQL 資料庫。

如何使用 Azure 入口網站刀監控管線與資料集的指示建立在本教學課程，請參閱[監視器資料集和管道的郵件](data-factory-monitor-manage-pipelines.md)。

監控您的資料管線，您也可以使用螢幕與管理應用程式。 請參閱[監視器及管理使用監視的應用程式的 Azure 資料工廠管線](data-factory-monitor-manage-app.md)的詳細資料使用的應用程式。


## <a name="data-factory-entities-in-the-template"></a>範本中的資料工廠實體

### <a name="define-data-factory"></a>定義資料工廠
您可以定義資料工廠在資源管理員範本，如下列範例所示︰  

    "resources": [
    {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "West US"
    }

DataFactoryName 定義為︰ 
      
    "dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]"

是唯一的字串，根據 [資源群組識別碼。  

### <a name="defining-data-factory-entities"></a>定義資料工廠項目
下列資料工廠實體定義 JSON 範本中︰ 

1. [Azure 連結的儲存服務](#azure-storage-linked-service)
2. [Azure SQL 連結服務](#azure-sql-database-linked-service)
3. [Azure blob 資料集](#azure-blob-dataset)
4. [Azure SQL 資料集](#azure-sql-dataset)
5. [使用複製活動的資料管線](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Azure 連結的儲存服務
您可以在此區段中指定 [名稱] 和 [索引鍵的 Azure 儲存體帳戶。 如需用來定義 Azure 儲存體連結服務 JSON 屬性的詳細資訊，請參閱[Azure 儲存體連結服務](data-factory-azure-blob-connector.md#azure-storage-linked-service)。 

    {
        "type": "linkedservices",
        "name": "[variables('azureStorageLinkedServiceName')]",
        "dependsOn": [
            "[variables('dataFactoryName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
            "type": "AzureStorage",
            "description": "Azure Storage linked service",
            "typeProperties": {
                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
            }
        }
    }

連接字串使用 storageAccountName 和 storageAccountKey 參數。 這些傳遞的參數使用的設定檔值。 定義也使用變數︰ azureStroageLinkedService 和 dataFactoryName 範本中的定義。 
    
#### <a name="azure-sql-database-linked-service"></a>Azure SQL 資料庫連結服務
您可以在此區段中指定 Azure SQL server 名稱、 資料庫名稱、 使用者名稱和使用者密碼。 如需用來定義 Azure SQL 連結服務 JSON 屬性的詳細資訊，請參閱[Azure SQL 連結服務](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties)。  

    {
        "type": "linkedservices",
        "name": "[variables('azureSqlLinkedServiceName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
            "type": "AzureSqlDatabase",
            "description": "Azure SQL linked service",
            "typeProperties": {
                "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
            }
        }
    }

連接字串使用 sqlServerName 與資料庫名稱、 sqlServerUserName，其值傳遞使用設定檔的 sqlServerPassword 參數。 定義也會使用下列變數從範本︰ azureSqlLinkedServiceName，dataFactoryName。

#### <a name="azure-blob-dataset"></a>Azure blob 資料集
您指定的 blob 容器、 資料夾及檔案包含輸入的資料的名稱。 如需詳細資訊用來定義 Azure Blob 資料集的 JSON 屬性，請參閱[Azure Blob 資料集屬性](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)。 


    {
        "type": "datasets",
        "name": "[variables('blobInputDatasetName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]",
          "[variables('azureStorageLinkedServiceName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
            "structure": [
            {
                "name": "Column0",
                "type": "String"
            },
            {
                "name": "Column1",
                "type": "String"
            }
            ],
            "typeProperties": {
                "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
                "fileName": "[parameters('sourceBlobName')]",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            },
            "external": true
        }
    }

#### <a name="azure-sql-dataset"></a>Azure SQL 資料集
您擁有從 Azure Blob 儲存體複製的資料 Azure SQL 資料庫中指定的資料表名稱。 如需詳細資訊用來定義 Azure SQL 資料集的 JSON 屬性，請參閱[Azure SQL 資料集屬性](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties)。 

    {
        "type": "datasets",
        "name": "[variables('sqlOutputDatasetName')]",
        "dependsOn": [
            "[variables('dataFactoryName')]",
            "[variables('azureSqlLinkedServiceName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
            "structure": [
            {
                "name": "FirstName",
                "type": "String"
            },
            {
                "name": "LastName",
                "type": "String"
            }
            ],
            "typeProperties": {
                "tableName": "[parameters('targetSQLTable')]"
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

#### <a name="data-pipeline"></a>資料管線
您定義將資料從 Azure blob 資料集到 Azure SQL 資料集的管線。 用來在此範例中定義的管線 JSON 元素的說明，請參閱[管線 JSON](data-factory-create-pipelines.md#pipeline-json) 。 

    {
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
            "start": "2016-10-02T00:00:00Z",
            "end": "2016-10-03T00:00:00Z"
        }
    }

## <a name="reuse-the-template"></a>重複使用的範本 
在教學課程中，您可以建立的範本定義資料工廠實體和傳遞的參數值的範本。 管線會將資料從 Azure 儲存體帳戶到指定參數透過 Azure SQL 資料庫。 若要將資料工廠實體部署至不同的環境中使用相同的範本，您建立的每個環境參數檔案，然後將其部署至該環境時。     

範例︰  

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Dev.json

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Test.json

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Production.json

請注意的第一個命令的開發環境、 測試環境中，第二個和第三個生產環境使用參數檔案。  

您也可以重複使用的範本，以執行重複的工作。 例如，您需要建立多個資料工廠實作相同的邏輯的一或多個管線與，但每個資料工廠使用不同的 Azure 儲存和 Azure SQL 資料庫帳戶。 在此案例中，您使用相同的範本 （開發、 測試或生產） 相同的環境中使用不同的參數檔案來建立資料工廠。   

