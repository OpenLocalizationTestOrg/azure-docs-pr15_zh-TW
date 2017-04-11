<properties
   pageTitle="上傳的大量資料，將資料湖存放使用離線的方法 |Microsoft Azure"
   description="使用 AdlCopy 工具，將資料複製到資料湖存放區 Azure 儲存體二進位大型物件"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/07/2016"
   ms.author="nitinme"/>

# <a name="use-azure-import-export-service-for-offline-copy-of-data-to-data-lake-store"></a>Azure 匯入匯出服務用於資料至資料湖存放區的離線複本

本文中，您將學習如何複製大型資料集 (> 200 GB) 使用離線複本的方法，例如[Azure 匯入/匯出服務](../storage/storage-import-export-service.md)Azure 資料湖存放區。 具體來說，使用本文中的範例檔案是 339,420,860,416 位元組即約 319 GB 的磁碟上。 現在就讓我們呼叫此檔案 319GB.tsv。

Azure 匯入/匯出服務可讓您對安全地傳輸傳送硬碟 Azure 資料中心 Azure blob 儲存體的大量資料。

## <a name="prerequisites"></a>必要條件

這份文件之前，您必須具備下列項目︰

- **Azure 訂閱**。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。

- **Azure 儲存體帳戶**。

- **Azure 資料湖分析帳戶 （可省略）** -請參閱[開始使用 Azure 資料湖分析](../data-lake-analytics/data-lake-analytics-get-started-portal.md)如需如何建立資料湖存放帳戶的指示。


## <a name="preparing-the-data"></a>準備資料

再使用 [匯入/匯出服務，我們必須中斷的資料檔案傳輸**至複本的是小於 200 GB**的大小。 這是因為匯入工具不適用於大於 200 GB 的檔案。 若要符合，在本教學課程分割檔案到 100 GB 每位元組的區塊。 您可以輕鬆地使用[Cygwin](https://cygwin.com/install.html)。 Cygwin 支援 Linux 命令可讓使用者輕鬆地執行此動作。 我們的情況，我們會使用下列命令。

    split -b 100m 319GB.tsv

[分割] 作業會建立下列名稱的檔案。

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>準備就緒磁碟的資料

依照在[使用 Azure 匯入/匯出服務](../storage/storage-import-export-service.md)（**準備磁碟機**] 區段中） 來準備您的硬碟。 以下是如何準備的磁碟機整個流程。

1. 採購符合用於 Auzre 匯入/匯出服務要求的硬碟。

2. 找出 Azure 儲存體帳戶會將資料複製一次 si 運送至 Azure 資料中心。

3. 使用[Azure 匯入/匯出工具](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409)，命令列公用程式。 以下是如何使用工具片段範例。

    ````
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ````
    如何使用**Azure 匯入/匯出工具**的更多範例程式碼片段，請參閱[使用 Azure 匯入/匯出服務](../storage/storage-import-export-service.md)。

4. 上述的命令會建立日誌檔案在指定的位置。 若要從[Azure 傳統入口網站](https://manage.windowsazure.com)中建立匯入工作，您會使用這個筆記本檔案。

## <a name="create-an-import-job"></a>建立匯入作業

您現在可以建立[使用 Azure 匯入/匯出](../storage/storage-import-export-service.md)服務中使用的指示 （在下**建立匯入工作**> 一節） 匯入作業。 匯入作業，為其他詳細資料，也提供準備的磁碟機時所建立的筆記本檔案。

## <a name="physically-ship-the-disks"></a>實際運送磁碟

您現在可以實際運送的磁碟位置資料會複製到您建立匯入工作時所提供的儲存空間 Azure Blob Azure 資料中心。 此外，同時建立工作，如果您選擇提供追蹤資訊之後，您可以立即回到您匯入工作與更新追蹤號碼。

## <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-store"></a>Azure 儲存體 Blob 將資料複製到 Azure 資料湖存放區

匯入工作的狀態] 顯示已完成之後，您可以驗證資料是在您已指定的儲存空間 Azure Blob 中使用。 您然後可以使用各種不同的方法，將資料從 Azure 儲存體 Blob Azure 資料湖存放區。 所有可用的選項上傳的資料，請參閱[將資料湖存放區的 Ingesting 資料](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-store)。

在此區段中，我們會提供可用來建立用於複製資料 Azure 資料工廠管線 JSON 定義。 您可以使用從[Azure 入口網站](../data-factory/data-factory-copy-activity-tutorial-using-azure-portal.md)或[Visual Studio](../data-factory/data-factory-copy-activity-tutorial-using-visual-studio.md)或[PowerShell 的 Azure](../data-factory/data-factory-copy-activity-tutorial-using-powershell.md)這些 JSON 定義。

### <a name="source-linked-service-azure-storage-blob"></a>來源連結服務 (Azure 儲存體 Blob)

````
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
````

### <a name="target-linked-service-azure-data-lake-store"></a>目標連結服務 （Azure 資料湖儲存區）

````
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Store with your access rights>",
            "dataLakeStoreUri": "https://<adls_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
````
### <a name="input-data-set"></a>輸入的資料集
````
{
    "name": "InputDataSet",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "importcontainer/vf1/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
````
### <a name="output-data-set"></a>輸出資料集
````
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStoreLinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
````
### <a name="pipeline-copy-activity"></a>管線 （複製活動）
````
{
    "name": "CopyImportedData",
    "properties": {
        "description": "Pipeline with copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity"
            }
        ],
        "start": "2016-02-08T22:00:00Z",
        "end": "2016-02-08T23:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
````
如需有關如何使用 Azure 資料工廠從 Azure 儲存體 Blob 和 Azure 資料湖存放移動資料的詳細資訊，請參閱[移動到 Azure 資料湖存放區使用 Azure 資料工廠 Azure 儲存體 Blob 的資料](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store)。

## <a name="reconstruct-the-data-files-in-azure-data-lake-store"></a>重建 Azure 資料湖存放區中的資料檔案

我們開始 319 GB 大小並中斷其的較小的檔案，讓它可以傳輸使用 Azure 匯入/匯出服務的檔案。 現在，資料是 Azure 資料湖存放區中，我們可以 reconstrcut 原始大小的檔案。 您可以使用下列 PowerShell 的 Azure cmldts 執行此作業。

````
# Login to our account
Login-AzureRmAccount

# List your subscriptions
Get-AzureRmSubscription

# Switch to the subscription you want to work with
Set-AzureRmContext –SubscriptionId
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzureRmDataLakeStoreItem -AccountName "<adls_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv”
````

## <a name="next-steps"></a>後續步驟

- [保護資料湖存放區中的資料](data-lake-store-secure-data.md)
- [使用資料湖存放 Azure 資料湖狀況分析](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [使用資料湖存放 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)
