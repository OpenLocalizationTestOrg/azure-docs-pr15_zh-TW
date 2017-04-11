<properties
    pageTitle="什麼是 Azure 自動化 |Microsoft Azure"
    description="瞭解 Azure 自動化提供哪些值，並取得常見問題的解答，以便您可以開始建立，使用 runbooks 和 Azure 自動化 DSC。"
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
    keywords="自動化、 azure 自動化、 azure 自動化範例是什麼"/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article" 
    ms.date="05/10/2016"
    ms.author="magoedte;bwren"/>

# <a name="azure-automation-overview"></a>Azure 自動化概觀

Microsoft Azure 自動化提供使用者便可自動化經常執行雲端版和企業版環境中手動較長時間執行、 容易發生錯誤，與經常重複工作的方式。 它節省時間增加可靠性的一般的系統管理工作，並甚至排程進行自動執行定期間隔。 您可以自動化程序使用 runbooks 或自動化使用需要狀態組態設定管理。 本文提供 Azure 自動化簡短的概觀，並回答一些常見的問題。 您可以參考此文件庫，如需詳細資訊，在不同的主題中的其他文章。


## <a name="automating-processes-with-runbooks"></a>使用 runbooks 自動化程序

Runbook 是一組中 Azure 自動化執行某些自動化程序的工作。 可能很簡單的程序，例如啟動虛擬機器和建立的記錄項目，或您可能需要將其他較小的 runbooks 執行複雜的程序，跨多個資源或更多個 cloud 與內部部署環境複雜 runbook。  

