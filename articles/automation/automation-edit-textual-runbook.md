<properties 
    pageTitle="編輯文字 runbooks 中 Azure 自動化"
    description="本文提供不同的程序使用 PowerShell 和 PowerShell 工作流程 runbooks Azure 自動化中使用文字編輯器。"
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="stevenka"
    editor="tysonn" />
<tags 
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="02/23/2016"
    ms.author="magoedte;bwren" />

# <a name="editing-textual-runbooks-in-azure-automation"></a>編輯文字 runbooks 中 Azure 自動化

文字編輯器中 Azure 自動化可用來編輯[PowerShell runbooks](automation-runbook-types.md#powershell-runbooks)與[PowerShell 工作流程 runbooks](automation-runbook-types.md#powershell-workflow-runbooks)。 這有 intellisense 和色彩編碼的其他特殊功能等其他程式碼編輯器，以協助您存取資源 runbooks 通用的一般功能。  本文會提供執行這個編輯器的不同函數的詳細的步驟。

文字編輯器] 中包含的活動、 資產及子 runbooks 的程式碼插入 runbook 功能。 而不是輸入程式碼中自己，您可以從可用的資源的清單中選取並插入 runbook 適當的程式碼。

每個 runbook Azure 自動化中的有兩個版本的草稿及發佈。 編輯 runbook 的草稿版本，並可執行，然後將它發佈。 無法編輯的發佈版本。 如需詳細資訊，請參閱[發佈 runbook](automation-creating-importing-runbook.md#publishing-a-runbook) 。

若要使用[圖形 Runbooks](automation-runbook-types.md#graphical-runbooks)，請參閱[Azure 自動化中撰寫的圖形](automation-graphical-authoring-intro.md)。

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>若要編輯 runbook Azure 入口網站

您可以使用下列程序來開啟 runbook 在文字編輯器中編輯。

1. 在 Azure 入口網站中，選取您的自動化帳戶。
2. 按一下 [ **Runbooks** ] 方塊，以開啟 runbooks 清單。
3. 按一下您想要編輯，然後按一下 [**編輯**] 按鈕 runbook 的名稱。
6. 執行所需的編輯。
7. 完成編輯時，請按一下 [**儲存**]。
8. 如果您想要的發佈 runbook 最新的草稿版本，請按一下 [**發佈**]。

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>若要插入 runbook cmdlet

2. 在文字編輯器的畫布上，將游標置於您要放置 cmdlet 的位置。
3. 展開文件庫控制項中的**Cmdlet**節點。 
3. 展開包含您想要使用的 cmdlet 的模組。
4. 以滑鼠右鍵按一下要插入或選取 [**新增至畫布**指令程式。  如果 cmdlet 設定多個參數，便會新增預設的設定。  您也可以展開 cmdlet 來選取不同的參數集。
4. Cmdlet 的程式碼會插入和它的參數的整個清單。
5. 提供適當的值取代周圍大括弧 <> 任何必要的參數的資料類型。  移除不需要任何參數。

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>若要插入 runbook 子 runbook 的程式碼

2. 在文字編輯器的畫布上，將游標置於您要放置[子 runbook](automation-child-runbooks.md)的程式碼的位置。
3. 展開文件庫控制項中的**Runbooks**節點。 
3. 以滑鼠右鍵按一下要插入或選取 [**新增至畫布**runbook。
4. 包含任何 runbook 參數的任何預留位置插入子 runbook 的程式碼。
5. 取代適當的值，每個參數的版面配置區。

### <a name="to-insert-an-asset-into-a-runbook"></a>若要插入 runbook 資產

2. 在文字編輯器的畫布上，將游標置於您要放置子 runbook 的程式碼的位置。
3. 展開文件庫控制項中的**資產**節點。 
4. 展開節點資產您想要的類型。
3. 以滑鼠右鍵按一下要插入或選取 [**新增至畫布**資產。  [變數資產](automation-variables.md)，根據您是否要取得或設定變數，選取 [**新增 「 取得變數 」 畫布**] 或 [**新增至畫布的 「 設定變數 」** 。
4. 資產的程式碼會插入 runbook。



## <a name="to-edit-a-runbook-with-the-azure-portal"></a>若要編輯 runbook Azure 入口網站

您可以使用下列程序來開啟 runbook 在文字編輯器中編輯。

1. 在 Azure 入口網站中，選取 [**自動化**，然後按一下 [自動化帳戶的名稱。
2. 選取 [ **Runbooks** ] 索引標籤。
3. 按一下您想要編輯，然後選取 [**作者**] 索引標籤 runbook 的名稱。
5. 按一下畫面底部的 [**編輯**] 按鈕。
6. 執行所需的編輯。
7. 完成編輯時，請按一下 [**儲存**]。
8. 如果您想要的發佈 runbook 最新的草稿版本，請按一下 [**發佈**]。

### <a name="to-insert-an-activity-into-a-runbook"></a>若要插入 Runbook 活動

1. 在文字編輯器的畫布上，將游標置於您要放置活動的位置。
1. 在畫面底部，按一下 [**插入**再**活動**。
1. 在 [**整合模組**] 欄中，選取包含活動的模組。
1. 在 [**活動**] 窗格中，選取 [活動]。
1. 在 [**描述**] 欄中，請注意活動的描述。 或者，您可以按一下 [檢視詳細說明]，即可啟動說明在瀏覽器中的活動。
1. 按一下右箭號。  如果活動有參數，就會列出資訊的情形。
1. 按一下 [檢查] 按鈕。  若要執行的活動的程式碼會插入 runbook。
1. 如果活動需要參數，提供適當的值取代以 <> 大括號括住的資料類型。

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>若要插入 runbook 子 runbook 的程式碼

1. 在文字編輯器的畫布上，將游標置於您要放置[子 runbook](automation-child-runbooks.md)的位置。
2. 在畫面底部，按一下 [**插入**]，然後**Runbook**]。
3. 選取要插入從中心] 資料行，然後按一下右箭號 runbook。
4. 如果 runbook 參數，就會列出資訊的情形。
5. 按一下 [檢查] 按鈕。  執行選取的 runbook 程式碼會插入目前的 runbook。
7. 如果 runbook 需要參數，提供適當的值取代以 <> 大括號括住的資料類型。

### <a name="to-insert-an-asset-into-a-runbook"></a>若要插入 runbook 資產

1. 在文字編輯器的畫布上，將游標置於您要放置擷取資產活動的位置。
1. 在畫面底部，按一下 [**插入**，然後**設定**]。
1. 在 [**設定動作**] 欄中，選取您想要的動作。
1. 從可用的資產，在中心欄中選取。
1. 按一下 [檢查] 按鈕。  取得或設定資產的程式碼會插入 runbook。



## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>若要編輯使用 Windows PowerShell 的 Azure 自動化 runbook

若要編輯使用 Windows PowerShell runbook，您可以使用您所選擇的編輯器]，並將其儲存為.ps1 檔案。 [取得 AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/getazurerunbookdefinition)指令程式可用來擷取 runbook，然後[設定 AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/setazurerunbookdefinition) cmdlet 來取代現有的草稿 runbook 修改過的內容。

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>若要擷取 Runbook，使用 Windows PowerShell 的內容

下列範例命令顯示如何擷取 runbook 的指令碼，並將其儲存至指令碼檔。 在此範例中，擷取草稿版本。 您也可擷取 runbook 的發佈版本，雖然您無法變更此版本。

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"
    
    $runbookDefinition = Get-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Slot Draft
    $runbookContent = $runbookDefinition.Content

    Out-File -InputObject $runbookContent -FilePath $scriptPath

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>若要變更使用 Windows PowerShell Runbook 的內容

下列範例命令顯示如何 runbook 的現有內容取代指令碼檔的內容。 請注意，這是與[匯入從使用 Windows PowerShell 指令碼檔 runbook](../automation-creating-or-importing-a-runbook#ImportRunbookScriptPS)相同的範例程序。

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

    Set-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Path $scriptPath -Overwrite
    Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName

## <a name="related-articles"></a>相關的文章

- [建立或匯入 runbook 中 Azure 自動化](automation-creating-importing-runbook.md)
- [學習 PowerShell 工作流程](automation-powershell-workflow.md)
- [圖形化撰寫中 Azure 自動化](automation-graphical-authoring-intro.md)
- [憑證](automation-certificates.md)
- [連線](automation-connections.md)
- [認證](automation-credentials.md)
- [排程](automation-schedules.md)
- [變數](automation-variables.md)