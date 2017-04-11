<properties
    pageTitle="重新訓練現有的預測 web 服務 |Microsoft Azure"
    description="瞭解如何重新訓練模型及更新 web 服務中 Azure 電腦學習使用新訓練的模型。"
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondl"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/06/2016"
    ms.author="v-donglo"/>


# <a name="retrain-an-existing-predictive-web-service"></a>重新訓練現有的預測 web 服務

這份文件說明訓練的程序，針對下列情況︰

* 您有訓練實驗以及您已經部署 operationalized 的 web 服務為預測實驗。
* 您有新的資料要用來執行其計分預測 web 服務。

開始使用您現有的 web 服務和實驗，必須遵循下列步驟︰

1. 更新模型。
    1. 修改訓練實驗允許的 web 服務輸入和輸出。
    2. 部署訓練實驗為訓練 web 服務。
    3. 若要重新訓練模型使用訓練實驗批次執行服務 (BES)。
2. 您可以使用 Azure 電腦學習 PowerShell cmdlet 來更新預測實驗。
    1.  Azure 資源管理員帳戶登入。
    2.  取得 web 服務定義。
    3.  將 web 服務定義匯出為 JSON 中。
    4.  更新中 JSON ilearner blob 的參照。
    5.  JSON 匯入 web 服務定義。
    6.  使用新的 web 服務定義更新 web 服務。

## <a name="deploy-the-training-experiment"></a>部署訓練實驗

若要部署訓練實驗為訓練 web 服務，您必須新增 web 服務輸入和輸出模型。 只要連接*Web 服務輸出*模組實驗的*[訓練模型][train-model]*模組，請啟用訓練實驗以產生新的訓練的模型預測實驗中，您可以使用。 如果您有*評估模型*模組，您也可以附加 web 服務輸出來作為輸出獲得評估的結果。

若要更新訓練實驗︰

1. 您的資料輸入 （例如*Clean 遺失資料*模組） 連線*Web 服務輸入*模組。 一般而言，您會想要確保原始訓練資料的相同方式，來處理您輸入的資料。
2. 連線至您的*訓練模型*模組的輸出的*Web 服務輸出*模組。
3. 如果您有*評估模型*模組，您想要輸出的評估結果連線的*評估模型*模組輸出*Web 服務輸出*模組。

執行實驗。

接下來，您必須部署訓練實驗為產生的訓練的模型和模型評估結果的 web 服務。  

