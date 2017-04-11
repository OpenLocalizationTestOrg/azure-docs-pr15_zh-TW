<properties 
   pageTitle="Runbook 設定"
   description="說明中 Azure 自動化，以及如何變更使用 Azure 管理入口網站和 Windows PowerShell runbook 組態設定。"
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="bwren" />

# <a name="runbook-settings"></a>Runbook 設定

每個 runbook Azure 自動化中的有多個設定，協助通過身分驗證]，並變更其記錄的行為。 這些設定下方說明如何進行修改後面跟著程序。

## <a name="settings"></a>設定

### <a name="name-and-description"></a>名稱與描述

已建立之後，您無法變更 runbook 的名稱。 描述是選擇性的而且最多 512 個字元。

### <a name="tags"></a>標記

標籤可讓您指定不同的單字或片語，以協助您識別 runbook。 比方說，送出到[Runbook 庫](https://msdn.microsoft.com/library/dn781422.aspx)runbook 之後，您可以指定特定的標籤，以識別 runbook 應該會列在哪一個類別。 您可以指定多個標籤 runbook 以逗號分隔。

### <a name="logging"></a>記錄

根據預設，不會寫入工作歷程記錄的詳細資訊和進度的記錄。 您可以變更特定的 runbook 登入這些記錄的設定。 如需有關這些記錄的詳細資訊，請參閱[Runbook 輸出] 和 [郵件](https://msdn.microsoft.com/library/dn879148.aspx)。

## <a name="changing-runbook-settings"></a>變更 runbook 設定

### <a name="changing-runbook-settings-with-the-azure-management-portal"></a>變更 runbook 設定 Azure 管理入口網站

您可以從 [**設定**] 頁面的 runbook 變更 runbook Azure 管理入口網站中的設定。

1. 在 Azure 管理入口網站中，選取**自動化**，然後按一下 [自動化帳戶的名稱。
1. 選取 [ **Runbooks** ] 索引標籤。
1. 按一下 [runbook 的名稱。
1. 選取 [**設定**] 索引標籤。

### <a name="changing-runbook-settings-with-windows-powershell"></a>使用 Windows PowerShell 的變更 runbook 設定

若要變更 runbook 的設定，您可以使用[設定 AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690275.aspx)指令程式。 如果您想要指定多個標記，您可以 [提供給陣列或一串標籤參數以逗號分隔值。 您可以取得[取得 AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690278.aspx)與目前的標籤。

下列範例命令顯示如何設定 runbook 的屬性。 這個範例將三個標籤新增至現有的標籤，並指定詳細資訊記錄應會記錄。

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $tags = (Get-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName).Tags
    $tags += "Tag1,Tag2,Tag3"
    Set-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## <a name="related-articles"></a>相關的文章
- [Runbook 輸出與訊息](../automation-runbook-output-and-messages) 
- [建立或匯入 Runbook](https://msdn.microsoft.com/library/dn643637.aspx) 