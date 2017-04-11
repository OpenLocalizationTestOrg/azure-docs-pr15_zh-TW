<properties
    pageTitle="建立第一個資料工廠 （剩餘） |Microsoft Azure"
    description="在本教學課程中，您可以建立使用資料工廠 REST API 範例 Azure 資料工廠管線。"
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"
/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/16/2016"
    ms.author="spelluru"/>

# <a name="tutorial-build-your-first-azure-data-factory-using-data-factory-rest-api"></a>教學課程︰ 建立您使用的資料工廠 REST API 的第一個 Azure 資料工廠
> [AZURE.SELECTOR]
- [概觀與先決條件](data-factory-build-your-first-pipeline.md)
- [Azure 入口網站](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [資源管理員範本](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

本文中，您可以使用 [資料工廠 REST API 來建立您的第一個 Azure 資料工廠。

## <a name="prerequisites"></a>必要條件
- 閱讀[教學課程概觀](data-factory-build-your-first-pipeline.md)文章並完成的**必要**步驟。
- [Curl](https://curl.haxx.se/dlwiz/)您在電腦上安裝。 若要建立資料工廠捲曲工具使用其餘命令中。 
- 從[這份文件](../resource-group-create-service-principal-portal.md)，請遵循指示︰ 
    1. 建立 Web 應用程式名稱為**ADFGetStartedApp** Azure Active Directory 中。
    2. 取得**用戶端識別碼**及**密碼金鑰**。 
    3. 取得**租用戶 ID**。 
    4. 指派**資料工廠參與者**角色**ADFGetStartedApp**應用程式。  
- 安裝[Azure PowerShell](../powershell-install-configure.md)。  
- 啟動**PowerShell** ，並執行下列命令。 請保持 PowerShell 的 Azure 開啟到本教學課程結尾。 如果您關閉並重新開啟時，必須先再次執行命令。
    1. 執行**登入 AzureRmAccount** ，並輸入使用者名稱和密碼，您用於登入 Azure 入口網站。  
    2. 執行**取得 AzureRmSubscription**檢視此帳戶的所有訂閱。
    3. 執行**取得 AzureRmSubscription-SubscriptionName NameOfAzureSubscription |設定 AzureRmContext**以選取您想要使用的訂閱。 取代**NameOfAzureSubscription** Azure 訂閱的名稱。 
3. 建立 Azure 資源群組**ADFTutorialResourceGroup** PowerShell 中執行下列命令︰  

        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    部分本教學課程中的步驟假設您使用 ADFTutorialResourceGroup 資源群組。 如果您使用不同的資源群組，您需要在此教學課程中使用 ADFTutorialResourceGroup 取代資源群組的名稱。

## <a name="create-json-definitions"></a>建立 JSON 定義
建立下列 JSON curl.exe 所在的資料夾中的檔案。 

### <a name="datafactoryjson"></a>datafactory.json 
> [AZURE.IMPORTANT] 名稱必須是全域唯一的因此您可能要首碼尾碼 ADFCopyTutorialDF，使其唯一的名稱。 

    {  
        "name": "FirstDataFactoryREST",  
        "location": "WestUS"
    }  

### <a name="azurestoragelinkedservicejson"></a>azurestoragelinkedservice.json
> [AZURE.IMPORTANT] 取代**accountname**和**accountkey**名稱與您的 Azure 儲存體帳戶金鑰。 若要瞭解如何取得儲存 access 金鑰，請參閱[檢視、 複製及重新產生的儲存空間便捷鍵](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)。

    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    }


### <a name="hdinsightondemandlinkedservicejson"></a>hdinsightondemandlinkedservice.json

    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.2",
                "clusterSize": 1,
                "timeToLive": "00:30:00",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }

下表提供說明中的程式碼片段使用 JSON 屬性︰

| 屬性 | 描述 |
| :------- | :---------- |
| 版本 | 指定的 HDInsight 版本建立要 3.2 捨位。 | 
| ClusterSize | HDInsight 叢集的大小。 | 
| TimeToLive | 指定的空閒時間 HDInsight 叢集，刪除之前。 |
| linkedServiceName | 指定的儲存空間帳戶用來儲存記錄檔所產生的 HDInsight |

請注意下列重點︰ 

- 資料 Factory 為您建立**Windows 型**HDInsight 叢集，使用上述 JSON。 您也可以讓它建立**Linux 型**HDInsight 叢集。 如需詳細資訊，請參閱[視 HDInsight 連結服務](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)。 
- 您可以使用**您自己的 HDInsight 叢集**，而不是使用視 HDInsight 叢集。 如需詳細資訊，請參閱[HDInsight 連結服務](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)。
- HDInsight 叢集建立**預設容器**中 JSON (**linkedServiceName**) 中所指定的 blob 儲存體。 刪除叢集時，HDInsight 並不會刪除此容器。 此行為是設計。 視需要 HDInsight 連結服務時，每次處理除非有現有的扇形區，建立叢集 HDInsight live 叢集 (**timeToLive**)，而且完成處理之後刪除。

    當處理更多的扇形區，您會看到您 Azure blob 儲存體中的許多容器。 如果您不需要進行的工作的疑難排解，您可能要以減少儲存空間成本將它們刪除。 這些容器的名稱所遵循的圖樣的線條: 「 adf**yourdatafactoryname**-**linkedservicename**datetimestamp 」。 您可以使用工具，例如[Microsoft 儲存檔案總管](http://storageexplorer.com/)來刪除容器中將 Azure blob 儲存體。

如需詳細資訊，請參閱[視 HDInsight 連結服務](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)。 

### <a name="inputdatasetjson"></a>inputdataset.json

    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }


JSON 定義具名**AzureBlobInput**，代表管道活動的輸入的資料的資料集。 此外，它會指定輸入的資料位於稱為**adfgetstarted** blob 容器和稱為**inputdata**] 資料夾中。

下表提供說明中的程式碼片段使用 JSON 屬性︰

| 屬性 | 描述 |
| :------- | :---------- |
| 類型 | [類型] 屬性設 AzureBlob，因為資料位於 Azure blob 儲存體。 |  
| linkedServiceName | 指向您先前建立 StorageLinkedService。 |
| 檔案名稱 | 這是選擇性屬性。 如果您省略此屬性時，所挑選的 folderPath 的所有檔案。 在此情況下，只 input.log 會進行處理。 |
| 類型 | 以文字格式的記錄檔是，因此我們使用 TextFormat。 | 
| columnDelimiter | 以逗號 （，） 分隔的記錄檔中的資料行 |
| 頻率/間隔 | 設定為 [月與間隔的 frequency 不是 1，表示您輸入的扇形區使用每個月。 | 
| 外部 | 設定此屬性，則為 true，則如果輸入的資料不會產生資料工廠服務。 | 

### <a name="outputdatasetjson"></a>outputdataset.json

    {
        "name": "AzureBlobOutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adfgetstarted/partitioneddata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            }
        }
    }

JSON 定義具名**AzureBlobOutput**，代表管道活動的輸出資料的資料集。 此外，它會指定結果會儲存稱為**adfgetstarted** blob 容器和稱為**partitioneddata**] 資料夾中。 **可用性**] 區段中指定的輸出資料集，則會產生每個月。

