<properties 
   pageTitle="在 Azure 自動化子 runbooks |Microsoft Azure"
   description="說明啟動 Azure 自動化從另一個 runbook runbook 及共用資訊它們之間的不同方法。"
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
   ms.date="08/17/2016"
   ms.author="magoedte;bwren" />

# <a name="child-runbooks-in-azure-automation"></a>在 Azure 自動化子 runbooks

它是在撰寫可重複使用、 模組與其他 runbooks 就能使用的不連續函數 runbooks Azure 自動化的最佳作法。 父 runbook 通常會呼叫一或多個子 runbooks 執行所需的功能。 有兩種方式撥打子 runbook，且每個您應該瞭解的明顯差異，讓您可以決定，將會最適合您不同的情況。

##  <a name="invoking-a-child-runbook-using-inline-execution"></a>叫用使用內嵌執行子 runbook

叫用 runbook 直接從另一個 runbook，您可以使用 runbook 的名稱，並提供它的參數的值，完全等您想要使用的活動或指令程式。  在相同的自動化帳戶中的所有 runbooks 都可用於所有其他人使用這種方式。 父 runbook 等候子 runbook，完成後，移至下一行，而且任何輸出會傳回直接到上層。

當您叫用 runbook 內嵌時，它會在上層 runbook 為相同的工作中執行。 會執行子 runbook 工作記錄中的指示。 任何例外從子 runbook 輸出的任何資料流都可與上層相關聯。 這中較少的工作結果，以便於追蹤及進行疑難排解，因為擲回子 runbook 以及任何資料流輸出的任何例外狀況相關聯的父項工作。

Runbook 發行時，必須已經發行任何子 runbooks 它所呼叫。 這是因為編譯 runbook 時，Azure 自動化組建與任何子 runbooks 的關聯。 如果不是，上層 runbook 發佈正常運作，會出現，但啟動時，會產生例外狀況。 如果這種情況，您可以重新上層 runbook 發佈才能正確參照子 runbooks。 您不需要重新發佈上層 runbook，如果因為關聯會已經建立子 runbooks 的任何變更。

名為直接子項 runbook 的參數可以是任何資料類型，包括複雜的物件，還有沒有[JSON 序列化](automation-starting-a-runbook.md#runbook-parameters)因此當您開始使用 Azure 管理入口網站 runbook 時或啟動 AzureRmAutomationRunbook 指令程式。


### <a name="runbook-types"></a>Runbook 類型

哪些類型可以彼此呼叫︰

- [PowerShell runbook](automation-runbook-types.md#powershell-runbooks) [圖形 runbooks](automation-runbook-types.md#graphical-runbooks)可以通話和每個其他內嵌 （兩者都是根據 PowerShell）。
- [PowerShell 工作流程 runbook](automation-runbook-types.md#powershell-workflow-runbooks)和圖形的 PowerShell 工作流程 runbooks 打電話 （都 PowerShell 工作流程為基礎） 的其他每個內嵌
- PowerShell 類型及 PowerShell 工作流程類型無法撥打彼此直接編輯，而且必須使用開始 AzureRmAutomationRunbook。
    
當沒有發佈順序的重要性︰

- 發佈的順序 runbooks 只重要的 PowerShell 工作流程和圖形的 PowerShell 工作流程 runbooks。


當您呼叫使用內嵌執行圖形或 PowerShell 工作流程子 runbook 時，您只使用 runbook 的名稱。  當您呼叫 PowerShell 子 runbook 時，您必須單引號開頭的名稱*。\\ * 若要指定指令碼位於本機的目錄。 

### <a name="example"></a>範例

下列範例會叫用接受三個參數與複雜的物件、 整數、 布林值測試子 runbook。 子 runbook 的輸出指派給變數。  在這個案例中，子項 runbook 是 PowerShell 工作流程 runbook

    $vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true

下列是使用 PowerShell runbook 為子系相同的範例。

    $vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = .\PS-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true



##  <a name="starting-a-child-runbook-using-cmdlet"></a>開始使用 cmdlet 子 runbook

若要開始 runbook，[若要開始使用 Windows PowerShell runbook](../automation-starting-a-runbook.md#starting-a-runbook-with-windows-powershell)中所述，您可以使用[開始 AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx)指令程式。 有兩種使用這個 cmdlet 模式。  在一個模式中，指令程式會傳回作業識別碼一經為子 runbook 建立子工作。  在其他模式中，指定您啟用哪些**-等候**參數，指令程式會等到子工作完成，並將輸出從傳回子 runbook。

開始使用 cmdlet 子 runbook 上的工作會從父 runbook 執行中的個別工作。 此結果比叫用 runbook 內嵌的多個工作，並使其更容易追蹤。 上層可以非同步啟動多個子 runbooks，不必等候每完成。 平行呼叫子 runbooks 內嵌的相同類型，上層 runbook 就必須使用[平行關鍵字](automation-powershell-workflow.md#parallel-processing)。

開始使用 cmdlet 子 runbook 參數作為 hashtable [Runbook 參數](automation-starting-a-runbook.md#runbook-parameters)中所述。 只要簡單的資料類型可供。 如果 runbook 有與複雜的資料類型的參數，然後將其必須稱為內嵌。

### <a name="example"></a>範例

下列範例子 runbook 開頭參數，然後等待以完成使用開始 AzureRmAutomationRunbook-等候參數。 完成時，其輸出會收集從子 runbook。

    $params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true} 
    $joboutput = Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-ChildRunbook" -ResouceGroupName "LabRG" –Parameters $params –wait


## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>撥子 runbook 方法的比較

下表摘要列出兩種方法從另一個 runbook 呼叫 runbook 之間的差異。

| | 內嵌| Cmdlet|
|:---|:---|:---|
|工作|子 runbooks 執行上層為相同的工作。|為子 runbook 建立個別的工作。|
|執行|父 runbook 等待子 runbook，完成後，繼續進行。|父 runbook 繼續子 runbook 開始後立即*或*上層 runbook 等待子工作完成。|
|輸出|父 runbook 從子 runbook 能直接到成果。|父 runbook 必須擷取輸出從子 runbook 工作*或*上層 runbook 可直接輸出從取得子 runbook。|
|參數|子 runbook 參數值分別指定，並且可以使用任何資料類型。|值必須結合為單一 hashtable 子 runbook 參數，以及僅包含簡單，陣列，及物件資料類型，使用 JSON 序列化。|
|自動化帳戶|父 runbook 只能使用子 runbook 在相同的自動化帳戶。|如果您已經連線至該，上層 runbook 可以使用子 runbook，從同一份 Azure 訂閱與不同訂閱任何自動化帳戶。|
|發佈|在發佈上層 runbook 前，必須發佈子 runbook。|隨時上層 runbook 開始之前，必須發佈子 runbook。|

## <a name="next-steps"></a>後續步驟

- [啟動 Azure 自動化 runbook](automation-starting-a-runbook.md)
- [Runbook 輸出和 Azure 自動化中的郵件](automation-runbook-output-and-messages.md)
