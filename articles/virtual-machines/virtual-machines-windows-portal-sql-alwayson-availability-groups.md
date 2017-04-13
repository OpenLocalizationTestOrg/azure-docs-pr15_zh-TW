<properties
    pageTitle="一律在可用性群組 Azure VM 在自動設定-資源管理員"
    description="建立 Azure 資源管理員模式中的 Azure 虛擬機器一直在可用性群組。 本教學課程主要使用使用者介面，若要自動建立整個方案。"
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/20/2016"
    ms.author="MikeRayMSFT" />

# <a name="configure-always-on-availability-group-in-azure-vm-automatically---resource-manager"></a>一律在可用性群組 Azure VM 在自動設定-資源管理員

> [AZURE.SELECTOR]
- [資源管理員︰ 範本](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
- [資源管理員︰ 手動](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
- [傳統︰ UI](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
- [傳統︰ PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)

<br/>

本端對端教學課程中會顯示如何使用 Azure 資源管理員虛擬機器中建立 SQL Server 可用性群組。 教學課程中使用 Azure 刀設定的範本。 您會檢閱預設設定，輸入所需的設定]，然後更新刀入口網站中的，為您逐步瞭解此教學課程。

在本教學課程結尾，SQL Server 可用性群組方案 Azure 中的會包含下列元素︰

- 包含多個子網路，包括前端與後端子網路虛擬網路

- 兩個網域控制站的 Active Directory (AD) 網域

- 兩個 SQL Server Vm 部署到後端的子網路及 AD 網域

- 3-節點 WSFC 叢集節點多數仲裁模型

- 使用兩個同步認可複本可用性資料庫的可用性群組

下圖是解決方案的圖形化表示。

![Azure 中 AG 測試實驗室架構](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

所有在本方案中的資源所屬到單一資源群組。

本教學課程假設下列動作︰

- 您已經有 Azure 帳戶。 如果您還沒有，[註冊試用帳戶](http://azure.microsoft.com/pricing/free-trial/)。

- 您已經知道如何佈建 SQL Server VM 從使用 GUI 虛擬機器圖庫。 如需詳細資訊，請參閱[佈建之 SQL Server Azure 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)

- 您已經有可用性群組深入的瞭解。 如需詳細資訊，請參閱[一直在可用性群組 (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx)。

>[AZURE.NOTE] 如果您想要使用 SharePoint 中的可用性群組，請參閱[設定 SQL Server 2012 一直在可用性群組的 SharePoint 2013](http://technet.microsoft.com/library/jj715261.aspx)。

在本教學課程中，您會使用 Azure 入口網站︰

- 選取 [永遠在範本] 入口網站

- 檢視範本的設定，然後更新您的環境的幾個組態設定

- 監控 Azure，為它建立整個環境

- 連線到其中一個網域，然後到其中一個 SQL Server

[AZURE.INCLUDE [availability-group-template](../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]


## <a name="provision-the-cluster-from-the-gallery"></a>佈建叢集從圖庫

Azure 提供整個方案庫。 若要找出範本︰

1.  Azure 入口網站使用您的帳戶登入。
1.  在 [Azure 入口網站，按一下**+ 新增。** 入口網站隨即會開啟新的刀。
1.  **AlwaysOn**新刀搜尋。
![尋找 AlwaysOn 範本](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
1.  搜尋結果中找出**SQL Server AlwaysOn 叢集**。
![AlwaysOn 範本](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
1.  在**選取的部署模型**選擇 [**資源管理員**]。

### <a name="basics"></a>基本概念

按一下 [在**基本功能**，並設定下列項目︰

- **管理員的使用者名稱**是兩個執行個體的 SQL Server 上的 [網域管理員權限與 SQL Server 系統管理員固定的伺服器角色的成員的使用者帳戶。 在此教學課程中，使用**DomainAdmin**。

- **密碼**是網域系統管理員帳戶的密碼。 使用複雜的密碼。 確認密碼。

- **訂閱**是 Azure 會執行的所有資源可用性群組部署都結算的訂閱。 如果您的帳戶有多個訂閱，您可以指定不同的訂閱。

- **資源群組**是將所屬的所有 Azure 資源本教學課程中所建立的群組名稱。 在此教學課程中，使用**SQL-HA-路由**。 如需詳細資訊，請參閱 （Azure 資源管理員概觀） [資源-群組 overview.md / # 資源的群組]。

- **位置**會是此教學課程中的資源將會建立 Azure 區域。 選取 [裝載基礎結構的 Azure 區域]。

以下是**基本概念**刀的外觀︰

![基本概念](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

- 按一下**[確定]**。

### <a name="domain-and-network-settings"></a>網域和網路的設定

此 Azure 庫範本建立新的網域與新的網域控制站。 也會建立新的網路和兩個子網路。 範本不會啟用建立現有的網域或虛擬網路中的伺服器。 下一步是設定的網域和網路的設定。

在**網域和網路設定**刀檢閱網域和網路設定的預設的值︰

- **樹系根網域名稱**是用於 AD 網域主控叢集的網域名稱。 教學課程中，使用**contoso.com**。

- **虛擬網路名稱**是 Azure 虛擬網路的網路名稱。 在此教學課程中，使用**autohaVNET**。

- **網域控制站子網路名稱**是部分的主控的網域控制站的虛擬網路的名稱。 在此教學課程中，使用**子網路-1**。 子網路會使用地址前置詞**10.0.0.0/24**。

- **SQL Server 子網路名稱**是虛擬網路主機 SQL Server 與檔案共用見證部分的名稱。 在此教學課程中，使用**子網路 2**。 子網路會使用地址前置詞**10.0.1.0/26**。

若要進一步瞭解虛擬網路[Azure，請參閱 「 虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。  

**網域和網路設定**看起來應該像這樣︰

![網域和網路的設定](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

如有需要，您可能會變更這些值。 在此教學課程中，我們使用預設的值。

- 檢閱設定，然後按一下**[確定]**。

###<a name="availability-group-settings"></a>可用群組設定

在**可用群組設定**檢閱可用性群組和接聽程式的預設的值。

- **可用性群組名稱**是 [可用性] 群組中的直的資源名稱。 在此教學課程中，使用**Contoso ag**。

- **可用群組接聽名稱**會使用叢集和內部負載平衡器。 連線至 SQL Server 的用戶端可以使用此名稱，以連線至資料庫的適當的複本。 在此教學課程中，使用**Contoso 接聽程式**。

-  **可用群組接聽連接埠**指定的 TCP 連接埠的 SQL Server 接聽程式會使用。 本教學課程中使用的預設連接埠， **1433年**。

如有需要，您可能會變更這些值。 本教學課程中使用的預設的值。  

![可用群組設定](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

- 按一下**[確定]**。

###<a name="vm-size-storage-settings"></a>虛擬記憶體大小，儲存設定

在**虛擬記憶體大小]、 [儲存設定**選擇的 SQL Server 虛擬機器大小並檢閱其他設定。

- **SQL Server 虛擬機器大小**介於兩個 SQL Server Azure 虛擬機器大小。 選擇適合您的工作量虛擬機器大小。 如果您要建立教學課程此環境使用**DS2**。 生產負載選擇可以支援工作負載的虛擬機器大小。 許多生產工作負載會要求**DS4**或更大。 範本會建立兩個虛擬機器的大小，並在每一個安裝 SQL Server。 如需詳細資訊，請參閱[虛擬機器的大小](virtual-machines-linux-sizes.md)。

>[AZURE.NOTE]Azure 將會安裝 SQL Server 企業版。 成本取決於年度] 和 [虛擬機器大小。 如需詳細瞭解目前的成本的詳細資訊，請參閱[虛擬機器價格](http://azure.microsoft.com/pricing/details/virtual-machines/#Sql)。

- **網域控制站的虛擬機器大小**為網域控制站的虛擬機器大小。 在此教學課程中，使用**D2**。

- **檔案共用見證虛擬機器大小**的檔案共用見證的虛擬機器大小。 在此教學課程中，使用**A1**。

- **SQL 儲存帳戶**是按住作業系統磁碟與 SQL Server 資料的儲存空間帳戶的名稱。 在此教學課程中，使用**alwaysonsql01**。

- **DC 儲存帳戶**是網域控制站的儲存空間帳戶的名稱。 在此教學課程中，使用**alwaysondc01**。

- TB 中的 [ **SQL Server 資料磁碟大小**是 SQL Server 資料磁碟 TB 的大小。 指定 1 到 4。 這是將附加至每個 SQL Server 資料磁碟的大小。 本教學課程中使用 「 **1**」。

- **儲存最佳化**設定工作量類型為根據的 SQL Server 虛擬機器特定的儲存空間設定的設定。 在這個案例中所有的 SQL Server 會使用設為唯讀 Azure 磁碟主機快取中的進階版儲存空間。 此外，您可以將工作負載的 SQL Server 設定最佳化選擇其中一個三個設定︰

    - **一般的工作量**設定任何特定設定的設定

    - **交易處理**設定追蹤旗標 1117年和 1118

    - **資料倉儲**設定追蹤旗標 1117年和 610

在此教學課程中，使用**一般的工作量**。

![VM 大小儲存設定](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

- 檢閱設定，然後按一下**[確定]**。

####<a name="a-note-about-storage"></a>存放裝置的相關附註

其他最佳化取決於 SQL Server 資料磁碟的大小。 針對資料磁碟的每個 tb，Azure 新增額外的 1 TB 進階版儲存 (SSD)。 當伺服器需要 2 TB 以上時，範本會在每一個 SQL Server 上建立儲存集區。 儲存集區是一種存放虛擬化多張光碟都能提供更高的容量、 恢復功能，以及效能。  範本然後儲存集區上建立儲存空間，並提供此為單一資料作業系統。 範本會用於 SQL Server，將這個磁碟指定為資料磁碟。 範本會調整儲存集區的 SQL Server 使用下列設定︰

- 等量大小是虛擬磁碟的間隔設定。 交易工作負載的這是設定為 64 KB。 倉儲工作負載的資料的設定，則 256 KB。

- 恢復功能很簡單 （沒有恢復功能）。

>[AZURE.NOTE] Azure 進階版儲存本機重複，而單一的區域] 中保持三份資料，以便在儲存集區的其他恢復功能，則不需要。

- 欄數等於儲存集區中的磁碟數目。

如需瞭解儲存空間的詳細資訊請參閱空間和儲存集區︰

- [儲存空間的概觀](http://technet.microsoft.com/library/hh831739.aspx)。

- [Windows 伺服器備份和儲存集區](http://technet.microsoft.com/library/dn390929.aspx)

如需有關 SQL Server 組態的最佳作法的詳細資訊，請參閱[Azure 虛擬機器中的 SQL Server 的效能最佳作法](virtual-machines-windows-sql-performance.md)


###<a name="sql-server-settings"></a>SQL Server 設定

在**SQL Server 設定**檢閱及修改 SQL Server VM 名稱前置詞、 SQL Server 版本、 SQL Server 服務帳戶及密碼和 SQL 自動修正進行的維修作業排程。

- **SQL Server 名稱前置詞**用來建立每一個 SQL Server 的名稱。 在此教學課程中，使用**Contoso ag**。 SQL Server 名稱會*Contoso-ag-0*到*Contoso-ag-1*。

- **SQL Server 版本**是版本的 SQL Server。 在此教學課程中，使用**SQL Server 2014**。 您也可以選擇**SQL Server 2012**或**SQL Server 2016**。

- **SQL Server 服務帳戶使用者名稱**是 SQL Server 服務的網域帳戶名稱。 在此教學課程中，使用**sqlservice**。

- **密碼**是 SQL Server 服務帳戶的密碼。  使用複雜的密碼。 確認密碼。

- **SQL 自動修正進行的維修作業排程**識別 Azure 會自動更新 SQL Server 的工作日。 在此教學課程中，輸入**星期日**。

- **SQL 自動修正進行的維修作業開始小時**是 Azure 區域的一天的時間時自動修正會開始。

>[AZURE.NOTE]每個 VM 的修補視窗是由一小時交錯。 若要避免中斷的服務，只有一個虛擬機器被修補一次。

![SQL Server 設定](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

檢閱設定，然後按一下**[確定]**。

###<a name="summary"></a>摘要

在 [摘要] 頁面上 Azure 驗證設定。 您也可以下載範本。 檢閱摘要。 按一下**[確定]**。

###<a name="buy"></a>購買

此最終刀包含**使用條款**和**隱私權聲明**。 請檢閱這項資訊。 當您準備好進行 Azure 開始建立虛擬機器，及所有其他必要資源可用性群組時，按一下 [**建立**]。

資源群組及所有的資源，將會建立 Azure 入口網站。

##<a name="monitor-deployment"></a>監視器部署

監控從 Azure 入口網站的部署進度。 圖示代表部署自動已釘選至 Azure 入口網站的儀表板。

![Azure 儀表板](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

##<a name="connect-to-sql-server"></a>連線至 SQL Server

在沒有網際網路連線的虛擬機器上執行新的 SQL Server 執行個體。 不過，網域控制站沒有網際網路連線的圖示。 若要連線至 SQL server，與遠端桌面，其中一個網域控制站的第一個 RDP。 從來源之網域控制站開啟第二個 RDP 到 SQL Server。

主要網域控制站 RDP，請執行下列步驟︰

1.  從 Azure 入口網站儀表板非常已成功部署。

1.  按一下 [**資源**]。

1.  在 [**資源**刀中，按一下 [ **ad 主要 dc**是主要網域控制站的虛擬機器的電腦名稱]。

1.  按一下 [為**ad 主要 dc**刀的 [**連線**]。 在瀏覽器會詢問您是否要開啟或儲存遠端連接物件。 按一下 [**開啟**]。
![連線到 DC](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/13-ad-primary-dc-connect.png)
1.  **遠端桌面連線**可能警告您找不到此遠端連線的發行者。 按一下 [**連線**]。

1.  Windows 安全性會提示您輸入您的認證以連線至的主要網域控制站的 IP 位址。 按一下 [**使用另一個帳戶**]。 輸入**使用者名稱** **contoso\DomainAdmin**。 這是您所選擇的系統管理員使用者名稱的帳戶。 使用複雜您選擇設定範本時的密碼。

1.  **遠端桌面**可能對您提出警告的遠端電腦無法驗證安全性憑證問題。 隨即會顯示您的安全性憑證的名稱。 如果您已追蹤教學課程名稱會變成**ad-主要-dc.contoso.com**。 按一下**[是]**。

您現在已經連線到的主要網域控制站。 RDP 到 SQL Server，請執行下列步驟︰

1.  在網域控制站，開啟 [**遠端桌面連線**。

1.  **電腦**中，輸入其中一個 SQL 伺服器的名稱。 在此教學課程中，輸入**sqlserver 0**。

1.  使用相同的使用者帳戶和用於 RDP 網域控制站的密碼。

您現在已經連線到 SQL Server RDP 使用。 您可以開啟 SQL Server management studio 中、 連線至預設的執行個體的 SQL Server，並確認 availabilty 群組設定。