### <a name="pipelinejson"></a>pipeline.json
> [AZURE.IMPORTANT] **Storageaccountname**取代您的 Azure 儲存體帳戶名稱。 


    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [{
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<stroageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<stroageaccountname>t.blob.core.windows.net/partitioneddata"
                    }
                },
                "inputs": [{
                    "name": "AzureBlobInput"
                }],
                "outputs": [{
                    "name": "AzureBlobOutput"
                }],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }],
            "start": "2016-07-10T00:00:00Z",
            "end": "2016-07-11T00:00:00Z",
            "isPaused": false
        }
    }

JSON 程式碼片段，在您建立包含使用登錄區 HDInsight 叢集上的程序資料至單一活動的管線。

登錄區指令檔、 **partitionweblogs.hql**，儲存在 （指定 scriptLinkedService，稱為**StorageLinkedService**） Azure 儲存體帳戶和**指令碼**] 資料夾中容器**adfgetstarted**。

[**定義**] 區段中指定執行階段設定為登錄區設定值傳遞給登錄區指令碼 (如 ${hiveconf: inputtable}，${hiveconf:partitionedtable})。

管線的**開始**和**結束**屬性會指定作用中的管線期間。

活動 JSON 中，您可以指定登錄區指令碼執行計算指定**linkedServiceName** – **HDInsightOnDemandLinkedService**上。

