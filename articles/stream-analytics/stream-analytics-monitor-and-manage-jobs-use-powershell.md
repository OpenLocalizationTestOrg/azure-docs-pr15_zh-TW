<properties 
    pageTitle="監控和管理資料流分析工作使用 PowerShell |Microsoft Azure" 
    description="瞭解如何使用 PowerShell 的 Azure 和 cmdlet 來監控和管理資料流分析工作。" 
    keywords="azure powershell、 azure powershell cmdlet powershell 命令 powershell 指令碼" 
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>


# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>監控和管理資料流分析工作透過 Azure PowerShell cmdlet

瞭解如何監控和管理資料流分析與 Azure PowerShell cmdlet powershell 指令碼的資源執行資料流分析資料的基本工作。

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>執行 Azure PowerShell 指令程式串流分析的先決條件

 - 建立您的訂閱中的 Azure 資源群組。 以下是範例 Azure PowerShell 指令碼。 PowerShell 的 Azure 資訊，請參閱[安裝及設定 PowerShell 的 Azure](../powershell-install-configure.md);  

Azure PowerShell 0.9.8:  

        # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group if you have more than one subscription on your account.
        Select-AzureSubscription -SubscriptionName <subscription name>
 
        # If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
        #Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>

Azure PowerShell 1.0:  

        # Log in to your Azure account
        Login-AzureRmAccount

        # Select the Azure subscription you want to use to create the resource group.
        Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

        # If Stream Analytics has not been registered to the subscription, remove remark symbol below (#) to run the Register-AzureProvider cmdlet to register the provider namespace.
        #Register-AzureRmResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
        
        # Create an Azure resource group
        New-AzureRMResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
        


> [AZURE.NOTE] 以程式設計方式建立的資料流分析作業沒有監控預設為啟用。  您可以手動啟用監控 Azure 入口網站中，瀏覽至工作的 [監視器] 頁面，按一下 [啟用] 按鈕，或您可以透過以下程式位於[Azure 資料流分析-監視器串流分析作業以程式設計方式](stream-analytics-monitor-jobs.md)的步驟，即可。

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Azure 的 PowerShell 指令程式串流分析
下列 Azure PowerShell 指令程式可用來監控和管理 Azure 資料流分析工作。 請注意，PowerShell 的 Azure 不同版本。 
**在範例中所列的第一個命令適用於 PowerShell 的 Azure 0.9.8，第二個命令是 Azure PowerShell 1.0。** Azure PowerShell 1.0 命令將會永遠獲得 「 AzureRM 」 命令中。

### <a name="get-azurestreamanalyticsjob--get-azurermstreamanalyticsjob"></a>取得 AzureStreamAnalyticsJob |取得 AzureRMStreamAnalyticsJob
清單中的 Azure 訂閱或指定的資源] 群組中，定義的所有資料流分析作業，或取得資源群組內的特定工作的相關資訊。

**範例 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob

這個 PowerShell 命令會傳回 Azure 訂閱中所有的資料流分析工作的相關資訊。

**範例 2**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 

這個 PowerShell 命令會傳回 StreamAnalytics 預設-Central-適用於美國的 [資源] 群組中所有的資料流分析工作的相關資訊。

**範例 3**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob

這個 PowerShell 命令傳回 StreamAnalytics 預設-Central-適用於美國的 [資源] 群組中的資料流分析工作 StreamingJob 的相關資訊。

### <a name="get-azurestreamanalyticsinput--get-azurermstreamanalyticsinput"></a>取得 AzureStreamAnalyticsInput |取得 AzureRMStreamAnalyticsInput
列出所有中指定的資料流分析作業，所定義的輸入，或取得有關特定的輸入的資訊。

**範例 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

這個 PowerShell 命令會傳回所有工作 StreamingJob 中定義的輸入的相關資訊。

**範例 2**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream

這個 PowerShell 命令傳回名稱為 EntryStream 工作 StreamingJob 中定義的輸入的資訊。

### <a name="get-azurestreamanalyticsoutput--get-azurermstreamanalyticsoutput"></a>取得 AzureStreamAnalyticsOutput |取得 AzureRMStreamAnalyticsOutput
列出所有輸出中指定的資料流分析作業，定義或取得特定的輸出的相關資訊。

**範例 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

這個 PowerShell 命令傳回工作 StreamingJob 中定義的輸出的相關資訊。

**範例 2**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output

這個 PowerShell 命令會傳回名稱為工作 StreamingJob 中定義的輸出的輸出的相關資訊。

### <a name="get-azurestreamanalyticsquota--get-azurermstreamanalyticsquota"></a>取得 AzureStreamAnalyticsQuota |取得 AzureRMStreamAnalyticsQuota
取得配額的相關資訊串流中指定的單位。

**範例 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsQuota –Location "Central US" 

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsQuota –Location "Central US" 

