<properties
    pageTitle="重新新的 web 服務使用的電腦學習管理 PowerShell 指令程式的訓練 |Microsoft Azure"
    description="瞭解如何以程式設計方式重新訓練模型及更新 web 服務中使用電腦學習管理 PowerShell cmdlet Azure 電腦學習使用新訓練的模型。"
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondlaghaeian"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="v-donglo"/>

# <a name="retrain-a-new-web-service-using-the-machine-learning-management-powershell-cmdlets"></a>重新訓練新的 web 服務使用電腦學習管理 PowerShell cmdlet

當您重新訓練新的 web 服務時，您就會更新的預測 web 服務定義来參照的新訓練的模型。  

## <a name="prerequisites"></a>必要條件

您必須設定訓練實驗及預測的實驗[重新訓練電腦學習以程式設計方式模型](machine-learning-retrain-models-programmatically.md)中所示。 

>[AZURE.IMPORTANT] 預測實驗必須部署為 Azure 資源管理員 （新式） 根據電腦，學習 web 服務。 
 
如需有關部署 web 服務的詳細資訊，請參閱[部署 Azure 電腦學習 web 服務](machine-learning-publish-a-machine-learning-web-service.md)。

此程序需要您安裝 Azure 電腦學習 Cmdlet。 安裝的電腦學習指令程式的資訊，請參閱 MSDN 上的[Azure 電腦學習 Cmdlet](https://msdn.microsoft.com/library/azure/mt767952.aspx)參考。

從訓練輸出複製下列資訊︰

* BaseLocation
* RelativeLocation

您所採取的步驟如下︰

1.  Azure 資源管理員帳戶登入。
2.  取得 web 服務定義
3.  將 Web 服務定義匯出為 JSON
4.  更新中 JSON ilearner blob 的參照。
5.  JSON 匯入 Web 服務定義
6.  使用新的 Web 服務定義更新 web 服務

## <a name="sign-in-to-your-azure-resource-manager-account"></a>Azure 資源管理員帳戶登入

從 Azure 帳戶使用[新增 AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) cmdlet PowerShell 環境中，您必須先登入。

## <a name="get-the-web-service-definition"></a>取得 Web 服務定義

接下來，取得 Web 服務，則可電話[取得 AzureRmMlWebService](https://msdn.microsoft.com/library/mt619267.aspx)指令程式。 Web 服務定義內部呈現訓練的 web 服務的模型，而不是直接修改。 請確定您的預測實驗和不訓練實驗擷取 Web 服務定義。

    $wsd = Get-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

若要判斷現有的 web 服務的資源群組名稱，請執行取得 AzureRmMlWebService cmdlet 不具任何參數，以在您的訂閱中顯示的 web 服務。 找出的 web 服務，再看看它 web 服務 id。 資源群組的名稱*resourceGroups*項目之後，就是在 ID] 中的第四個項目。 在下列範例中，資源群組的名稱會是預設-MachineLearning-SouthCentralUS。

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

或者，若要判斷現有的 web 服務的資源群組名稱，登入 Microsoft Azure 機器學習 Web 服務入口網站。 選取 [web 服務]。 資源群組的名稱會*resourceGroups*項目之後，只是 web 服務的 URL，第五個項目。 在下列範例中，資源群組的名稱會是預設-MachineLearning-SouthCentralUS。

    https://services.azureml.net/subscriptions/<subcription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237


## <a name="export-the-web-service-definition-as-json"></a>將 Web 服務定義匯出為 JSON

若要修改定義，訓練的模型，以使用新訓練模型，您必須先使用[匯出 AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767935.aspx)指令程式匯出到 JSON 格式檔案。

    Export-AzureRmMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

## <a name="update-the-reference-to-the-ilearner-blob-in-the-json"></a>更新中 JSON ilearner blob 的參照。

在資產，找出 [訓練的模型] 中，以 ilearner blob 的 URI 更新*locationInfo*節點的*uri*值。 URI 是由組合*BaseLocation*和從重新訓練通話 BES 的輸出*RelativeLocation*產生。

     "asset3": {
        "name": "Retrain Samp.le [trained model]",
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

## <a name="import-the-json-into-a-web-service-definition"></a>JSON 匯入 Web 服務定義

若要將已修改的 JSON 檔案轉換回更新 Predicative 實驗，您可以使用 Web 服務定義，您必須使用[匯入 AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767925.aspx)指令程式。

    $wsd = Import-AzureRmMlWebService -InputFile "C:\temp\mlservice_export.json"


## <a name="update-the-web-service-with-new-web-service-definition"></a>使用新的 Web 服務定義更新 web 服務

最後，您可以使用[更新 AzureRmMlWebService](https://msdn.microsoft.com/library/azure/mt767922.aspx) cmdlet 來更新預測實驗。

    Update-AzureRmMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'  -ServiceUpdates $wsd

## <a name="summary"></a>摘要

您可以使用電腦學習 PowerShell 管理 cmdlet，更新訓練的模型的預測 Web 服務啟用案例，例如︰

* 以新資料重新訓練定期的模型。
* 模型給客戶的通訊群組以讓他們重新訓練使用自己的資料模型為目標。
