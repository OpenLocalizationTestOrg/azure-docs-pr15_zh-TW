<properties 
   pageTitle="Azure 自動化 Runbook 類型"
   description="描述您可以使用中 Azure 自動化，您應該考慮決定要使用哪些類型時的考量的 runbooks 不同類型。 "
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
   ms.date="09/12/2016"
   ms.author="bwren" />

# <a name="azure-automation-runbook-types"></a>Azure 自動化 runbook 類型

Azure 自動化支援下表中的四種類型的 runbooks 簡要說明。  以下各節提供關於包括何時使用每個考量每一種類型的詳細資訊。


| 類型 |  描述 |
|:---|:---|
| [圖形](#graphical-runbooks) | Windows PowerShell 和 Azure 入口網站中的建立及編輯完全中相關圖形編輯器基礎。 | 
| [圖形化 PowerShell 工作流程](#graphical-runbooks) | 根據 [Windows PowerShell 的工作流程和所建立和編輯完全之圖形的編輯器中 Azure 入口網站。 
| [PowerShell](#powershell-runbooks) | 根據 [Windows PowerShell 指令碼的文字 runbook。
| [PowerShell 工作流程](#powershell-workflow-runbooks) | Windows PowerShell 的工作流程所根據的文字 runbook。 |


## <a name="graphical-runbooks"></a>圖形化 runbooks

[圖形化](automation-runbook-types.md#graphical-runbooks)和圖形 PowerShell 工作流程 runbooks 建立和編輯 Azure 入口網站中的圖形化編輯器。  您可以匯出至檔案]，然後將它們匯入到另一個自動化帳戶，但您無法建立或編輯使用其他工具。  圖形化 runbooks 產生 PowerShell 代碼，但您無法直接檢視或修改的程式碼。 圖形化 runbooks 無法轉換為其中一種[文字格式](automation-runbook-types.md)，也可以文字 runbook 轉換成圖形格式。 圖形化 runbooks 可以轉換成圖形 PowerShell 工作流程 runbooks 期間匯入，反之亦然。

### <a name="advantages"></a>優點

- 建立 runbooks 的[PowerShell](automation-powershell-workflow.md)的基本知識。
- 視覺效果呈現管理程序。
- 若要建立高等級的工作流程的子 runbooks 為包含其他 runbooks。


### <a name="limitations"></a>限制

- 無法編輯 runbook 外部 Azure 入口網站。
- 可能會要求包含 PowerShell 執行複雜的邏輯的程式碼的程式碼活動。
- 無法檢視，或直接編輯 PowerShell 程式碼所建立的圖形化的工作流程。 請注意，您可以檢視您建立的任何程式碼活動的程式碼。


## <a name="powershell-runbooks"></a>PowerShell runbooks

Windows PowerShell 以 PowerShell runbooks。  您會直接編輯 Azure 入口網站中使用文字編輯器 runbook 的程式碼。  您也可以使用任何離線文字編輯器，並[匯入 runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) Azure automation。

### <a name="advantages"></a>優點

- 實作與 PowerShell 沒有 PowerShell 工作流程的額外複雜的程式碼的所有複雜邏輯。 
- Runbook 啟動比圖形或 PowerShell 工作流程 runbooks 快，因為它不需要進行編譯後再繼續執行。

### <a name="limitations"></a>限制

- 必須熟悉 PowerShell 指令碼。
- 無法使用[平行處理](automation-powershell-workflow.md#parallel-processing)平行執行多個動作。
- 繼續 runbook 發生錯誤時，無法使用[檢查點](automation-powershell-workflow.md#checkpoints)。
- PowerShell 工作流程 runbooks 和圖形 runbooks 只能作為子 runbooks 使用開始 AzureAutomationRunbook cmdlet 建立新的工作。

### <a name="known-issues"></a>已知的問題
以下是目前的 PowerShell runbooks 的已知的問題。

- PowerShell runbooks 無法無法擷取加密的[變數資產](automation-variables.md)null 值。
- PowerShell runbooks 無法擷取與[變數資產](automation-variables.md)*~*的名稱。
- 取得程序中的 PowerShell 循環 runbook 之後關於 80 反覆運算可能會當機。 
- 如果嘗試一次撰寫大量資料輸出資料流時，可能會失敗 PowerShell runbook。   您通常可以解決此問題，藉由輸出只將使用大型物件時，所需的資訊。  例如，輸出類似*取得程序*，而不是您可以輸出只*取得程序的必要的欄位 |選取 ProcessName，CPU*。

## <a name="powershell-workflow-runbooks"></a>PowerShell 工作流程 runbooks

PowerShell 工作流程 runbooks 是文字 runbooks 根據[Windows PowerShell 的工作流程](automation-powershell-workflow.md)。  您會直接編輯 Azure 入口網站中使用文字編輯器 runbook 的程式碼。  您也可以使用任何離線文字編輯器，並[匯入 runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) Azure automation。

### <a name="advantages"></a>優點

- 實作與 PowerShell 工作流程程式碼的所有複雜邏輯。
- 若要繼續 runbook 錯誤的情況下使用[檢查點](automation-powershell-workflow.md#checkpoints)。
- 使用[平行處理](automation-powershell-workflow.md#parallel-processing)平行執行多個動作。
- 可以包含其他圖形 runbooks 和 PowerShell 工作流程 runbooks 為子 runbooks 建立高等級的工作流程。


### <a name="limitations"></a>限制

- 作者必須熟悉 PowerShell 工作流程。
- Runbook 必須處理較複雜的 PowerShell 工作流程例如[還原序列化物件](automation-powershell-workflow.md#code-changes)。
- Runbook 耗費的時間比 PowerShell runbooks 開始，因為它需要進行編譯後再繼續執行。
- PowerShell runbooks 只能作為子 runbooks 使用開始 AzureAutomationRunbook cmdlet 建立新的工作。


## <a name="considerations"></a>考量

您應該考慮下列其他考量時決定要使用的特定的 runbook 哪些類型。

- 您無法將 runbooks 圖形為文字的類型或倒過來。
- 有一些限制為子 runbook 使用不同類型的 runbooks。  如需詳細資訊，請參閱[在 Azure 自動化子 runbooks](automation-child-runbooks.md) 。

  
## <a name="next-steps"></a>後續步驟

- 若要進一步瞭解圖形 runbook 撰寫，請參閱[Azure 自動化中撰寫的圖形化](automation-graphical-authoring-intro.md)
- 若要瞭解 PowerShell 和 PowerShell 之間的差異工作流程的 runbooks，請參閱[學習 Windows PowerShell 的工作流程](automation-powershell-workflow.md)
- 如需有關如何建立或匯入 Runbook 的詳細資訊，請參閱[建立或匯入 Runbook](automation-creating-importing-runbook.md)