比方說，您可能會有現有手動程序截斷 SQL 資料庫，如果已經達到大小上限，其中包含多個步驟，例如連線到伺服器，連接至資料庫、 取得目前資料庫的大小、 核取 [是否有超過臨界值，然後捨去，而通知使用者。 而不是以手動方式執行這些步驟，您可以建立會以單一程序執行這些工作的所有 runbook。 您想要開始 runbook、 提供所需的資訊，例如 SQL server 名稱、 資料庫名稱和收件者的電子郵件與然後坐程序完成時，回。 


## <a name="what-can-runbooks-automate"></a>什麼能 runbooks 自動化？

在 Azure 自動化 Runbooks 根據 Windows PowerShell 或 Windows PowerShell 的工作流程，讓他們執行任何 PowerShell 可以執行的項目。 如果應用程式或服務的 API，然後 runbook 可以使用它。 如果您有 PowerShell 模組的應用程式，然後您可以 Azure 自動化載入該模組並您 runbook 中包含這些 cmdlet。 Azure 自動化 runbooks Azure 雲端中執行，並可以存取任何雲端資源可以從雲端存取的外部資源。 使用[混合式 Runbook 工作者](automation-hybrid-runbook-worker.md)，runbooks 可以在您的區域資料中心管理本機資源執行。 


## <a name="getting-runbooks-from-the-community"></a>從社群取得 runbooks

[Runbook 圖庫](automation-runbook-gallery.md#runbooks-in-runbook-gallery)中包含 runbooks microsoft，您可以使用社群不會變更您的環境中，或加以自訂您自己的目的。 這也是必做為參考，瞭解如何建立您自己的 runbooks。 您甚至可以參與庫您認為其他使用者可能會發現有幫助您自己 runbooks。 


## <a name="creating-runbooks-with-azure-automation"></a>建立 Runbooks Azure 自動化 

您可以從頭開始[建立您自己的 runbooks](automation-creating-importing-runbook.md)或修改 runbooks [Runbook 庫](http://msdn.microsoft.com/library/azure/dn781422.aspx)，您自己的需求。 有三種不同[runbook 類型](automation-runbook-types.md)，您可以選擇從根據您的需求和 PowerShell 體驗。 如果您想要直接使用 PowerShell 的程式碼，然後您可以使用[PowerShell runbook](automation-runbook-types.md#powershell-runbooks)或[PowerShell 工作流程 runbook](automation-runbook-types.md#powershell-workflow-runbooks)編輯離線] 或 [與[文字編輯器](http://msdn.microsoft.com/library/azure/dn879137.aspx)Azure 入口網站中的文件。 如果您想要編輯 runbook 無須公開基礎的程式碼，您可以建立使用[圖形編輯器](automation-graphical-authoring-intro.md)Azure 入口網站中的[圖形化 runbook](automation-runbook-types.md#graphical-runbooks) 。 

比較偏好觀看讀取嗎？ 看看 Microsoft Ignite 2015 中的工作階段中的視訊] 底下。 附註︰ 正確，因為這段影片錄製 Azure 自動化已許多前進的概念與這段影片中所討論的功能時，它現在有更多使用者介面中 Azure 入口網站中，並支援其他功能。

> [AZURE.VIDEO microsoft-ignite-2015-automating-operational-and-management-tasks-using-azure-automation]


## <a name="automating-configuration-management-with-desired-state-configuration"></a>自動設定管理，所需的狀態設定 

[PowerShell DSC](https://technet.microsoft.com/library/dn249912.aspx)是管理平台可讓您管理、 部署及強制執行的實際主辦城市] 和 [使用宣告式的 PowerShell 語法的虛擬機器設定。 您可以定義設定中央 DSC 提取伺服器上的目標電腦自動擷取及套用。 DSC 提供一組可用來管理設定與資源的 PowerShell 指令程式。  

[Azure 自動化 DSC](automation-dsc-overview.md)是提供服務所需的企業環境的 PowerShell DSC 雲端解決方案。  您可以管理中 Azure 自動化 DSC 資源，並將設定套用至虛擬或實體從 Azure 雲端中的 DSC 提取伺服器擷取的電腦。  同時也會提供報告通知您的重要事件，例如節點有萬一從其指定設定，當新的設定已套用的服務。 


## <a name="creating-your-own-dsc-configurations-with-azure-automation"></a>建立您自己 DSC 組態 Azure 自動化

[DSC 設定](automation-dsc-overview.md#azure-automation-dsc-terms)指定節點的所需的狀態。  多個節點可以套用相同的設定，以確保所有他們保持相同的狀態。  您可以設定，使用任何文字編輯器] 中建立您的本機電腦上，然後將其匯 Azure 自動化位置，您可以編譯，並將其套用節點。


## <a name="getting-modules-and-configurations"></a>如何取得模組和設定 

您可以取得[PowerShell 模組](automation-runbook-gallery.md#modules-in-powershell-gallery)包含 runbooks 與 DSC [PowerShell 圖庫](http://www.powershellgallery.com/)中的設定，您可以使用的 cmdlet。 您可以啟動此圖庫從 Azure 入口網站，並直接將 Azure 自動化匯入模組，或您可以下載並手動將其匯入。 您無法直接從 Azure 入口網站，安裝模組，但您可以下載安裝您想要的任何其他模組。 


## <a name="example-practical-applications-of-azure-automation"></a>Azure 自動化範例實務應用程式 

以下是一些範例什麼是 Azure 自動化案例的類型。 

* 建立並複製虛擬機器中不同的 Azure 訂閱。 
* 排程從本機電腦的檔案複製至 Azure Blob 儲存體容器。 
* 自動偵測到拒絕服務攻擊時，例如拒絕要求從用戶端的安全性功能。 
* 請確定電腦持續對齊設定的安全性原則。
* 管理連續的部署應用程式碼在雲端，然後在部署基礎結構。 
* 建立實驗室環境中 Azure Active Directory 樹系。 
* 如果 DB 接近最大值，則捨去 SQL 資料庫中的資料表。 
* 遠端更新環境設定為 Azure 的網站。 


## <a name="how-does-azure-automation-relate-to-other-automation-tools"></a>Azure 自動化如何與其他自動化工具？

[服務管理自動化 (SMA)](http://technet.microsoft.com/library/dn469260.aspx)被要自動化私人雲端中的管理工作。 安裝至本機資料中心元件的[Microsoft Azure 套件](https://www.microsoft.com/en-us/server-cloud/)。 SMA 和 Azure 自動化使用相同的 runbook 格式根據 Windows PowerShell 和 Windows PowerShell 的工作流程，但 SMA 不支援[圖形 runbooks](automation-graphical-authoring-intro.md)。  

[系統管理中心 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx)適用於內部部署資源的自動化。 它使用 Azure 自動化，服務管理自動化不同 runbook 格式，並已經建立 runbooks，而不需要任何指令碼的圖形化介面。 其 runbooks 被組成從專為 Orchestrator 所撰寫的整合套件的活動。 


## <a name="where-can-i-get-more-information"></a>哪裡可以取得詳細資訊？ 

各種不同的資源，可讓您深入瞭解 Azure 自動化並建立您自己的 runbooks。 

* **Azure 自動化文件庫**是您所在的權限。 此文件庫中的文章提供完整的文件的設定和管理的 Azure 自動化，並為製作您自己的 runbooks。 
* [Azure PowerShell cmdlet](http://msdn.microsoft.com/library/jj156055.aspx)提供使用 Windows PowerShell 的 Azure 作業自動化的資訊。 Runbooks 會使用這些 cmdlet 來使用 Azure 資源。 
* [管理部落格](https://azure.microsoft.com/blog/tag/azure-automation/)會提供從 Microsoft Azure 自動化和其他管理技術上的最新的資訊。 您必須訂閱保持最新的最新版 Azure 自動化小組此部落格。 
* [自動化論壇，在](http://go.microsoft.com/fwlink/p/?LinkId=390561)可讓您可以張貼關於 Azure 自動化 Microsoft 和自動化社群解決的問題。 
* [Azure 自動化 Cmdlet](https://msdn.microsoft.com/library/mt244122.aspx)提供自動化管理工作的資訊。 包含 cmdlet 來管理自動化帳戶、 資產 runbooks，DSC。


## <a name="can-i-provide-feedback"></a>我可以提供意見反應？ 

**請提供意見反應 ！** 如果您要尋找 Azure 自動化 runbook 解決方案或整合模組，張貼的指令碼要求指令碼中心。 如果您的意見反應或功能要求 Azure 自動化，張貼在[使用者的語音](http://feedback.windowsazure.com/forums/34192--general-feedback)。 感謝您 ！ 