> [AZURE.NOTE] 在上述範例中所用的 JSON 屬性的詳細資料，請參閱[的管線剖析](data-factory-create-pipelines.md#anatomy-of-a-pipeline)。 

## <a name="set-global-variables"></a>設定全域變數

在 PowerShell 的 Azure 後取代的值，使用您自己，執行下列命令︰

> [AZURE.IMPORTANT] 如需相關指示，取得用戶端識別碼，用戶端 [私人]，請參閱[先決條件](#prerequisites)一節租用戶 ID，以及訂閱 id。   

    $client_id = "<client ID of application in AAD>"
    $client_secret = "<client key of application in AAD>"
    $tenant = "<Azure tenant ID>";
    $subscription_id="<Azure subscription ID>";

    $rg = "ADFTutorialResourceGroup"
    $adf = "FirstDataFactoryREST"



## <a name="authenticate-with-aad"></a>AAD 驗證方法

    $cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
    $responseToken = Invoke-Command -scriptblock $cmd;
    $accessToken = (ConvertFrom-Json $responseToken).access_token;
    
    (ConvertFrom-Json $responseToken) 



## <a name="create-data-factory"></a>建立資料工廠

您可以在此步驟中，建立名為**FirstDataFactoryREST**Azure 資料工廠。 資料工廠多可以有一或多個管線。 管線可以有一或多個活動。 例如，複製活動，將資料複製目的地資料存放區並執行，將資料轉換的登錄區指令碼至 HDInsight 登錄區活動的來源。 執行下列命令以建立資料 factory: 

1. 將命令指派給命名**cmd**的變數。 

    確認的資料 factory 名稱您指定以下 (ADFCopyTutorialDF) 符合**datafactory.json**中指定的名稱。 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/FirstDataFactoryREST?api-version=2015-10-01};
2. 使用**叫用] 命令**，以執行命令。

        $results = Invoke-Command -scriptblock $cmd;
3. 檢視結果。 如果資料 factory 已成功建立，您會看到**結果**; 在資料工廠的 JSON否則，您會看到一則錯誤訊息。  

        Write-Host $results

請注意下列重點︰
 
- Azure 資料工廠名稱必須是全域唯一的。 如果您看到的結果中的錯誤︰**資料工廠名稱 「 FirstDataFactoryREST 」 會無法使用**，請執行下列步驟︰  
    1. 變更**datafactory.json**檔案中的名稱 (例如，yournameFirstDataFactoryREST)。 請參閱[資料工廠-命名規則](data-factory-naming-rules.md)命名規則資料工廠成品主題。
    2. 在第一個命令中**$cmd**變數指派一個值，FirstDataFactoryREST 取代為新名稱，然後執行命令。 
    3. 請執行下兩個命令叫用 REST API，以建立資料 factory 並列印作業的結果。 
- 若要建立資料工廠執行個體，您必須是 Azure 訂閱參與者/管理員
- 可能在未來，因此就會公開顯示的 DNS 名稱登錄資料 factory 的名稱。
- 如果您收到錯誤訊息: 「**使用命名空間 Microsoft.DataFactory 未註冊此訂閱**]，請執行下列其中一項，然後再試一次發佈︰ 

    - 在 PowerShell 的 Azure，執行下列命令以註冊資料工廠提供者︰ 
        
            Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
        您可以執行下列命令以確認您的提供者登錄的資料工廠︰ 
    
            Get-AzureRmResourceProvider
    - 使用 Azure 訂閱到[Azure 入口網站](https://portal.azure.com)登入並瀏覽至資料工廠刀 （或） Azure 入口網站中建立資料工廠。 這個動作會自動註冊您的提供者。

建立管線之前, 必須先建立幾個資料工廠實體。 您先建立連結至您的資料存放區的 [資料儲存區/計算定義的輸入與輸出資料集，以代表連結的資料存放區中的資料連結的服務。 

## <a name="create-linked-services"></a>建立連結的服務 
在此步驟中，您連結 Azure 儲存體帳戶並視需要 Azure HDInsight 叢集至您的資料工廠。 Azure 儲存體帳戶會保留在這個範例中的管線的輸入與輸出資料。 連結的 HDInsight 服務用來執行這個範例中的管線活動中指定的登錄區指令碼。 

### <a name="create-azure-storage-linked-service"></a>建立連結的 Azure 儲存服務
在此步驟中，您可以連結 Azure 儲存體帳戶至您的資料工廠。 使用此教學課程中，您可以使用相同的 Azure 儲存體帳戶儲存輸入輸出資料和 HQL 指令碼檔。

1. 將命令指派給命名**cmd**的變數。 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azurestoragelinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
2. 使用**叫用] 命令**，以執行命令。
 
        $results = Invoke-Command -scriptblock $cmd;
3. 檢視結果。 如果已順利建立連結的服務，您會看到**結果**; 在連結的服務 JSON否則，您會看到一則錯誤訊息。
  
        Write-Host $results

### <a name="create-azure-hdinsight-linked-service"></a>建立連結的 Azure HDInsight 服務
在此步驟中，您可以連結視 HDInsight 叢集至您的資料工廠。 HDInsight 叢集自動在執行階段建立及刪除之後，為了處理及空閒指定的時間量。 您可以使用您自己的 HDInsight 叢集，而不是使用視 HDInsight 叢集。 如需詳細資訊，請參閱[計算連結的服務](data-factory-compute-linked-services.md)。  

1. 將命令指派給命名**cmd**的變數。
 
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@hdinsightondemandlinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/hdinsightondemandlinkedservice?api-version=2015-10-01};
2. 使用**叫用] 命令**，以執行命令。

        $results = Invoke-Command -scriptblock $cmd;
