<properties 
   pageTitle="學習 PowerShell 工作流程"
   description="本文被要為快速教學單元的作者熟悉 PowerShell 瞭解 PowerShell 和 PowerShell 工作流程的特定差異。"
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

# <a name="learning-windows-powershell-workflow"></a>學習 Windows PowerShell 的工作流程

在 Azure 自動化 Runbooks 實作為 Windows PowerShell 的工作流程。  Windows PowerShell 工作流程的 Windows PowerShell 指令碼類似，但有可能會造成混淆給新的使用者一些顯著差異。  本文適用於使用者已熟悉 PowerShell，並簡要說明您需要的如果您想要用於 runbook PowerShell 工作流程轉換 PowerShell 指令碼的概念。  

工作流程是程式化、 連線的步驟執行較長時間執行工作，或需要多個步驟的協調多個裝置或受管理的節點的順序。 在標準的指令碼工作流程的優點包括同時執行的動作，根據多個裝置的功能和能夠自動修復失敗。 Windows PowerShell 工作流程是運用 Windows Workflow Foundation 的 Windows PowerShell 指令碼。 當工作流程使用 Windows PowerShell 語法撰寫且由 Windows PowerShell 啟動時，它會處理 Windows Workflow Foundation。

本文中的主題的詳細資訊，請參閱[開始使用 Windows PowerShell 的工作流程](http://technet.microsoft.com/library/jj134242.aspx)。

## <a name="types-of-runbook"></a>Runbook 的類型

有三種類型的 runbook Azure 自動化、 *PowerShell 工作流程*、 *PowerShell*和*圖形*。  當您建立 runbook，您無法將 runbook 轉換為其他類型，就會建立後，您可以定義 runbook 類型。

PowerShell 工作流程 runbooks 和 PowerShell runbooks 的使用者想要直接使用 PowerShell 程式碼是使用文字編輯器中 Azure 自動化或離線編輯器例如 PowerShell ise [以系統。 如果您正在建立 PowerShell 工作流程 runbook，您應該瞭解這份文件中的資訊。 

圖形化 runbooks 可讓您建立 runbook 使用相同的活動和 cmdlet，但使用圖形化介面隱藏基礎 PowerShell 工作流程的複雜性。  例如檢查點和平行執行本文中的概念仍會套用至圖形 runbooks，但是您不必擔心詳細的語法。 

## <a name="basic-structure-of-a-workflow"></a>工作流程的基本結構

轉換 PowerShell 工作流程的 PowerShell 指令碼的第一個步驟有該**工作流程**關鍵字。  工作流程開始**工作流程**關鍵字後面的括號括住的指令碼本文。 下列語法所示的**工作流程**關鍵字遵循的工作流程名稱。 

    Workflow Test-Workflow
    {
       <Commands>
    }

工作流程的名稱必須符合自動化 runbook 的名稱。 如果匯入 runbook，檔名，必須符合的工作流程名稱和.ps1 必須結尾。

若要新增參數至工作流程，請使用**參數**關鍵字，就如同指令碼。 

## <a name="code-changes"></a>程式碼變更

PowerShell 工作流程程式碼看起來類似於除了幾項重大變更的 PowerShell 指令碼。  下列各節說明您需要進行 PowerShell 指令碼，若要執行工作流程中的變更。

### <a name="activities"></a>活動

活動是工作流程中的特定任務。 如同指令碼組成的一或多個命令，工作流程是由組成的一或多個序列中執行的活動。 Windows PowerShell 的工作流程自動轉換的 Windows PowerShell cmdlet 活動時執行工作流程。 當您其中一個這些 cmdlet] 中指定您 runbook Windows Workflow Foundation 是實際執行相對應的活動。 沒有相對應的活動這些 cmdlet 的 Windows PowerShell 的工作流程會自動執行[InlineScript](#inlinescript)活動中的指令程式。 有一組 cmdlet 排除，除非您明確 InlineScript 區塊中包含無法用於工作流程。 如需這些概念的詳細資訊，請參閱[使用指令碼工作流程中的活動](http://technet.microsoft.com/library/jj574194.aspx)。

工作流程活動共用一組通用的參數，若要設定的作業。 如需工作流程一般參數的詳細資訊，請參閱[about_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx)。

### <a name="positional-parameters"></a>位置參數

您無法使用位置參數活動與工作流程中的指令程式。  所有這種方法是，您必須使用參數名稱。

例如，請考慮下列程式碼，取得所有執行中的服務。

     Get-Service | Where-Object {$_.Status -eq "Running"}

如果您嘗試執行工作流程中的此相同的程式碼，您會收到訊息等 」 參數集無法解析使用指定的已命名參數。 」  若要修正這個問題，只要提供參數名稱，如下列所示。

    Workflow Get-RunningServices
    {
        Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
    }

### <a name="deserialized-objects"></a>還原序列化的物件

還原序列化工作流程中的物件。  這表示其屬性是仍然可以使用，但不是它們的方法。  例如，請考慮下列 PowerShell 程式碼的停駐點服務使用的服務物件的 [停止] 方法。

    $Service = Get-Service -Name MyService
    $Service.Stop()

如果您嘗試執行此工作流程中，您會收到的錯誤訊息 「 方法引動不支援在 Windows PowerShell 工作流程]。  

其中一個選項是將這兩行程式碼，在這種情況 $Service [InlineScript](#InlineScript)區塊中就會在區塊中的服務物件。 

    Workflow Stop-Service
    {
        InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
        }
    } 

如果有的話，請使用其他的 cmdlet 執行相同的功能，方法為另一個選項。  在我們的範例，停止服務 cmdlet 提供停駐點的方法，相同的功能，然後您可以使用下列的工作流程。

    Workflow Stop-MyService
    {
        $Service = Get-Service -Name MyService
        Stop-Service -Name $Service.Name
    }


## <a name="inlinescript"></a>InlineScript

**InlineScript**活動時，您需要執行一或多個命令，為傳統的 PowerShell 指令碼，而不是 PowerShell 工作流程。  工作流程中的命令會傳送到 Windows Workflow Foundation 處理，而 Windows PowerShell 會處理 InlineScript 區塊中的命令。 

InlineScript 使用下方顯示的語法。

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

指派輸出變數後，您可以從 InlineScript 傳回輸出。 下列範例服務停駐點，然後輸出服務名稱。

    Workflow Stop-MyService
    {
        $Output = InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


您可以將值傳遞到 InlineScript 區塊，但您必須使用**$Using**範圍輔助按鍵。  下列範例是與上一個範例相同，唯一的變數所提供的服務名稱。 

    Workflow Stop-MyService
    {
        $ServiceName = "MyService"
    
        $Output = InlineScript {
            $Service = Get-Service -Name $Using:ServiceName
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


雖然 InlineScript 活動特定工作流程中的要徑，他們不支援的工作流程架構，就和應該只用於時所需的原因如下︰

- 您無法使用[檢查點](#Checkpoints)InlineScript 區塊。 如果區塊中發生失敗，所以必須繼續從區塊的開頭。
- 您無法使用[平行執行](#parallel-execution)InlineScriptBlock 內。
- InlineScript 影響延展性的工作流程，因為它會保留 InlineScript 區塊的整個長度的 Windows PowerShell 工作階段。

如需有關使用 InlineScript，請參閱[在工作流程中執行的 Windows PowerShell 命令](http://technet.microsoft.com/library/jj574197.aspx)和[about_InlineScript](http://technet.microsoft.com/library/jj649082.aspx)。


## <a name="parallel-processing"></a>平行處理

Windows PowerShell 的工作流程的優點之一是能夠執行依序在與一般的指令碼平行，而不是一組的命令。 

若要建立指令碼區塊的多個會同時執行的命令，您可以使用**平行**關鍵字。 這是使用下方顯示的語法。 在此情況下，Activity1 和 Activity2 就會開始一次。 只有在同時 Activity1 和 Activity2 已完成後，就會開始 Activity3。

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>


例如，請考慮下列 PowerShell 命令，將多個檔案複製到的網路目的地。  讓該檔案必須完成複製下一步] 開始之前，會依序執行這些命令。     

    $Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
    $Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
    $Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt

下列工作流程中平行執行這些相同的命令，所有開始同時複製。  只是所有之後完全複製顯示完成的訊息。

    Workflow Copy-Files
    {
        Parallel 
        {
            $Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
            $Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
            $Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
        }

        Write-Output "Files copied."
    }


您可以使用**ForEach-平行**建構同時集合中的每個項目的程序命令。 循序執行指令碼區塊中的命令時，會處理平行集合中的項目。 這是使用下方顯示的語法。 在此情況下，Activity1 就會同時集合中的所有項目的開始。 針對每個項目，Activity1 完成後，就會開始 Activity2。 只有在同時 Activity1 和 Activity2 已完成的所有項目後，就會開始 Activity3。

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

下列範例是類似前一個範例平行複製檔案。  在此情況下，郵件會顯示的每個檔案之後會複製。  只是所有之後完全複製是最後完成顯示的訊息。

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach -Parallel ($File in $Files) 
        {
            $Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
        }
        
        Write-Output "All files copied."
    }

> [AZURE.NOTE]  我們不建議平行執行子 runbooks，因為這可能會產生不可靠的結果。  從子 runbook 有時輸出不會顯示，並設定一個子 runbook 可能會影響其他平行子 runbooks 


## <a name="checkpoints"></a>檢查點

*重點複習*是工作流程，其中包含目前值的變數和至該點產生任何輸出的目前狀態的快照。 如果工作流程以錯誤，或暫停，則下一次執行時，會開始從其檢查，而不是 worfklow 開頭的最後一個點。  **重點複習工作流程**活動的工作流程中，您可以設定檢查點。

在下列範例中，顯示發生 Activity2 之後的例外狀況造成結束工作流程。 再次執行工作流程時，它會開始執行 Activity2，因為這是設定的最後一個檢查點之後。

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

之後，可能會容易例外狀況，且不能活動重複如果繼續工作流程，您應該在 [工作流程設定檢查點。 例如，您的工作流程可能會建立虛擬機器。 您可以設定檢查點之前和之後的命令來建立虛擬機器。 如果建立失敗，請再命令可能會重複如果重新啟動工作流程。 如果 worfklow 失敗之後建立順利完成，然後虛擬機器將不會建立一次時繼續工作流程。

下列範例會將多個檔案複製到網路位置，並設定檢查點之後的每個檔案。  網路位置遺失時，工作流程會結束錯誤。  再次啟動時，便會繼續，這表示已複製的檔案會被略過的最後一個檢查點。

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach ($File in $Files) 
        {
            $Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
            Checkpoint-Workflow
        }
        
        Write-Output "All files copied."
    }

因為您連絡[暫停工作流程](https://technet.microsoft.com/library/jj733586.aspx)活動之後，或上次儲存點之後，不會保存使用者名稱的認證，您需要設定設為 null，然後再從擷取資產存放區呼叫 [**暫停工作流程**或檢查點之後的認證。  否則，您可能會收到下列錯誤訊息︰ 無法繼續*工作流程工作，可能是因為持續資料無法完全儲存或儲存持續資料已損毀。您必須重新啟動工作流程。*

下列相同的程式碼會示範如何處理此您 PowerShell 工作流程 runbooks。

       
    workflow CreateTestVms
    {
       $Cred = Get-AzureAutomationCredential -Name "MyCredential"
       $null = Add-AzureRmAccount -Credential $Cred

       $VmsToCreate = Get-AzureAutomationVariable -Name "VmsToCreate"

       foreach ($VmName in $VmsToCreate)
         {
          # Do work first to create the VM (code not shown)
        
          # Now add the VM
          New-AzureRmVm -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

          # Checkpoint so that VM creation is not repeated if workflow suspends
          $Cred = $null
          Checkpoint-Workflow
          $Cred = Get-AzureAutomationCredential -Name "MyCredential"
          $null = Add-AzureRmAccount -Credential $Cred
         }
     } 


如果您使用執行為帳戶設定與主要服務對其進行驗證，這樣就不需要。  

如需有關檢查點的詳細資訊，請參閱[新增指令碼工作流程的檢查點](http://technet.microsoft.com/library/jj574114.aspx)。


## <a name="next-steps"></a>後續步驟

- 若要開始使用 PowerShell 工作流程 runbooks，請參閱[我的第一個 PowerShell 工作流程 runbook](automation-first-runbook-textual.md) 
