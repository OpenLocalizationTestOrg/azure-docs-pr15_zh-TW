<properties 
    pageTitle="在 Azure 自動化測試 runbook |Microsoft Azure"
    description="您在 Azure 自動化發佈 runbook 之前，您可以測試，以確保的預期般運作。  本文將說明如何測試 runbook 並檢視輸出。"
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

# <a name="testing-a-runbook-in-azure-automation"></a>在 Azure 自動化測試 runbook
當您測試 runbook 時，[草稿版本](automation-creating-importing-runbook.md#publishing-a-runbook)會執行，完成執行任何動作。 建立沒有工作歷程記錄時，但[輸出](automation-runbook-output-and-messages.md#output-stream)] 和 [[警告與錯誤](automation-runbook-output-and-messages.md#message-streams)的資料流會顯示在輸出] 窗格。 [詳細資料流](automation-runbook-output-and-messages.md#message-streams)郵件會顯示輸出] 窗格中，只有當[$VerbosePreference 變數](automation-runbook-output-and-messages.md#preference-variables)設定為 [繼續]。

即使正在執行的草稿版本，runbook 仍正常執行工作流程，並執行任何動作，針對資源環境中。 因此，您應該只測試 runbooks 非生產資源。

若要測試每一種[類型的 runbook](automation-runbook-types.md)的程序相同，且並沒有在測試文字編輯器 Azure 入口網站中的圖形化編輯器之間的差異。  


## <a name="to-test-a-runbook-in-the-azure-portal"></a>若要測試 runbook Azure 入口網站中

您可以使用 Azure 入口網站中的任何[runbook 類型](automation-runbook-types.md)。

1. 在[文字編輯器](automation-editing-a-runbook.md#Portal)」 或 「[圖形編輯器](automation-graphical-authoring-intro.md)中開啟 runbook 的草稿版本。
2. 按一下 [**測試**] 按鈕開啟測試刀。
3. 如果 runbook 參數，他們會列在的左窗格，您可以在此提供用於測試的值。
4. 如果您想要在[混合式 Runbook 工作者](automation-hybrid-runbook-worker.md)執行測試，然後**執行設定**變更為**混合式背景工作**，並選取目標群組的名稱。  否則，保留預設**Azure**執行在雲端的測試。
5. 按一下 [**開始**] 按鈕，以開始測試。
6. 如果 runbook [PowerShell 流程](automation-runbook-types.md#powershell-workflow-runbooks)或[圖形](automation-runbook-types.md#graphical-runbooks)，然後您可以停止或暫停其時測試與輸出窗格下方的按鈕。 當您暫停 runbook 時，會在完成之前在暫停目前的活動。 Runbook 會暫停之後，即可停止或重新啟動。
7. 檢查 [輸出] 窗格中 runbook 的輸出]。


## <a name="next-steps"></a>後續步驟

- 若要瞭解如何建立或匯入 runbook，請參閱[建立或匯入 runbook 中 Azure 自動化](automation-creating-importing-runbook.md)
- 若要深入瞭解設定撰寫的圖形，請參閱[圖形撰寫中 Azure 自動化](automation-graphical-authoring-intro.md)
- 若要開始使用 PowerShell 工作流程 runbooks，請參閱[我的第一個 PowerShell 工作流程 runbook](automation-first-runbook-textual.md)
- 若要深入瞭解設定 runboks 傳回的狀態訊息和錯誤，包括建議的作法，請參閱[Runbook 輸出和 Azure 自動化中的郵件](automation-runbook-output-and-messages.md)