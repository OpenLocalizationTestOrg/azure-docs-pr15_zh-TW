<properties
   pageTitle="技術指南︰ 復原來自內部部署至 Azure |Microsoft Azure"
   description="瞭解及設計修復系統從內部部署基礎結構 Azure 文章"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#<a name="azure-resiliency-technical-guidance-recovery-from-on-premises-to-azure"></a>Azure 恢復技術指南︰ 從內部部署至 Azure 中的復原

Azure 提供完整的服務進行高可用性和損毀修復啟用的內部部署資料中心，以 Azure 副檔名︰

* __網路__︰ 虛擬私人網路，您使用安全地擴充至雲端內部網路。
* __計算__︰ 使用 HYPER-V 內部部署的客戶可以 「 請拿起和 shift 」 Azure 至現有的虛擬機器 (Vm)。
* __儲存空間__︰ StorSimple 會將您的檔案系統延伸至 Azure 儲存體。 備份 Azure 服務會提供檔案與 SQL 資料庫的備份到 Azure 儲存體。
* __資料庫複寫__︰ SQL Server 2014 （或更新版本） 可用性] 群組，您可以實作高可用性和損毀復原您的內部部署資料。

##<a name="networking"></a>網路

您可以使用 Azure 虛擬網路 Azure 中建立邏輯隔離的節並安全地將其連線至您的內部部署資料中心或單一用戶端電腦，使用 IPsec 連線。 與虛擬網路，您可以利用可調整，視需要基礎結構的 Azure 中同時連線至資料和應用程式於內部部署，包括執行 Windows Server、 大型主機以及 UNIX 系統。 如需詳細資訊，請參閱[Azure 網路文件](../virtual-network/virtual-networks-overview.md)。

##<a name="compute"></a>計算

如果您使用 HYPER-V 內部部署，您可以 「 請拿起和 shift 」 現有 Azure 虛擬機器與服務提供者執行 Windows Server 2012 （或更新版本），不進行變更的 vm 或轉換 VM 格式。 如需詳細資訊，請參閱[關於磁碟和 Azure 虛擬機器 Vhd](../virtual-machines/virtual-machines-linux-about-disks-vhds.md)。

##<a name="azure-site-recovery"></a>Azure 網站復原

若要復原損毀的服務 (DRaaS)，請 Azure 提供[Azure 網站復原](https://azure.microsoft.com/services/site-recovery/)。 Azure 網站復原提供 VMware，HYPER-V，和實體伺服器的完整保護。 Azure 網站復原，您可以使用另一個內部部署伺服器或 Azure 為您修復的網站。 如需有關 Azure 網站復原的詳細資訊，請參閱[Azure 網站復原文件](https://azure.microsoft.com/documentation/services/site-recovery/)。

##<a name="storage"></a>儲存空間

有幾種使用 Azure 備份的網站，內部部署資料。

###<a name="storsimple"></a>StorSimple

安全地並無障礙 StorSimple 整合內部部署的應用程式的雲端儲存空間。 它也會提供提供高效能分層的本機及雲端儲存空間、 即時封存、 雲端的資料保護和損毀修復的單一應用裝置。 如需詳細資訊，請參閱[StorSimple 產品頁面](https://azure.microsoft.com/services/storsimple/)。

###<a name="azure-backup"></a>Azure 的備份

Azure 備份啟用雲端備份使用熟悉的備份工具在 Windows Server 2012 （或更新版本），在 Windows Server 2012 基本 （或更新版本） 及系統管理中心 2012年資料保護管理員 （或更新版本）。 這些工具會提供備份管理無關的儲存位置的備份，是否在本機磁碟或 Azure 儲存體中的工作流程。 資料備份到雲端之後，授權的使用者可以輕鬆地復原的任何伺服器的備份。

累加備份，檔案的變更會傳送至雲端。 這可協助有效率地使用儲存空間，減少頻寬使用量，支援的資料的多個版本的時間點復原。 您也可以使用其他的功能，例如資料保留原則、 資料壓縮和節流的資料傳輸。 使用 Azure 做為備份位置的優點明顯備份就會自動 「 離線 」。 如此額外的需求，以保護現場備份的媒體。

如需詳細資訊，請參閱[什麼是 Azure 備份？](../backup/backup-introduction-to-azure-backup.md)和[DPM 資料設定 Azure 備份](https://technet.microsoft.com/library/jj728752.aspx)。

##<a name="database"></a>資料庫

您可以使用 AlwaysOn 可用性群組、 資料庫鏡像、 記錄傳送和備份 SQL Server 資料庫混合式 IT 環境中有損壞修復解決方案，以及還原與 Azure Blob 儲存體。 所有這些解決方案使用 SQL Server 執行 Azure 虛擬機器上的商務連絡人。

在混合式 IT 環境資料庫複本存在兩個內部部署和雲端中，可 AlwaysOn 可用性群組。 下圖所示。

![SQL Server AlwaysOn 可用性群組中的混合式雲端架構](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-3.png)

資料庫鏡像也可以跨越內部部署伺服器和憑證為主的安裝程式中的雲端。 下圖說明這個概念。

![在混合式雲端架構的 SQL Server 資料庫鏡像](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-4.png)

記錄傳送可以用於 Azure 虛擬機器中的 SQL Server 資料庫與同步處理內部部署資料庫。

![SQL Server 記錄檔中的混合式雲端架構傳送](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-5.png)

最後，您可以直接至 Azure Blob 儲存體備份設定的內部部署的資料庫。

![備份 SQL Server 中的混合式雲端架構的 Azure Blob 儲存體](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-6.png)

如需詳細資訊，請參閱[Azure 虛擬機器中的 SQL Server 的高可用性和損毀復原](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md)和[備份與還原 Azure 虛擬機器中的 SQL server](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md)。

##<a name="checklists-for-on-premises-recovery-in-microsoft-azure"></a>Microsoft Azure 中的內部部署復原的檢查清單

###<a name="networking"></a>網路

  1. 請檢閱這份文件的 [網路] 區段。
  2. 使用安全連線至雲端的內部部署的虛擬網路。

###<a name="compute"></a>計算

  1. 請檢閱這份文件的 [計算] 區段。
  2. 重新 Vm 放置 HYPER-V 和 Azure 之間。

###<a name="storage"></a>儲存空間

  1. 請檢閱這份文件的 [儲存] 區段。
  2. 利用 StorSimple 服務使用雲端儲存空間。
  3. 使用備份 Azure 服務。

###<a name="database"></a>資料庫

  1. 請檢閱這份文件的資料庫一節。
  2. 請考慮使用 SQL Server Azure Vm 上，為備份。
  3. 設定 AlwaysOn 可用性群組。
  4. 設定憑證為基礎的資料庫鏡像。
  5. 使用記錄傳送。
  6. 備份資料庫內部部署 Azure Blob 儲存體。

##<a name="next-steps"></a>後續步驟

本文是著重於[Azure 恢復技術指南](./resiliency-technical-guidance.md)一系列的一部分。 本系列文章會[從資料損毀或不慎刪除復原](./resiliency-technical-guidance-recovery-data-corruption.md)。