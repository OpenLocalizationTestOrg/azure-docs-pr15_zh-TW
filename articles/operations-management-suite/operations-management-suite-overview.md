<properties
   pageTitle="作業管理套件 (OMS) 概觀 |Microsoft Azure"
   description="Microsoft 作業管理套件 (OMS) 是 Microsoft 的雲端 IT 管理解決方案，可協助您管理及保護您的內部部署與雲端基礎結構。  本文會識別不同 OMS 中包含的服務，並提供其詳細內容的連結。"
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="bwren" />

# <a name="what-is-operations-management-suite-oms"></a>什麼是作業管理套件 (OMS)？

Microsoft 作業管理套件 (OMS) 是 Microsoft 的雲端 IT 管理解決方案，可協助您管理及保護您的內部部署與雲端基礎結構。  由於 OMS 被當作雲端服務，您可以將它和使用快速使用最小的投資的基礎結構服務。  傳送自動儲存您正在進行的維護的新功能和升級成本。

除了自己提供寶貴的服務，OMS 可以整合與系統管理中心等系統管理中心 Operations Manager 擴充至雲端，您現有的管理投資的元件。  系統管理中心，以及 OMS，則可提供完整的混合式管理體驗共同作業。

下列各節提供高階 OMS 及實作它們的服務不同的值區域的描述。  您可以參考 OMS 架構，以概略不同 OMS 元件之前檢閱每個詳細的說明文件。


## <a name="insight-and-analyticsmediaoperations-management-suite-overviewicon-insight-analyticspng-insight-and-analytics"></a>![充分並分析資料](media/operations-management-suite-overview/icon-insight-analytics.png) 充分並分析資料

[記錄檔分析](http://azure.microsoft.com/documentation/services/log-analytics)可協助您收集、 建立關聯、 搜尋及在記錄檔和效能產生的作業系統和應用程式的資料上。 它會讓您隨時分析數百萬筆記錄的所有工作負載和伺服器，不論其實體位置使用整合式的搜尋及自訂的儀表板的即時作業深入資訊。

您可以輕鬆地加入解決方案的定義收集資料的記錄檔分析及分析的邏輯。  解決方案可能包含已自動傳送給代理程式的最小] 或 [沒有設定的其他功能。  除了使用個別的解決方案所提供的分析工具，您可以執行的自訂搜尋過整個資料集以建立資料系統與應用程式之間的關聯。  


## <a name="automation--controlmediaoperations-management-suite-overviewicon-automation-controlpng-automation--control"></a>![自動化與控制](media/operations-management-suite-overview/icon-automation-control.png) 自動化與控制

Azure 自動化會自動與[runbooks](../automation/automation-runbook-types.md)根據 PowerShell 並執行 Azure 雲端中的系統管理程序。  Runbooks 可以存取任何產品或服務，可使用 PowerShell 資源包括其他雲朵例如 Amazon Web 服務 (AWS)。  Runbooks 也可以在您的區域資料中心管理本機資源的伺服器上執行。

Azure 自動化提供[PowerShell DSC](../automation/automation-dsc-overview.md)設定的管理。  您可以建立及管理裝載於 Azure DSC 資源，套用到雲端和內部部署系統，以定義和自動強制使用自己的設定。


## <a name="protection-and-recoverymediaoperations-management-suite-overviewicon-protection-recoverypng-protection-and-disaster-recovery"></a>![保護及修復](media/operations-management-suite-overview/icon-protection-recovery.png) 保護與損毀修復

[Azure 備份](http://azure.microsoft.com/documentation/services/backup)保護您的應用程式的資料，並保留其年沒有任何資金投資與最小的操作成本。  可以備份實體和虛擬 Windows 伺服器，除了例如 SQL Server 和 SharePoint 應用程式工作負載的資料。  它也可由系統管理中心資料保護管理員 (DPM) 複寫 Azure 重複性和長期儲存受保護的資料。

[Azure 網站復原](http://azure.microsoft.com/documentation/services/site-recovery)提供您的業務連續性和損毀復原 (BCDR) 策略來協調複寫、 錯誤移轉及復原的內部部署 HYPER-V 虛擬機器、 VMware 虛擬機器，以及實體 Windows/Linux 伺服器。 您可以複製次要資料中心的機器，或將它們複寫到 Azure 來擴充資料中心。 網站復原也提供簡單錯誤移轉及復原的工作量。 它與 SQL Server AlwaysOn，例如損毀復原機制整合，並提供修復計劃層多台電腦上的工作負載的簡單容錯移轉。


## <a name="oms-security-and-compliancemediaoperations-management-suite-overviewicon-security-compliancepng-security-and-compliance"></a>![OMS 安全性和法規遵循](media/operations-management-suite-overview/icon-security-compliance.png) 安全性和法規遵循
安全性和法規遵循可協助您識別、 評估並降低基礎結構的安全性風險。  在分析記錄資料，並從代理程式系統，協助您確保您的環境進行中的安全性設定的記錄檔分析多個方案透過實作 OMS 的這些功能。

- [安全性及稽核解決方案](oms-security-getting-started.md )收集並分析來指出可疑的活動的受管理系統的安全性事件。
- [反惡意程式碼解決方案](log-analytics-malware.md )報表上的 [受管理系統的反惡意程式碼保護狀態。  
- 系統更新解決方案執行安全性更新分析及其他更新您受管理的系統，以便您輕鬆找出系統要求修補。


## <a name="next-steps"></a>後續步驟
- 深入了解[記錄分析](http://azure.microsoft.com/documentation/services/log-analytics)。
- 深入了解[Azure 自動化](../automation/automation-intro.md)。
- 深入了解[Azure 備份](http://azure.microsoft.com/documentation/services/backup)。
- 深入了解[Azure 網站復原](http://azure.microsoft.com/documentation/services/site-recovery)。