在實驗畫布底部，按一下**設定 Web 服務**]，然後選取**[新增] 部署 Web 服務**。 Azure 機器學習 Web 服務入口網站會開啟 [**部署 Web 服務**] 頁面。 輸入您的 web 服務的名稱，選擇 [付款計劃，然後再按一下 [**部署**。 您可以只使用批次執行方法建立訓練的模型。


## <a name="retrain-the-model-with-new-data-by-using-bes"></a>重新以新資料模型訓練使用 BES

例如，我們將使用 C# 建立訓練應用程式。 您也可以使用 Python 或 R 範例程式碼，完成這項工作。

若要打電話給訓練 Api:

1. 在 Visual Studio 建立 C# 主控台應用程式 (**新增** > **專案** > **Windows 桌面** > **主控台應用程式**)。
2.  登入電腦學習 Web 服務入口網站。
3.  按一下您要使用的 web 服務。
2.  按一下 [**取用**]。
3.  在頁面底部的 [**消耗**，在 [**範例**] 區段中，按一下 [**批次**]。
5.  複製批次執行的範例 C# 程式碼，並將它貼到 Program.cs 檔案。 請確定命名空間保持不變。

新增註解中所指定的 NuGet 封裝 Microsoft.AspNet.WebApi.Client。 若要新增 Microsoft.WindowsAzure.Storage.dll 的參照，您可能必須安裝[Azure 儲存體服務的用戶端文件庫](https://www.nuget.org/packages/WindowsAzure.Storage)。

以下螢幕擷取畫面顯示**消耗**在 Azure 機器學習 Web 服務入口網站。

![使用頁面][1]

### <a name="update-the-apikey-declaration"></a>更新 apikey 宣告

找出**apikey**宣告︰

    const string apiKey = "abc123"; // Replace this with the API key for the web service

在 [**消耗**頁面的 [**基本消耗資訊**] 區段中，找出主索引鍵並將其複製到**apikey**宣告。

### <a name="update-the-azure-storage-information"></a>更新 Azure 儲存體資訊

BES 碼範例上傳至 Azure 儲存體從本機磁碟機 (例如，「 C:\temp\CensusIpnput.csv 」) 檔案、 程序，並將結果寫回到 Azure 儲存體。  

Azure 儲存體資訊更新，請您必須擷取儲存體帳戶名稱、 金鑰，以及容器資訊從 Azure 傳統入口網站，您儲存帳戶及更新後執行您的實驗結果的工作流程 correspondi 然後應該如下︰

![工作流程執行後的結果][4]ng 程式碼中的值。

1. Azure 傳統入口網站登入。
1. 在左側導覽畫面中的欄中，按一下 [**儲存**]。
1. 從儲存的帳戶清單中，選取其中一個 retrained 的模型儲存。
1. 在頁面底部，按一下 [**管理便捷鍵**。
1. 複製儲存**Access 的主索引鍵**，並關閉對話方塊。
1. 在頁面頂端，按一下 [**容器**]。
1. 選取現有的容器，或建立新的項目並儲存名稱。

尋找*StorageAccountName* *StorageAccountKey*，與*StorageContainerName*宣告及更新您儲存在 [傳統] 入口網站中的值。

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

您也必須確定此輸入的檔案可在程式碼中所指定的位置。

### <a name="specify-the-output-location"></a>指定輸出位置

當您在要求的內容中指定的輸出位置時，必須指定*RelativeLocation*中指定檔案的副檔名為`ilearner`。 下列範例，請參閱︰

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

下列是訓練輸出的範例︰![重新訓練輸出][6]

## <a name="evaluate-the-retraining-results"></a>評估訓練的結果

當您執行應用程式時，包括的 URL 及共用的存取簽章權杖所需存取評估結果。

您可以結合*BaseLocation*、 *RelativeLocation*和*SasBlobToken*從*output2*的輸出結果 （如訓練輸出圖所示），並將完整的 URL 貼到瀏覽器網址列，請參閱 retrained 模型的效能結果。  

檢查，判斷是否新訓練的模型會執行清楚取代現有的結果。

複製*BaseLocation* *RelativeLocation*，與*SasBlobToken*輸出結果。

## <a name="retrain-the-web-service"></a>重新訓練的 web 服務

當您重新訓練新的 web 服務時，您就會更新的預測 web 服務定義来參照的新訓練的模型。 Web 服務定義內部呈現訓練的 web 服務的模型，而不是直接可修改。 請確定您的預測實驗和不訓練實驗擷取 web 服務定義。

## <a name="sign-in-to-azure-resource-manager"></a>登入至 Azure 資源管理員

您必須先登入您 Azure 帳戶從 PowerShell 環境中使用[新增 AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx)指令程式。

## <a name="get-the-web-service-definition-object"></a>取得 Web 服務定義物件

接下來，取得 Web 服務定義物件，則可電話[取得 AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx)指令程式。

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

若要判斷現有的 web 服務的資源群組名稱，請執行取得 AzureRmMlWebService cmdlet 不具任何參數，以在您的訂閱中顯示的 web 服務。 找出的 web 服務，再看看它 web 服務 id。 資源群組的名稱*resourceGroups*項目之後，就是在 ID] 中的第四個項目。 在下列範例中，資源群組的名稱會是預設-MachineLearning-SouthCentralUS。

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

或者，若要判斷現有的 web 服務的資源群組名稱，請登入 Azure 機器學習 Web 服務入口網站。 選取 [web 服務]。 資源群組的名稱會*resourceGroups*項目之後，只是 web 服務的 URL，第五個項目。 在下列範例中，資源群組的名稱會是預設-MachineLearning-SouthCentralUS。

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-object-as-json"></a>匯出為 JSON Web 服務定義物件

若要修改的訓練的模型，以使用新訓練的模型定義，您必須先使用[匯出 AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx)指令程式匯出到 JSON 格式的檔案。

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob"></a>更新 ilearner blob 的參照

在資產，找出 [訓練的模型] 中，以 ilearner blob 的 URI 更新*locationInfo*節點的*uri*值。 URI 是由組合*BaseLocation*和從重新訓練通話 BES 的輸出*RelativeLocation*產生。

     "asset3": {
        "name": "Retrain Sample [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

## <a name="import-the-json-into-a-web-service-definition-object"></a>JSON 匯入 Web 服務定義物件

若要將已修改的 JSON 檔案轉換回更新 predicative 實驗，您可以使用 Web 服務定義物件，您必須使用[匯入 AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx)指令程式。

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service"></a>更新 web 服務

最後，更新預測實驗中使用[更新 AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx)指令程式。

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -

[1]: ./media/machine-learning-retrain-existing-arm-web-service/machine-learning-retrain-models-consume-page.png
[4]: ./media/machine-learning-retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE04.png
[6]: ./media/machine-learning-retrain-existing-arm-web-service/machine-learning-retrain-models-programmatically-IMAGE06.png

<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
