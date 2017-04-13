<properties
    pageTitle="將內部部署一直在可用性群組延伸到 Azure |Microsoft Azure"
    description="本教學課程中使用傳統的部署模型，以建立的資源，並說明如何使用 [新增複本精靈在 SQL Server 管理 Studio (SSMS) Azure 中新增永遠在可用性群組複本。"
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="07/12/2016"
    ms.author="MikeRayMSFT" />

# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>可延伸至 Azure 的內部部署永遠上可用群組

一律在可用性群組提供群組的資料庫可用性藉由新增第二個複本。 這些複本允許失敗移轉若失敗的資料庫。 此外，可用來卸載讀取的負載或備份工作。

您可以延伸到 Microsoft Azure 的內部部署可用性群組佈建一或多個與 SQL Server Azure Vm，然後為複本將其新增至您的內部部署可用性群組。

本教學課程假設您有下列動作︰

- 作用中的 Azure 訂閱。 您可以[註冊免費試用版](https://azure.microsoft.com/pricing/free-trial/)。

- 現有永遠在可用性群組內部部署。 如需可用群組的詳細資訊，請參閱[永遠在可用性群組](https://msdn.microsoft.com/library/hh510230.aspx)。

- 內部網路和 Azure 虛擬網路之間的連線。 如需有關如何建立此虛擬網路的詳細資訊，請參閱[設定網站-VPN Azure 傳統入口網站中](../vpn-gateway/vpn-gateway-site-to-site-create.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="add-azure-replica-wizard"></a>新增 Azure 複本精靈

本節說明如何使用**新增 Azure 複本精靈**延長您永遠在可用性] 群組中的方案包含 Azure 的複本。

1. 從內 SQL Server Management Studio 中，展開**永遠在高可用性** > **可用性群組** > **可用性群組名稱**。

1. **可用性複本**，以滑鼠右鍵按一下，然後按一下 [**新增複本**]。

1. 根據預設，會顯示**可用群組精靈新增的複本**。 按一下 [**下一步**]。  如果您已選取**不會顯示一次此頁面**底部的頁面在先前啟動時的選項此精靈，此畫面會不會顯示。

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)

1. 您必須連線至所有現有的次要複本。 您可以按一下 [**連線...** 旁邊，每個複本，或者您可以按一下 [**連線所有...** 在畫面底部。 驗證後，請按 [**下一步**前進到下一個畫面。

1. 在 [**指定複本**] 頁面上多個索引標籤會列出上方︰**複本**、**結束點**、**備份喜好設定**] 和**接聽程式**。 從 [**複本**] 索引標籤中，按一下 [**新增 Azure 複本...** 若要啟動新增 Azure 複本精靈。

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)

1. 如果您已安裝前，請選取現有的 Azure 管理憑證從本機 Windows 認證存放區。 選取或輸入 Azure 訂閱的識別碼，如果您已使用前一個]。 您可以按一下 [下載] 下載和安裝 Azure 管理憑證和下載使用 Azure 帳戶的訂閱清單。

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)

1. 您會填入值會用來建立 Azure 虛擬機器 (VM) 裝載複本的頁面上的每一個欄位。

  	|設定|描述|
|---|---|
|**圖像**|選取您要的組合的作業系統和 SQL Server|
|**虛擬記憶體大小**|選取最適合您的業務需求的 VM 大小|
|**VM 名稱**|指定新 VM 的唯一名稱。 名稱必須介於 3 到 15 個字元、 可以包含字母、 數字和連字號及必須以字母開頭和結尾字母或數字。|
|**VM 使用者名稱**|指定會變成 VM 上的系統管理員帳戶的使用者名稱|
|**VM 系統管理員密碼**|指定新的帳戶的密碼|
|**確認密碼**|確認新帳戶的密碼|
|**虛擬網路**|指定應該使用新的 VM Azure 虛擬網路。 如需有關虛擬網路的詳細資訊，請參閱[虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。|
|**虛擬網路的子網路**|指定應該使用新的 VM 虛擬網路子網路|
|**網域**|確認網域的預先填入的值是正確的|
|**使用者的網域名稱**|指定本機叢集節點上的本機管理員群組中的帳戶|
|**密碼**|指定之網域的使用者名稱的密碼|

1. 按一下**[確定**]，以驗證部署設定。

1. 法律條款會顯示在下一步。 閱讀並按一下**[確定]** ，如果您同意以下條款。

1. **指定複本**頁面會顯示一次。 確認新 Azure 複本**複本**、**結束點**和**備份喜好設定**] 索引標籤上的設定。 修改設定以符合您的業務需求。  如需包含在這些索引標籤上的參數的詳細資訊，請參閱[指定複本頁面 （新可用性群組精靈/新增複本精靈）](https://msdn.microsoft.com/library/hh213088.aspx)。請注意，無法使用 [接聽] 索引標籤包含 Azure 複本的可用性群組建立接聽程式。 此外，如果已建立接聽程式之前啟動精靈，您會收到訊息，指出不支援 Azure 中。 我們會看看如何建立接聽**建立可用性群組接聽程式**] 區段中。

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)

1. 按一下 [**下一步**]。

1. 選取您想要使用 [**選取初始資料同步處理**頁面上，按一下 [**下一步**的資料同步處理方法。 在大部分情況下，選取 [**完整資料同步處理**]。 如詳細資料同步處理方法的詳細資訊，請參閱[選取初始資料同步處理] 頁面 （永遠在可用性群組精靈）](https://msdn.microsoft.com/library/hh231021.aspx)。

1. 檢閱 [**驗證**] 頁面上的結果。 修正未完成的問題，必要時，重新執行驗證。 按一下 [**下一步**]。

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)

1. 檢閱 [**摘要**] 頁面上的設定，然後按一下 [**完成**]。

1. 開始佈建的程序。 當精靈完成作業成功之後時，按一下 [**關閉**結束精靈。

>[AZURE.NOTE] 新增 Azure 複本精靈 Users\User Name\AppData\Local\SQL Server\AddReplicaWizard 中建立的記錄檔。 這個記錄檔可用來疑難排解失敗 Azure 複本部署。 如果精靈無法執行任何動作，所有的上一個作業被復原，包括刪除能夠的 VM。

## <a name="create-an-availability-group-listener"></a>建立可用性群組接聽程式

在建立可用性群組之後，您應該建立用戶端連線到複本的接聽程式。 接聽直接連入連線的主要或唯讀的次要複本。 如需有關接聽程式的詳細資訊，請參閱[設定一律上顯示狀態中的群組 Azure ILB 接聽程式](virtual-machines-windows-classic-ps-sql-int-listener.md)。

## <a name="next-steps"></a>後續步驟

除了使用**新增 Azure 複本精靈**將您永遠在可用性群組延伸到 Azure，您可能也會將某些 SQL Server 負載完全 Azure。 若要開始，請參閱[佈建 Azure SQL Server 虛擬機器](virtual-machines-windows-portal-sql-server-provision.md)。

執行 SQL Server Azure Vm 中其他主題，請參閱[SQL Server Azure 虛擬機器上的商務連絡人](virtual-machines-windows-sql-server-iaas-overview.md)。
