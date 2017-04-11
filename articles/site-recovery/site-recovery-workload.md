<properties
    pageTitle="您可以使用 Azure 網站復原保護哪些負載？"
    description="Azure 網站復原保護您的工作量及應用程式的協調複寫、 錯誤移轉及復原的內部部署虛擬機器和實體伺服器 Azure 或內部部署的次要網站"
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/10/2016"
    ms.author="raynew"/>

# <a name="what-workloads-can-you-protect-with-azure-site-recovery"></a>您可以使用 Azure 網站復原保護哪些負載？


本文將說明工作量及應用程式，您可以複製 Azure 網站復原服務。

在底端的本文中，或在[Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上張貼任何註解或問題。

## <a name="overview"></a>概觀

組織必須若要保留工作量及資料安全且可供計劃和計畫停機時間期間，越快越復原正常運作狀況的業務連續性和損毀復原 (BCDR) 策略。

網站復原是 Azure 服務可提供您 BCDR 策略。 使用網站復原，您可以部署應用程式注意複寫至雲端，或在次要網站。 無論您的應用程式會在 Windows 或 Linux 型、 執行實體在伺服器上，VMware 或 HYPER-V，您可以使用網站復原來協調試驗，請執行損毀復原測試並執行容錯移轉和回復。


網站復原整合與 Microsoft office 應用程式，包括 SharePoint、 Exchange、 Dynamics、 SQL Server 和 Active Directory。 Microsoft 也與密切前置廠商包括 Oracle、 SAP、 IBM 及紅色角色。 您可以自訂應用程式的應用程式為基礎的複寫解決方案。

## <a name="why-use-site-recovery-for-application-replication"></a>為什麼要使用網站復原複寫應用程式？

網站復原佔應用程式層級保護及復原，如下所示︰

- 應用程式無關，提供受支援的電腦上執行任何工作負載的複寫。
- 接近同步，與複寫 RPOs 低 30 秒數，以符合最重要的商務應用程式的需求。
- 應用程式一致的快照，針對單一或多層應用程式。
- 整合 SQL Server AlwaysOn，與其他應用程式層級複寫技術，包括 AD 複寫 SQL AlwaysOn、 Exchange 資料庫可用性群組 (DAGs) 和 Oracle 資料保護與合作關係。
- 彈性修復計劃，可讓您要還原整個應用程式堆疊，只要按一下，並包含計劃中包含外部指令碼及手動的動作。
- 進階的網路管理網站復原和 Azure，以簡化應用程式的網路需求，包括可保留 IP 位址的負載平衡與整合使用 Azure 流量管理員中，針對設定低 RTO 網路 switchovers。
-  豐富的自動化的文件庫提供生產準備、 應用程式可以下載並修復計劃與整合的指令碼。



## <a name="workload-summary"></a>摘要的工作量

網站復原可以複寫的支援的電腦上執行任何應用程式。 此外我們已在協助產品小組執行其他特定應用程式的測試。

**工作負載** | **複製 HYPER-V Vm 次要網站** | **Azure 複寫 HYPER-V Vm** | **複製 VMware Vm 次要網站** | **Azure 複寫 VMware Vm**
---|---|---|---|---
Active Directory DNS | Y | Y | Y | Y
Web 應用程式 （IIS、 SQL） | Y | Y | Y | Y
系統管理中心 Operations Manager | Y | Y | Y | Y
Sharepoint | Y | Y | Y | Y
SAP<br/><br/>將非叢集 Azure 複寫 SAP 網站 | Y （測試 microsoft） | Y （測試 microsoft） | Y （測試 microsoft） | Y （測試 microsoft）
Exchange (非 DAG) | Y | 即將推出 | Y | Y
遠端桌面/VDI | Y | Y | Y | N/A
Linux （作業系統和應用程式） | Y （測試 microsoft） | Y （測試 microsoft） | Y （測試 microsoft） | Y （測試 microsoft）
Dynamics AX | Y | Y | Y | Y
Dynamics CRM | Y | 即將推出 | Y | 即將推出
Oracle | Y （測試 microsoft） | Y （測試 microsoft） | Y （測試 microsoft） | Y （測試 microsoft）
Windows 檔案伺服器 | Y | Y | Y | Y


## <a name="replicate-active-directory-and-dns"></a>複製 Active Directory 和 DNS

Active Directory 和 DNS 基礎結構是不可或缺大部分的企業應用程式。 期間損毀修復，必須保護與復原這些基礎結構的元件之前復原您的工作量及應用程式。

若要建立完成自動化的損壞復原計畫 Active Directory 和 DNS，您可以使用網站復原。 例如，如果您想要無法透過 SharePoint 及 SAP 從主要次要網站，您可以設定的第一次，無法在 Active Directory 復原方案，然後容錯 Active Directory 所依賴的其他應用程式的其他應用程式特定修復方案。

[瞭解更多](site-recovery-active-directory.md)有關保護 Active Directory 和 DNS。

## <a name="protect-sql-server"></a>保護 SQL Server

SQL Server 資料服務 foundation 提供許多商務應用程式在內部部署資料中心的資料服務。  網站復原可與 SQL Server HA/DR 技術來保護使用 SQL Server 的多層的企業應用程式。 網站復原提供︰

- SQL Server 的簡單且經濟損壞修復解決方案。 將多個版本和版本的 SQL Server 獨立伺服器和叢集，複寫 Azure 或次要網站。  
- SQL AlwaysOn 可用性] 群組，來管理容錯移轉和 Azure 網站復原復原方案回復整合。
- 在應用程式，包括 SQL Server 資料庫中所有層的端對端修復計劃。
- 藉由 「 爆裂 」 進行到 Azure 更大的 IaaS 虛擬機器大小，按比例縮放的 SQL Server 的最大使用量載入網站復原。
- 輕鬆測試 SQL Server 損毀修復。 您可以執行測試容錯移轉來分析資料，並執行相容性檢查，而不會影響您生產環境。

