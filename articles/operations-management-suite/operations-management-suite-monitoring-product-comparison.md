<properties 
   pageTitle="監控產品比較 Microsoft |Microsoft Azure"
   description="Microsoft 作業管理套件 (OMS) 是以 Microsoft 的雲端基礎可協助您管理及保護您的內部部署與雲端基礎結構的 IT 管理解決方案。  本文會識別不同 OMS 中包含的服務，並提供其詳細內容的連結。"
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="bwren" />

# <a name="microsoft-monitoring-product-comparison"></a>Microsoft 監控產品的比較

本文提供其架構，他們如何監視資源，及如何執行這些收集的資料分析邏輯而言是系統管理中心的作業管理員 (SCOM) 和記錄分析作業管理套件 (OMS) 之間的比較。  這是為了讓您其差異和相對強度的基本概念。  

## <a name="basic-architecture"></a>基本架構
### <a name="system-center-operations-manager"></a>系統管理中心 Operations Manager

所有的是 SCOM 元件被安裝在您的資料中心。  [代理程式安裝](http://technet.microsoft.com/library/hh551142.aspx)在 Windows 和 Linux 電腦所管理的是 SCOM。  代理程式連線至[管理伺服器](https://technet.microsoft.com/library/hh301922.aspx)是 SCOM 資料庫及資料倉庫與通訊。  代理程式依賴連線到管理伺服器的網域驗證。  受信任的網域以外的人員可以執行憑證驗證]，或連線到[閘道伺服器](https://technet.microsoft.com/library/hh212823.aspx)。

是 SCOM 需要兩個 SQL 資料庫，一個用於操作資料，另一個資料倉庫支援報告及資料分析。  在[報表伺服器](https://technet.microsoft.com/library/hh298611.aspx)執行 SQL Reporting Services 報告資料倉庫的資料。 

是 SCOM 可以監視使用管理套件的產品，例如[Azure](https://www.microsoft.com/download/details.aspx?id=38414)、 [Office 365](https://www.microsoft.com/download/details.aspx?id=43708)，以及[AWS](http://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/AWSManagementPack.html)雲端資源。  這些管理封包作為探索雲端資源] 及 [執行的工作流程的 proxy 測量其效能及可用性一或多個本機代理程式。  Proxy 代理程式也會使用[監視器網路裝置](https://technet.microsoft.com/library/hh212935.aspx)及其他外部資源。

操作主控台是可連線到其中一個管理伺服器，並可讓系統管理員檢視及分析收集的資料，並設定是 SCOM 環境的 Windows 應用程式。  Web 主控台可以在任何 IIS 伺服器上裝載，並提供透過瀏覽器的資料分析。

![是 SCOM 架構](media/operations-management-suite-monitoring-product-comparison/scom-architecture.png)

### <a name="log-analytics"></a>記錄狀況分析

Azure 雲端中的大部分 OMS 元件是，因此您可以在部署及管理其最小的成本與系統管理工作。  收集記錄分析的所有資料會都儲存於 OMS 存放庫。

記錄檔分析可以收集資料，其中三個來源︰

- 實體與執行 Windows 和[Microsoft 監控代理程式 (MMA)](https://technet.microsoft.com/library/mt484108.aspx)或 Linux [Linux 的作業管理套件代理程式](https://technet.microsoft.com/library/mt622052.aspx)的虛擬機器。  這些機器可以內部部署或虛擬機器 Azure 或另一個雲端。
- [Azure 診斷](../cloud-services/cloud-services-dotnet-diagnostics.md)資料收集 Azure 工作者角色、 網頁角色或虛擬機器 Azure 儲存體帳戶。
- [連線至是 SCOM 管理群組](https://technet.microsoft.com/library/mt484104.aspx)。  在此組態中，代理程式通訊的是 SCOM 管理伺服器的資料傳送到是 SCOM 資料庫位置，然後傳送 OMS 資料存放區。
系統管理員分析收集的資料，並設定記錄分析 OMS 入口網站裝載於 Azure，並可以從任何瀏覽器存取。  標準平台的可存取此資料的行動應用程式。

![記錄檔分析架構](media/operations-management-suite-monitoring-product-comparison/log-analytics-architecture.png)

### <a name="integrating-scom-and-log-analytics"></a>整合是 SCOM 和記錄狀況分析

記錄檔分析是 SCOM 使用做為資料來源時，您可以利用混合式監控環境中的兩個產品的功能。  您可以設定為受 OMS，除了繼續執行管理套件從是 SCOM 操作主控台到現有的是 SCOM 代理程式。  
從連線的是 SCOM 管理群組的資料傳送到記錄分析使用四種方法之一︰

- 事件效能資料會收集代理程式，並傳送至是 SCOM。  管理伺服器中是 SCOM 然後會傳送至記錄分析的資料。
- 部分的事件，例如 IIS 記錄與安全性事件繼續會直接傳送到記錄分析代理程式。
- 一些解決方案會傳送至代理程式的其他軟體或需要安裝軟體時，會收集其他資料。  此資料會通常會直接傳送給記錄分析。
- 有些方案會收集資料，直接從不是來自代理程式的是 SCOM 管理伺服器。  例如，[通知管理解決方案](https://technet.microsoft.com/library/mt484092.aspx)會通知從是 SCOM 後所建立。

## <a name="monitoring-logic"></a>監控邏輯
是 SCOM 與記錄分析處理類似收集代理程式的資料，但有基本的差異，和如何定義和實作其邏輯的資料收集並分析收集的資料的方式。

### <a name="operations-manager"></a>Operations Manager
監視是 SCOM 邏輯中實作[管理套件](https://technet.microsoft.com/library/hh457558.aspx)包含邏輯探索監控，元件衡量這些元素，以及用來收集資料，以分析的狀況。  監視資料可能是非常簡單，收集的事件或效能計數器，或它可能使用複雜實作指令碼中的邏輯值。  管理套件，包括完整監視可供各種不同的[Microsoft 和協力廠商應用程式](http://go.microsoft.com/fwlink/?LinkId=82105)除了硬體和網路的裝置。  您可以[製作您自己的管理組件](http://aka.ms/mpauthor)的自訂應用程式。

管理套件包含每執行某些 distinct 監控函數，例如取樣效能計數器、 檢查服務的狀態，或執行指令碼的多個工作流程。  每個工作流程獨立執行，並定義自己結果，例如的資料庫，會將寫入與是否將會產生提醒。 

您可以覆寫的工作流程工作，例如他們執行時，這些錯誤，請考慮臨界值的頻率的詳細資料] 與 [產生的警示的嚴重性。  您也可以新增您自己的工作流程，以提供的其他功能。

![覆寫](media/operations-management-suite-monitoring-product-comparison/scom-overrides.png)

管理套件 Operations Manager 資料庫中安裝並自動管理伺服器分散式代理程式。  每個代理程式會自動下載管理套件，並載入與對方已安裝的應用程式相關的工作流程。  收集代理程式的資料已傳送至要插入是 SCOM 資料庫及資料倉庫管理伺服器。  操作主控台可讓您檢視及分析資料的自訂檢視、 儀表板，並包含在 management pack 中的報表。

下圖說明的管理組件。

![管理套件流程](media/operations-management-suite-monitoring-product-comparison/scom-mpflow.png)

### <a name="log-analytics"></a>記錄狀況分析
#### <a name="event-and-performance-collection"></a>事件及效能集合
記錄檔分析會事件和效能計數器從使用來源，例如 Windows 事件記錄檔 IIS 記錄與系統的代理程式系統。  您可以定義的準則的資料是收集透過記錄分析入口網站]，然後建立記錄檔的查詢，以分析收集的資料。  當您建立 OMS 工作區中，您可以定義的特定應用程式的其他資料定義一組標準的準則。 

![在記錄檔分析中定義的事件記錄檔](media/operations-management-suite-monitoring-product-comparison/log-analytics-definedata.png)

是 SCOM 有許多詳細的工作流程，通常是定義特定準則的資料及回應應該執行的動作，記錄分析有多個一般準則資料集合。  記錄查詢及解決方案提供分析及雲端中的特定資料的方式增添後所收集的更多目標的準則。

#### <a name="solutions"></a>解決方案
解決方案的資料收集與分析，提供其他邏輯。  您可以選取要從方案庫新增至您的 OMS 訂閱方案。

![方案庫](media/operations-management-suite-monitoring-product-comparison/log-analytics-solutiongallery.png)

在提供的分析事件和 OMS 存放庫中收集的效能計數器雲端主要執行解決方案。  他們也可以定義收集您可以使用記錄查詢或 OMS 儀表板中的方案所提供的其他使用者介面分析的其他資料。 

例如，[追蹤修訂的解決方案](https://technet.microsoft.com/library/mt484099.aspx)偵測到代理程式的系統上變更設定，並將事件，您可以使用彙總的數個圖形檢視分析 OMS 存放庫偵測到的變更。  您可以向下切入從記錄查詢顯示收集方案的詳細的資料的摘要檢視。


雖然您可以選取您新增至您的訂閱的方案，您目前沒有能力建立您自己的解決方案。  您可以選取事件和效能計數器來收集及建立自訂的檢視，根據您自己記錄的查詢。

下圖中摘要監控邏輯記錄分析。

![記錄檔分析解決方案流程](media/operations-management-suite-monitoring-product-comparison/log-analytics-solution-flow.png)

## <a name="health-monitoring"></a>監控健康狀況
### <a name="operations-manager"></a>Operations Manager
是 SCOM 可以模型在應用程式的不同元件，每個提供即時的狀況。  這可讓您不只檢視偵測的錯誤和效能一段時間，也會在任何時候驗證應用程式或系統的實際狀況和每個元件。  因為它了解應用程式所提供的時段，狀況中的引擎是 SCOM 也支援服務等級協定 (SLA) 的分析及報告可用性的一段時間的應用程式。

例如，下列檢視] 會顯示 SQL 資料庫引擎是 SCOM 監視即時健康的情況。  健康狀況的每個資料庫的資料庫引擎會顯示在下方下半部的檢視。

![狀態] 檢視](media/operations-management-suite-monitoring-product-comparison/scom-state-view.png)

如下所示的其中一個資料庫引擎狀況檔案總管，以用來決定其整體的健康情況的監視器。  在 SQL management pack 中所定義和執行所有 SQL 資料庫引擎是 SCOM 發現這些監視器。

![狀況總管](media/operations-management-suite-monitoring-product-comparison/scom-health-explorer.png)

合併多個系統元件測量的分散式應用程式的狀況。  這可能是包含多個分散式的元件的商務應用程式的特別有用。  您可以建立計算的每個元件都健康情況的模型的彙總套件，將應用程式的可用性。

Active Directory 是提供模型，以分析其分散式的元件的一個 management pack 中，範例。  下列範例圖表會顯示的狀況的整體的環境及樹系、 網域及網域控制站之間的關聯。  每個元件包含子元件，類似於 SQL 上述範例中的多部監視器。

![是 SCOM 圖表檢視](media/operations-management-suite-monitoring-product-comparison/scom-diagram-view.png)

### <a name="log-analytics"></a>記錄狀況分析
OMS 不包含模型應用程式的一般引擎或測量其即時狀況。  個別的解決方案可能會評估整體的特定收集資料為基礎的服務健康狀況和他們可能會在執行即時分析代理程式安裝自訂邏輯。  Access 的雲端的解決方案是至 OMS 存放庫執行，因此可以通常會提供更深入的分析，通常是由管理套件比。 

例如[AD 評估與 SQL 評定解決方案](https://technet.microsoft.com/library/mt484102.aspx)分析收集的資料，並提供不同層面的環境的等級。  其包含的改良功能，可改善可用性和環境的效能的建議。

![AD 評估解決方案](media/operations-management-suite-monitoring-product-comparison/log-analytics-ad-assessment.png)

## <a name="data-analysis"></a>資料分析
是 SCOM 與記錄分析每個提供不同的功能，以分析收集的資料。  是 SCOM 會有分析各種不同的格式，以及展示資料表格式資料倉庫從報表中的最近資料作業主控台中檢視和儀表板。  記錄檔分析提供完整的記錄查詢語言與介面分析 OMS 存放庫中的資料。  是 SCOM 記錄分析使用做為資料來源時，將存放庫包含收集的是 SCOM，因此可以使用記錄分析工具來分析資料的兩個系統資料。

### <a name="operations-manager"></a>Operations Manager

#### <a name="views"></a>檢視
在作業主控台中的檢視可讓您檢視不同的格式，通常是表格式事件、 通知，及狀態資料的不同資料類型是 SCOM 收集到和折線圖的效能資料的圖形。  檢視執行最小的分析或合併彙算資料的但不要讓您根據特定準則篩選。 

![檢視](media/operations-management-suite-monitoring-product-comparison/scom-views.png)

管理組件通常會提供支援的應用程式或監視的系統的多個檢視。  這可能會包含不同的物件的管理套件發現，提醒檢視偵測到問題及效能計數器狀態檢視。

檢視都特別適合分析包括開啟通知的環境的目前狀態及監視的系統和物件的狀況。  您可以像下詳細的事件或效能資料才能診斷其根本原因支援特定的提醒。 同樣地，您可以檢視的效能與不同元件的應用程式來評估其目前的健康情況的狀況。

#### <a name="dashboards"></a>儀表板
儀表板中操作主控台主要會使用相同的資料檢視但更易於自訂，並可以包含更豐富視覺效果。  標準的儀表板的一組可您可以輕鬆地自訂您自己的目的。  您也可以使用 PowerShell widget 可以顯示 PowerShell 查詢所傳回的資料。

![儀表板](media/operations-management-suite-monitoring-product-comparison/scom-dashboard.png)

開發人員可以新增自訂的元件至 [儀表板其管理組件中包含的功能。  這些可能會具有特定的特定的應用程式，例如在以下所示的 SQL management pack 中的儀表板。  此儀表板也可另存為範本，自訂的版本。

![SQL 儀表板](media/operations-management-suite-monitoring-product-comparison/scom-sql-dashboard.png)

#### <a name="reports"></a>報表
報表是 SCOM 分析表格式資料倉庫的資料。  他們可以列印並以不同的檔案格式，包括 PDF、 CSV，以及 Word 自動傳遞排程。  報表會使用資料倉庫的資料，使其特別適合的長期趨勢分析。

管理組件通常會特定應用程式提供自訂報表。  您也可以選取從一般報告，您可以自訂您的應用程式或執行臨機操作分析的文件庫。

以下是範例效能報告顯示藉由使用 Active Directory Management Pack 收集的資料。

![報表](media/operations-management-suite-monitoring-product-comparison/scom-report.png)

### <a name="log-analytics"></a>記錄狀況分析
記錄檔分析具有[查詢語言](https://technet.microsoft.com/library/mt484120.aspx)，您可以使用跨多個應用程式，而不需要建立自訂的檢視或報表的資料執行分析。  因此 OMS 實作在雲端後，查詢和資料分析的效能沒有任何硬體限制，且可以快速分析包括數百萬筆記錄的查詢。 

記錄分析中的查詢，也會影響其他功能的基礎。  您可以將查詢儲存、 匯出至 Excel，其結果或就會自動執行定期間隔，並產生警示，如果其結果符合特定準則。  

![記錄查詢流程](media/operations-management-suite-monitoring-product-comparison/log-analytics-query-flow.png)

以下是記錄分析查詢的範例。  在此範例中使用 「 啟動 」 名稱中的所有事件會傳回和分組依據 [event 識別碼。  使用者只提供的查詢，並記錄分析動態產生的使用者介面，來執行分析。  選取清單中的任何項目會傳回事件的詳細的資料。

![記錄查詢](media/operations-management-suite-monitoring-product-comparison/log-analytics-query.png)

除了提供臨機操作分析，可以儲存以供日後使用，如下列範例所示也加入您[OMS 儀表板](http://technet.microsoft.com/library/mt484090.aspx)中記錄分析的查詢。

![OMS 儀表板](media/operations-management-suite-monitoring-product-comparison/log-analytics-dashboard.png)

## <a name="next-steps"></a>後續步驟

- 部署[系統管理中心 Operations Manager (是 SCOM)](https://technet.microsoft.com/library/hh205987.aspx)。
- 登入[記錄檔](https://azure.microsoft.com/documentation/services/log-analytics)分析。  