3. 檢視結果。 如果已順利建立連結的服務，您會看到**結果**; 在連結的服務 JSON否則，您會看到一則錯誤訊息。  

        Write-Host $results

## <a name="create-datasets"></a>建立資料集
在此步驟中，您可以建立資料集，以表示輸入與輸出的登錄區處理資料。 您之前在本教學課程中建立**StorageLinkedService**參考這些資料集。 指向 Azure 儲存體帳戶和資料集之間的連結的服務指定容器、 資料夾、 檔案名稱中含有輸入的儲存空間和輸出資料。   

### <a name="create-input-dataset"></a>建立輸入資料集
在此步驟中，您可以建立代表輸入 Azure Blob 儲存體中儲存的資料輸入的資料集。

1. 將命令指派給命名**cmd**的變數。 

        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
2. 使用**叫用] 命令**，以執行命令。

        $results = Invoke-Command -scriptblock $cmd;
3. 檢視結果。 如果資料集已成功建立，您會看到**結果**; 中的資料集 JSON否則，您會看到一則錯誤訊息。
  
        Write-Host $results
### <a name="create-output-dataset"></a>建立輸出資料集
在此步驟中，您可以建立輸出資料集來代表 Azure Blob 儲存體中儲存的輸出資料。

1. 將命令指派給命名**cmd**的變數。
 
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobOutput?api-version=2015-10-01};
2. 使用**叫用] 命令**，以執行命令。

        $results = Invoke-Command -scriptblock $cmd;
3. 檢視結果。 如果資料集已成功建立，您會看到**結果**; 中的資料集 JSON否則，您會看到一則錯誤訊息。
  
        Write-Host $results 

## <a name="create-pipeline"></a>建立管線
在此步驟中，您可以建立您的第一個管線與**HDInsightHive**活動。 輸入的扇形區有每月 (頻率︰ Month、 間隔︰ 1) 輸出扇形區不會產生每月與活動的排程器屬性也會設定為每個月。 必須符合的輸出資料集與活動排程器設定。 目前，輸出資料集是什麼磁碟機排程，因此您必須建立輸出資料集，即使活動不會產生任何輸出。 如果活動不需要任何輸入，您可以略過建立輸入資料集。  

確認 [請參閱**input.log**資料夾中的檔案**adfgetstarted/inputdata** Azure blob 儲存體中，執行下列命令以部署管道的郵件]。 **啟動**和**結束**時間過去的經驗，並設定**isPaused**設為 false，因為管線 （活動管道） 執行部署之後，立即。 

1. 將命令指派給命名**cmd**的變數。
 
        $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
2. 使用**叫用] 命令**，以執行命令。

        $results = Invoke-Command -scriptblock $cmd;
3. 檢視結果。 如果資料集已成功建立，您會看到**結果**; 中的資料集 JSON否則，您會看到一則錯誤訊息。  

        Write-Host $results
5. 恭喜您，您已成功建立您的第一個管線使用 PowerShell 的 Azure ！

