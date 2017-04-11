<properties
    pageTitle="SQL (PaaS) 資料庫與 SQL Server 上 Vm (IaaS) 雲端 |Microsoft Azure"
    description="瞭解哪一個雲端 SQL Server 選項是否符合您的應用程式: (PaaS) 的 Azure SQL 資料庫或雲端上 Azure 虛擬機器中的 SQL Server。"
    services="sql-database, virtual-machines"
    keywords="SQL Server 雲端，在雲端，PaaS 資料庫的 SQL Server 雲端 SQL Server，DBaaS"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor="cjgronlund"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/06/2016"
    ms.author="carlrab"/>

# <a name="choose-a-cloud-sql-server-option-azure-sql-paas-database-or-sql-server-on-azure-vms-iaas"></a>選擇 [SQL Server 選項雲端: (PaaS) 的 Azure SQL 資料庫或 SQL Server Azure Vm (IaaS) 上

Azure 具有裝載 Microsoft Azure 中的 SQL Server 工作負載的兩個選項︰

* [Azure SQL 資料庫](https://azure.microsoft.com/services/sql-database/)︰ SQL 資料庫的原生至雲端，也稱為的平台服務 (PaaS) 資料庫或服務 (DBaaS) 軟體為-的-服務 (SaaS) 應用程式開發最佳化的資料庫。 提供與大部分的 SQL Server 功能相容性]。 如需有關 PaaS 的詳細資訊，請參閱[什麼是 PaaS](https://azure.microsoft.com/overview/what-is-paas/)。
* [SQL Server Azure 虛擬機器上的商務連絡人](https://azure.microsoft.com/services/virtual-machines/sql-server/)︰ 安裝 SQL Server，並裝載在雲端上 Windows Server 虛擬機器 (Vm) Azure，也就是以服務 (IaaS) 基礎結構上執行。
SQL Server Azure 虛擬機器上最適合移轉現有的 SQL Server 應用程式。 所有版本和版本的 SQL Server 都可供使用。 提供與 SQL Server，讓您裝載任何數量的資料庫，為所需和執行跨資料庫交易 100%相容性]。 提供在 SQL Server 和 Windows 上的 「 完全控制。

瞭解如何的每個選項都放入 Microsoft 資料平台，並取得符合您的業務需求 [右側] 選項的說明。 無論您排列優先順序開支或其他項目之前的最小管理，本文可協助您決定哪種方法提供對您最重要的業務需求。


## <a name="microsofts-data-platform"></a>Microsoft 的資料平台

若要瞭解 Azure 與內部部署的 SQL Server 資料庫的任何討論區中第一個項目是，您可以使用它。 Microsoft 的資料平台運用 SQL Server 技術，並可在實體的內部部署電腦、 私人雲端環境、 協力廠商主控私人雲端環境和公用雲端。 SQL Server Azure 虛擬 mchines 上可讓您透過組合內部部署和雲端裝載的部署，這些環境使用相同的伺服器產品、 開發工具與專業知識的一組時的唯一的各種企業需求。

   ![雲端 SQL Server 選項︰ 雲端中的 SQL server IaaS，或 SaaS SQL 資料庫。](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

如圖所示，各可以特性的層級管理必須在基礎結構 （在 X 座標軸），及資料庫層級的彙總和自動化 （在 Y 軸） 來達成的成本效率的程度。

應用程式時，會提供主機服務應用程式的 SQL Server 部分 [四種基本的選項︰

- 非虛擬化實際的電腦上的 SQL Server
- 內部部署的 SQL Server 虛擬化機器 （私人雲端）
- SQL Server 中 Azure 虛擬機器 （Microsoft 公用雲端）
- Azure SQL 資料庫 （Microsoft 公用雲端）

在下列區段中，您深入瞭解 Microsoft 公用雲端中的 SQL Server: Azure SQL 資料庫及 SQL Server Azure Vm 上。 此外，您探索一般商務動力判斷哪一個選項最適合您的應用程式。

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>深入瞭解 Azure SQL 資料庫及 SQL Server Azure Vm 上

**Azure SQL 資料庫**是關聯式資料庫-為-的-服務 (DBaaS) 裝載於落在*軟體-為-的-服務 (SaaS)*與*平台-為-的-服務 (PaaS)*產業類別 Azure 雲端。 [SQL 資料庫](sql-database-technical-overview.md)是內建在標準化的硬體與軟體擁有、 裝載，且由 Microsoft 維護。 使用 SQL 資料庫]，您可以直接在使用內建功能的服務上開發。 使用時，SQL 資料庫]，您 pay-as-you-go 的出縮放為更大的電源，不中斷的選項。

**SQL Server 上 Azure 虛擬機器 (Vm)**落在產業類別*基礎結構為-的-服務 (IaaS)* ，並可讓您在雲端的虛擬機器中執行 SQL Server。 類似於 SQL 資料庫，它是內建在標準的硬體所擁有、 裝載，及維護 microsoft。 VM 在使用 SQL Server，您可以有支付-為您行動的 SQL Server 圖像中已包含 SQL Server 授權或輕鬆地使用現有的授權。 您可以輕鬆地也比例向上/向下] 和 [暫停/恢復 VM 視。

一般而言，這兩個 SQL 選項適用於不同的用途︰

- 以減少佈建和管理許多資料庫的最小的整體成本最佳化**SQL 資料庫**。 它會減少進行中的管理成本，因為您沒有任何虛擬機器、 作業系統或資料庫軟體管理。 您沒有管理升級、 高的顯示狀態或[備份](sql-database-automated-backups.md)。 一般而言，Azure SQL 資料庫可以大幅增加使用單一的受管理的資料庫的數字 IT 或開發資源。
- 針對移轉 Azure 現有的應用程式，或擴充至雲端混合式部署中現有的內部部署應用程式，而最佳化**SQL Server 執行 Azure Vm** 。 此外，您可以使用虛擬機器中的 SQL Server，開發及測試傳統的 SQL Server 應用程式。 Azure Vm 在 SQL Server，就可以使用完整系統管理權限專用的 SQL Server 執行個體和雲端 VM。 當組織已有若要維持虛擬機器 IT 資源時，則合適的選項。 這些功能可讓您建立高度自訂，處理您的應用程式特定的效能及可用性需求系統。

下表摘要列出 SQL 資料庫和 SQL Server Azure Vm 上的主要特性︰

|       | SQL 資料庫 | Azure 虛擬機器中的 SQL Server|
| -------------- | ------------ | ---------------------- |
| **最適合用於的︰** | 新雲端設計的應用程式開發和行銷時間限制。 |需要快速移轉至雲端的最小變更現有應用程式。 快速開發和測試案例當您不想要購買的內部部署非生產 SQL Server 硬體。 |
|| 需要資料庫的內建的可用性、 損毀修復和升級的小組。 |小組，可以設定及管理高可用性、 損毀修復，及修補 SQL Server。 自動化的功能大幅簡化此提供部分。 |
||小組，不想要管理的基本作業系統及設定的設定。| 如果您需要自訂的環境的完整系統管理權限。|
||資料庫的 1 TB 或更大的資料庫可[水平或垂直分割](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling)使用擴充模式。|SQL Server 執行個體最多 64 TB 的儲存空間。 執行個體可支援所需的資料庫。 |
||[建置軟體為-的-服務 (SaaS) 應用程式](sql-database-design-patterns-multi-tenancy-saas-applications.md)。| 移轉和建置企業版和混合式部署的應用程式。|
|||||
|**資源︰**|您不想要採用 IT 資源設定及管理的基礎結構，但想要著重於應用程式層級。|您有一些 IT 的資源設定及管理。 自動化的功能大幅簡化此提供部分。|
|**取得成本︰**|排除硬體成本，並減少系統管理成本。|排除硬體成本。|
|**業務連續性︰**|內建故障了基礎結構功能，除了 Azure SQL 資料庫會提供功能，例如[自動的備份](sql-database-automated-backups.md)、[還原時間點](sql-database-recovery-using-backups.md#point-in-time-restore)、[地理還原](sql-database-recovery-using-backups.md#geo-restore)及增加業務連續性[作用中的地理複寫](sql-database-geo-replication-overview.md)。 如需詳細資訊，請參閱[SQL 資料庫 business 連續性概觀](sql-database-business-continuity.md)。|SQL Server Azure Vm 在可讓您設定您的資料庫特定需求的高可用性和損毀修復解決方案。 因此，您可以有高度最佳化的系統應用程式。 您可以測試並自己需要時執行容錯移轉。 如需詳細資訊，請參閱[可用性和損毀修復的 SQL Server Azure 虛擬機器上的商務連絡人](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md)。|
|**混合式雲端︰**|您的內部部署應用程式可以存取 Azure SQL 資料庫中的資料。|使用 SQL Server Azure Vm 上，您可以讓應用程式，只能在雲端中執行，只能在內部部署。 例如，您可以透過[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)雲端擴充您內部網路和 Active Directory 網域。 此外，您可以將內部部署資料檔案儲存在使用[SQL Server 資料檔案中 Azure](http://msdn.microsoft.com/library/dn385720.aspx)Azure 儲存體。 如需詳細資訊，請參閱[SQL Server 2014 混合式雲端簡介](http://msdn.microsoft.com/library/dn606154.aspx)。|
||支援[SQL Server 交易複寫](https://msdn.microsoft.com/library/mt589530.aspx)為訂閱者將資料。|完全支援[SQL Server 交易複寫](https://msdn.microsoft.com/library/mt589530.aspx)、 [AlwaysOn 可用性群組](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md)、 整合服務及記錄傳送至複製資料。 此外，完全支援傳統的 SQL Server 備份|
|||||
|||||

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>選擇 Azure SQL 資料庫或 SQL Server Azure Vm 上的商務升級

### <a name="cost"></a>成本

無論您是現金流量，或在緊密預算限制的運作方式建立公司小組 strapped 啟動，有限資金，通常會主要驅動程式決定如何主控您的資料庫時。 在此區段中，您瞭解帳單和授權有關這兩個關聯式資料庫選項 Azure 中的基本概念︰ SQL 資料庫及 SQL Server Azure Vm 上。 您也瞭解計算合計的應用程式的成本。

#### <a name="billing-and-licensing-basics"></a>計費與授權的基本概念

**SQL 資料庫**是以服務，而不能搭配授權銷售為 [客戶]。  [SQL Server Azure Vm 上](../virtual-machines/virtual-machines-windows-sql-server-iaas-overview.md)是銷售與包含的授權您使用的每個分鐘。 如果您有現有的授權，您也可以使用它。  

目前， **SQL 資料庫**有多個服務層級，這些都根據服務層固定速率每小時計費和您所選擇的效能層級。 此外，是向您收費的外寄的網際網路流量在一般[資料傳輸速度](https://azure.microsoft.com/pricing/details/data-transfers/)。 基本、 標準和進階版服務層級的設計是預期的效能，以符合您的應用程式的最大使用量需求的多個效能層級。 您可以變更之間服務層和效能層級，以符合您的應用程式的分色處理量需求。 如果資料庫有大量的交易和支援許多使用者同時的需求，我們建議您進階版服務層級。 目前的支援的服務層上最新的資訊，請參閱[Azure SQL 資料庫服務層](sql-database-service-tiers.md)。 您也可以建立[彈性的資料庫資料庫](sql-database-elastic-pool.md)至效能間共用資源資料庫執行個體。

**SQL 資料庫**]，資料庫軟體，會自動設定、 修正的項目，與 microsoft，這樣您管理成本升級。 此外，[內建的備份](sql-database-automated-backups.md)功能可協助您節省大量成本，尤其是當您有大量的資料庫時。

與**SQL Server Azure Vm 上**，您可以使用任何的平台提供 SQL Server 圖像 （包括授權），或將您的 SQL Server 授權。 所有支援的 SQL Server 版本 (2008R2，2012年、 2014年 2016年)，可以使用版本 （開發人員快速、 網頁、 標準、 企業版）。 此外，提到您-擁有的授權版本 (BYOL) 的圖像可供使用。 使用 Azure 提供圖像，操作的成本會依虛擬記憶體大小和您所選擇的 SQL Server 版本而定。 虛擬記憶體大小或 SQL Server 版本，無論您是付款每分鐘授權的成本 SQL Server 和 Windows Server，以及 Azure 儲存空間的成本 VM 磁碟。 每個分鐘付款選項可讓您使用的 SQL Server，只要購買加法 SQL Server 不需要的授權。 如果您將您自己的 SQL Server 授權 Azure，您會 Windows Server] 及 [僅限儲存空間成本。 如需有關如何將-您-擁有授權的詳細資訊，請參閱[透過 Azure 上軟體保證授權行動性](https://azure.microsoft.com/pricing/license-mobility/)。

#### <a name="calculating-the-total-application-cost"></a>計算合計的應用程式的成本

當您開始使用雲端平台時，請執行您的應用程式的成本會包含開發和管理成本，以及公用雲端平台服務成本。

以下是 Azure Vm 上執行 SQL 資料庫和 SQL Server 中的應用程式的詳細的成本的計算︰

**使用 Azure SQL 資料庫︰**

*總成本的應用程式 = 高度最小化的管理成本 + 軟體開發成本 + SQL 資料庫服務成本*

**使用 SQL Server Azure Vm 上︰**

*總成本的應用程式 = 高度最小化的軟體開發成本 + 管理成本 + SQL Server 和 Windows Server 授權的成本 + Azure 儲存空間的成本*

如需有關價格的詳細資訊，請參閱下列資源︰

- [SQL 資料庫價格](https://azure.microsoft.com/pricing/details/sql-database/)
- [虛擬機器價格](https://azure.microsoft.com/pricing/details/virtual-machines/) [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql)和[Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Azure 價格計算機](https://azure.microsoft.com/pricing/calculator/)

> [AZURE.NOTE] 有不適用或無法使用 SQL 資料庫的 SQL Server 功能的一小部分。 如需詳細資訊，請參閱[SQL 資料庫一般限制、 指導方針](sql-database-general-limitations.md)和[SQL 資料庫 TRANSACT-SQL 資訊](sql-database-transact-sql-information.md)。 如果您要移動現有的 SQL Server 方案到雲端後，請參閱[移轉到 Azure SQL 資料庫的 SQL Server 資料庫](sql-database-cloud-migrate.md)。 當您移轉現有的內部部署 SQL Server 應用程式到 SQL 資料庫時，請考慮更新應用程式，以善用 cloud services 提供的功能。 例如，您可以考慮使用[Azure Web 應用程式服務](https://azure.microsoft.com/services/app-service/web/)或[Azure 雲端服務](https://azure.microsoft.com/services/cloud-services/)來主控您的應用程式層級，若要增加成本優點。

### <a name="administration"></a>管理

許多企業，切換到雲端服務的決策是像更相關卸載複雜度所管理的成本。 **SQL 資料庫**]，使用 Microsoft 管理基礎硬體。 Microsoft 自動複製所有資料，提供高可用性、 設定與升級資料庫軟體、 管理負載平衡和伺服器失敗時，會變透明的容錯移轉。 您可以繼續管理您的資料庫，但您不再需要管理資料庫引擎、 伺服器作業系統或硬體。  您可以繼續管理的項目範例包括資料庫並登入、 索引和查詢微調及稽核與安全性。

與**SQL Server Azure Vm 上**，您有完全控制權的作業系統和 SQL Server 執行個體設定。 使用 VM，是由您來決定何時更新或升級的作業系統和資料庫軟體和安裝其他例如防毒軟體。 大幅簡化修補、 備份和高可用性提供一些自動化的功能。 此外，您可以控制 VM、 磁碟數目及他們存放裝置設定的大小。 Azure 可讓您可以視需要變更 VM 的大小。 詳細資訊，請參閱[虛擬機器和 Azure 雲端服務的大小](../virtual-machines/virtual-machines-linux-sizes.md)。 

### <a name="service-level-agreement-sla"></a>服務等級協定 (SLA)

許多的 IT 部門會議時間保密義務服務等級協定 (SLA) 的上方的優先順序。 在此區段中，我們看看 SLA 套用至每個資料庫裝載選項。

**SQL 資料庫**基本、 標準和進階版服務層 Microsoft 提供可用性 SLA 99.99%。 最新的資訊，請參閱[服務等級協定](https://azure.microsoft.com/support/legal/sla/sql-database/)。 SQL 資料庫服務層和支援的業務連續性方案在最新的資訊，請參閱[服務層](sql-database-service-tiers.md)。

**Azure Vm 上執行的 SQL Server**，Microsoft 提供 SLA 涵蓋虛擬機器 99.95%的可用性。 此 SLA 並未涵蓋 VM 上執行的程序 （例如 SQL Server)，並要求您主控可用性集中至少兩個 VM 執行個體。 最新的資訊，請參閱[VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)。 資料庫高可用性 (HA) Vm 內，您應該在 SQL Server，例如[AlwaysOn 可用性群組](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx)設定其中一個支援的可用性選項。 使用支援的可用性選項不提供其他 SLA，但可以讓您達到 > 99.99%資料庫可用性。

### <a name="market"></a>上市時間

**SQL 資料庫**是正確的方案雲端設計應用程式開發人員生產力並快速時間上市時要徑。 以程式設計方式 DBA 類似的功能，其所完美雲端架構設計和開發人員，降低需要管理基礎的作業系統和資料庫。 例如，您可以使用的[REST API](http://msdn.microsoft.com/library/azure/dn505719.aspx)及[PowerShell Cmdlet](http://msdn.microsoft.com/library/azure/dn546726.aspx)自動化和管理系統管理作業數以千計的資料庫。 [彈性的資料庫集區](sql-database-elastic-pool.md)等功能可讓您著重於應用程式層和更快速上市發表您的方案。

**SQL Server Azure Vm 上執行**的是完美如果現有或新增應用程式需要大型資料庫、 互相關聯的資料庫或在 SQL Server] 或 [Windows 中的所有功能的存取權。 也很適合當您想要移轉現有內部部署應用程式與資料庫來為 Azure-是。 因為您不需要變更簡報、 應用程式及資料層級，您可以在重新您現有的解決方案架構節省時間與預算。 不過，您就能專注在移轉至 Azure 和執行 Azure 的平台所需的一些效能最佳化的所有方案。 如需詳細資訊，請參閱[效能的 SQL Server Azure 虛擬機器上的商務連絡人的最佳作法](../virtual-machines/virtual-machines-windows-sql-performance.md)。

## <a name="summary"></a>摘要

本文探索 SQL 資料庫和 SQL Server 上 Azure 虛擬機器 (Vm)，並討論一般商務動力可能會影響由您來決定。 建議您的摘要如下︰

如果，請選擇**Azure SQL 資料庫**︰

- 您要建立新的雲端應用程式，以善用省下的成本及效能最佳化雲端服務，提供。 這種方法提供完整的管理雲端服務的優勢，可協助降低初始時間上市，並提供長期的成本最佳化。

- 您想要有 Microsoft 執行一般的管理作業，在您的資料庫，並要求資料庫的更強可用性 Sla。



如果，請選擇 [ **SQL Server Azure Vm 上**︰

- 您有現有的內部部署應用程式，您想要移轉或擴充至雲端，或如果您想要建立企業應用程式大於 1 TB。 這個方法提供 100 %sql 相容性、 大型資料庫容量、 SQL Server 和 Windows 的完全控制權的優點，而且安全通道內部部署。 這個方法最小化的開發和修改現有的應用程式的成本。

- 您有現有的 IT 資源，而且最後擁有修補、 備份和資料庫的可用性。 請注意的一些自動化的功能大幅簡化這些作業。 


## <a name="next-steps"></a>後續步驟
- 請參閱[SQL 資料庫教學課程︰ 使用 Azure 入口網站的分鐘建立 SQL 資料庫](sql-database-get-started.md)若要開始使用 SQL 資料庫。
- 請參閱 [SQL 資料庫價格] (https://azure.microsoft.com/pricing/details/sql-database/)。
- 請參閱瞭解如何開始使用 SQL Server Azure Vm 上[佈建 Azure 中的 SQL Server 虛擬機器](../virtual-machines/virtual-machines-windows-portal-sql-server-provision.md)。
- 請參閱[SQL Server Azure 虛擬機器上︰ 學習路徑](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/)。