這個 PowerShell 命令會傳回的配額及使用方式串流單位的相關資訊，在 [美國中部區域。

### <a name="get-azurestreamanalyticstransformation--getazurermstreamanalyticstransformation"></a>取得 AzureStreamAnalyticsTransformation |GetAzureRMStreamAnalyticsTransformation
取得特定轉換的定義中的資料流分析工作相關的資訊。

**範例 1**

Azure PowerShell 0.9.8:  

    Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

Azure PowerShell 1.0:  

    Get-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob

這個 PowerShell 命令傳回稱為 StreamingJob 工作 StreamingJob 中轉換的相關資訊。

### <a name="new-azurestreamanalyticsinput--new-azurermstreamanalyticsinput"></a>新 AzureStreamAnalyticsInput |新 AzureRMStreamAnalyticsInput
建立新的輸入資料流分析工作，內或更新現有的指定的輸入。
  
在.json 檔案或命令列上，可以指定輸入名稱。 如果同時指定命令列上的名稱必須是檔案中的項目相同。

如果您已指定已經存在的輸入，並不指定 – 強制參數，指令程式會詢問是否要取代現有的輸入。

如果您指定 – 強制參數，並指定現有輸入名稱、 輸入將確認不會被取代。

JSON 檔案結構及內容的詳細資訊，請參閱[建立輸入 （Azure 資料流分析）] [msdn-rest-api-create-stream-analytics-input]區段中的[資料流分析管理 REST API 參考庫][stream.analytics.rest.api.reference]。

**範例 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 

這個 PowerShell 命令從檔案 Input.json 建立新的輸入。 如果已經定義現有的輸入輸入的定義檔案中所指定的名稱，指令程式會詢問要將它取代。

**範例 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream

這個 PowerShell 命令建立新的輸入稱為 EntryStream 工作中。 如果已定義名稱的現有輸入，指令程式會詢問要將它取代。

**範例 3**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force

這個 PowerShell 命令會取代現有的輸入來源稱為 EntryStream 與檔案中定義的定義。

### <a name="new-azurestreamanalyticsjob--new-azurermstreamanalyticsjob"></a>新 AzureStreamAnalyticsJob |新 AzureRMStreamAnalyticsJob
在 [Microsoft Azure 中建立新的資料流分析工作或更新現有的指定工作的定義。

在.json 檔案或命令列上，可以指定工作的名稱。 如果同時指定命令列上的名稱必須是檔案中的項目相同。

如果您指定的工作名稱已經存在，不指定 – 強制參數，指令程式會詢問是否要取代現有的工作。

如果您指定 – 強制參數，並指定現有的工作名稱，請確認不會被取代工作定義。

JSON 檔案結構及內容的詳細資訊，請參閱[建立資料流分析工作][msdn-rest-api-create-stream-analytics-job]區段中的[資料流分析管理 REST API 參考庫][stream.analytics.rest.api.reference]。

**範例 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 

這個 PowerShell 命令建立新的工作中 JobDefinition.json 的定義。 如果已經定義工作定義檔案中指定的名稱與現有的工作，指令程式會詢問要將它取代。

**範例 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force

這個 PowerShell 命令會取代 StreamingJob 的工作定義。

### <a name="new-azurestreamanalyticsoutput--new-azurermstreamanalyticsoutput"></a>新 AzureStreamAnalyticsOutput |新 AzureRMStreamAnalyticsOutput
建立新的輸出資料流分析工作，內或更新現有的輸出。  

在.json 檔案或命令列上，可以指定輸出的名稱。 如果同時指定命令列上的名稱必須是檔案中的項目相同。

如果您指定的輸出的已存在，不指定 – 強制參數，指令程式會詢問是否要取代現有的輸出。

如果您指定 – 強制參數，並指定現有輸出名稱、 確認不會被取代成果。

JSON 檔案結構及內容的詳細資訊，請參閱[建立輸出 （Azure 資料流分析）] [msdn-rest-api-create-stream-analytics-output]區段中的[資料流分析管理 REST API 參考庫][stream.analytics.rest.api.reference]。

**範例 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output

這個 PowerShell 命令建立新的輸出工作 StreamingJob 中稱為 「 輸出 」。 如果已定義名稱的現有輸出，指令程式會詢問要將它取代。

**範例 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force

這個 PowerShell 命令中的工作 StreamingJob 取代 「 輸出 」 的定義。

### <a name="new-azurestreamanalyticstransformation--new-azurermstreamanalyticstransformation"></a>新 AzureStreamAnalyticsTransformation |新 AzureRMStreamAnalyticsTransformation
建立新的轉換資料流分析工作，內或更新現有的轉換。
  
在.json 的檔案或命令列上，可以指定轉換的名稱。 如果同時指定命令列上的名稱必須是檔案中的項目相同。

如果您指定已經存在的轉換，不指定 – 強制參數，指令程式會詢問是否要取代現有的轉換。

如果您指定 – 強制參數，並指定現有的轉換名稱，請確認不會被取代轉換。

JSON 檔案結構及內容的詳細資訊，請參閱[建立轉換 （Azure 資料流分析）] [msdn-rest-api-create-stream-analytics-transformation]區段中的[資料流分析管理 REST API 參考庫][stream.analytics.rest.api.reference]。

**範例 1**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform

這個 PowerShell 命令建立新的轉換工作 StreamingJob 中稱為 StreamingJobTransform。 如果現有的轉換已定義名稱，指令程式會詢問要將它取代。

**範例 2**

Azure PowerShell 0.9.8:  

    New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

Azure PowerShell 1.0:  

    New-AzureRMStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force

 這個 PowerShell 命令會取代 StreamingJobTransform StreamingJob 工作中的定義。

### <a name="remove-azurestreamanalyticsinput--remove-azurermstreamanalyticsinput"></a>移除 AzureStreamAnalyticsInput |移除 AzureRMStreamAnalyticsInput
非同步刪除特定的輸入從 Microsoft Azure 中資料流分析工作。  
如果您指定 – 強制參數，輸入將未確認刪除。

**範例 1**

Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream

這個 PowerShell 命令 StreamingJob 工作中移除輸入的 EventStream。  

### <a name="remove-azurestreamanalyticsjob--remove-azurermstreamanalyticsjob"></a>移除 AzureStreamAnalyticsJob |移除 AzureRMStreamAnalyticsJob
非同步刪除 Microsoft Azure 中的特定資料流分析工作。  
如果您指定 – 強制參數，確認不會刪除作業。

**範例 1**

Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

這個 PowerShell 命令移除 StreamingJob 的工作。  

### <a name="remove-azurestreamanalyticsoutput--remove-azurermstreamanalyticsoutput"></a>移除 AzureStreamAnalyticsOutput |移除 AzureRMStreamAnalyticsOutput
非同步刪除特定的輸出從 Microsoft Azure 中資料流分析工作。  
如果您指定 – 強制參數，確認不會刪除輸出。

**範例 1**

Azure PowerShell 0.9.8:  

    Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0:  

    Remove-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

輸出輸出中的工作 StreamingJob 這個 PowerShell 命令移除。  

### <a name="start-azurestreamanalyticsjob--start-azurermstreamanalyticsjob"></a>開始 AzureStreamAnalyticsJob |開始 AzureRMStreamAnalyticsJob
非同步部署和啟動 Microsoft Azure 中的資料流分析工作。

**範例 1**

Azure PowerShell 0.9.8:  

    Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Azure PowerShell 1.0:  

    Start-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

這個 PowerShell 命令啟動 StreamingJob 自訂輸出開始時間設定為 [2012 年 12 月 12 日 12:12:12 工作 UTC。


### <a name="stop-azurestreamanalyticsjob--stop-azurermstreamanalyticsjob"></a>停止 AzureStreamAnalyticsJob |停止 AzureRMStreamAnalyticsJob
非同步停止執行 Microsoft Azure 中資料流分析工作，並取消配置所使用的資源。 工作定義和中繼資料仍可在您的訂閱，透過 Azure 入口網站和管理的 Api，例如，可以編輯並重新啟動工作。 您不會停止狀態中工作。

**範例 1**

Azure PowerShell 0.9.8:  

    Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

Azure PowerShell 1.0:  

    Stop-AzureRMStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 

這個 PowerShell 命令停止工作 StreamingJob。  

### <a name="test-azurestreamanalyticsinput--test-azurermstreamanalyticsinput"></a>測試 AzureStreamAnalyticsInput |測試 AzureRMStreamAnalyticsInput
測試連線至指定的輸入資料流分析的能力。

**範例 1**

Azure PowerShell 0.9.8:  

    Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

Azure PowerShell 1.0:  

    Test-AzureRMStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream

這個 PowerShell 命令測試 StreamingJob 輸入 EntryStream 的連線狀態。  

###<a name="test-azurestreamanalyticsoutput--test-azurermstreamanalyticsoutput"></a>測試 AzureStreamAnalyticsOutput |測試 AzureRMStreamAnalyticsOutput
測試連線至指定的輸出資料流分析的能力。

**範例 1**

Azure PowerShell 0.9.8:  

    Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

Azure PowerShell 1.0:  

    Test-AzureRMStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output

輸出連線狀態輸出中 StreamingJob 這個 PowerShell 命令測試。  

## <a name="get-support"></a>取得支援
進一步協助，請嘗試我們[Azure 資料流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。 


## <a name="next-steps"></a>後續步驟

- [Azure 資料流分析簡介](stream-analytics-introduction.md)
- [開始使用 Azure 資料流狀況分析](stream-analytics-get-started.md)
- [不按比例縮放 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure 資料流分析查詢語言參照](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 資料流分析管理 REST API 參照](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 



[msdn-switch-azuremode]: http://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 
