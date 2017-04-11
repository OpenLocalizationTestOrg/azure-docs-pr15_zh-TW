<properties
pageTitle="從一個實驗建立多個模型 |Microsoft Azure"
description="使用 PowerShell 來建立多個電腦學習模型和 web 服務端點具有相同的演算法，但不同訓練資料集。"
services="machine-learning"
documentationCenter=""
authors="hning86"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="machine-learning"
ms.workload="data-services"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="10/03/2016"
ms.author="garye;haining"/>

# <a name="create-many-machine-learning-models-and-web-service-endpoints-from-one-experiment-using-powershell"></a>建立多個電腦學習模型和 web 服務端點從一個實驗使用 PowerShell

以下是常見的電腦學習問題︰ 您想要建立具有相同的訓練課程工作流程並使用相同的演算法，但有做為輸入不同的訓練資料集的許多模型。 本文將示範如何進行此動作在 Azure 電腦學習 Studio 中使用只單一實驗。

例如，假設您擁有自己的全域 safetystockllevel 租賃而企業。 您想要建立預測歷史資料為基礎的租賃要求迴歸模型。 您有 1000 租賃位置世界，而且您所收集到的每個包含重要的功能，例如日期、 時間、 天氣的位置和流量的每個位置的特定資料集。

您無法訓練一次在所有的地方使用合併的版本，所有的資料集的模型。 但每個您位置都有唯一的環境，因為更好的方法就是訓練分別為每個位置使用資料集的迴歸模型。 如此一來，每個訓練的模型可能會考慮*不同存放區的大小、 大量、 地理位置、 母體、 safetystockllevel 易於流量環境等等*。

可能是最好的方法，但不想要建立 1000 訓練實驗中 Azure 電腦學習使用各代表不同的位置。 除了驚人的任務，也會看起來很效率，因為每個實驗得都相同除了訓練資料集的元件。

所幸，我們可以完成使用[Azure 電腦學習重新訓練 API](machine-learning-retrain-models-programmatically.md)和自動化工作[學習 PowerShell 的 Azure 電腦](machine-learning-powershell-module.md)。

> [AZURE.NOTE] 若要讓我們執行速度更快的範例，我們將會減少 1000 10 的位置。 但相同的原則和程序適用於 1000 的位置。 唯一的差別，如果您想要從 1000 的資料集訓練您可能會想要的同時執行下列 PowerShell 指令碼。 如何執行的是本文中的範圍之內，但您可以找到範例 PowerShell 多執行緒處理在網際網路上。  

## <a name="set-up-the-training-experiment"></a>設定訓練實驗

