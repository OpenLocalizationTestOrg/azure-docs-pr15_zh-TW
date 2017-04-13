<properties
    pageTitle="SQL Server Azure 虛擬機器上概觀 |Microsoft Azure"
    description="進一步瞭解如何在 Azure 虛擬機器上執行完整的 SQL Server 版本。 取得所有的 SQL Server VM 圖像與相關的內容的直接連結。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/19/2016"
    ms.author="jroth"/>

# <a name="overview-of-sql-server-on-azure-virtual-machines"></a>SQL Server Azure 虛擬機器上的概觀

本主題會說明執行 SQL Server Azure 虛擬機器 (Vm) 上的選項，以及[入口網站的圖像連結](#option-1-create-a-sql-vm-with-per-minute-licensing)和[一般工作](#manage-your-sql-vm)的概觀。

>[AZURE.NOTE] 如果您已經熟悉 SQL Server，只是要瞭解如何將 SQL Server VM 部署，請參閱[佈建 Azure 入口網站中的 SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)。

## <a name="overview"></a>概觀
如果您是資料庫管理員或開發人員，Azure Vm 提供移動您的內部部署的 SQL Server 負載和雲端應用程式。 以下影片會提供 SQL Server Azure Vm 技術的概觀。

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

視訊包括以下區域︰

|時間|區域|
|---|---|
| 00:21 | 什麼是 Azure Vm？ |
| 01:45 | 安全性 |
| 02:50 | 連線 |
| 03:30 | 儲存可靠性與效能 |
| 05:20 | VM 大小 |
| 05:54 | 可用性及 SLA |
| 07:30 | 設定支援 |
| 08:00 | 監控 |
| 08:32 | 示範︰ 建立 SQL Server 2016 VM |

>[AZURE.NOTE] 視訊著重於 SQL Server 2016，但 Azure 提供許多版本的 SQL Server，包括 2008年、 2012年，於 2014 年，以及 2016 VM 圖像。 

## <a name="scenarios"></a>案例
有許多您可以選擇主控 Azure 中的資料的原因。 如果您的應用程式移至 Azure，它就會改善效能，也移動資料。 但還有其他優點。 您會自動全域的目前狀態與損毀復原多個資料中心有權存取。 資料也是高度安全及持續性。

SQL Server Azure Vm 上執行是 Azure 中儲存關聯式資料的其中一個選項。 很不錯的選擇多個案例。 例如，可能會想要設定 Azure VM 盡可能內部部署的 SQL Server 電腦同樣。 或您可能會想要執行相同的資料庫伺服器上的其他應用程式與服務。 有兩個主要的資源，可協助您思考更多的案例及考量︰

 - [SQL Server Azure 虛擬機器上](https://azure.microsoft.com/services/virtual-machines/sql-server/)提供最佳的案例 Azure Vm 在使用 SQL Server 的概觀。 
 - [選擇雲端 SQL Server] 選項: (PaaS) 的 Azure SQL 資料庫或 SQL Server Azure Vm (IaaS) 上](../sql-database/sql-database-paas-vs-sql-server-iaas.md)提供詳細的比較 SQL 資料庫與 SQL Server VM 上執行。

## <a name="create-a-new-sql-vm"></a>建立新的 SQL VM
下列各節會提供 Azure 入口網站的直接連結 SQL Server 虛擬機器庫圖像。 根據您所選取的圖像，您可以有支付的 SQL Server 授權成本為基礎每分鐘，或您就可以將您自己的授權 (BYOL)。

教學課程中，[佈建 Azure 入口網站中的 SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)中尋找這個程序的逐步指引。 此外，檢閱[效能的 SQL Server Vm 的最佳作法](virtual-machines-windows-sql-performance.md)，以及說明如何選取適當的電腦大小和可用期間佈建其他功能。

## <a name="option-1-create-a-sql-vm-with-per-minute-licensing"></a>選項 1︰ 建立 SQL VM 每分鐘授權
下表提供虛擬機器圖庫中提供的 SQL Server 圖像的矩陣。 按一下以開始建立新的 SQL VM 與您指定的版本、 版本與作業系統的任何連結。

|版本|作業系統|版本|
|---|---|---|
|**SQL 2016**|Windows Server 2012 R2|[企業](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMEnterpriseWindowsServer2012R2)、[標準](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMStandardWindowsServer2012R2)、[網頁](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMWebWindowsServer2012R2)、[開發](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMDeveloperWindowsServer2012R2)、 [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMExpressWindowsServer2012R2)|
|**SQL 2014 SP1**|Windows Server 2012 R2|[企業](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1EnterpriseWindowsServer2012R2)、[標準](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1StandardWindowsServer2012R2)、 [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1WebWindowsServer2012R2) [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1ExpressWindowsServer2012R2)|
|**SQL 2014 年**|Windows Server 2012 R2|[企業](https://portal.azure.com/#create/Microsoft.SQLServer2014EnterpriseWindowsServer2012R2)、[標準](https://portal.azure.com/#create/Microsoft.SQLServer2014StandardWindowsServer2012R2)、[網頁](https://portal.azure.com/#create/Microsoft.SQLServer2014WebWindowsServer2012R2)|
|**SQL 2012 SP3**|Windows Server 2012 R2|[企業](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2)、[標準](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2)、 [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2) [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012 R2|[企業](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012R2)、[標準](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012R2)、[網頁](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012|[企業](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012)、[標準](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012)、 [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012) [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2ExpressWindowsServer2012)|
|**SQL 2008 R2 SP3**|Windows Server 2008 R2|[企業](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2)、[標準](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2)、[網頁](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2)|
|**SQL 2008 R2 SP3**|Windows Server 2012|[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2012)|

## <a name="option-2-create-a-sql-vm-with-an-existing-license"></a>選項 2︰ 建立 SQL VM 與現有的授權
您也可以將您自己的授權 (BYOL)。 在此案例中，您只是付款 vm 不含任何額外的費用的授權 SQL Server。 若要使用您自己的授權，請使用 SQL Server 版本、 版本，和作業系統下列矩陣。 在入口網站，這些圖像名稱作為前置詞**{BYOL}**。

|版本|作業系統|版本|
|---|---|---|
|**SQL Server 2016**|Windows Server 2012 R2|[企業 BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2)，[標準 BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[企業 BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1EnterpriseWindowsServer2012R2)，[標準 BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1StandardWindowsServer2012R2)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[企業 BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2)，[標準 BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2)|

> [AZURE.IMPORTANT] 若要使用 BYOL VM 圖像，您必須與企業[授權行動性透過 Azure 上軟體保證](https://azure.microsoft.com/pricing/license-mobility/)合約。 您也需要有效的授權，您想要使用的 SQL Server 版本/版本。 您必須[提供必要的 BYOL 資訊至 Microsoft](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf)的佈建您 VM **10**天內。

## <a name="manage-your-sql-vm"></a>管理您的 SQL VM
佈建之後您 SQL Server VM，有幾個選用的管理工作。 在許多方面，您可以設定，並管理 SQL Server，完全等您想要管理內部部署的 SQL Server 執行個體。 不過，某些工作專屬於 Azure。 下列各節醒目提示的詳細資訊連結這些區域中，部分。

### <a name="connect-to-the-vm"></a>連線至 VM
其中一個最基本的管理步驟是連線到您 SQL Server VM，透過 [工具] 中，例如 SQL Server 管理 Studio (SSMS)。 如需如何連線至您新的 SQL Server VM 上的指示，請參閱[連線至 SQL Server 虛擬機器上 Azure](virtual-machines-windows-sql-connect.md)。

### <a name="migrate-your-data"></a>移轉您的資料
如果您有現有的資料庫時，您需要移到新提供的 SQL VM。 移轉選項和指示的清單，請參閱[移轉 Azure VM 上 SQL Server 資料庫](virtual-machines-windows-migrate-sql.md)。

### <a name="configure-high-availability"></a>設定高可用性
如果您需要高的顯示狀態，請考慮設定 SQL Server 可用性群組。 包括虛擬網路中的多個 Azure Vm。 Azure 入口網站提供的範本，這項設定，您所設定。 如需詳細資訊，請參閱[設定 Azure 資源管理員虛擬機器中 AlwaysOn 可用性群組](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)。 如果您想要手動設定您的可用性群組及相關聯的接聽，請參閱[Azure VM 中設定 AlwaysOn 可用性群組](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)。

其他可用性考量，請參閱[高可用性及損毀修復的 SQL Server Azure 虛擬機器中的商務連絡人](virtual-machines-windows-sql-high-availability-dr.md)。

### <a name="back-up-your-data"></a>備份您的資料
Azure Vm 可以利用[自動備份](virtual-machines-windows-sql-automated-backup.md)，定期建立備份的資料庫以 blob 儲存體。 您可以手動使用此技巧。 如需詳細資訊，請參閱[SQL Server 備份與還原使用 Azure 儲存體](virtual-machines-windows-use-storage-sql-server-backup-restore.md)。 如需所有的備份與還原選項的概觀，請參閱[備份與還原的 SQL Server Azure 虛擬機器中的商務連絡人](virtual-machines-windows-sql-backup-recovery.md)。

### <a name="automate-updates"></a>自動更新
Azure Vm 可以使用[自動修正](virtual-machines-windows-sql-automated-patching.md)排程安裝重要視窗進行的維修作業] 視窗與 SQL Server 會自動更新。

### <a name="customer-experience-improvement-program-ceip"></a>客戶經驗改進計畫 (CEIP)
依預設會啟用客戶經驗改進計畫 (CEIP)。 這會定期會傳送至 Microsoft，以協助改善 SQL Server 的報表。 沒有必要與 CEIP，除非您想要將它佈建之後停用的管理任務。 您可以自訂，或停用 CEIP 的 vm 與遠端桌面連線。 再執行**SQL Server 錯誤與使用情況報告**公用程式。 依照指示停用報告。 

如需詳細資訊，請參閱 CEIP 主題的一節[接受授權合約](https://msdn.microsoft.com/library/ms143343.aspx)。 

## <a name="next-steps"></a>後續步驟
SQL server Azure 虛擬機器上[探索學習路徑](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/)。

更多的問題嗎？ 首先，請參閱[SQL Server Azure 虛擬機器常見問題集上](virtual-machines-windows-sql-server-iaas-faq.md)。 但也將您的問題或註解新增至底部的 [使用 Microsoft 或社群任何 SQL VM 主題。
