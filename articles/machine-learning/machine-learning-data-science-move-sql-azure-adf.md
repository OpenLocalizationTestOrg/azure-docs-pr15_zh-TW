<properties
    pageTitle="將資料從內部部署的 SQL Server Azure 資料工廠與 SQL Azure |Azure"
    description="設定 ADF 管線，可將兩個資料移轉活動的共同移動每日之間的內部部署和雲端中的資料庫。"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="bradsev" />


# <a name="move-data-from-an-on-premise-sql-server-to-sql-azure-with-azure-data-factory"></a>將資料從內部部署的 SQL server Azure 資料工廠與 SQL Azure

本主題說明如何將資料從內部部署的 SQL Server 資料庫移到 SQL Azure 資料庫，透過 Azure Blob 儲存體使用 Azure 資料工廠 (ADF)。

以下主題說明如何將資料的內嵌**功能表**連結目標的環境可以儲存與小組資料科學程序期間處理資料。

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


## <a name="intro"></a>簡介︰ 什麼是 ADF 以及何時該使用移轉資料？

Azure 資料工廠是完全受管理的雲端基礎資料整合服務協調，會自動執行的動作及資料轉換。 ADF 模型中的關鍵概念是管道的郵件。 管線是邏輯群組的活動，每個定義要包含在資料集中的資料上執行的動作。 連結的服務用來定義所需的資料連線到資料資源的工廠資訊。

ADF，與現有的資料處理服務可以組成資料管線高可用及受管理中的雲端。 這些資料管線排定內嵌、 準備、 轉換、 分析和發佈資料，並 ADF 管理協調複雜的資料和處理相依性。 解決方案可以快速建置和部署在雲端，連線越來越多的內部部署與雲端資料來源。

請考慮使用 ADF:

- 當資料需要持續移轉在混合式部署的情況下，存取內部部署和雲端的資源 
- 當資料交易，或需要修改，或已被移轉時，對其新增的商務邏輯。 