## <a name="monitor-pipeline"></a>監視器管線
在此步驟中，您可以使用 [資料工廠 REST API 來監視管線所產生的扇形區。

    $ds ="AzureBlobOutput"

    $cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};

    $results2 = Invoke-Command -scriptblock $cmd;

    IF ((ConvertFrom-Json $results2).value -ne $NULL) {
        ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
    } else {
            (convertFrom-Json $results2).RemoteException
    }


> [AZURE.IMPORTANT] 
> 視需要 HDInsight 叢集建立通常會有時 （大約 20 分鐘）。 因此，預期管線需要**30 分鐘**才能扇形區 [處理程序。  

執行叫用] 命令和下一個步驟，直到您看到的扇形區，**準備好**狀態或**失敗**」 狀態。 準備好狀態扇形區後，請核取**adfgetstarted**容器中的輸出資料您 blob 儲存體中的 [ **partitioneddata** ] 資料夾。  視需要 HDInsight 叢集的建立通常需要一些時間。

![輸出資料](./media/data-factory-build-your-first-pipeline-using-rest-api/three-ouptut-files.png)

> [AZURE.IMPORTANT] 扇形區已成功處理時，會刪除輸入的檔案。 因此，如果您想要重新執行扇形區，或重複動作教學課程，檔案上傳輸入 (input.log) adfgetstarted 容器的 inputdata 資料夾。

您也可以使用 Azure 入口網站，來監視扇形區，以及任何問題進行疑難排解。 請參閱[監視使用 Azure 入口網站的管線](data-factory-build-your-first-pipeline-using-editor.md##monitor-pipeline)詳細資料。  

## <a name="summary"></a>摘要 
在本教學課程中，您可以建立程序資料 Azure 資料工廠 HDInsight hadoop 叢集上執行登錄區指令碼。 您使用資料工廠編輯器 Azure 入口網站中執行下列步驟︰  

1.  建立 Azure**資料工廠**。
2.  建立兩個**連結的服務**︰
    1.  **Azure 儲存體**連結服務連結輸入輸出檔案會保留資料 factory 您 Azure blob 儲存體。
    2.  **Azure HDInsight**視連結的服務連結資料 factory 視 HDInsight Hadoop 叢集。 Azure 資料工廠建立 HDInsight Hadoop 叢集只時間處理程序輸入的資料，並產生輸出資料。 
3.  建立兩個**資料集**，說明管道 HDInsight 登錄區活動的輸入與輸出資料。 
4.  建立**管線**與**HDInsight 登錄區**活動。 

## <a name="next-steps"></a>後續步驟
在本文中，您建立管線與視 Azure HDInsight 叢集執行登錄區指令碼至轉換活動 （HDInsight 活動）。 若要瞭解如何使用複製活動，將資料複製到 Azure SQL Azure Blob，請參閱[教學課程︰ 資料複製到 Azure SQL Azure Blob](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

## <a name="see-also"></a>另請參閱
| 主題 | 描述 |
| :---- | :---- |
| [資料工廠 REST API 參照](https://msdn.microsoft.com/library/azure/dn906738.aspx) |  請參閱資料工廠 cmdlet 全面涵蓋所有內容的文件 |
| [資料轉換活動](data-factory-data-transformation-activities.md) | 本文提供的資料轉換活動 （例如在本教學課程中所使用的 HDInsight 登錄區轉換） 清單 Azure 資料工廠支援。 |
| [排程和執行](data-factory-scheduling-and-execution.md) | 本文說明 Azure 資料 Factory 應用程式模型的排程和執行方面。 |
| [管線](data-factory-create-pipelines.md) | 本文可協助您瞭解管線和 Azure 資料工廠，以及如何用來建構端對端的資料導向的工作流程案例或企業版中的活動。 |
| [資料集](data-factory-create-datasets.md) | 本文可協助您瞭解 Azure 資料工廠中的資料集。
| [監控和管理的管線使用 Azure 入口網站刀](data-factory-monitor-manage-pipelines.md) | 本文將說明如何監控、 管理及偵錯使用 Azure 入口網站刀管線。 |
| [監控和管理使用監視的應用程式的管線](data-factory-monitor-manage-app.md) | 本文將說明如何監控、 管理及偵錯管線使用 [監控和管理應用程式。 