我們使用範例[訓練體驗](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Training-Experiment-1)，我們已建立[Cortana 智慧圖庫](http://gallery.cortanaintelligence.com)中。 開啟這個實驗[Azure 電腦學習 Studio](https://studio.azureml.net)工作區中。

>[AZURE.NOTE] 若要遵循此範例中，您可能會想要使用標準工作區，而不是免費的工作區。 我們將會建立一個端點每位客戶-10 的結束點的總計，並且的免費的工作區僅限於 3 的結束點之後需要標準的工作區。 如果您只有一個可用的工作區，只要修改下列 3 位置允許指令碼。

實驗從 Azure 儲存體帳戶匯入訓練資料集*customer001.csv*使用**匯入資料**模組。 假設我們已從所有 safetystockllevel 租賃位置收集訓練資料集，並以範圍是從*rentalloc001.csv*到*rentalloc10.csv*的檔案名稱將其儲存在相同的 blob 儲存位置。

![圖像](./media/machine-learning-create-models-and-endpoints-with-powershell/reader-module.png)

請注意**訓練模型**模組中已加入**Web 服務輸出**模組。
Web 服務為部署這個實驗時，結束點與相關聯，輸出會傳回訓練的模型中的.ilearner 檔案格式。

也請注意，我們已設定 web 服務參數的**資料匯入**模組使用的 url。 這個選項可讓我們使用參數以指定訓練的模型，每個位置的個別訓練資料集。
還有其他方法，我們無法完成，例如使用 web 服務參數中的 SQL 查詢，以取得資料從 SQL Azure 資料庫，或只使用**Web 服務輸入**模組中資料集的傳送到 web 服務。

![圖像](./media/machine-learning-create-models-and-endpoints-with-powershell/web-service-output.png)

現在讓我們來執行這項使用預設值*rental001.csv*與訓練資料集的訓練課程實驗。 如果您檢視的**評估**模組輸出 （按一下 [輸出] 並選取**視覺效果**），您可以查看我們取得*AUC*不錯效能 = 0.91。 現在，我們已準備好部署不在這個訓練實驗 web 服務。

## <a name="deploy-the-training-and-scoring-web-services"></a>部署訓練和計分 web 服務

若要部署的訓練課程 web 服務，按一下實驗畫布之下的 [**設定 Web 服務**] 按鈕並選取**部署 Web 服務**。 呼叫此 web 服務 「 「 Safetystockllevel 租賃訓練 」。

現在，我們需要部署計分的 web 服務。
若要這麼做，我們可以按一下 [畫布之下的 [**設定 Web 服務**，然後選取 [**預測 Web 服務**。 這樣會建立計分的實驗。
我們需要幾個稍微調整，使其運作的 web 服務，例如 [移除標籤欄 「 計數通知: 「 從輸入資料，限制只有的執行個體識別碼和對應的輸出預測值。

若要儲存您自己的您可以直接已準備圖庫中開啟[預測實驗](https://gallery.cortanaintelligence.com/Experiment/Bike-Rental-Predicative-Experiment-1)。

若要部署 web 服務，執行預測實驗中，然後按一下畫布之下的 [**部署 Web 服務**] 按鈕。 命名為 「 Safetystockllevel 租賃計分 」 的計分 web 服務]。

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>使用 PowerShell 建立 10 相同的 web 服務端點

這個 web 服務隨附的預設端點。 但由於無法更新我們不為感興趣的預設端點。 我們需要做些什麼是，建立 10 個額外的端點，分別為每個位置。 我們會使用 PowerShell 來執行此動作。

首先，我們會為我們的 PowerShell 環境設定︰

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and is properly set to point to the valid Workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

然後，執行下列 PowerShell 命令︰

    # Create 10 endpoints on the scoring web service.
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpointName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

現在我們建立 10 結束點，然後它們都包含相同的訓練的模型訓練*customer001.csv*上。 您可以在 Azure 管理入口網站中檢視。

![圖像](./media/machine-learning-create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>更新使用不同的訓練資料集使用 PowerShell 端點

下一步是唯一訓練每個客戶的個別資料模型與更新的結束點。 但我們需要以產生這些模型**Safetystockllevel 租賃訓練課程**web 服務的第一次。 回到**Safetystockllevel 租賃訓練課程**web 服務。 我們需要呼叫其 BES 端點 10 次 10 個不同的訓練資料集，才能產生 10 個不同的模型。 若要這麼做，我們將使用**InovkeAmlWebServiceBESEndpoint** PowerShell 指令程式。

您也必須將 blob 儲存體帳戶提供的認證`$configContent`，也就是在欄位`AccountName`，`AccountKey`和`RelativeLocation`。 `AccountName`可以是您帳戶的名稱，如**傳統 Azure 管理入口網站**（[*儲存*] 索引標籤） 中所示。 一旦您在儲存的帳戶，按一下其`AccountKey`即可找到按底部的 [**管理便捷鍵**] 按鈕，然後複製*Access 的主索引鍵*。 `RelativeLocation`是相對於您的儲存空間的路徑儲存新的模型。 例如，路徑`hai/retrain/bike_rental/`容器，名稱會指向下方的指令碼`hai`，及`/retrain/bike_rental/`子資料夾。 目前您無法建立透過 UI，入口網站的子資料夾，但有[許多 Azure 儲存體探險者](../storage/storage-explorers.md)，可讓您執行此作業。 建議您在您要儲存新的訓練的模型 （.ilearner 檔案），如下所示的儲存空間中建立新的容器︰ 您儲存頁面上，按一下 [**新增**] 按鈕，底部，並將其命名`retrain`。 在 [摘要] 下的指令碼必要的變更屬於`AccountName`，`AccountKey`和`RelativeLocation`(:`"retrain/model' + $seq + '.ilearner"`)。

    # Invoke the retraining API 10 times
    # This is the default (and the only) endpoint on the training web service
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

>[AZURE.NOTE] BES 端點是只支援的模式這項作業。 給無法用於產生訓練的模型。

您可以看到上方，而非建構 10 不同 BES 工作設定 json 檔案，我們動態改為建立 config 字串和摘要*jobConfigString*參數的**InvokeAmlWebServceBESEndpoint** cmdlet，因為實際上不是需要保留在磁碟上的複本。

如果一切順利，一段時間之後您應該會看到 10.ilearner 檔案， *model001.ilearner* *model010.ilearner*，以從 Azure 儲存體帳戶中。 現在，我們已準備好更新**修補程式 AmlWebServiceEndpoint** PowerShell 指令程式的使用這些模型我們 10 計分 web 服務端點。 請記住，一次，我們只修補我們以程式設計方式先前建立的非預設結束點。

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '<my_blob_sas_token>'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }

此應該相當快速執行。 執行完成時，我們會已成功建立 10 預測 web 服務端點，每個包含唯一訓練特定的資料集上租賃位置，所有從單一訓練實驗的訓練的模型。 要進行確認，您可以嘗試呼叫使用**InvokeAmlWebServiceRRSEndpoint**指令程式，這些端點提供相同的輸入資料，您應該會看到不同的預測的結果，因為模型以不同的訓練課程集合訓練。

## <a name="full-powershell-script"></a>完整的 PowerShell 指令碼

以下是完整的程式碼的清單︰

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and properly set to point to the valid workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

    # Create 10 endpoints on the scoring web service
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpontName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

    # Invoke the retraining API 10 times to produce 10 regression models in .ilearner format
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '?test'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }
