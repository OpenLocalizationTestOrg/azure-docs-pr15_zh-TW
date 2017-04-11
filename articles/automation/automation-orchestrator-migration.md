<properties
   pageTitle="從 Orchestrator 升級至 Azure 自動化 |Microsoft Azure"
   description="說明如何將系統管理中心 Orchestrator runbooks 和整合套件移轉到 Azure 自動化。"
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


# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>從 Orchestrator 升級至 Azure 自動化 （beta 版）

在[系統管理中心 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) Runbooks 根據從整合套件時在 Azure 自動化 runbooks 根據 Windows PowerShell 撰寫專為 Orchestrator 的活動。  [圖形化 runbooks](automation-runbook-types.md#graphical-runbooks) Azure 自動化中有 Orchestrator runbooks 以類似的外觀與他們代表 PowerShell cmdlet、 子 runbooks 和資產的活動。

[系統管理中心 Orchestrator 移轉工具組](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all)包含工具，協助您轉換為 Azure 自動化 runbooks Orchestrator。  除了轉換 runbooks 自己，您必須將 runbooks 使用活動的整合套件轉換成整合模組中使用 Windows PowerShell cmdlet。  

以下是將 Orchestrator runbooks 轉換成 Azure 自動化的基本程序。  這些步驟會說明以下各節中的詳細資料。

1.  下載[系統管理中心 Orchestrator 移轉工具組](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all)其中包含的工具和本文所述的模組。
2.  [標準活動模組](#standard-activities-module)匯入 Azure 自動化。  這包含已轉換的轉換 runbooks 可能會使用標準 Orchestrator 活動的版本。
3.  使用您存取系統管理中心的 runbooks 這些整合套件匯 Azure 自動化[系統管理中心 Orchestrator 整合模組](#system-center-orchestrator-integration-modules)。
4.  將使用[整合套件轉換程式](#integration-pack-converter)的自訂和協力廠商的整合套件，並將 Azure 自動化匯入。
5.  將 Orchestrator runbooks 使用[Runbook 轉換程式](#runbook-converter)，並在 Azure 自動化安裝。
6.  手動 Runbook 轉換程式不會轉換這些資源之後，請在 Azure 自動化中建立所需的 Orchestrator 資產。
7.  設定[混合式 Runbook 工作者](#hybrid-runbook-worker)執行轉換的 runbooks 存取本機資源的區域資料中心。

## <a name="service-management-automation"></a>服務管理自動化

[服務管理自動化](http://technet.microsoft.com/library/dn469260.aspx)(SMA) 會儲存在您的區域資料中心 Orchestrator，例如執行 runbooks，並為 Azure 自動化使用相同的整合模組。 [Runbook 轉換程式](#runbook-converter)轉換 Orchestrator runbooks 圖形 runbooks 透過 SMA 中不支援。  您仍然可以安裝[活動的標準模組](#standard-activities-module)和[系統管理中心 Orchestrator 整合模組](#system-center-orchestrator-integration-modules)，將 SMA，但您必須手動[重新撰寫您 runbooks](http://technet.microsoft.com/library/dn469262.aspx)。

## <a name="hybrid-runbook-worker"></a>混合式 Runbook 工作者

在 Orchestrator Runbooks 會儲存在資料庫伺服器上，並執行 runbook 在伺服器上，在您的區域資料中心。  在 Azure 自動化 Runbooks Azure 雲端中的儲存，可以在您使用[混合式 Runbook 工作者](automation-hybrid-runbook-worker.md)的區域資料中心執行。  這是如何您通常會執行 runbooks 轉換的 Orchestrator，因為它們設計用來在本機的伺服器上執行。

## <a name="integration-pack-converter"></a>整合套件轉換程式

整合套件轉換程式將用來根據可以匯入 Azure 自動化或服務管理自動化的 Windows PowerShell 的整合模組[Orchestrator 整合工具組 (OIT)](http://technet.microsoft.com/library/hh855853.aspx)所建立的整合套件。  

當您執行整合套件轉換程式時，您會看到精靈可讓您選取 [整合套件 (.oip) 檔案。  然後，精靈會列出該整合套件中包含的活動，並可讓您選取 [將移轉。  當您完成精靈時，它會建立整合模組中的原始的整合套件的活動的每個包含對應的指令程式。


### <a name="parameters"></a>參數

整合套件中的活動的任何內容會轉換為參數的整合模組中對應的指令程式。  Windows PowerShell cmdlet 有一組的[常見的參數](http://technet.microsoft.com/library/hh847884.aspx)，可以使用所有指令程式。  例如，-詳細的參數會使 cmdlet 輸出作業的詳細的資訊。  沒有 cmdlet 可能必須具有相同名稱的一般參數的參數。  如果活動具有相同名稱的一般參數的屬性，精靈會提示您提供的參數另一個名稱。

### <a name="monitor-activities"></a>監視器活動

監視器 runbooks Orchestrator 中的開始[活動監視器](http://technet.microsoft.com/library/hh403827.aspx)，並執行持續等候由特定的事件。  Azure 自動化不支援監視器 runbooks，因此不會轉換任何監視器的活動的整合套件中。  不過，版面配置區指令程式會建立在監視器活動的整合模組。  這個指令程式沒有的功能，但其允許使用它來安裝任何轉換的 runbook。  此 runbook 不能在 Azure 自動化中執行，但它可以讓您可以修改安裝。

### <a name="integration-packs-that-cannot-be-converted"></a>無法轉換的整合套件

整合套件不是使用 OIT 無法轉換整合套件轉換程式。 此外，還有一些目前無法轉換的 Microsoft 使用此工具提供的整合套件。  已轉換的版本，這些整合套件的已[提供下載](#system-center-orchestrator-integration-modules)，讓他們可以安裝在 Azure 自動化或服務管理自動化。


## <a name="standard-activities-module"></a>標準活動模組

Orchestrator 包含一組[標準的活動](http://technet.microsoft.com/library/hh403832.aspx)，不會包含在整合套件，但會使用許多 runbooks。  標準活動模組是整合模組包含相當於 cmdlet 的每一個活動。  匯入使用標準的活動任何轉換的 runbooks 之前，您必須在 Azure 自動化安裝此整合模組。

支援轉換的 runbooks，除了在標準的活動] 模組中的指令程式可用熟悉 Orchestrator 由來建立新的 runbooks Azure 自動化中。  雖然可以執行 cmdlet 的標準活動的所有功能，它們可能以不同方式運作。  已轉換的標準活動模組中的指令程式會使用其對應的活動相同，並使用相同的參數。  這可協助他們切換到 Azure 自動化 runbooks 中現有的 Orchestrator runbook 作者。

## <a name="system-center-orchestrator-integration-modules"></a>系統管理中心 Orchestrator 整合模組

Microsoft 提供[整合套件](http://technet.microsoft.com/library/hh295851.aspx)建置 runbooks 便可自動化系統管理中心元件和其他產品。  這些整合套件部分目前根據 OIT，但無法目前轉換成整合模組由於已知問題。  [系統管理中心 Orchestrator 整合模組](https://www.microsoft.com/download/details.aspx?id=49555)包含可匯入 Azure 自動化與服務管理自動化這些整合套件已轉換的版本。  

這項工具 RTM 版，將發佈根據整合套件轉換程式可以轉換 OIT 整合套件的更新的版本。  也會提供指南來協助您轉換 runbooks 使用從不根據 OIT 整合套件的活動。

## <a name="runbook-converter"></a>Runbook 轉換程式

Runbook 轉換程式會將 Orchestrator runbooks 轉換成可以匯入 Azure 自動化的[圖形化 runbooks](automation-runbook-types.md#graph-runbooks) 。  

Runbook 轉換程式用作 PowerShell 模組中，使用稱為**ConvertFrom SCORunbook**執行轉換指令程式。  當您安裝的工具時，就會建立載入指令程式的 PowerShell 工作階段的捷徑。   

下列是轉換 Orchestrator runbook，其匯入 Azure 自動化的基本程序。  下列各節提供使用的工具和處理轉換 runbooks 進一步詳細資料。

1. 匯出 Orchestrator 一或多個 runbooks。
2. 取得 runbook 中的所有活動的整合模組。
3. 轉換 Orchestrator runbooks 在匯出的檔案。
4. 檢閱記錄來驗證轉換，以及決定任何必要的手動工作中的資訊。
4. 已轉換的 runbooks 匯入 Azure 自動化。
5. Azure 自動化建立任何必要的資產。
6. 編輯] 中修改任何必要的活動 Azure 自動化 runbook。

### <a name="using-runbook-converter"></a>使用 Runbook 轉換程式

**ConvertFrom SCORunbook**的語法如下所示︰

    ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string> 

- RunbookPath-包含轉換 runbooks 匯出檔案的路徑。
- 模組-以逗號分隔整合模組包含 runbooks 活動的清單。
- OutputFolder-建立資料夾路徑轉換圖形 runbooks。 


下列範例命令將轉換 runbooks 稱為**MyRunbooks.ois_export**匯出檔案中。  這些 runbooks 使用 Active Directory 與資料保護管理員的整合套件。

    ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks" 


### <a name="log-files"></a>記錄檔

Runbook 轉換程式會建立下列記錄檔中已轉換的 runbook 的相同位置。  如果已經存在的檔案，它們會被複寫的最後一個轉換資訊。

| 檔案 | 內容 |
|:---|:---|
| Runbook 轉換程式 Progress.log | 每個活動成功轉換的資訊與每個活動無法轉換成警告轉換的詳細的步驟。 |
| Runbook 轉換程式 Summary.log  | 摘要的最後一個轉換包括任何警告，並追蹤您需要建立所需的轉換 runbook 一個變數，例如執行的工作。  |

### <a name="exporting-runbooks-from-orchestrator"></a>匯出 Orchestrator runbooks

Runbook 轉換程式可用於從包含一或多個 runbooks Orchestrator 匯出檔案。  在匯出的檔案，就會建立每個 Orchestrator runbook 對應 Azure 自動化的 runbook。  

匯出 Orchestrator runbook，請以滑鼠右鍵按一下 runbook Runbook 設計工具中的名稱，然後選取 [**匯出**。  若要匯出的資料夾中的所有 runbooks，請以滑鼠右鍵按一下資料夾的名稱，然後選取 [**都匯出**。


### <a name="runbook-activities"></a>Runbook 活動

Runbook 轉換程式會將每個活動中 Orchestrator runbook 轉換成 Azure 自動化相對應的活動。  無法轉換這些活動的版面配置區活動會建立在警告文字 runbook。  Azure 自動化匯入已轉換的 runbook 之後，您必須將任何這些活動取代有效執行所需的功能的活動。

在[標準活動模組](#standard-activities-module)中的任何 Orchestrator 活動將會轉換。  有一些標準 Orchestrator 活動，但不是本單元中並不會轉換。  例如**傳送平台事件**有不相等的 Azure 自動化，由於 Orchestrator 特定事件。

由於 Azure 自動化中的沒有對應至無法轉換[監視器活動](https://technet.microsoft.com/library/hh403827.aspx)。  例外狀況的[轉換整合套件](#integration-pack-converter)，將會轉換成版面配置區活動監視器活動。

如果您提供的整合模組的路徑和**模組**參數，將會轉換任何從[轉換整合套件](#integration-pack-converter)的活動。  系統管理中心整合套件，您可以使用[系統管理中心 Orchestrator 整合模組](#system-center-orchestrator-integration-modules)。


### <a name="orchestrator-resources"></a>Orchestrator 資源

Runbooks、 不其他 Orchestrator 資源例如計數器、 變數或連線，只會將轉換 Runbook 轉換程式。  Azure 自動化不支援計數器。  支援的變數和連線，但您必須以手動方式建立。  記錄檔就會通知您，如果 runbook 需要這類資源，並指定對應中的已轉換的 runbook 才能正常 Azure 自動化建立所需的資源。

例如，runbook 可能會使用變數填入活動特定的值。  轉換的 runbook 會將活動，並使用 Orchestrator 變數相同的名稱，指定在 Azure 自動化的變數的資產。  這會轉換後會建立**Runbook 轉換程式 Summary.log**檔案中所述。  您必須先使用 runbook Azure 自動化中手動建立此變數的資產。 

 
### <a name="input-parameters"></a>輸入的參數

在 Orchestrator Runbooks 接受**初始化資料**活動的輸入的參數。  如果要轉換 runbook 包含此活動，請在 Azure 自動化 runbook 中[輸入的參數](automation-graphical-authoring-intro.md#runbook-input-and-output)會建立活動的每個參數。  已轉換的 runbook 擷取，並傳回每個參數中建立[工作流程指令碼控制項](automation-graphical-authoring-intro.md#activities)活動。  輸出請參閱此活動 runbook 中使用 [輸入的參數的任何活動。

這種方法，使用的原因是最適合鏡像 Orchestrator runbook 中的功能。  在新的圖形化 runbooks 活動直接請參閱在輸入參數使用 Runbook 輸入的資料來源。


### <a name="invoke-runbook-activity"></a>呼叫 Runbook 活動

Runbooks Orchestrator 中的其他 runbooks 開頭**叫用 Runbook**活動。 如果要轉換 runbook 包含此活動，**等待 [完成**] 選項設定，然後 runbook 活動不會建立在轉換 runbook。  如果 [**完成，請等候**] 選項未設定，然後工作流程指令碼活動會建立用於**開始 AzureAutomationRunbook**開始 runbook。  Azure 自動化匯入已轉換的 runbook 之後，您必須修改此活動活動中指定的資訊。




## <a name="related-articles"></a>相關的文章

- [系統管理中心 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx)
- [服務管理自動化](https://technet.microsoft.com/library/dn469260.aspx)
- [混合式 Runbook 工作者](automation-hybrid-runbook-worker.md)
- [Orchestrator 標準活動](http://technet.microsoft.com/library/hh403832.aspx)
- [下載系統管理中心 Orchestrator 移轉工具組](https://www.microsoft.com/en-us/download/details.aspx?id=47323)
 
