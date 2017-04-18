<properties
    pageTitle="設定 Azure VM-傳統一直在可用性群組"
    description="建立 Azure 虛擬機器持續的可用性群組。 本教學課程主要使用的使用者介面與工具而不是指令碼。"
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/22/2016"
    ms.author="mikeray" />

# <a name="configure-always-on-availability-group-in-azure-vm---classic"></a>設定 Azure VM-傳統一直在可用性群組

> [AZURE.SELECTOR]
- [資源管理員︰ 範本](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
- [資源管理員︰ 手動](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
- [傳統︰ UI](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
- [傳統︰ PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)

<br/>

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


本端對端教學課程中會顯示如何實作可用性群組使用 SQL Server 永遠 Azure 虛擬機器上執行。

教學課程結尾您 SQL Server 永遠解決方案 Azure 中的會包含下列元素︰

- 包含多個子網路，包括前端與後端子網路虛擬網路

- 網域控制站的 Active Directory (AD) 網域

- 兩個 SQL Server Vm 部署到後端的子網路及 AD 網域

- 3-節點 WSFC 叢集節點多數仲裁模型

- 使用兩個同步認可複本可用性資料庫的可用性群組

下圖是解決方案的圖形化表示。

![Azure 中 AG 測試實驗室架構](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC791912.png)

請注意，這是一個可能的設定。 例如，您可以減少的 Vm 可用性兩個複本群組才能儲存為仲裁檔案共用見證 2 節點 WSFC 集中使用的網域控制站 Azure 中的計算小時。 這個方法減少 VM 計算一個以上的設定。

本教學課程假設下列動作︰

- 您已經有 Azure 帳戶。

- 您已經知道如何佈建傳統的 SQL Server VM 從使用 GUI 虛擬機器圖庫。

- 您已經有深入瞭解永遠在可用性群組。 如需詳細資訊，請參閱[永遠在可用性群組 (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)。

>[AZURE.NOTE] 如果您有興趣永遠在可用性群組使用 SharePoint，請參閱[設定 SQL Server 2012 永遠開啟可用性群組的 SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx)。

## <a name="create-the-virtual-network-and-domain-controller-server"></a>建立虛擬網路和網域控制站伺服器

您開始使用新的 Azure 試用帳戶。 完成您的帳戶設定後，您應該 Azure 傳統入口網站的主畫面中。

1. 按一下左下角的頁面] 的 [**新增**] 按鈕，如下所示。

    ![按一下 [新增]，請在入口網站](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665511.gif)

1. 按一下 [**網路服務**]，然後按一下**虛擬網路**，然後按一下**自訂建立**，如下所示。

    ![建立虛擬網路](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665512.gif)

1. 在 [**建立的虛擬網路**] 對話方塊中建立新的虛擬網路以下列設定值逐步執行頁面。 

  	|頁面|設定|
|---|---|
|虛擬網路詳細資料|**名稱 = ContosoNET**<br/>**地區 = 西美制]。**|
|DNS 伺服器及 VPN 連線|無|
|虛擬網路位址空間|設定顯示在下面的螢幕擷取畫面︰ ![建立虛擬網路](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784620.png)|

1. 接下來，您可以建立的 VM 您將使用的網域控制站 (DC)。 按一下 [**新增**一次，然後**計算**，然後**虛擬機器**、]，然後**從圖庫**中，如下所示。

    ![建立 VM](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784621.png)

1. 在 [**建立虛擬機器**] 對話方塊來設定新 VM 以下列設定值逐步執行頁面。 

  	|頁面|設定|
|---|---|
|選取的虛擬機器作業系統|Windows Server 2012 R2 資料中心|
|虛擬機器設定|**版本發行日期**= （最新）<br/>**虛擬機器名稱**= ContosoDC<br/>**層**= 標準<br/>**大小**= A2 （2 核心）<br/>**新的使用者名稱**= AzureAdmin<br/>**新密碼**= Contoso ！ 000<br/>**確認**= Contoso ！ 000|
|虛擬機器設定|**雲端服務**= 建立新的雲端服務<br/>**雲端服務的 DNS 名稱**= 唯一的雲端服務名稱<br/>**DNS 名稱**= 唯一的名稱 (例如︰ ContosoDC123)<br/>**地區/相關性群組/虛擬網路**= ContosoNET<br/>**虛擬網路子**= Back(10.10.2.0/24)<br/>**儲存帳戶**= 使用自動產生的儲存空間帳戶<br/>**可用性設定**= （無）|
|虛擬機器選項|使用預設值|

當您完成設定新的 VM 時，等待要 provsioned VM。 此程序需要一些時間才能完成，，如果您按一下**虛擬機器**中的索引標籤 Azure 傳統入口網站，就可以看見 ContosoDC 循環狀態，從**開始 （提供）**以**停止**、**啟動**、**執行 （提供）**，及最後**執行**。

現在成功提供 DC 伺服器。 接下來，您就會設定 Active Directory 網域 DC 伺服器上。

## <a name="configure-the-domain-controller"></a>設定網域控制站

在下列步驟中，您可以設定 ContosoDC 電腦 corp.contoso.com 網域控制站。

1. 在入口網站中，選取 [ **ContosoDC**電腦]。 在 [**儀表板**] 索引標籤上按一下 [**連線**至開啟 RDP 遠端桌面存取檔案。

    ![連線到 Vritual 機器](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784622.png)

1. 登入您設定管理員帳戶 (**\AzureAdmin**) 和密碼 (**Contoso ！ 000**)。

1. 根據預設，應該會顯示 [**伺服器管理員**儀表板。

1. 按一下儀表板上的 [**新增角色和功能**] 連結。

    ![伺服器總管新增角色](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784623.png)

1. 直到移至 [**伺服器角色**] 區段，請選取 [**下一步**]。

1. 選取 [ **Active Directory 網域服務**與**DNS 伺服器**角色]。 出現提示時，新增任何這些角色所需的其他功能。

    >[AZURE.NOTE] 您會收到警告，沒有靜態 IP 位址驗證。 如果您正在測試設定，按一下 [繼續]。 針對生產案例[使用 PowerShell 來設定網域控制站電腦的靜態 IP 位址](./virtual-network/virtual-networks-reserved-private-ip.md)。

    ![新增 [角色] 對話方塊](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784624.png)

1. 鍵，直到您到達 [**確認**] 區段中，請按一下 [**下一步**]。 選取 [**自動如有必要，請重新啟動目的伺服器**] 核取方塊。

1. 按一下 [**安裝**]。

1. 功能可讓您完成安裝之後，返回 [**伺服器管理員**儀表板。

1. 選取左窗格中的新**AD DS**選項。

1. 按一下 [黃色警告列上的**更多**連結]。

    ![在 [DNS Server VM AD DS] 對話方塊](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784625.png)

1. 在 [**所有伺服器工作詳細資料**] 對話方塊的 [**動作**] 欄中，按一下 [**升階為網域控制站此伺服器**。

1. 在**Active Directory 網域服務設定精靈**中，使用下列的值︰

  	|頁面|設定|
|---|---|
|部署設定|**新增新樹系**= 選取<br/>**根網域名稱**= corp.contoso.com|
|網域控制站選項|**密碼**= Contoso ！ 000<br/>**確認密碼**= Contoso ！ 000|

1. 按一下 [**下一步**可幫助精靈中的其他頁面。 **先決條件核取 [**在頁面上，確認您看到下列訊息︰**所有必要的檢查已成功傳遞**。 請注意，您應該檢視任何適用的警告訊息，可以繼續進行安裝。

1. 按一下 [**安裝**]。 **ContosoDC**虛擬機器會自動重新啟動。

## <a name="configure-domain-accounts"></a>設定網域的帳戶

接下來的步驟設定以供日後使用 Active Directory (AD) 帳戶]。

1. 登入回**ContosoDC**電腦。

1. 在**伺服器管理員]**選取 [**工具]** ，然後按一下 [ **Active Directory 管理中心**。

    ![Active Directory 系統管理中心](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784626.png)

1. 在**Active Directory 管理中心**選取**corp （本機）**從左側的窗格。

1. 在右側的 [**工作**] 窗格中，選取 [**新增**]，然後按一下 [**使用者**]。 使用下列設定︰

  	|設定|值|
|---|---|
|**名字**|安裝|
|**使用者 SamAccountName**|安裝|
|**密碼**|Contoso ！ 000|
|**確認密碼**|Contoso ！ 000|
|**其他密碼選項**|選取|
|**密碼永不過期**|核取|

1. 按一下**[確定]**以建立**安裝**的使用者]。 此帳戶將用來設定容錯移轉叢集和 [可用性] 群組中。

1. 使用相同的步驟建立兩個額外的使用者︰ **CORP\SQLSvc1**和**CORP\SQLSvc2**。 這些帳戶會用於 SQL Server 執行個體。接下來，您需要提供**CORP\Install**必要的權限設定 Windows 服務容錯移轉叢集 (WSFC)。

1. 在**Active Directory 管理中心**中，選取 [在左窗格中的**corp （本機）** ]。 然後在右側的 [**工作**] 窗格中，按一下 [**內容**。

    ![CORP 使用者屬性](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784627.png)

1. 選取**延伸**，然後按一下 [**安全性**] 索引標籤上的 [**進階**] 按鈕。

1. 在 [ **Corp 的進階安全性設定**] 對話方塊。 按一下 [**新增**]。

1. 按一下 [**選取一個主體**]。 搜尋**CORP\Install**。 按一下**[確定]**。

1. 選取 [**讀取所有內容**和**建立電腦物件**權限]。

    ![Corp 使用者權限](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784628.png)

1. 按一下**[確定**]，然後再按一次**[確定]** 。 關閉 [corp 屬性] 視窗。

現在您完成設定 Active Directory 和使用者物件時，您會建立三個 SQL Server Vm 並加入此網域。

## <a name="create-the-sql-server-vms"></a>建立 SQL Server Vm

接下來，建立三個 Vm，包括 WSFC 叢集節點和兩個 SQL Server Vm。 若要建立每一個 Vm，回到 [Azure 傳統入口網站，按一下 [**新增**、**計算**、**虛擬機器**]，然後**從圖庫**。 然後使用下表中的範本，協助您建立 Vm。

|頁面|VM1|VM2|VM3|
|---|---|---|---|
|選取的虛擬機器作業系統|**Windows Server 2012 R2 資料中心**|**SQL Server 2014 RTM 企業版**|**SQL Server 2014 RTM 企業版**|
|虛擬機器設定|**版本發行日期**= （最新）<br/>**虛擬機器名稱**= ContosoWSFCNode<br/>**層**= 標準<br/>**大小**= A2 （2 核心）<br/>**新的使用者名稱**= AzureAdmin<br/>**新密碼**= Contoso ！ 000<br/>**確認**= Contoso ！ 000|**版本發行日期**= （最新）<br/>**虛擬機器名稱**= ContosoSQL1<br/>**層**= 標準<br/>**大小**= A3 （4 個核心）<br/>**新的使用者名稱**= AzureAdmin<br/>**新密碼**= Contoso ！ 000<br/>**確認**= Contoso ！ 000|**版本發行日期**= （最新）<br/>**虛擬機器名稱**= ContosoSQL2<br/>**層**= 標準<br/>**大小**= A3 （4 個核心）<br/>**新的使用者名稱**= AzureAdmin<br/>**新密碼**= Contoso ！ 000<br/>**確認**= Contoso ！ 000|
|虛擬機器設定|**雲端服務**= 先前建立的唯一雲端服務的 DNS 名稱 (例如︰ ContosoDC123)<br/>**地區/相關性群組/虛擬網路**= ContosoNET<br/>**虛擬網路子**= Back(10.10.2.0/24)<br/>**儲存帳戶**= 使用自動產生的儲存空間帳戶<br/>**可用性設定**= 建立可用性設定<br/>**可用性設定名稱**= SQLHADR|**雲端服務**= 先前建立的唯一雲端服務的 DNS 名稱 (例如︰ ContosoDC123)<br/>**地區/相關性群組/虛擬網路**= ContosoNET<br/>**虛擬網路子**= Back(10.10.2.0/24)<br/>**儲存帳戶**= 使用自動產生的儲存空間帳戶<br/>**可用性設定**= 的 SQLHADR （您也可以設定設定建立電腦之後的可用性。 所有的三個機器應該指派給 SQLHADR 可用性設定。）|**雲端服務**= 先前建立的唯一雲端服務的 DNS 名稱 (例如︰ ContosoDC123)<br/>**地區/相關性群組/虛擬網路**= ContosoNET<br/>**虛擬網路子**= Back(10.10.2.0/24)<br/>**儲存帳戶**= 使用自動產生的儲存空間帳戶<br/>**可用性設定**= 的 SQLHADR （您也可以設定設定建立電腦之後的可用性。 所有的三個機器應該指派給 SQLHADR 可用性設定。）|
|虛擬機器選項|使用預設值|使用預設值|使用預設值|

<br/>

>[AZURE.NOTE] 先前的設定會建議標準層虛擬機器，因為基本層機器不支援負載平衡稍後建立可用性群組接聽所需的結束點。 此外，以下建議機器大小是為了在 Azure Vm 測試可用性群組。 生產工作負載的最佳效能，請參閱 SQL Server 的電腦大小和[效能的最佳作法 SQL Server Azure 虛擬機器中的商務連絡人](virtual-machines-windows-sql-performance.md)中設定的建議。

後三個 Vm 完全佈建後，您需要加入**corp.contoso.com**網域，並授與 CORP\Install 電腦的系統管理權限。 若要這麼做，請針對每個三個 Vm 使用下列步驟。

1. 首先，變更慣用的 DNS 伺服器位址。 首先 VM 選取清單中，按一下 [**連線**] 按鈕，下載到您的本機目錄的每個 VM 遠端桌面 (RDP) 檔案。 若要選取 VM，按一下任何位置但第一個儲存格的列中，如下所示。

    ![下載 RDP 檔案](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664953.jpg)

1. 啟動下載的 RDP 檔案並登入 VM 使用您設定管理員帳戶 (**BUILTIN\AzureAdmin**) 和密碼 (**Contoso ！ 000**)。

1. 當您登入時，您應該會看到 [**伺服器管理員**儀表板。 按一下左窗格中的 [**本機伺服器**]。

1. 選取**指派 DHCP，IPv6 IPv4 位址**的連結。

1. 在**網路連線**] 視窗中，選取 [網路] 圖示。

    ![變更 VM 慣用的 DNS 伺服器](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784629.png)

1. 在命令列中，按一下 [**變更這個連線的設定**（根據您視窗的大小，您可能必須按一下雙向右箭號，以查看此命令）。

1. 選取**網際網路通訊協定版本 4 (TCP/IPv4)** ，然後按一下 [內容]。

1. 選取 [使用下列 DNS 伺服器位址，並指定**10.10.2.4** **慣用的 DNS 伺服器**中。

1. 地址**10.10.2.4**指派給 VM 10.10.2.0/24 子網路中 Azure 虛擬網路中的地址，而該 VM 會**ContosoDC**。 若要確認**ContosoDC**的 IP 位址，如下所示，在命令提示字元中，使用**nslookup contosodc** 。

    ![使用 NSLOOKUP 尋找 DC IP 位址](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664954.jpg)

1. 按一下 [O**K** ，然後**關閉**認可變更]。 現在，您就可以加入**corp.contoso.com**VM。

1. 回到 [**本機伺服器**] 視窗中，按一下 [**工作群組**] 連結。

1. 在**電腦名稱**] 區段中，按一下 [**變更**]。

1. 選取 [**網域**] 核取方塊，然後在 [文字] 方塊中輸入**corp.contoso.com** 。 按一下**[確定]**。

1. 在 [ **Windows 安全性**快顯功能表] 對話方塊中，指定預設網域系統管理員帳戶 (**CORP\AzureAdmin**) 和密碼認證 (**Contoso ！ 000**)。

1. 當您看到 「 歡迎使用 corp.contoso.com 網域 」 的訊息時，請按一下**[確定]**。

1. 按一下 [**關閉**]，然後按一下快顯功能表] 對話方塊中的 [**立即重新啟動]** 。

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-vm"></a>在每個 VM 以系統管理員身分新增 Corp\Install 使用者︰

1. 等到 VM 重新啟動時，再啟動 RDP 檔案登入 VM 使用**BUILTIN\AzureAdmin**帳戶。

1. 在 [**伺服器管理員**選取 [**工具**]，然後按一下**電腦管理。**

    ![電腦管理](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784630.png)

1. 在**電腦管理**] 視窗中，展開 [**本機使用者和群組**]，然後選取 [**群組]**。

1. 按兩下 [**系統管理員**] 群組。

1. 在 [**系統管理員屬性**] 對話方塊中，按一下 [**新增**] 按鈕。

1. 輸入使用者**CORP\Install**，然後再按一下**[確定**]。 時提示輸入認證，請使用**AzureAdmin**帳戶**Contoso ！ 000**密碼。

1. 按一下**[確定**] 關閉 [**系統管理員屬性**] 對話方塊。

### <a name="add-the-failover-clustering-feature-to-each-vm"></a>將每個 VM**容錯**的功能。

1. 在 [**伺服器管理員**儀表板，按一下 [**新增角色和功能**]。

1. 在**新增角色和功能] 精靈**，按一下 [**下一步**直到移至 [**功能**] 頁面。

1. 選取 [**容錯**]。 出現提示時，新增其他任何相關的功能。

    ![新增容錯功能 vm](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784631.png)

1. 按一下 [**下一步**，然後再按一下 [**安裝**]**確認**頁面上。

1. 完成**容錯**安裝功能時，請按一下 [**關閉**]。

1. 登出 VM。

1. 所有的三個伺服器- **ContosoWSFCNode** **ContosoSQL1**，與**ContosoSQL2**重複此區段中的步驟。

SQL Server Vm 現在佈建後，執行，但它們會安裝 SQL server 使用預設選項。

## <a name="create-the-wsfc-cluster"></a>建立 WSFC 叢集

在此區段中，您可以建立 WSFC 叢集裝載您稍後將會建立可用性群組。 現在，您應該執行下列三個 Vm WSFC 叢集中的每一個︰

- 完整佈建在 Azure 中

- 結合的 VM 網域

- 新增的**CORP\Install**本機系統管理員群組

- 新增 [容錯功能

全部這些是在每個 VM 的必要條件，您可以加入 WSFC 叢集之前。

此外，請注意，Azure 虛擬網路未在內部部署網路的相同方式運作。 您需要建立叢集以下列順序︰

1. 建立單一節點叢集在其中一個節點 (**ContosoSQL1**)。

1. 修改未使用的 IP 位址 (**10.10.2.101**) 的叢集 IP 位址。

1. 顯示叢集名稱。

1. 新增其他節點 （**ContosoSQL2**和**ContosoWSFCNode**）。

請遵循下列步驟以完成完全設定叢集這些工作。

1. 啟動**ContosoSQL1** RDP 檔案，並使用網域帳戶**CORP\Install**登入。

1. 在 [**伺服器管理員**儀表板中，選取 [**工具**]，然後按一下**容錯移轉叢集管理員**。

1. 在左窗格中，以滑鼠右鍵按一下**容錯移轉叢集管理員**]，然後按一下**建立叢集**，如下所示。

    ![建立叢集](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784632.png)

1. 在建立叢集精靈] 中，建立一個節點叢集逐步頁面，使用下列設定︰

  	|頁面|設定|
|---|---|
|開始之前|使用預設值|
|選取 [伺服器|輸入**ContosoSQL1** **輸入伺服器名稱**，然後按一下 [**新增**|
|驗證警告|選取 [否]。 我此叢集，不需要 microsoft 的支援，因此不想執行驗證測試**。當我按一下 [下一步] 時，繼續建立叢集**。|
|管理叢集存取點|在 [**叢集名稱**類型**Cluster1**|
|確認|除非您使用的儲存空間，請使用預設值。 請參閱下表。|

    >[AZURE.WARNING] 如果您使用的[儲存空間](https://technet.microsoft.com/library/hh831739)，將儲存集區群組多個磁碟，您必須取消核取 [**新增到叢集所有符合資格的儲存空間**的核取方塊**確認**頁面上。 如果您無法取消選取這個選項，將會中斷連結虛擬磁碟叢集程序時。 如此一來，他們也不會出現在磁碟管理員 」 或 「 檔案總管] 鍵，直到儲存空間移除了叢集，然後重新附加使用 PowerShell。

1. 在左窗格中，展開**容錯移轉叢集管理員**]，然後按一下**Cluster1.corp.contoso.com**。

1. 在中間窗格中，向下捲動**叢集核心資源**] 區段，然後展開**名稱︰ Clutser1**詳細資料。 您應該會看到的**名稱**和**失敗**狀態的**IP 位址**資源。 無法連線的 IP 位址資源，因為叢集指派為機器本身，相同的 IP 位址已重複的地址。

1. 失敗的**IP 位址**資源，以滑鼠右鍵按一下，然後按一下 [**摘要資訊**。

    ![叢集內容](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784633.png)

1. 選取**的靜態 IP 位址**，然後在 [地址的文字] 方塊中指定**10.10.2.101** 。 接著，按一下**[確定]**。

1. 在 [**叢集核心資源**] 區段中，以滑鼠右鍵按一下**名稱︰ Cluster1**並按一下**[連線**。 兩個資源在線上，請等候。 線上叢集名稱資源時，它會更新以新的 AD 電腦帳戶的 DC 伺服器。 此 AD 帳戶會用於稍後再執行可用性群組直條圖] 群組中的服務。

1. 最後，您可以新增其餘的節點叢集。 在瀏覽器樹狀目錄中，以滑鼠右鍵按一下**Cluster1.corp.contoso.com**並按一下**新增節點**，如下所示。

    ![新增到叢集節點](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784634.png)

1. 在**新增節點精靈**] 中，按一下 [**下一步**]。 在 [**選取 [伺服器**] 頁面新增**ContosoSQL2**和**ContosoWSFCNode**清單，**輸入伺服器名稱**] 中，輸入伺服器名稱，然後按一下 [**新增]**。 當您完成時，請按一下 [**下一步**]。

1. 在 [**驗證警告**] 頁面上，按一下 [**否]** （在生產的情況下您應該執行驗證測試）。 然後，請按一下 [**下一步**]。

1. 在 [**確認**] 頁面上，按一下 [**下一步**新增節點。

    >[AZURE.WARNING] 如果您使用的[儲存空間](https://technet.microsoft.com/library/hh831739)，將儲存集區群組多個磁碟，您必須取消核取**將所有符合資格的儲存空間叢集新增**核取方塊。 如果您無法取消選取這個選項，將會中斷連結虛擬磁碟叢集程序時。 如此一來，他們也不會出現在磁碟管理員 」 或 「 檔案總管] 鍵，直到儲存空間移除了叢集，然後重新附加使用 PowerShell。

1. 一旦叢集加入節點，請按一下 [**完成**]。 叢集有三個節點，並列出**節點**容器中，現在應該會顯示容錯移轉叢集管理員。

1. 登出遠端桌面工作階段。

## <a name="prepare-the-sql-server-instances-for-availability-group"></a>準備可用性] 群組中的 SQL Server 執行個體

在此區段中，您將會執行下列動作**ContosoSQL1**和**contosoSQL2**上︰

- 新增必要的權限，設定為預設的 SQL Server 執行個體的**NT AUTHORITY\System**登入

- 新增**CORP\Install**為系統管理員角色的預設 SQL Server 執行個體

- 開啟遠端存取的 SQL Server 的防火牆

- 啟用永遠在可用性群組功能

- 分別**CORP\SQLSvc1**和**CORP\SQLSvc2**，變更 SQL Server 服務帳戶

任何順序，才能執行這些動作。 不過，執行下列步驟將逐步其順序。 **ContosoSQL1**和**ContosoSQL2**遵循的步驟︰

1. 如果您不具有 vm 登出遠端桌面工作階段，立即執行。

1. 啟動 RDP 檔案**ContosoSQL1**和**ContosoSQL2**並**BUILTIN\AzureAdmin**身分登入。

1. SQL Server 登入並搭配必要權限，請先新增**NT AUTHORITY\System** 。 啟動**SQL Server Management Studio 中**。

1. 按一下 [連線到預設的 SQL Server 執行個體的**連線**。

1. 在 [**物件總管]**中，展開 [**安全性**]，然後再展開**登入**。

1. 以滑鼠右鍵按一下**NT AUTHORITY\System**登入，然後按一下 [**屬性**]。

1. 在 [**安全性實體安全性實體**] 頁面的本機] 選取下列權限**授與**，然後按一下**[確定]**。

    - 變更任何可用性] 群組

    - 連線 SQL

    - 檢視伺服器狀態

1. 接下來，新增**CORP\Install**為**系統管理員**角色的預設 SQL Server 執行個體。 在**物件總管]**中，以滑鼠右鍵按一下 [**登入**，然後按一下 [**新的登入**。

1. 輸入**CORP\Install** **登入名稱**。

1. 在 [**伺服器角色**] 頁面中，選取 [**系統管理員**]。 接著，按一下**[確定]**。 登入建立後，您可以展開**登入****物件檔案總管**中看到它。

1. 接下來，您建立 SQL Server 的防火牆規則。 從**[開始**] 畫面中，啟動 [**具有進階安全性的 Windows 防火牆**]。

1. 在左窗格中，選取 [**輸入規則**]。 在右窗格中，按一下 [**新增規則**]。

1. 在 [**規則類型**] 頁面選取 [**程式**]，然後按一下 [**下一步**]。

1. 在 [**程式**] 頁面中，選取**這個程式路徑**，然後輸入**%ProgramFiles%\Microsoft SQL Server\MSSQL12。MSSQLSERVER\MSSQL\Binn\sqlservr.exe**中 [文字] 方塊 （如果您是依照這些指示，但使用 SQL Server 2012，SQL Server 目錄**MSSQL11。MSSQLSERVER**)。 然後按一下 [**下一步**。

1. 在 [**動作**] 頁面保持選取的**儲存格內允許連線**，然後按一下 [**下一步**。

1. 在 [**設定檔**] 頁面接受預設設定，然後按一下 [**下一步**。

1. 在 [**名稱**] 頁面中，指定規則的名稱，例如**SQL Server （程式規則）**在 [**名稱**] 文字方塊中，然後按一下 [**完成**]。

1. 接下來，您可以啟用**永遠在可用性群組**功能。 從**[開始**] 畫面中，啟動 [ **SQL Server 組態管理員**]。

1. 在瀏覽器樹狀目錄中，按一下 [ **SQL Server 服務**，然後以滑鼠右鍵按一下 [ **SQL Server ()** nt，按一下 [**內容**。

1. **永遠在高可用性**] 索引標籤，然後選取**啟用永遠在可用性群組**，如下所示，，然後按一下 [**套用**。 在快顯對話方塊中，按一下**[確定]** ，然後還不關閉 [屬性] 視窗。 當您變更服務帳戶之後，您會重新啟動 SQL Server 服務。

    ![永遠啟用可用群組](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665520.gif)

1. 接下來，您可以變更的 SQL Server 服務帳戶。 **登入**] 索引標籤，然後輸入**CORP\SQLSvc1** （適用於**ContosoSQL1**) 或 （適用於**ContosoSQL2**) **CORP\SQLSvc2** **帳戶名稱**，然後填入並確認密碼，，然後按一下**[確定]**。

1. 在快顯視窗中，按一下 [ **]** ，重新啟動 SQL Server 服務。 重新啟動 SQL Server 服務之後，請在 [屬性] 視窗中您所做的變更會生效。

1. 登出 Vm。

## <a name="create-the-availability-group"></a>建立 [可用性] 群組

您已準備好要設定可用性群組。 以下是大綱，其中您將會執行的動作︰

- 在**ContosoSQL1**上建立新的資料庫 (**MyDB1**)

- 完整的備份，然後交易記錄備份的資料庫

- 還原完整並登**ContosoSQL2**備份**NORECOVERY**選項

- 使用同步的認可與自動容錯移轉時，可讀的次要複本建立可用性群組 (**AG1**)

### <a name="create-the-mydb1-database-on-contososql1"></a>建立 MyDB1 資料庫 ContosoSQL1 上︰

1. 如果您有不已登入登出遠端桌面工作階段**ContosoSQL1**和**ContosoSQL2**，立即執行。

1. 啟動**ContosoSQL1** RDP 檔案和**CORP\Install**身分登入。

1. 在 [**檔案總管]**中，在下**c:\**，建立名為目錄**備份**。 您會使用此目錄使用備份和還原資料庫。

1. 以滑鼠右鍵按一下新的目錄，指向 [**共用對象**]，然後按一下**特定的人員**，如下所示。

    ![建立備份] 資料夾](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665521.gif)

1. 新增**CORP\SQLSvc1**和不妨的**讀/寫**權限，然後新增**CORP\SQLSvc2**和提供的**讀取**權限，如下所示，，然後按一下 [**共用]**。 檔案共用程序完成之後，請按一下 [**完成**]。

    ![授與備份資料夾的權限](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665522.gif)

1. 接下來，您會建立資料庫。 從**[開始**] 功能表中，啟動**SQL Server Management Studio**中，然後按一下 [**連線**連線到預設的 SQL Server 執行個體。

1. 在**物件總管]**中，以滑鼠右鍵按一下**資料庫**並按一下**新的資料庫**。

1. 在 [**資料庫名稱**] 輸入**MyDB1**，然後按一下**[確定**]。

### <a name="take-a-full-backup-of-mydb1-and-restore-it-on-contososql2"></a>備份的 MyDB1，並將它還原 ContosoSQL2 上︰

1. 接下來，您需要完整備份的資料庫。 在**物件總管]**中，展開**資料庫**，然後**MyDB1**，以滑鼠右鍵按一下，然後指向 [**工作**]，然後按一下**備份**。

1. 在 [**來源**] 區段中，請**備份類型**設為 [**完整**。 在 [ **Destination** ] 區段中，按一下 [若要移除的備份檔案的預設檔案路徑的 [**移除**]。

1. 在 [ **Destination** ] 區段中，按一下 [**新增**]。

1. 在 [**檔案名稱**] 文字方塊中，輸入**\\ContosoSQL1\backup\MyDB1.bak**。 按一下**[確定**]，然後再按一下 [**確定**] 以備份資料庫中。 備份作業完成後，請按一下**[確定**] 關閉對話方塊。

1. 接下來，您需要交易記錄檔備份的資料庫。 在**物件總管]**中，展開**資料庫**，然後**MyDB1**，以滑鼠右鍵按一下，然後指向 [**工作**]，然後按一下**備份**。

1. 在**備份**類型] 中，選取 [**交易記錄檔**]。 保留**目的地**檔案路徑，設定您先前指定，然後按一下**[確定]**。 備份作業完成後，請再次按一下**[確定]** 。

1. 接下來，您可以還原**ContosoSQL2**全和交易記錄檔備份。 啟動**ContosoSQL2** RDP 檔案和**CORP\Install**身分登入。 保留**ContosoSQL1**的遠端桌面工作階段開啟。

1. 從**[開始**] 功能表中，啟動**SQL Server Management Studio**中，然後按一下 [**連線**連線到預設的 SQL Server 執行個體。

1. 在**物件總管]**中，以滑鼠右鍵按一下**資料庫**，然後按一下 [**還原資料庫**。

1. 在 [**來源**] 區段中，選取 [**裝置**]，然後按一下**...** ] 按鈕。

1. 在 [**選取備份裝置**，請按一下 [**新增**]。

1. 備份檔案的位置輸入\\ContosoSQL1\backup，然後按一下 [重新整理]，然後選取 MyDB1.bak，然後按一下 [確定]，然後再按一下 [確定]。 您現在應該會看到完整的備份，並在 [備份的記錄檔備份設定還原窗格。

1. 移至 [選項] 頁面，然後選取與 NORECOVERY 還原復原狀態，然後按一下 [確定] 以還原資料庫。 還原作業完成後，按一下 [確定]。

### <a name="create-the-availability-group"></a>建立 [可用性] 群組中︰

1. 回到遠端桌面工作階段的**ContosoSQL1**。 **物件檔案總管**中 SSMS，以滑鼠右鍵按一下 [**永遠在高可用性**，然後按一下 [**新的顯示狀態群組精靈**] 中，如下所示。

    ![啟動新的可用性群組精靈](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665523.gif)

1. 在 [**簡介**] 頁面上，按一下 [**下一步**]。 在 [**指定可用性群組名稱**] 頁面中，輸入**AG1** **可用性群組名稱**，然後再按一下 [**下一步**]。

    ![新增 AG 精靈] 中，指定 AG 名稱](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665524.gif)

1. 在 [**選取資料庫**] 頁面選取**MyDB1** ，然後按一下 [**下一步**。 資料庫符合可用性群組的必要條件，因為您已經在預定的主要複本上至少有一個完整的備份。

    ![新增 AG 精靈] 中，選取資料庫](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665525.gif)

1. 在 [**指定複本**] 頁面上，按一下 [**新增複本**]。

    ![新增 AG 精靈] 中，指定複本](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665526.gif)

1. **連線至伺服器**] 對話方塊會顯示。 輸入**ContosoSQL2** **伺服器名稱**，然後按一下 [**連線**]。

    ![新的 AG 精靈，連線到伺服器](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665527.gif)

1. 回 [**指定複本**] 頁面中，您現在應該會看到**ContosoSQL2**列在 [**可用的複本**。 設定複本，如下所示。 當您完成時，請按一下 [**下一步**]。

    ![新增 AG 精靈] 中，指定複本 （完成）](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665528.gif)

1. 在 [**選取初始資料同步處理**] 頁面選取 [**只加入**，然後按一下 [**下一步**。 您已經完成資料同步處理手動當您在**ContosoSQL1**原本全和交易備份和還原**ContosoSQL2**。 您可以改為選擇不執行備份和還原您的資料庫作業選取**完整**，讓您執行資料同步處理新可用性群組精靈。 不過，建議您不要很大一些企業中找到的資料庫。

    ![新增 AG 精靈] 中，選取最初的資料同步處理](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665529.gif)

1. 在 [**驗證**] 頁面上，按一下 [**下一步**]。 此頁面看起來應該像下面。 沒有接聽設定的警告，因為您未設定可用性群組接聽程式。 您可以略過此警告，，因為此教學課程，不會設定接聽程式。 若要設定接聽完成本教學課程後，請參閱[設定一律上顯示狀態中的群組 Azure ILB 接聽程式](virtual-machines-windows-classic-ps-sql-int-listener.md)。

    ![新增 AG 精靈] 驗證](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665530.gif)

1. 在 [**摘要**] 頁面上，按一下 [**完成**]，然後稍候精靈設定新的顯示狀態群組]。 在 [**進度**] 頁面中，您可以按一下 [**更多詳細資料**，若要檢視詳細的進度。 當精靈完成時，檢查 [**結果**] 頁面，請確認已成功建立可用性群組，如下所示，然後按一下 [**關閉**] 關閉精靈。

    ![新增 AG 精靈] 結果](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665531.gif)

1. 在**物件總管]**中，展開**永遠在高可用性**、 再展開**可用性群組**。 您現在應該會看到此容器中的 [新增可用性] 群組。 以滑鼠右鍵按一下**AG1 （主要）** ，按一下 [**顯示的儀表板**。

    ![顯示 AG 儀表板](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665532.gif)

1. 您**隨時都在儀表板**應該類似以下所示的項目。 您可以看到複本、 容錯移轉模式的每個複本和同步處理狀態。

    ![AG 儀表板](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665533.gif)

1. 返回 [**伺服器管理員]**，選取 [**工具**]，然後啟動**容錯移轉叢集管理員**。

1. 展開**Cluster1.corp.contoso.com**，然後再展開**服務及應用程式**。 選取 [**角色**]，並注意**AG1**可用性群組 」 角色所建立。 請注意，AG1 沒有任何 IP 位址，用戶端的資料庫可以連線到可用性] 群組中，因為您未設定接聽程式。 您可以將直接連接到讀寫作業的主要節點和唯讀查詢的次要節點。

    ![AG 容錯移轉叢集管理員中的商務連絡人](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665534.gif)

>[AZURE.WARNING] 請勿嘗試透過可用性群組從容錯移轉叢集管理員失敗。 所有的容錯移轉作業應該**一律在儀表板**中 SSMS 中從執行。 如需詳細資訊，請參閱[在使用 WSFC 容錯移轉叢集管理員可用群組的限制](https://msdn.microsoft.com/library/ff929171.aspx)。

## <a name="next-steps"></a>後續步驟
您現在成功實作 SQL Server 永遠在 Azure 中建立可用性群組。 若要設定的接聽這個可用性群組，請參閱[設定一律上顯示狀態中的群組 Azure ILB 接聽程式](virtual-machines-windows-classic-ps-sql-int-listener.md)。

如需使用 SQL Server Azure 中的其他資訊，請參閱[SQL Server Azure 虛擬機器上的商務連絡人](virtual-machines-windows-sql-server-iaas-overview.md)。
