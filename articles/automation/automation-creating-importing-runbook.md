<properties
    pageTitle="建立或匯入 runbook 中 Azure 自動化"
    description="本文將說明如何建立新的 runbook Azure 自動化，或從檔案匯入。"
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
    ms.author="magoedte;bwren" />

# <a name="creating-or-importing-a-runbook-in-azure-automation"></a>建立或匯入 runbook 中 Azure 自動化

您可以新增 runbook Azure 自動化，[建立一個新](#creating-a-new-runbook)或從檔案，或從[Runbook 庫](automation-runbook-gallery.md)匯入現有的 runbook。 本文提供建立和 runbooks 匯入檔案相關資訊。  您可以取得所有上存取社群 runbooks 和[Azure 自動化 Runbook 和模組樣式庫](automation-runbook-gallery.md)中的模組的詳細資料。

## <a name="creating-a-new-runbook"></a>建立新的 runbook

您可以建立新的 runbook Azure 自動化使用 Windows PowerShell 的 Azure 入口網站中。 只要 runbook 建立之後，您可以編輯並使用[學習 PowerShell 工作流程](automation-powershell-workflow.md)及[圖形中 Azure 自動化撰寫](automation-graphical-authoring-intro.md)中的資訊。

### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-classic-portal"></a>若要建立新的 Azure 自動化 runbook Azure 傳統入口網站

您只使用 Azure 入口網站中的[PowerShell 工作流程 runbooks](automation-runbook-types.md#powershell-workflow-runbooks) 。

1. 在傳統 Azure 入口網站中，按一下，**新****服務應用程式**、**自動化**、 **Runbook**、**快速建立**。
2. 輸入所需的資訊，然後再按一下 [**建立**]。 Runbook 名稱必須以字母開頭，並且可以有字母、 數字、 底線和虛線。
3. 如果您想要編輯 runbook 現在，然後按一下 [**編輯 Runbook**。 否則，請按一下**[確定]**。
4. 您的新 runbook 會出現在 [ **Runbooks** ] 索引標籤。


### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-portal"></a>若要建立新的 Azure 自動化 runbook Azure 入口網站

1. 在 [Azure 入口網站，開啟您自動化的帳戶。
2. 按一下 [ **Runbooks** ] 方塊，以開啟 runbooks 清單。
3. 按一下 [**新增 runbook** ] 按鈕，然後**建立新的 runbook**上。
2. 輸入 runbook 的**名稱**，然後選取其[類型](automation-runbook-types.md)。 Runbook 名稱必須以字母開頭，並且可以有字母、 數字、 底線和虛線。
3. 按一下 [**建立**]，建立 runbook 並開啟編輯器]。


### <a name="to-create-a-new-azure-automation-runbook-with-windows-powershell"></a>若要建立新的 Azure 自動化 runbook 使用 Windows PowerShell

[新增 AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt619376.aspx)指令程式可用來建立空白的[PowerShell 工作流程 runbook](automation-runbook-types.md#powershell-workflow-runbooks)。 您可以指定**名稱**參數來建立空白的 runbook，您可以稍後編輯，或您可以指定匯入的 runbook 檔案**路徑**參數。 **輸入**參數也應該包含指定的四個 runbook 類型。

下列範例命令顯示如何建立新的空白 runbook。

    New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
    -Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell

## <a name="importing-a-runbook-from-a-file-into-azure-automation"></a>從檔案匯入 runbook，將 Azure 自動化

您可以建立新的 runbook Azure 自動化中匯入的 PowerShell 指令碼或 PowerShell 工作流程 （.ps1 分機） 或匯出的圖形化 runbook (.graphrunbook)。  您必須指定將建立從納入考量下列事項匯入[的 runbook 類型](automation-runbook-types.md)。

- .Graphrunbook 檔案可能只能匯入新[圖形 runbook](automation-runbook-types.md#graphical-runbooks)，及圖形 runbooks 只能建立從.graphrunbook 檔案。
- 包含 PowerShell 工作流程的.ps1 檔案只可以匯入[PowerShell 工作流程 runbook](automation-runbook-types.md#powershell-workflow-runbooks)。  如果檔案包含多個 PowerShell 工作流程，將會失敗匯入。 每個工作流程至自己的檔案]，然後在每個個別匯入。
- 不包含工作流程.ps1 檔案可以匯入[PowerShell runbook](automation-runbook-types.md#powershell-runbooks)或[PowerShell 工作流程 runbook](automation-runbook-types.md#powershell-workflow-runbooks)。  如果將其匯入 PowerShell 工作流程 runbook，工作流程]，然後將會轉換，然後指定所做的變更 runbook 中會包含註解。

### <a name="to-import-a-runbook-from-a-file-with-the-azure-classic-portal"></a>從 Azure 傳統入口網站使用的檔案匯入 runbook
您可以使用下列程序將指令碼檔匯入 Azure 自動化。  請注意，您可以只將匯入.ps1 檔案 PowerShell 工作流程 runbook 使用此入口網站。  您必須使用 Azure 入口網站的其他類型。

1. 在 Azure 管理入口網站中，選取**自動化**，然後選取自動化帳戶。
2. 按一下 [**匯入**]。
3. 按一下 [**瀏覽至檔案**，然後找出指令碼匯入的檔案。
4. 如果您想要編輯 runbook 現在，然後按一下 [**編輯 Runbook**。 否則，請按一下 [確定]。
5. 新 runbook **Runbooks** ] 索引標籤上，會出現自動化帳戶。
6. 您必須[發佈 runbook](#publishing-a-runbook)才能執行。


### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>若要從 Azure 入口網站使用的檔案匯入 runbook
您可以使用下列程序將指令碼檔匯入 Azure 自動化。  

>[AZURE.NOTE] 請注意，您可以只將匯入.ps1 檔案 PowerShell 工作流程 runbook 使用入口網站。

1. 在 [Azure 入口網站，開啟您自動化的帳戶。
2. 按一下 [ **Runbooks** ] 方塊，以開啟 runbooks 清單。
3. 按一下 [**新增 runbook** ] 按鈕，然後**匯入**]。
4. 按一下您要選取要匯入的檔案**Runbook 檔案**
2. 啟用 [**名稱**] 欄位時，如果您有變更的選項。  Runbook 名稱必須以字母開頭，並且可以有字母、 數字、 底線和虛線。
3. 會自動選取[runbook 類型](automation-runbook-types.md)，但您可以變更類型後採取至帳戶適用的限制。 
3. 自動化帳戶，新 runbook 會出現在 runbooks 的清單。
4. 您必須[發佈 runbook](#publishing-a-runbook)才能執行。

>[AZURE.NOTE] 您匯入圖形的 runbook 或圖形的 PowerShell 工作流程 runbook 之後，您會有轉換為其他類型，如果想要的選項。 您無法轉換為文字。

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>若要從使用 Windows PowerShell 指令碼匯入 runbook

您可以使用 [[匯入 AzureRMAutomationRunbook](https://msdn.microsoft.com/library/mt603735.aspx)指令程式匯入的指令碼檔案為草稿 PowerShell 工作流程 runbook。 匯入如果 runbook 已經存在，將會失敗，除非您使用*-強制*參數。 

下列範例命令顯示如何將指令碼檔匯入 runbook。

    $automationAccountName =  "AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
    $RGName = "ResourceGroup"

    Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
    -ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
    -Type PowerShellWorkflow 


## <a name="publishing-a-runbook"></a>發佈 runbook

當您建立或匯入新的 runbook 時，您必須發佈才能執行。  每個 runbook 中自動化有草稿與發佈版本。 只有發佈版本可執行，並可編輯的草稿版本。 發佈版本不會受草稿版本的任何變更。 時應提供使用草稿版本，然後您發佈的草稿版本使用覆寫的發佈版本。

## <a name="to-publish-a-runbook-using-the-azure-classic-portal"></a>若要發佈 runbook，使用 [Azure 傳統的入口網站

1. 開啟 runbook Azure 傳統入口網站中。
1. 在畫面頂端，按一下 [**作者**]。
1. 在畫面的底部，按一下 [**發佈**]，然後**是**驗證訊息。

## <a name="to-publish-a-runbook-using-the-azure-portal"></a>若要發佈 runbook，使用 [Azure 入口網站

1. 開啟 runbook Azure 入口網站中。
1. 按一下 [**編輯**] 按鈕。
1. 驗證的郵件，按一下 [**發佈**] 按鈕，然後**[是**]。


## <a name="to-publish-a-runbook-using-windows-powershell"></a>若要發佈 runbook，使用 Windows PowerShell

您可以使用[發佈 AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603705.aspx) cmdlet 來發佈 runbook 使用 Windows PowerShell。 下列範例命令顯示如何發佈範例 runbook。

    $automationAccountName =  AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $RGName = "ResourceGroup"

    Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
    -Name $runbookName -ResourceGroupName $RGName


## <a name="next-steps"></a>後續步驟
- 若要瞭解您可以助益 Runbook 和 PowerShell 模組庫，請參閱[Azure 自動化 Runbook 和模組組件庫](automation-runbook-gallery.md)
- 若要進一步瞭解如何使用文字編輯器中編輯 PowerShell 和 PowerShell 工作流程 runbooks，請參閱[編輯文字 runbooks 中 Azure 自動化](automation-edit-textual-runbook.md)
- 若要進一步瞭解圖形 runbook 撰寫，請參閱[Azure 自動化中撰寫的圖形化](automation-graphical-authoring-intro.md)
