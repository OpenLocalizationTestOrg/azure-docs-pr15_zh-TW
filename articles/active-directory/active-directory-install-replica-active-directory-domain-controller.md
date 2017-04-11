<properties
    pageTitle="Azure 中安裝複本 Active Directory 網域控制站 |Microsoft Azure"
    description="說明如何從內部部署 Active Directory 樹系 Azure 虛擬機器上安裝網域控制站教學課程。"
    services="virtual-network"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="virtual-network"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="curtand"/>


# <a name="install-a-replica-active-directory-domain-controller-in-an-azure-virtual-network"></a>Azure 虛擬網路中安裝複本 Active Directory 網域控制站

本主題說明如何安裝其他網域控制站 （也稱為複本 Dc） 為內部部署的 Active Directory 網域 Azure 虛擬機器 (Vm) 中 Azure 虛擬網路上。

您也可能會感興趣的下列相關主題︰

-  您也可以安裝新的 Active Directory 樹系 Azure 虛擬網路上。 這些步驟，請參閱[安裝新的 Active Directory 樹系 Azure 虛擬網路上](../active-directory/active-directory-new-forest-virtual-machine.md)。
-  概念性瞭解 Azure 虛擬網路上安裝 Active Directory 網域服務 (AD DS) 的詳細資訊，請參閱[部署 Windows Server Active Directory Azure 虛擬機器上的商務連絡人的方針](https://msdn.microsoft.com/library/azure/jj156090.aspx)。


## <a name="scenario-diagram"></a>案例圖表

在此案例中，您必須存取應用程式在網域的伺服器上執行的外部使用者。 Vm 所執行的應用程式伺服器和 Azure 虛擬網路上所安裝的複本 Dc。 虛擬網路可以連接到內部部署網路[網站-VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md)連線下, 圖所示，或您可以使用[ExpressRoute](../expressroute/expressroute-locations-providers.md)連線速度。

應用程式伺服器和 Dc 部署散佈計算處理的另一個雲端服務中，並[可用性集](../virtual-machines/virtual-machines-windows-manage-availability.md)的改良的容錯能力。
Dc 會使用 Active Directory 複寫複寫彼此與內部部署的 dc。 需要沒有同步處理工具。

![圖表的步驟如下複本 Active Directory 網域控制站 Azure vnet][1]

## <a name="create-an-active-directory-site-for-the-azure-virtual-network"></a>建立 Active Directory 網站的 Azure 虛擬網路

最好代表對應的網路地區虛擬網路 Active Directory 中建立網站。 有助於最佳化驗證、 複寫及其他 DC 位置作業。 下列步驟說明如何建立網站，以及更多背景，請參閱[新增新的網站](https://technet.microsoft.com/library/cc781496.aspx)。

1. 開啟 [Active Directory 網站和服務︰**伺服器管理員** > **工具** > **Active Directory 網站和服務**。
2. 建立網站，以表示您用來建立 Azure 虛擬網路的地區︰ 按一下 [**網站** > **動作** > **新網站**> 輸入新的網站，例如 Azure 美國西部名稱 > 選取的網站連結 > **[確定]**。
3. 建立子網路並使用新的網站建立關聯︰ 按兩下**網站**> 以滑鼠右鍵按一下**子網路** > **新增子網路**> 輸入 IP 位址範圍 （例如案例圖表中 10.1.0.0/16) 的虛擬網路 > 選取新的 Azure 網站] > **[確定]**。

## <a name="create-an-azure-virtual-network"></a>建立 Azure 虛擬網路

1. 在[Azure 傳統入口網站](https://manage.windowsazure.com)中，按一下 [**新增** > **網路服務** > **虛擬網路** > **自訂建立**與使用下列值以完成精靈。

    在此精靈] 頁面上...  | 指定這些值
    ------------- | -------------
    **虛擬網路詳細資料**  | <p>名稱︰ 輸入虛擬網路，例如 WestUSVNet 的名稱。</p><p>地區︰ 選擇最接近的區域。</p>
    **DNS 及 VPN 連線**  | <p>DNS 伺服器︰ 指定的名稱及一或多個內部 DNS 伺服器 IP 位址。</p><p>連線︰ 選取 [**設定] 以網站 VPN**。</p><p>區域網路︰ 指定新的本機網路。</p><p>如果您使用的，而不 VPN ExpressRoute，請參閱[設定 Exchange 提供者所提供的 ExpressRoute 連線](../expressroute/expressroute-locations-providers.md)。</p>
    **連線至網站**  | <p>名稱︰ 輸入內部部署網路的名稱。</p><p>VPN 裝置 IP 位址︰ 指定會連線至虛擬網路的裝置的公用 IP 位址。 Nat 找不到的 VPN 裝置</p><p>位址︰ 指定的地址範圍內部部署網路 （例如 192.168.0.0/16 案例圖表中)。</p>
    **虛擬網路位址空間**  | <p>位址空間︰ 指定您想要執行 （例如案例圖表中 10.1.0.0/16) Azure 虛擬網路中的 Vm 的 IP 位址範圍。 此位址範圍無法與重疊的內部部署網路位址範圍。</p><p>子網路︰ 指定的名稱和地址的應用程式伺服器子網路 (例如 Frontend，10.1.1.0/24) 及網域控制站 (後端，例如 10.1.2.0/24)。</p><p>按一下 [**新增閘道器子網路**。</p>

2. 接下來，您可以設定虛擬網路閘道器至建立安全網站-VPN 連線。 如需相關指示，請參閱[設定虛擬網路閘道器](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md)。
3. 建立新的虛擬網路和內部部署 VPN 裝置之間網站-VPN 連線。 如需相關指示，請參閱[設定虛擬網路閘道器](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md)。


## <a name="create-azure-vms-for-the-dc-roles"></a>建立 Azure Vm 的 DC 角色

重複執行下列步驟，以建立裝載 DC 角色視 Vm。 您應該部署兩個以上的虛擬 Dc 提供容錯能力和重複。 如果 Azure 虛擬網路包含至少兩個同樣設定的 Dc （也就是說，有兩個 Gc，執行的 DNS 伺服器及都不會保留任何 FSMO 角色與等等） 然後將執行的 Dc 設定改良的容錯能力可用性 Vm。
若要建立 Vm 使用 Windows PowerShell，而非 UI，請參閱[建立及預先設定 Windows 型虛擬機器使用 Azure PowerShell](../virtual-machines/virtual-machines-windows-classic-create-powershell.md)。

1. 在[Azure 傳統入口網站](https://manage.windowsazure.com)中，按一下 [**新增** > **計算** > **虛擬機器** > **從圖庫**。 完成精靈中使用下列的值。 除非建議或需要另一個值，請接受設定的預設值。

    在此精靈] 頁面上...  | 指定這些值
    ------------- | -------------
    **選擇圖像**  | Windows Server 2012 R2 資料中心
    **虛擬機器設定**  | <p>虛擬機器名稱︰ 輸入單張標籤名稱 （例如 AzureDC1)。</p><p>新的使用者名稱︰ 輸入使用者名稱。 此使用者會 VM 上的本機管理員群組的成員。 您必須登入 VM 第一次此名稱。 內建的帳戶命名系統管理員將無法運作。</p><p>新密碼與確認︰ 輸入密碼</p>
    **虛擬機器設定**  | <p>雲端服務︰ 選擇 [第一個 vm 的 [<b>建立新的雲端服務</b>，然後選取該相同的雲端服務名稱，當您建立多個 Vm 裝載 DC 角色。</p><p>雲端服務的 DNS 名稱︰ 指定的全域唯一的名稱</p><p>地區/相關性群組/虛擬網路︰ 指定虛擬網路名稱 （例如 WestUSVNet)。</p><p>儲存帳戶︰ 第一個 vm 中選擇 [<b>使用自動產生的儲存空間帳戶</b>，然後選取該相同的儲存體帳戶名稱當您建立多個 Vm 裝載 DC 角色。</p><p>可用性集]: 選擇 [<b>建立可用性設定</b>]。</p><p>可用性設定名稱︰ 輸入可用性設定當您建立的第一個 VM，然後選取 [當您建立多個 Vm 相同名稱的名稱。</p>
    **虛擬機器設定**  | <p>選取 [<b>安裝 VM 代理程式</b>及任何其他您需要的副檔名。</p>
2. 將執行的 DC 伺服器角色每個 vm 附加磁碟。 儲存在 AD 資料庫、 記錄和 SYSVOL 需要額外的磁碟。 指定 （例如 10 GB) 磁碟的大小，並保留**Host （主機） 快取喜好設定**設為 [**無**]。 步驟，請參閱[如何將附加至 Windows 虛擬機器資料磁碟](../virtual-machines/virtual-machines-windows-classic-attach-disk.md)。
3. 第一次登入的 vm 後，開啟 [**伺服器管理員** > **檔案與存放服務**使用 NTFS 磁碟上建立區。
4. 為 Vm 執行 DC 角色中保留的靜態 IP 位址。 若要保留的靜態 IP 位址，下載 Microsoft Web 平台安裝程式並[安裝 PowerShell 的 Azure](../powershell-install-configure.md)及執行設定 AzureStaticVNetIP 指令程式。 例如︰

    「 取得 AzureVM-ServiceName AzureDC1-命名 AzureDC1 |設定 AzureStaticVNetIP IPAddress 10.0.0.4 |更新 AzureVM

如需有關如何設定的靜態 IP 位址的詳細資訊，請參閱[設定靜態 vm 內部 IP 位址](../virtual-network/virtual-networks-reserved-private-ip.md)。

## <a name="install-ad-ds-on-azure-vms"></a>Azure Vm 上安裝 AD DS

登入 VM，並確認 [跨網站-VPN 或 ExpressRoute 連線到資源有連線，在您的內部網路上。 再安裝 AD DS Azure Vm 上。 您可以使用您用於內部部署網路 （UI、 Windows PowerShell 或回應檔案） 安裝的其他 DC 的相同程序。 當您安裝 AD DS，請確定您指定新音量 AD 資料庫、 記錄及 SYSVOL 的位置。 如果您需要以進一步瞭解 AD DS 安裝，請參閱[安裝 Active Directory 網域服務 (層級 100)](https://technet.microsoft.com/library/hh472162.aspx) ，或[安裝複本 Windows Server 2012 網域控制站中現有的網域 (層級 200)](https://technet.microsoft.com/library/jj574134.aspx)。

## <a name="reconfigure-dns-server-for-the-virtual-network"></a>重新設定 DNS 伺服器的虛擬網路

1. 在[Azure 傳統入口網站](https://manage.windowsazure.com)中，按一下虛擬網路的名稱，然後按一下 [**設定**] 索引標籤[重新設定 DNS 伺服器 IP 位址，虛擬網路](../virtual-network/virtual-networks-manage-dns-in-vnet.md)使用指派給 Dc 複本，而不是內部 DNS 伺服器 IP 位址的靜態 IP 位址。

2. 若要確保所有上的複本 DC Vm 虛擬網路設定與使用 DNS 伺服器虛擬網路上，按一下**虛擬機器**、 每個 VM 中，按一下 [狀態] 欄，然後按一下**重新啟動**。 請等待 VM 顯示的**執行**狀態，再嘗試登入它。

## <a name="create-vms-for-application-servers"></a>建立應用程式伺服器 Vm

1. 重複建立 Vm 應用程式伺服器以執行下列步驟。 設定預設值為除非接受建議或需要另一個值。

    在此精靈] 頁面上...  | 指定這些值
    ------------- | -------------
    **選擇圖像**  | Windows Server 2012 R2 資料中心
    **虛擬機器設定**  | <p>虛擬機器名稱︰ 輸入單張標籤名稱 （例如 AppServer1)。</p><p>新的使用者名稱︰ 輸入使用者名稱。 此使用者會 VM 上的本機管理員群組的成員。 您必須登入 VM 第一次此名稱。 內建的帳戶命名系統管理員將無法運作。</p><p>新密碼與確認︰ 輸入密碼</p>
    **虛擬機器設定**  | <p>雲端服務︰ 第一個 vm 中選擇 [**建立新的雲端服務**，然後選取該相同的雲端服務名稱，當您建立多個 Vm 會裝載應用程式。</p><p>雲端服務的 DNS 名稱︰ 指定的全域唯一的名稱</p><p>地區/相關性群組/虛擬網路︰ 指定虛擬網路名稱 （例如 WestUSVNet)。</p><p>儲存帳戶︰ 第一個 vm 中選擇 [**使用自動產生的儲存空間帳戶**，然後選取該相同的儲存體帳戶名稱當您建立多個 Vm 會裝載應用程式。</p><p>可用性設定︰ 選擇 [**建立可用性設定**]。</p><p>可用性設定名稱︰ 輸入可用性設定當您建立的第一個 VM，然後選取 [當您建立多個 Vm 相同名稱的名稱。</p>
    **虛擬機器設定**  | <p>選取 [<b>安裝 VM 代理程式</b>及任何其他您需要的副檔名。</p>

2. 每個 VM 已佈建後，請登入，並加入網域。 在 [**伺服器管理員**] 中，按一下 [**本機伺服器** > **工作群組** > **變更...** 然後選取**網域**，並輸入您的內部部署網域的名稱。 提供的網域使用者，認證，然後重新啟動完成網域加入 VM。

若要建立 Vm 使用 Windows PowerShell，而非 UI，請參閱[建立及預先設定 Windows 型虛擬機器使用 Azure PowerShell](../virtual-machines/virtual-machines-windows-classic-create-powershell.md)。

如需有關如何使用 Windows PowerShell 的詳細資訊，請參閱[開始使用 Azure Cmdlet](https://msdn.microsoft.com/library/azure/jj554332.aspx)和[Azure Cmdlet 參考](https://msdn.microsoft.com/library/azure/jj554330.aspx)。

## <a name="additional-resources"></a>其他資源

-  [部署 Windows Azure 虛擬機器上的伺服器 Active Directory 的指導方針](https://msdn.microsoft.com/library/azure/jj156090.aspx)
-  [如何上傳現有的內部部署 Azure HYPER-V 網域控制站使用 PowerShell 的 Azure](http://support.microsoft.com/kb/2904015)
-  [Azure 虛擬網路上安裝新的 Active Directory 樹系](../active-directory/active-directory-new-forest-virtual-machine.md)
-  [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)
-  [Microsoft Azure IT 專業人員 IaaS: (01) 的虛擬機器基本概念](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
-  [Microsoft Azure IT 專業人員 IaaS: (05) 建立虛擬網路與跨內部部署連線](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
-  [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
-  [Azure 管理 Cmdlet](https://msdn.microsoft.com/library/azure/jj152841)

<!--Image references-->
[1]: ./media/active-directory-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png