ADF 允許排程和監視使用管理資料定期移動的簡易 JSON 指令碼的工作。 ADF 也有其他功能，例如支援複雜的作業。 如需有關 ADF 的詳細資訊，請參閱[Azure 資料工廠 (ADF)](https://azure.microsoft.com/services/data-factory/)的文件。


## <a name="scenario"></a>此案例

我們設定，可將兩個資料移轉活動 ADF 管線。 在一起他們每日和之間移動資料的內部部署的 SQL 資料庫在雲端 Azure SQL 資料庫。 兩個活動有︰

* Azure Blob 儲存體帳戶將資料複製為內部部署的 SQL Server 資料庫
* Azure Blob 儲存體帳戶資料複製到 Azure SQL 資料庫。

>[AZURE.NOTE] 顯示以下已經過修改將更詳細的教學課程 ADF 小組所提供的步驟進行︰ 適當時提供[將資料內部部署與雲端資料管理閘道器之間移動](../data-factory/data-factory-move-data-between-onprem-and-cloud.md)參照該主題的相關的章節。


## <a name="prereqs"></a>必要條件
本教學課程，假設您有︰

* **Azure 訂閱**。 如果您沒有訂閱，您可以註冊[免費試用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure 儲存體帳戶**。 您可以使用 Azure 儲存體帳戶將資料儲存在本教學課程。 如果您沒有安裝 Azure 儲存體帳戶，請參閱[建立儲存的帳戶](storage-create-storage-account.md#create-a-storage-account)。 您建立儲存帳戶後，您必須以取得帳戶金鑰] 用來存取儲存空間。 請參閱[檢視、 複製及重新產生的儲存空間便捷鍵](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)。
* **Azure SQL 資料庫**的存取。 如果您必須設定 Azure SQL 資料庫，[開始使用 Microsoft Azure SQL 資料庫](../sql-database/sql-database-get-started.md)tpoic 提供如何佈建新的執行個體 Azure SQL 資料庫的相關資訊。
* 安裝和設定**PowerShell 的 Azure**本機。 如需相關指示，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。

> [AZURE.NOTE] 此程序使用[Azure 入口網站](https://portal.azure.com/)。


##<a name="upload-data"></a>上傳到您內部部署的 SQL Server 的資料

我們使用[NYC 計程車資料集](http://chriswhong.com/open-data/foil_nyc_taxi/)示範移轉程序。 如上所述的文章，Azure blob 儲存體[NYC 計程車資料](http://www.andresmh.com/nyctaxitrips/)使用，NYC 計程車資料集。 資料有兩個檔案、 trip_data.csv 檔案包含差旅費詳細資料及 trip_far.csv 檔案，其中包含所支付的每個出差行程井然有序 fare 的詳細資料。 中[NYC 計程車往返資料集的描述](machine-learning-data-science-process-sql-walkthrough.md#dataset)所提供的範例和描述這些檔案。


您可以採用以下提供的自己的資料集的程序，或使用 NYC 計程車資料集的說明，請依照下列步驟。 若要將 NYC 計程車資料集上傳到您內部部署的 SQL Server 資料庫，請依照[大量匯入資料至 SQL Server 資料庫](machine-learning-data-science-process-sql-walkthrough.md#dbload)中的程序。 這些指示適用於 SQL Server 上 Azure 虛擬機器，但上傳至內部部署的 SQL Server 的程序相同。


##<a name="create-adf"></a>建立 Azure 資料工廠

建立新的 Azure 資料工廠] 和 [資源群組[Azure 入口網站](https://portal.azure.com/)中的指示進行會提供[建立 Azure 資料工廠](../data-factory/data-factory-build-your-first-pipeline-using-editor.md#step-1-creating-the-data-factory)。 新的 ADF 執行個體*adfdsp*名稱與名稱資源建立群組*adfdsprg*。


## <a name="install-and-configure-up-the-data-management-gateway"></a>安裝和設定資料管理閘道器設定

若要啟用使用內部部署的 SQL Server Azure 資料工廠管線，必須將其做為連結的服務新增至資料 factory。 若要建立連結的服務，用於內部部署的 SQL Server，您必須︰

- 下載並安裝 Microsoft 資料管理閘道到內部部署電腦上。 
- 設定連結內部部署資料來源，以使用閘道服務。 

資料管理閘道序列化和還原序列化位置主控的電腦上的來源與接收的資料。

設定指示及資料管理閘道器的詳細資訊，請參閱[將內部部署與雲端資料管理閘道器之間的資料](../data-factory/data-factory-move-data-between-onprem-and-cloud.md)


## <a name="adflinkedservices"></a>建立連線到資料資源的連結的服務

連結的服務定義 Azure 資料工廠連線至資料資源所需的資訊。 [建立連結的服務](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#step-2-create-linked-services)中提供的逐步程序建立連結的服務。

我們在這個案例中的連結的服務所需有三個資源。

1. [連結內部部署的 SQL Server 服務](#adf-linked-service-onprem-sql)
2. [連結的服務 Azure Blob 儲存體](#adf-linked-service-blob-store)
3. [連結的服務 Azure SQL 資料庫](#adf-linked-service-azure-sql)


###<a name="adf-linked-service-onprem-sql"></a>針對內部部署的 SQL Server 資料庫的連結的服務

若要建立連結內部部署的 SQL Server 服務︰

- 按一下 Azure 傳統入口網站上的 [ADF 登陸頁面中的**資料存放區** 
- 選取**SQL** ，然後輸入內部部署的 SQL Server 的*使用者名稱*和*密碼*的認證]。 您需要輸入伺服器名稱的**完整伺服器名稱反斜線執行個體名稱 (servername\instancename)**。 名稱連結的服務*adfonpremsql*。

###<a name="adf-linked-service-blob-store"></a>Blob 的連結的服務

若要建立連結的服務 Azure Blob 儲存體帳戶︰

- 按一下 Azure 傳統入口網站上的 [ADF 登陸頁面中的**資料存放區**
- 選取**Azure 儲存體帳戶** 
- 輸入 Azure Blob 儲存體帳戶金鑰]，然後容器名稱。 名稱連結服務*adfds*。

###<a name="adf-linked-service-azure-sql"></a>連結的服務 Azure SQL 資料庫

若要建立連結的服務 Azure SQL 資料庫︰

- 按一下 Azure 傳統入口網站上的 [ADF 登陸頁面中的**資料存放區**
- 選取**Azure SQL** ，然後輸入*使用者名稱*和*密碼*的認證 Azure SQL 資料庫]。 *使用者名稱*必須指定為*user@servername*。   


##<a name="adf-tables"></a>定義和建立資料表如何存取資料集

建立使用下列的指令碼基礎程序指定結構、 位置和可用的資料集的資料表。 JSON 檔案用來定義資料表。 如需有關這些檔案的結構的詳細資訊，請參閱[資料集](../data-factory/data-factory-create-datasets.md)。

> [AZURE.NOTE]  您可以執行`Add-AzureAccount`之前執行[新增 AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) cmdlet，以確認右 Azure 訂閱已為此命令執行指令程式。 如需此 cmdlet 的文件，請參閱[新增 AzureAccount](https://msdn.microsoft.com/library/azure/dn790372.aspx)。

JSON 型定義資料表中的使用下列名稱︰

* 在內部部署的 SQL server 中的**資料表名稱**是*nyctaxi_data*
* Azure Blob 儲存體帳戶中的**容器名稱**是*containername*  

三個資料表定義所需的這個 ADF 管線︰

1. [SQL 內部部署的資料表](#adf-table-onprem-sql)
2. [Blob 表格](#adf-table-blob-store)
3. [SQL Azure 資料表](#adf-table-azure-sql)

> [AZURE.NOTE]  這些程序使用 PowerShell 的 Azure 定義和建立 ADF 活動。 但是，這些工作也可以完成使用 Azure 入口網站。 如需詳細資訊，請參閱[建立輸入與輸出資料集](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#step-3-create-input-and-output-datasets)。

###<a name="adf-table-onprem-sql"></a>SQL 內部部署的資料表

在下列 JSON 檔案指定內部部署的 SQL Server 資料表定義︰

        {
            "name": "OnPremSQLTable",
            "properties":
            {
                "location":
                {
                "type": "OnPremisesSqlServerTableLocation",
                "tableName": "nyctaxi_data",
                "linkedServiceName": "adfonpremsql"
                },
                "availability":
                {
                "frequency": "Day",
                "interval": 1,   
                "waitOnExternal":
                {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
                }

                }
            }
        }

資料行名稱，並沒有包含在內。 若要子選取資料行名稱，包括其以下 （的詳細資料的核取[ADF 文件](../data-factory/data-factory-data-movement-activities.md )主題。

將資料表 JSON 定義複製到名*onpremtabledef.json*檔案的檔案，並將其儲存到其他位置 （以下其值假定為*C:\temp\onpremtabledef.json*）。 在 ADF 中建立表格，使用下列 PowerShell 的 Azure cmdlet:

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


###<a name="adf-table-blob-store"></a>Blob 表格
輸出 blob 位置的表格定義位於的下列 （對應 Azure blob ingested 將內部部署資料）︰

        {
            "name": "OutputBlobTable",
            "properties":
            {
                "location":
                {
                "type": "AzureBlobLocation",
                "folderPath": "containername",
                "format":
                {
                "type": "TextFormat",
                "columnDelimiter": "\t"
                },
                "linkedServiceName": "adfds"
                },
                "availability":
                {
                "frequency": "Day",
                "interval": 1
                }
            }
        }

將資料表 JSON 定義複製到名*bloboutputtabledef.json*檔案的檔案，並將其儲存到其他位置 （以下其值假定為*C:\temp\bloboutputtabledef.json*）。 在 ADF 中建立表格，使用下列 PowerShell 的 Azure cmdlet:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json  

###<a name="adf-table-azure-sq"></a>SQL Azure 資料表
SQL Azure 資料表的定義輸出位於的下列 （此結構描述地圖來自 blob 的資料）︰

    {
        "name": "OutputSQLAzureTable",
        "properties":
        {
            "structure":
            [
                { "name": "column1", type": "String"},
                { "name": "column2", type": "String"}                
            ],
            "location":
            {
                "type": "AzureSqlTableLocation",
                "tableName": "your_db_name",
                "linkedServiceName": "adfdssqlazure_linked_servicename"
            },
            "availability":
            {
                "frequency": "Day",
                "interval": 1            
            }
        }
    }

將資料表 JSON 定義複製到名*AzureSqlTable.json*檔案的檔案，並將其儲存到其他位置 （以下其值假定為*C:\temp\AzureSqlTable.json*）。 在 ADF 中建立表格，使用下列 PowerShell 的 Azure cmdlet:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json  


##<a name="adf-pipeline"></a>定義及建立管線

指定的所屬管線與使用下列的指令碼基礎程序建立管線活動。 JSON 檔案用來定義管線屬性。

* 指令碼假設的**管線名稱** *AMLDSProcessPipeline*。
* 另請注意，我們會設定執行每日和工作 (12 am UTC) 使用預設的執行時間的管線週期性。

> [AZURE.NOTE]下列程序使用 PowerShell 的 Azure 定義和建立 ADF 管道的郵件。 不過這項工作也可以完成使用 Azure 入口網站。 如需詳細資訊，請參閱[建立和執行管道的郵件](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#step-4-create-and-run-a-pipeline)。

使用先前所提供的資料表定義，ADF 的管線定義指定，如下所示︰

        {
            "name": "AMLDSProcessPipeline",
            "properties":
            {
                "description" : "This pipeline has one Copy activity that copies data from an on-premise SQL to Azure blob",
                 "activities":
                [
                    {
                        "name": "CopyFromSQLtoBlob",
                        "description": "Copy data from on-premise SQL server to blob",     
                        "type": "CopyActivity",
                        "inputs": [ {"name": "OnPremSQLTable"} ],
                        "outputs": [ {"name": "OutputBlobTable"} ],
                        "transformation":
                        {
                            "source":
                            {                               
                                "type": "SqlSource",
                                "sqlReaderQuery": "select * from nyctaxi_data"
                            },
                            "sink":
                            {
                                "type": "BlobSink"
                            }   
                        },
                        "Policy":
                        {
                            "concurrency": 3,
                            "executionPriorityOrder": "NewestFirst",
                            "style": "StartOfInterval",
                            "retry": 0,
                            "timeout": "01:00:00"
                        }       

                     },

                    {
                        "name": "CopyFromBlobtoSQLAzure",
                        "description": "Push data to Sql Azure",        
                        "type": "CopyActivity",
                        "inputs": [ {"name": "OutputBlobTable"} ],
                        "outputs": [ {"name": "OutputSQLAzureTable"} ],
                        "transformation":
                        {
                            "source":
                            {                               
                                "type": "BlobSource"
                            },
                            "sink":
                            {
                                "type": "SqlSink",
                                "WriteBatchTimeout": "00:5:00",             
                            }           
                        },
                        "Policy":
                        {
                            "concurrency": 3,
                            "executionPriorityOrder": "NewestFirst",
                            "style": "StartOfInterval",
                            "retry": 2,
                            "timeout": "02:00:00"
                        }
                     }
                ]
            }
        }

複製此 JSON 定義的管線成帶有名*pipelinedef.json*檔案的檔案，並將其儲存到其他位置 （以下其值假定為*C:\temp\pipelinedef.json*）。 建立管線 ADF 中，使用下列 PowerShell 的 Azure cmdlet:

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json

確認您可以看到 Azure 傳統入口網站中 ADF 中顯示為下列 （當您按一下 [圖表） 的管線

![](media/machine-learning-data-science-move-sql-azure-adf/DJP1kji.png)


##<a name="adf-pipeline-start"></a>開始管線
管線現在可以使用下列命令來執行︰

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

*開始日期*和*結束日期*的參數值必須要之間的管線執行的實際日期會被取代。

一旦管線執行時，您應該能夠看到在 blob，每日的一個檔案的所選容器中顯示的資料。

請注意，我們不運用相當所提供的功能 ADF 管道資料從屬參照。 如需有關如何進行此與 ADF 所提供的其他功能的詳細資訊，請參閱[ADF 文件](https://azure.microsoft.com/services/data-factory/)。
