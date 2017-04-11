<properties 
    pageTitle="啟動和停止 Azure 自動化-PowerShell 工作流程的虛擬機器 |Microsoft Azure"
    description="圖形化包括來啟動和停止傳統的虛擬機器 runbooks Azure 自動化案例的版本。"
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor="tysonn" />
<tags 
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="07/06/2016"
    ms.author="bwren" />

# <a name="azure-automation-scenario---starting-and-stopping-virtual-machines"></a>Azure 自動化案例-啟動和停止虛擬機器

這個案例中 Azure 自動化包含 runbooks 來啟動和停止傳統的虛擬機器。  您可以使用這個案例中的下列任何一項︰  

- 您自己的環境中使用不需要修改 runbooks。 
- 若要執行的自訂的功能 runbooks [修改]。  
- 呼叫 runbooks 從另一個 runbook 整體解決方案的一部分。 
- 若要瞭解 runbook 撰寫概念作為教學課程 runbooks。 

> [AZURE.SELECTOR]
- [圖形](automation-solution-startstopvm-graphical.md)
- [PowerShell 工作流程](automation-solution-startstopvm-psworkflow.md)

這是這種情況的 PowerShell 工作流程 runbook 版本。 也會有該使用[圖形 runbooks](automation-solution-startstopvm-graphical.md)。

## <a name="getting-the-scenario"></a>快速分析藍本

您可以從下列連結下載的兩個 PowerShell 工作流程 runbooks 包含這種情況。  請參閱這個案例中的圖形化 runbooks 連結[圖形的版本](automation-solution-startstopvm-graphical.md)。

