<properties 
   pageTitle="排程中 Azure 自動化 runbook |Microsoft Azure"
   description="說明如何建立排程中 Azure 自動化，以便在特定時間或循環的排程，您可以自動啟動 runbook。"
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
   ms.date="08/05/2016"
   ms.author="bwren" />

# <a name="scheduling-a-runbook-in-azure-automation"></a>排程中 Azure 自動化 runbook

若要安排 runbook 中 Azure 自動化，若要開始在指定的時間，您將其連結至一或多個排程。 您可以設定排程 runbooks Azure 傳統入口網站中，以及 runbooks Azure 入口網站中的 [執行一次或重複發生每小時] 或 [每日排程，您可以此外排定這些每週、 每月、 每週的特定天數或數天的月份，或在特定日期的月份。  Runbook 可以連結至多個排程，並排程可以有多個 runbooks 連結。


## <a name="creating-a-schedule"></a>建立排程

您可以建立新的排程的 runbooks Azure 入口網站，在 [傳統] 入口網站，或使用 Windows PowerShell 中。 您也可以建立新的排程，當您使用 [Azure 傳統或 Azure 入口網站的排程連結 runbook 的選項。

>[AZURE.NOTE] 當您將排程 runbook 關聯時，自動化將目前版本的模組儲存在您的帳戶，並連結的排程。  這表示，如果您使用 1.0 版模組在您帳戶中建立排程，然後再更新的版本 2.0 模組，排程會繼續使用 1.0。  若要使用的更新的模組版本，您必須建立新的排程。 

### <a name="to-create-a-new-schedule-in-the-azure-classic-portal"></a>Azure 傳統入口網站中建立新的排程

1. 在 Azure 傳統的入口網站中，選取自動化，然後再選取自動化帳戶的名稱。
1. 選取 [**資產**] 索引標籤。
1. 在視窗底部，按一下 [**新增設定**]。
1. 按一下 [**新增排程**。
1. 輸入**名稱**，您也可以選擇新的 schedule.your 排程**描述**會執行**一次**，**每小時**、**每日**、**每週**或**月**。
1. 指定 [**開始時間**和其他選項，根據您所選取之排程類型。

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Azure 入口網站中建立新的排程

1. 在 Azure 入口網站中，從您的自動化帳戶，按一下以開啟 [**資產**刀 [**資產**] 磚。
2. 按一下 [**排程**] 方塊，以開啟**排程**刀。
3. 按一下 [**新增排程**刀頂端。
4. 在**新的排程**刀中，輸入的**名稱**，以及您也可以新增排程的**描述**。
5. 選取**一次**] 或 [**循環**選取是否排程將會執行一次，或為重複發生的排程。  如果您選取**一次**指定的**開始時間**，然後按一下 [**建立**。  如果您選取 [**週期]**，指定的**開始時間**和您要重複 runbook 的頻率-**小時**、**天**、**週**或**月**的頻率。  如果您可以從下拉式清單中選取**一週**或**月**，**週期性] 選項**會顯示刀並在選取範圍，會出現 [**週期性] 選項**刀，如果您選取 [**一週**，您可以選取星期幾。  如果您選取**的月份**，您可以選擇**星期**或月行事曆上的特定天數及最後，請執行您想要執行的月份最後一天或不，然後按一下**[確定]**。   

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>若要使用 Windows PowerShell 中建立新的排程

您可以使用 [[新增 AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690271.aspx) cmdlet 來建立新的排程中 Azure 自動化傳統 runbooks 或[新增 AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603577.aspx) cmdlet 的 runbooks Azure 入口網站中。 您必須指定排程和應該執行的頻率的開始時間。

下列範例命令顯示如何建立新的 3:30 PM 開始在 2015 年 1 月 20 日 Azure 服務管理 cmdlet 在執行每日排程。

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName –StartTime "1/20/2016 15:30:00" –DayInterval 1

下列範例命令會顯示如何建立 15 日和使用 Azure 資源管理員 cmdlet 每個月的第 30 天的排程。

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
    -DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"
    

## <a name="linking-a-schedule-to-a-runbook"></a>連結至 runbook 的排程

Runbook 可以連結至多個排程，並排程可以有多個 runbooks 連結。 如果 runbook 參數，您可以為他們提供的值。 您必須提供的任何必要的參數的值，也可能會提供任何選用的參數值。  這些值將用於 runbook 所啟動此排程每次。  您可以將相同的 runbook 附加至另一個排程，並指定不同的參數值。


### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-classic-portal"></a>若要連結至 runbook Azure 傳統入口網站的 [排程

1. 在 Azure 傳統的入口網站中，選取**自動化**，然後按一下 [自動化帳戶的名稱。
2. 選取 [ **Runbooks** ] 索引標籤。
3. 按一下 [排程 runbook 的名稱。
4. 按一下 [**排程**] 索引標籤。
5. 如果 runbook 目前沒有連結至排程，然後您就會提供選項**連結至新的排程**或**連結至現有的排程**。  如果 runbook 目前已連結至排程，按一下 [**連結**] 視窗的底部存取這些選項。
6. 如果 runbook 參數，系統會提示您的值。  

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>若要連結至 runbook Azure 入口網站的 [排程

1. 在 Azure 入口網站中，從您的自動化帳戶，按一下 [ **Runbooks**方磚開啟**Runbooks**刀。
2. 按一下 [排程 runbook 的名稱。
3. 如果 runbook 目前沒有連結至排程，然後會提供您要建立新的排程或連結至現有的排程選項。  
4. 如果 runbook 參數，您可以選取選項**修改執行設定 (預設︰ Azure)** ，並**參數**刀呈現其中會相應地輸入的資訊。  

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>若要使用 Windows PowerShell runbook 連結排程

您可以使用[登錄 AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx)来連結的傳統 runbook 或[Register AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt603575.aspx) cmdlet runbooks Azure 入口網站中的排程。  您可以指定 runbook 的參數和參數的參數。 如需有關指定參數值，請參閱[啟動 Azure 自動化中的 Runbook](automation-starting-a-runbook.md) 。

下列範例命令顯示如何連結 Azure 服務管理 cmdlet 使用參數的排程。

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params

下列範例命令顯示如何使用參數 Azure 資源管理員 cmdlet runbook 連結排程。

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params `
    -ResourceGroupName "ResourceGroup01"

## <a name="disabling-a-schedule"></a>停用排程

當您停用排程時，將不會再連結至任何 runbooks 執行的排程。 您可以手動停用排程，或設定頻率排程的到期時間，當您建立。 當到達到期時間時，將會停用排程。

### <a name="to-disable-a-schedule-from-the-azure-classic-portal"></a>若要停用從 Azure 傳統入口網站的排程

您可以停用排程的排程詳細資料頁面從 Azure 傳統入口網站中的排程。

1. 在 Azure 傳統的入口網站中，選取自動化，然後按一下 [自動化帳戶的名稱。
1. 選取 [資產] 索引標籤。
1. 按一下以開啟其詳細資料] 頁面的排程的名稱。
2. 變更**啟用**，為 [**否]**。

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>若要停用從 Azure 入口網站的排程

1. 在 Azure 入口網站中，從您的自動化帳戶，按一下以開啟 [**資產**刀 [**資產**] 磚。
2. 按一下 [**排程**] 方塊，以開啟**排程**刀。
2. 按一下以開啟詳細資料刀排程的名稱。
3. 變更**啟用**，為 [**否]**。

### <a name="to-disable-a-schedule-with-windows-powershell"></a>若要停用使用 Windows PowerShell 的排程

若要變更現有排程傳統 runbook 或[設定 AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603566.aspx) cmdlet runbooks Azure 入口網站中的文件的屬性，您可以使用[設定 AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx)指令程式。 若要停用排程，指定**false** **IsEnabled**參數。

下列範例命令顯示如何停用排程使用 Azure 服務管理指令程式。

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false

下列範例命令顯示如何停用使用 Azure 資源管理員 cmdlet runbook 的排程。

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"


## <a name="next-steps"></a>後續步驟

- 若要進一步瞭解使用排程，請參閱[Azure 自動化排程資產](http://msdn.microsoft.com/library/azure/dn940016.aspx)
- 若要開始使用 runbooks Azure 自動化中，請參閱[啟動 Azure 自動化中的 Runbook](automation-starting-a-runbook.md) 