[瞭解更多](site-recovery-sql.md)有關保護 SQL server。

##<a name="protect-sharepoint"></a>保護 SharePoint

Azure 網站復原可協助保護 SharePoint 部署，如下所示︰

- 排除需要和損毀修復獨立伺服器陣列的相關聯的基礎結構成本。 使用網站復原 Azure 或次要網站複製整個伺服器陣列 （Web 應用程式與資料庫層）。
- 簡化的應用程式部署及管理。 部署至主網站的更新會自動複寫，且因此之後錯誤移轉及復原的次要網站中的伺服器陣列。 也可以降低管理複雜度和相關聯保持最新狀態獨立伺服器陣列的成本。
- 簡化開發 SharePoint 應用程式和藉由建立類似生產複製視複本環境測試和偵錯的測試。
- 若要將 SharePoint 部署移轉到 Azure 使用網站復原簡化轉場效果至雲端。

[瞭解更多](https://gallery.technet.microsoft.com/SharePoint-DR-Solution-f6b4aeae)有關保護 SharePoint。


## <a name="protect-dynamics-ax"></a>保護 Dynamics AX

Azure 網站復原可協助保護您 Dynamics AX ERP 的解決方案，以︰

- 協調複寫整個 Dynamics AX 環境的 （[資料庫層級，SharePoint 中的 [網頁和 AOS 層） Azure 或次要網站。
- 簡化移轉 Dynamics AX 部署至雲端 (Azure)。
- 簡化 Dynamics AX 應用程式開發，藉由建立類似生產複製視測試和偵錯的測試。

[瞭解更多](https://gallery.technet.microsoft.com/Dynamics-AX-DR-Solution-b2a76281)有關保護動態 AX。

## <a name="protect-rds"></a>保護 RDS

遠端桌面服務 (RDS) 可讓虛擬桌面基礎結構 (VDI) 工作階段為基礎的桌面與應用程式]，讓使用者可以隨處工作。 使用 Azure 網站復原，您可以︰

- 複寫受管理或受管理共用的虛擬桌面次要網站，與遠端應用程式和次要網站或 Azure 工作階段。
- 以下是您可以複製︰

**RDS** | **複製 HYPER-V Vm 次要網站** | **Azure 複寫 HYPER-V Vm** | **複製 VMware Vm 次要網站** | **Azure 複寫 VMware Vm** | **複製至次要網站的實體伺服器** | **Azure 複寫實體伺服器**
---|---|---|---|---|---|---
**集區式的虛擬桌面 （不受管理）** | [是] | 無 | [是] | 無 | [是] | 無
**集區式虛擬桌面 （受管理和不 UPD）** | [是] | 無 | [是] | 無 | [是] | 無
**遠端應用程式和桌面工作階段 （不含 UPD)** | [是] | [是] | [是] | [是] | [是] | [是]


[深入](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb)瞭解如何保護 RDS


## <a name="protect-exchange"></a>保護 Exchange

網站復原可協助保護 Exchange，如下所示︰

- 針對小型 Exchange 部署，例如單一或獨立的伺服器，網站復原可以複製並無法透過 Azure 或次要網站。
- 對於較大的部署，網站復原會與 Exchange DAGS 整合。
- Exchange DAGs 是 Exchange 損毀修復，在企業中的建議的解決方案。  網站復原修復計劃可以包含 DAGs，跨網站協調 DAG 容錯移轉。


[瞭解更多](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6)有關保護 Exchange。

## <a name="protect-sap"></a>保護 SAP

使用網站復原來保護您的 SAP 部署，如下所示︰

- 啟用保護整個 SAP 部署中，藉由 Azure 或次要網站複寫不同的部署圖層。
- 若要將 SAP 部署移轉到 Azure 使用網站復原，以簡化雲端移轉。
- 簡化 SAP 開發，測試，藉由建立類似生產複製視需要進行測試和偵錯應用程式。

[瞭解更多](http://aka.ms/asr-sap)有關保護 SAP。

## <a name="next-steps"></a>後續步驟

[準備網站復原部署](site-recovery-best-practices.md) 
