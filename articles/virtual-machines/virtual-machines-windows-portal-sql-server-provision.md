<properties
    pageTitle="佈建 SQL Server 虛擬機器 |Microsoft Azure"
    description="建立及連線至 SQL Server 中使用入口網站的 Azure 虛擬機器。 本教學課程中使用資源管理員模式。"
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    editor=""
    manager="jhubbard"
    tags="azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/21/2016"
    ms.author="jroth" />

# <a name="provision-a-sql-server-virtual-machine-in-the-azure-portal"></a>佈建 Azure 入口網站中的 SQL Server 虛擬機器

> [AZURE.SELECTOR]
- [入口網站](virtual-machines-windows-portal-sql-server-provision.md)
- [PowerShell](virtual-machines-windows-ps-sql-create.md)

本端對端教學課程中會顯示如何佈建虛擬機器執行 SQL Server 使用 Azure 入口網站。

Azure 虛擬機器 (VM) 庫包含包含 Microsoft SQL Server 的數個圖像。 按幾下滑鼠，您可以從圖庫選取其中一個 SQL VM 影像，並提供其 Azure 環境中。

在本教學課程中，您將會︰

- [從圖庫中選取的 SQL VM 圖像](#select-a-sql-vm-image-from-the-gallery)
- [設定並建立 VM](#configure-the-vm)
- [使用遠端桌面開啟 VM](#open-the-vm-with-remote-desktop)
- [遠端連線至 SQL Server](#connect-to-sql-server-remotely)

## <a name="select-a-sql-vm-image-from-the-gallery"></a>從圖庫中選取的 SQL VM 圖像

1. [Azure 入口網站](https://portal.azure.com)使用您的帳戶登入。

    >[AZURE.NOTE] 如果您沒有 Azure 帳戶，請造訪[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。

1. 在 Azure 入口網站中，按一下 [**新增]**。 入口網站開啟**新增**刀。 SQL Server VM 資源位於服務商場的**虛擬機器**群組。

1. 在 [**新增**刀中，按一下 [**虛擬機器**]。

1. 若要查看所有可用的圖像，按一下 [**查看所有****虛擬機器**刀上。

    ![Azure 虛擬機器刀](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-blade.png)

1. 在**資料庫伺服器**] 底下按一下 [ **SQL Server**。 您可能需要向下捲動來尋找**資料庫伺服器**。 檢視可用的 SQL Server 範本。

    ![虛擬機器庫 SQL 圖像](./media/virtual-machines-windows-portal-sql-server-provision/virtual-machine-gallery-sql-server.png)

1. 每個範本識別的 SQL Server 版本，以及作業系統。 從清單中選取其中一個這些圖像。 檢閱說明虛擬機器圖像的詳細資料刀。

    >[AZURE.NOTE] SQL VM 圖像的 SQL Server 包含授權的成本，將您建立的 VM 每分鐘價格。 有另一個選項可讓您-擁有的授權 (BYOL) 和支付，僅適用於 VM。 這些圖像的名稱會加上 {BYOL}。 如需此選項的詳細資訊，請參閱[開始使用 SQL Server Azure 虛擬機器上的商務連絡人](virtual-machines-windows-sql-server-iaas-overview.md)。

1. 在 [**選取部署模型**，確認已選取 [**資源管理員**。 資源管理員是新的虛擬機器建議的部署模型。 按一下 [**建立**]。

    ![建立 SQL VM 與資源管理員](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-sql-deployment-model.png)

## <a name="configure-the-vm"></a>設定 VM
有五個刀設定 SQL Server 虛擬機器。

| 步驟               | 描述                          |
|---------------------|-------------------------------|
| **基本概念**              | [基本設定](#1-configure-basic-settings)      |
| **大小**                | [選擇 [虛擬機器大小](#2-choose-virtual-machine-size)   |
| **設定**            | [設定選用的功能](#3-configure-optional-features)   |
| **SQL Server 設定** | [SQL server 設定](#4-configure-sql-server-settings) |
| **摘要**             | [檢閱摘要](#5-review-the-summary)            |

## <a name="1-configure-basic-settings"></a>1.設定基本設定
在**基本概念**刀，提供下列資訊︰

* 輸入唯一的虛擬機器**名稱**。
* 指定 VM 上的本機系統管理員帳戶的**使用者名稱**。 此帳戶也會新增至 SQL Server**系統管理員**固定的伺服器角色。
* 提供強式**密碼**。
* 如果您有多個訂閱，請確認新 vm 正確訂閱。
* 在 [**資源群組**] 方塊中，輸入新的資源群組的名稱。 或者，若要使用現有的資源] 群組中按一下 [**選取現有的**。 資源群組是 Azure （虛擬機器、 儲存帳戶、 虛擬網路等） 中的相關資源的集合。

    >[AZURE.NOTE] 如果您只要測試或瞭解 Azure 中的 SQL Server 部署，使用新的資源群組是很有幫助的。 測試您完成之後，刪除自動刪除 [VM 與該資源群組相關聯的所有資源的 [資源] 群組。 如需有關資源群組的詳細資訊，請參閱[Azure 資源管理員的概觀](../azure-resource-manager/resource-group-overview.md)。

* 選取此部署的**位置**。
* 按一下**[確定**] 儲存設定。

    ![SQL 基本概念刀](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-basic.png)

## <a name="2-choose-virtual-machine-size"></a>2.選擇虛擬機器大小
在 [**大小**] 步驟中，選擇虛擬機器中大小**選擇大小**刀。 刀一開始顯示於您所選取的範本為基礎的建議的機器大小。 也來估計執行 VM 每月的成本。

![SQL VM 大小選項](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-choose-a-size.png)

對於生產工作負載，我們建議選取支援[進階版儲存](../storage/storage-premium-storage.md)虛擬機器大小。 如果您不需要的層級的效能，請使用 [顯示所有電腦大小選項的 [**檢視全部**] 按鈕。 例如，您可能會使用較小的電腦開發或測試環境。

>[AZURE.NOTE] 如需有關虛擬機器大小請參閱[虛擬機器的大小](virtual-machines-windows-sizes.md)。 如需 SQL Server VM 大小的考量，請參閱[效能的最佳作法 SQL Server Azure 虛擬機器中的商務連絡人](virtual-machines-windows-sql-performance.md)。

選擇您的電腦大小]，然後再按一下 [**選取**。

## <a name="3-configure-optional-features"></a>3.設定選用的功能
在**設定**防禦，以設定網路，並監控虛擬機器 Azure 儲存。

- 在 [**儲存**] 底下，指定**輸入磁碟**的 [標準] 或 [進階版 (SSD)。 生產負載建議進階版儲存空間。

>[AZURE.NOTE] 如果您選取進階版 (SSD) 機器大小不支援進階版儲存時，會自動變更您的電腦的大小。  

- 在**儲存帳戶**，您可以接受能夠自動儲存體帳戶名稱。 您也可以按一下，選擇現有帳戶並設定儲存的帳戶類型的**儲存空間的帳戶**。 根據預設，Azure 會建立新的儲存空間帳戶與本機多餘的儲存空間。 如需儲存選項的詳細資訊，請參閱[Azure 儲存體複寫](../storage/storage-redundancy.md)。

- [**網路**] 下，您可以接受自動填入的值。 您也可以按一下每個功能，以手動方式設定**虛擬網路**、**子網路**、**公用 IP 位址**和**網路安全性群組**。 進行本教學課程，保留預設值。

- Azure**監控**預設啟用的指定 vm 相同的儲存空間帳戶。 您可以變更這些設定值。

- **可用性設定**，請在 [指定可用性設定。 在進行本教學課程中，您可以選取 [**無**。 如果您打算設定 SQL AlwaysOn 可用性群組，請設定 [可用性]，以避免重新建立虛擬機器。  如需詳細資訊，請參閱[管理可用性虛擬機器](virtual-machines-windows-manage-availability.md)。

當您完成設定這些設定，請按一下**[確定]**。

## <a name="4-configure-sql-server-settings"></a>4.設定 SQL server 設定
在 [ **SQL Server 設定**防禦，以設定特定的設定] 和 [最佳化 SQL Server。 您可以設定的 SQL Server 的設定包括下列各項。

| 設定               |
|---------------------|
| [連線](#connectivity)              |
| [驗證](#authentication)                |
| [存放裝置設定](#storage-configuration)            |
| [自動修正](#automated-patching) |
| [自動的備份](#automated-backup)             |
| [Azure 鍵保存庫整合](#azure-key-vault-integration)             |
| [R 服務](#r-services) |

### <a name="connectivity"></a>連線
在**SQL 連線**，指定您想要在此 VM 的 SQL Server 執行個體的存取類型。 在進行本教學課程中，選取 [允許連線至 SQL Server 的電腦或網際網路上的服務**公用 （網際網路）** ]。 選取此選項，Azure 會自動設定防火牆和網路安全性群組為允許連接埠 1433年流量。  

![SQL 連線選項](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-connectivity-alt.png)

若要連線至網際網路的 SQL Server，您也必須啟用 SQL Server 驗證下, 一節所述。

>[AZURE.NOTE] 若要新增更多的網路通訊的限制您的 SQL Server vm 可能是。 您可以建立 VM 之後編輯網路安全性群組。 如需詳細資訊，請參閱[什麼是網路安全性群組 (NSG)？](../virtual-network/virtual-networks-nsg.md)

如果您想啟用資料庫引擎透過網際網路連線，請選擇下列選項之一︰

- **本機 （內只能 VM)**若要允許連線至的 VM 內，僅從 SQL Server。
- **私人 （在虛擬網路）**允許連線至 SQL Server 的電腦或相同的虛擬網路中的服務。

>[AZURE.NOTE] SQL Server Express edition 的虛擬機器圖像不會自動啟用 TCP/IP 通訊協定。 這是也適用的公用與私人的連線選項。 Express 版本，您必須以[手動方式啟用 TCP/IP 通訊協定](#configure-sql-server-to-listen-on-the-tcp-protocol)使用 SQL Server 組態管理員建立 VM 之後。

一般而言，就能改善安全性選擇最為嚴格連線，可讓您的狀況。 但所有選項都可透過網路安全性群組規則和 SQL/Windows 驗證保護。

1433 預設**連接埠**。 您可以指定不同的連接埠號碼。
如需詳細資訊，請參閱[連線至 SQL Server 虛擬機器 （資源管理員） |Microsoft Azure](virtual-machines-windows-sql-connect.md)。

### <a name="authentication"></a>驗證
如果您需要 SQL Server 驗證，請按一下 [ **SQL 驗證**下的 [**啟用**]。

![SQL Server 驗證](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-authentication.png)

>[AZURE.NOTE] 如果您打算在網際網路 （亦即公用連線選項） 上存取 SQL Server，您必須啟用以下 SQL 驗證。 公用存取 SQL Server 需要使用 SQL 驗證。

如果您啟用 SQL Server 驗證，請指定**登入名稱**和**密碼**。 這個使用者名稱已被設定為的 SQL Server 驗證登入和**系統管理員**固定伺服器角色的成員。 驗證模式的相關資訊，請參閱[選擇驗證模式](http://msdn.microsoft.com/library/ms144284.aspx)。

如果您未啟用 SQL Server 驗證，然後您可以使用本機系統管理員帳戶上 VM 連線至 SQL Server 執行個體。

### <a name="storage-configuration"></a>存放裝置設定
按一下 [**儲存設定**到指定的儲存空間需求。

![SQL 存放裝置設定](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-storage.png)

>[AZURE.NOTE] 如果您選取標準的儲存空間，此選項無法使用。 自動儲存最佳化是僅適用於進階版儲存空間。

您可以輸入輸出秒作業 (IOPs)，處理量 MB/s，和總儲存空間大小為指定的需求。 使用滑動縮放比例設定這些值。 入口網站會自動計算根據這些需求的磁碟數目。

根據預設，Azure 最佳化 5000 IOPs、 200 Mb，及 1 TB 的儲存空間的儲存空間。 您可以變更根據工作負載這些儲存設定。 在 [**儲存最佳化**，選取下列選項之一︰

- **一般**的預設值，以及支援大部分的工作量。
- **交易**處理最佳化傳統資料庫 OLTP 工作負載的儲存空間。
- **資料倉儲**最佳化分析與報告工作負載的儲存空間。

>[AZURE.NOTE] 將滑桿上的上限您選取的虛擬機器大小而有所不同。

### <a name="automated-patching"></a>自動修正
依預設會啟用**自動修正**。 自動修正可讓 Azure 自動更新 SQL Server 和作業系統。 指定週、 時間及期間進行的維修作業] 視窗中的一天。 Azure 執行修補在此進行的維修作業] 視窗中。 進行的維修作業] 視窗排程使用 VM 地區設定的時間。 如果您不想自動更新 SQL Server 和作業系統 Azure，請按一下 [**停用**]。  

![SQL 自動修正](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-patching.png)

如需詳細資訊，請參閱[自動修正的 SQL Server Azure 虛擬機器中的商務連絡人](virtual-machines-windows-sql-automated-patching.md)。

### <a name="automated-backup"></a>自動的備份
啟用自動資料庫備份所有下**自動備份**的資料庫。 預設為停用自動的備份。

當您啟用 SQL 自動的備份時，您可以設定下列項目︰

- 保留期間 （天） 的備份
- 若要使用的備份的儲存空間帳戶
- 加密] 選項和密碼的備份

若要加密備份，請按一下 [**啟用**]。 然後指定的**密碼**。 Azure 建立的憑證來加密備份，並會使用指定的密碼保護的憑證。

![SQL 自動備份](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-autobackup.png)

 如需詳細資訊，請參閱[自動備份的 SQL Server Azure 虛擬機器中的商務連絡人](virtual-machines-windows-sql-automated-backup.md)。

### <a name="azure-key-vault-integration"></a>Azure 鍵保存庫整合
若要儲存 Azure 中的安全性機密資料，來加密，按一下**Azure 金鑰保存庫整合**，按一下 [**啟用**]。

![SQL Azure 金鑰保存庫整合](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-akv.png)

下表列出設定 Azure 金鑰保存庫整合所需的參數。

|參數|描述|範例|
|----------|----------|-------|
|**索引鍵保存庫 URL** |索引鍵保存庫的位置。|https://contosokeyvault.vault.azure.net/ |
|**主要名稱** |Azure Active Directory 服務主體名稱。 此名稱也稱為 「 用戶端識別碼。  |fde2b411-33 d 5 4e11 af04eb07b669ccf2|
| **主要的密碼**|Azure Active Directory 服務本金密碼。 這個密碼也稱為 「 用戶端密碼。 | 9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM =|
|**認證名稱**|**認證名稱**︰ AKV 整合建立 SQL Server，讓存取金鑰保存庫 VM 內認證。 選擇此認證的名稱。| mycred1|

如需詳細資訊，請參閱[SQL server Azure Vm 上設定 Azure 金鑰保存庫整合](virtual-machines-windows-ps-sql-keyvault.md)。

當您完成設定的 SQL Server 時，按一下**[確定]**。

### <a name="r-services"></a>R 服務
SQL Server 2016 企業版中，將使[SQL Server R Services](https://msdn.microsoft.com/library/mt604845.aspx)的選項。 這可讓您使用 SQL Server 2016 中的進階的分析。 按一下 [ **SQL Server 設定**刀上的 [**啟用**]。

![啟用 SQL Server R 服務](./media/virtual-machines-windows-portal-sql-server-provision/azure-vm-sql-server-r-services.png)

>[AZURE.NOTE] SQL Server 不是 2016年企業版的圖像，使 R 服務的選項會停用。

## <a name="5-review-the-summary"></a>5。 檢閱摘要
在**摘要**刀，檢閱摘要，然後按一下**[確定**] 以建立 SQL Server 與資源] 群組中，指定這個 VM 的資源。

您可以監視從 azure 入口網站的部署。 在畫面頂端的 [**通知**] 按鈕會顯示部署的基本狀態。

>[AZURE.NOTE] 為您提供了解部署時間，我部署 SQL VM 東亞美國地區的預設設定。 這項測試部署原本總計 26 分鐘才能完成。 但您可能會遇到更快速地速度變慢部署時間根據您的區域，並選取的設定。

## <a name="open-the-vm-with-remote-desktop"></a>使用遠端桌面開啟 VM

連線到遠端桌面的虛擬機器中使用下列步驟︰

1. 內建 Azure VM 之後，vm 圖示會顯示您 Azure 儀表板上。 您也可以藉由瀏覽您現有的虛擬機器找到它。 按一下 [新的 SQL 虛擬機器上。 **虛擬機器**刀會顯示您的虛擬機器詳細資料。
1. 在**虛擬機器**刀頂端，按一下 [**連線**]。
1. 在瀏覽器 vm 下載 RDP 檔案。 開啟 RDP 檔案。
    ![SQL vm 遠端桌面](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-remote-desktop.png)
1. 遠端桌面連線會通知您找不到此遠端連線的發行者。 按一下 [繼續**連線**]。
1. 在 [ **Windows 安全性**] 對話方塊中，按一下 [**使用另一個帳戶**]。
1. **使用者名稱**類型**\<使用者名稱 >**，其中<user name>是您指定的資料設定 VM 時的使用者名稱。 您必須新增名稱前面初始反斜線。
1. 輸入您先前設定的此 VM 的**密碼**，然後按一下 [連線的**[確定]** 。
1. 如果其他**遠端桌面連線**] 對話方塊詢問您是否連線，請按一下 [**是**]。

您連線至 SQL Server 虛擬機器之後，您可以啟動 SQL Server Management Studio，並使用 Windows 驗證使用您的本機系統管理員認證連線。 如果您啟用 SQL Server 驗證，您也可以連接的 SQL 驗證使用的 SQL 登入與佈建在您已設定的密碼。

電腦存取可讓您直接變更電腦和 SQL Server 根據您的需求的 [設定]。 例如，您無法設定防火牆設定或變更 SQL Server 組態設定。

## <a name="connect-to-sql-server-remotely"></a>遠端連線至 SQL Server

在本教學課程中，選取 [**公用**access 的虛擬機器和**SQL Server 驗證**。 這些設定會自動設定為允許從任何用戶端的 SQL Server 連線 （假設有正確的 SQL 登入） 在網際網路上的虛擬機器。

>[AZURE.NOTE] 如果您沒有選取 [公用提供時，必須執行額外步驟所需在網際網路上存取您的 SQL Server 執行個體。 如需詳細資訊，請參閱[連線至 SQL Server 虛擬機器](virtual-machines-windows-sql-connect.md)。

下列各節說明如何從不同的電腦連線至您的 SQL Server 執行個體，在您 VM 透過網際網路。

> [AZURE.INCLUDE [Connect to SQL Server in a VM Resource Manager](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="next-steps"></a>後續步驟
如需使用 SQL Server Azure 中的其他資訊，請參閱[SQL Server Azure 虛擬機器上的商務連絡人](virtual-machines-windows-sql-server-iaas-overview.md)和[常見問題集](virtual-machines-windows-sql-server-iaas-faq.md)。

SQL Server Azure 虛擬機器上的商務連絡人的視訊概觀，您可以觀看[Azure VM 是 SQL Server 2016 最佳的平台](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016)。

SQL server Azure 虛擬機器上[探索學習路徑](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/)。
