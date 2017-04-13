<properties
    pageTitle="SQL Server Azure 虛擬機器常見問題集上的 |Microsoft Azure"
    description="本文提供有關執行 SQL Server Azure Vm 的常見問題集的回答。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="v-shysun"
    manager="felixwu"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/13/2016"
    ms.author="v-shysun"/>

# <a name="sql-server-on-azure-virtual-machines-faq"></a>SQL Server Azure 虛擬機器常見問題集

本主題提供一些最常見執行[SQL Server Azure 虛擬機器上的商務連絡人](https://azure.microsoft.com/services/virtual-machines/sql-server/)的相關問題的答案。

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="frequently-asked-questions"></a>常見問題集

1. **如何建立與 SQL Server Azure 虛擬機器？**

    有兩種方式執行此動作。 最簡單的方法是，建立虛擬機器包含 SQL Server。 在註冊 Azure 和建立 SQL VM 從入口網站上的教學課程，請參閱[佈建 Azure 入口網站中的 SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)。 您也可以手動 VM 上安裝 SQL Server，並重複使用[授權行動性透過 Azure 上軟體保證](https://azure.microsoft.com/pricing/license-mobility/)與內部部署授權的選項。

1. **什麼是 SQL Vm 與 SQL 資料庫服務之間的差異？**

    概念上，執行 SQL Server Azure 虛擬機器上不的不同執行遠端資料中心的 SQL Server。 相反地， [SQL 資料庫](../sql-database/sql-database-technical-overview.md)提供資料庫為-的-服務。 SQL 資料庫]，與您沒有主控您的資料庫的電腦存取。 如需完整的比較，請參閱[選擇雲端 SQL Server] 選項: (PaaS) 的 Azure SQL 資料庫或 SQL Server Azure Vm (IaaS) 上](../sql-database/sql-database-paas-vs-sql-server-iaas.md)。

1. **如何將我內部部署的 SQL Server 資料庫移轉至雲端**

    第一次建立 Azure 虛擬機器與 SQL Server 執行個體。 該執行個體，然後移轉內部部署資料庫。 資料移轉策略，請參閱[移轉到 SQL Server Azure VM 在 SQL Server 資料庫](virtual-machines-windows-migrate-sql.md)。

2. **我是否可以變更已安裝的功能或相同 VM 上安裝 SQL Server 的第二個執行個體？**

    [是]。 SQL Server 安裝媒體位於**C**磁碟機上的資料夾。 從該位置新增新的 SQL Server 執行個體，或變更其他已安裝的功能的 SQL Server 的電腦上執行**Setup.exe** 。

3. **如何升級至新的中的 SQL Server Azure VM 版本/版本？**

    目前，是 SQL server Azure VM 在執行任何就地升級。 使用所需的 SQL Server 版本/版本，建立新的 Azure 虛擬機器，然後將資料庫移轉至新的伺服器使用標準[資料移轉技巧](virtual-machines-windows-migrate-sql.md)。

4. **如何安裝我授權的版本的 SQL Server Azure VM 上？**

    將 SQL Server 安裝媒體複製到 Windows Server VM，然後再安裝 VM 上的 [SQL Server。 授權的原因，您必須[透過 Azure 上軟體保證授權行動性](https://azure.microsoft.com/pricing/license-mobility/)。

5. **您是否已支付的 VM SQL 成本，如果只待命/容錯移轉用？**

    如果您要建立 SQL VM 透過庫，您必須備用 SQL vm 另外取得授權然後價格是一樣的。 如果使用[授權行動性](https://azure.microsoft.com/pricing/license-mobility/)的虛擬機器中手動安裝 SQL Server 時，必須有一個免費被動式 SQL 執行個體容錯移轉選項。 請檢閱的限制和需求。

6. **套用的方式更新和 service pack 在 SQL Server VM？**

    虛擬機器讓您控制主機機器，包括何時及如何套用更新。 針對作業系統中，您可以手動套用 windows 更新，或者您可以啟用稱為[自動修正](virtual-machines-windows-classic-sql-automated-patching.md)排程服務。 自動化 Patching 安裝標示為重要事項，包括 SQL Server 更新該類別中的任何更新。 必須手動安裝 SQL Server 其他選用的更新。

7. **是否可以設定不會顯示 （適用於 Windows 2008 R2 範例 + SQL Server 2012） 的虛擬機器] 庫中的設定？**

    [否]。 虛擬機器庫包含影像，SQL Server，您必須選擇其中一個提供圖像。

9. **如何在我 Azure VM 安裝 SQL 資料工具？**

    下載並安裝 SQL 資料工具從[Microsoft SQL Server Data Tools for Visual Studio 2013 的商務智慧功能](https://www.microsoft.com/en-us/download/details.aspx?id=42313)。

## <a name="resources"></a>資源

SQL Server Azure 虛擬機器上的商務連絡人的概觀，您可以觀看[Azure VM 是 SQL Server 2016 最佳的平台](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016)的視訊。 您也可以在主題中， [SQL Server Azure 虛擬機器概觀上](virtual-machines-windows-sql-server-iaas-overview.md)取得很好的簡介。

其他資源包括︰

- [佈建 Azure 入口網站中的 SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)
- [SQL server Azure VM 上移轉資料庫](virtual-machines-windows-migrate-sql.md)
- [可用性及損毀修復 Azure 虛擬機器中的 SQL server](virtual-machines-windows-sql-high-availability-dr.md)
- [效能最佳作法 SQL Server Azure 虛擬機器中的商務連絡人](virtual-machines-windows-sql-performance.md)
- [應用程式模式和 Azure 虛擬機器中的 SQL Server 的開發策略](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)