| Runbook | 連結 | 類型 | 描述 |
|:---|:---|:---|:---|
| 開始 AzureVMs | [啟動 Azure 傳統 Vm](https://gallery.technet.microsoft.com/Start-Azure-Classic-VMs-86ef746b) | PowerShell 工作流程 | 開頭的所有傳統的虛擬機器中的 Azure subscriptionor 所有虛擬機器特定服務名稱。 |
| 停止 AzureVMs | [停止 Azure 傳統 Vm](https://gallery.technet.microsoft.com/Stop-Azure-Classic-VMs-7a4ae43e) | PowerShell 工作流程 | 停止在自動化帳戶中的所有虛擬機器或所有的虛擬機器使用特定的服務名稱。  |


## <a name="installing-and-configuring-the-scenario"></a>安裝及設定此案例

### <a name="1-install-the-runbooks"></a>1.安裝 runbooks

下載之後 runbooks，您可以將它們在[匯入 Runbook](http://msdn.microsoft.com/library/dn643637.aspx#ImportRunbook)使用程序匯入。

### <a name="2-review-the-description-and-requirements"></a>2.檢閱的描述與需求
Runbooks 包含註解的說明文字包含描述所需的資產。  您也可以從這篇文章取得相同的資訊。 

### <a name="3-configure-assets"></a>3.設定資產
Runbooks 要求動作的資產，您必須建立並填入適當的值。

| 資產類型 | 資產名稱 | 描述 |
|:---|:---|:---|:---|
| 認證 | AzureCredential | 包含具有啟動和停止虛擬機器 Azure 訂閱中的權限的帳戶認證。  或者，您也可以**新增 AzureAccount**活動的**認證**參數中指定另一個認證資產。 |
| 變數 | AzureSubscriptionId | 包含您的 Azure 訂閱訂閱 ID。 |

## <a name="using-the-scenario"></a>使用此案例

### <a name="parameters"></a>參數

每個 runbooks 有下列參數。  您必須提供的任何必要的參數的值，並可以選擇性地為提供的值其他參數根據您的需求。

| 參數 | 類型 | 強制性 | 描述 |
|:---|:---|:---|:---|
| ServiceName | 字串 | 無 | 如果提供值，然後開始或停止所有的虛擬機器服務名稱。  如果未不提供任何值，然後 Azure 訂閱中的所有傳統虛擬機器會啟動或停止。 |
| AzureSubscriptionIdAssetName | 字串 | 無 | 包含的[變數資產](#installing-and-configuring-the-scenario)，包含訂閱 ID Azure 訂閱的名稱。  如果您不指定的值，則會使用*AzureSubscriptionId* 。  |
| AzureCredentialAssetName | 字串 | 無 | 包含的[認證資產](#installing-and-configuring-the-scenario)包含使用 runbook 認證的名稱。  如果您不指定的值，則會使用*AzureCredential* 。  |

### <a name="starting-the-runbooks"></a>啟動 runbooks

您可以在[啟動 Azure 自動化中 runbook](automation-starting-a-runbook.md)使用任何一種方法其中一項 runbooks 開始在這個案例中。

下列範例命令會使用 Windows PowerShell 來執行**StartAzureVMs**與服務名稱*MyVMService*進行所有虛擬機器。

    $params = @{"ServiceName"="MyVMService"}
    Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Start-AzureVMs" –Parameters $params

### <a name="output"></a>輸出

Runbooks 將[輸出郵件](automation-runbook-output-and-messages.md)的每個虛擬機器指出已成功提交啟動或停止指示。  您可以尋找輸出來決定的每個 runbook 結果中的特定字串。  下表列出可能的輸出字串。

| Runbook | 條件 | 訊息 |
|:---|:---|:---|
| 開始 AzureVMs | 虛擬機器已執行  | MyVM 正在執行 |
| 開始 AzureVMs | 啟動要求成功送出的虛擬機器 | 已啟動 MyVM |
| 開始 AzureVMs | 虛擬機器開始要求失敗  | MyVM 無法啟動 |
| 停止 AzureVMs | 已經停止虛擬機器  | 已經停止 MyVM |
| 停止 AzureVMs | 停止要求成功送出的虛擬機器 | 已停止 MyVM |
| 停止 AzureVMs | 虛擬機器停駐點要求失敗  | 若要停止失敗 MyVM |

例如，下列程式碼片段 runbook 從嘗試開始服務名稱*MyServiceName*所有虛擬機器。  如果有任何開始要求會失敗，則可以採取錯誤的動作。 

    $results = Start-AzureVMs -ServiceName "MyServiceName"
    foreach ($result in $results) {
        if ($result -like "* has been started" ) {
            # Action to take in case of success.
        }
        else {
            # Action to take in case of error.
        }
    }


## <a name="detailed-breakdown"></a>詳細的分析

以下是在這個案例中 runbooks 詳細的解說。  您可以使用這項資訊至自訂 runbooks 或只是想瞭解從他們的撰寫您自己的自動化案例。

### <a name="parameters"></a>參數

    param (
        [Parameter(Mandatory=$false)] 
        [String]  $AzureCredentialAssetName = 'AzureCredential',
        
        [Parameter(Mandatory=$false)]
        [String] $AzureSubscriptionIdAssetName = 'AzureSubscriptionId',

        [Parameter(Mandatory=$false)] 
        [String] $ServiceName
    )

工作流程開始以取得[輸入的參數](#using-the-scenario)值。  如果不提供資產名稱會使用預設的名稱。

### <a name="output"></a>輸出

    # Returns strings with status messages
    [OutputType([String])]

這一行宣告 runbook 的輸出會字串。  這不是必要，但 runbook 作為[子 runbook](automation-child-runbooks.md) ，以便上層 runbook 就會知道發生的事輸出類型時的最佳作法。

### <a name="authentication"></a>驗證

    # Connect to Azure and select the subscription to work against
    $Cred = Get-AutomationPSCredential -Name $AzureCredentialAssetName
    $null = Add-AzureAccount -Credential $Cred -ErrorAction Stop
    $SubId = Get-AutomationVariable -Name $AzureSubscriptionIdAssetName
    $null = Select-AzureSubscription -SubscriptionId $SubId -ErrorAction Stop

下一步線設定[認證](automation-configuring.md#configuring-authentication-to-azure-resources)和 Azure 用於 runbook 的其餘部分的訂閱。
第一次我們使用**取得 AutomationPSCredential**取得存放的認證以啟動和停止虛擬機器中的 Azure 訂閱的存取權的資產。 **新增 AzureAccount**然後會使用這項資產設定認證。  輸出會被指派 dummy 變數，讓它不含 runbook 輸出。  

**取得 AutomationVariable**與訂閱，以**選取 AzureSubscription**設定，然後擷取的訂閱識別碼變數的資產。

### <a name="get-vms"></a>取得 Vm

    # If there is a specific cloud service, then get all VMs in the service,
    # otherwise get all VMs in the subscription.
    if ($ServiceName) 
    { 
        $VMs = Get-AzureVM -ServiceName $ServiceName
    }
    else 
    { 
        $VMs = Get-AzureVM
    }

**取得 AzureVM**用來擷取虛擬機器 runbook 可搭配運作。  如果**ServiceName**輸入變數中提供的值，則會擷取只虛擬機器服務名稱。  如果**ServiceName**是空值，會擷取所有虛擬機器。

### <a name="startstop-virtual-machines-and-send-output"></a>啟動停止虛擬機器並將輸出傳送

    # Start each of the stopped VMs
    foreach ($VM in $VMs)
    {
        if ($VM.PowerState -eq "Started")
        {
            # The VM is already started, so send notice
            Write-Output ($VM.InstanceName + " is already running")
        }
        else
        {
            # The VM needs to be started
            $StartRtn = Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName -ErrorAction Continue

            if ($StartRtn.OperationStatus -ne 'Succeeded')
            {
                # The VM failed to start, so send notice
                Write-Output ($VM.InstanceName + " failed to start")
            }
            else
            {
                # The VM started, so send notice
                Write-Output ($VM.InstanceName + " has been started")
            }
        }
    }

下一個線條逐步執行每個虛擬機器。  第一次檢查已在執行的虛擬機器**PowerState**或停止，根據 runbook。  如果已經在目標狀態，郵件會傳送至 [輸出和 runbook 結束。  如果沒有出現，然後**開始 AzureVM**或**停止 AzureVM**用來啟動或停止虛擬機器要求儲存一個變數的結果。  輸出指定是否要啟動或停止要求已成功送出然後傳送郵件。


## <a name="next-steps"></a>後續步驟

- 若要進一步瞭解如何使用 [子 runbooks，請參閱[在 Azure 自動化子 runbooks](automation-child-runbooks.md) 
- 若要進一步瞭解輸出訊息期間 runbook 執行和記錄，協助進行疑難排解，請參閱[Runbook 輸出和 Azure 自動化中的郵件](automation-runbook-output-and-messages.md)
