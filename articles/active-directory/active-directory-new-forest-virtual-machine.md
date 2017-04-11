<properties
    pageTitle="Azure 虛擬網路上安裝的 Active Directory 樹系 |Microsoft Azure"
    description="說明如何建立新的 Active Directory 樹系 Azure 虛擬網路上的虛擬機器 (VM) 教學課程。"
    services="active-directory, virtual-network"
    keywords="active directory 虛擬機器，安裝 active directory 樹系，azure active directory 影片 "
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    tags=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/10/2016"
    ms.author="markusvi"/>


# <a name="install-a-new-active-directory-forest-on-an-azure-virtual-network"></a>Azure 虛擬網路上安裝新的 Active Directory 樹系

本主題說明如何建立新的 Windows Server Active Directory 環境 Azure 虛擬機器 (VM) 虛擬網路上[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)上。 在此情況下，Azure 虛擬網路未連線到內部部署的網路。

您也可能會感興趣的下列相關主題︰

- 視訊顯示這些步驟，請參閱[如何安裝新的 Active Directory 樹系 Azure 虛擬網路上](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
- 您也可以[設定網站-VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) ，並在 [安裝新樹系或擴充 Azure 虛擬網路的內部部署樹系。 這些步驟，請參閱[安裝複本 Active Directory 網域控制站 Azure 虛擬網路中](../active-directory/active-directory-install-replica-active-directory-domain-controller.md)。
-  概念性瞭解 Azure 虛擬網路上安裝 Active Directory 網域服務 (AD DS) 的詳細資訊，請參閱[部署 Windows Server Active Directory Azure 虛擬機器上的商務連絡人的方針](https://msdn.microsoft.com/library/azure/jj156090.aspx)。

## <a name="scenario-diagram"></a>案例圖表

在此案例中，您必須存取應用程式在網域的伺服器上執行的外部使用者。 執行應用程式伺服器 Vm Vm 執行網域控制站的安裝及自己 Azure 虛擬網路中的雲端服務中安裝。 這也是包含設定改良的容錯能力的可用性。

![虛擬機器中 Azure 虛擬網路上的 active Directory 樹系][1]7
## <a name="how-does-this-differ-from-on-premises"></a>這有何內部部署？

不太多 Azure 上安裝網域控制站，與內部部署的差異。 下表列出的主要差異。

若要設定...  | 內部部署  | Azure 虛擬網路
------------- | -------------  | ------------
**網域控制站的 IP 位址**  | 指定網路介面卡屬性的靜態 IP 位址   | 若要指派的靜態 IP 位址設定 AzureStaticVNetIP 指令程式
**DNS 用戶端解析程式**  | 網路介面卡的網域成員屬性設定慣用和其他 DNS 伺服器位址   | 設定 DNS 伺服器位址的虛擬網路屬性
**Active Directory 資料庫儲存空間**  | 您也可以從 C:\ 變更預設儲存位置  | 您需要從 C:\ 變更預設儲存位置



## <a name="create-an-azure-virtual-network"></a>建立 Azure 虛擬網路

1. Azure 傳統入口網站登入。
2. 建立虛擬網路。 按一下 [**網路** > **建立虛擬網路**。 使用下表中的值，以完成精靈。

    在此精靈] 頁面上...  | 指定這些值
    ------------- | -------------
    **虛擬網路詳細資料**  | <p>名稱︰ 輸入您的虛擬網路的名稱</p><p>地區︰ 選擇最接近的區域</p>
    **DNS 及 VPN**  | <p>將 [DNS 伺服器] 保留空白</p><p>未選取 [VPN] 選項</p>
    **虛擬網路位址空間**  | <p>子網路名稱︰ 輸入您子網路的名稱</p><p>開始 IP: <b>10.0.0.0</b></p><p>CIDR: <b>/24 (256)</b></p>



## <a name="create-vms-to-run-the-domain-controller-and-dns-server-roles"></a>建立 Vm 執行的網域和 DNS 伺服器角色

重複執行下列步驟，以建立裝載 DC 角色視 Vm。 您應該部署兩個以上的虛擬 Dc 提供容錯能力和重複。 如果 Azure 虛擬網路包含至少兩個同樣設定的 Dc （也就是說，有兩個 Gc，執行的 DNS 伺服器及都不會保留任何 FSMO 角色與等等） 然後將執行的 Dc 設定改良的容錯能力可用性 Vm。

若要建立 Vm 使用 Windows PowerShell，而非 UI，請參閱[建立及預先設定 Windows 型虛擬機器使用 Azure PowerShell](../virtual-machines/virtual-machines-windows-classic-create-powershell.md)。

1. 在 [傳統] 入口網站，按一下 [**新增** > **計算** > **虛擬機器** > **從圖庫**。 完成精靈中使用下列的值。 除非建議或需要另一個值，請接受設定的預設值。

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

## <a name="install-windows-server-active-directory"></a>安裝 Windows Server Active Directory

使用[安裝 AD DS](https://technet.microsoft.com/library/jj574166.aspx)您使用內部部署的相同常式 （也就是您可以使用 UI、 回應檔案或 Windows PowerShell）。 您需要提供安裝新的樹系的系統管理員認證。 若要指定 Active Directory 資料庫、 記錄和 SYSVOL 的位置，您所附加的 vm 的其他資料磁碟從作業系統磁碟機變更預設儲存位置。

DC 安裝完成後，請再次連線至 VM 並登入，亦即。 請記得要指定網域認證。

## <a name="reset-the-dns-server-for-the-azure-virtual-network"></a>重設為 Azure 虛擬網路的 DNS 伺服器

1. 重設 DNS 轉寄站設定新的 DC/DNS 伺服器上。
  1. 在 [伺服器管理員] 中，按一下 [**工具]** > **DNS**。
  2. 在**DNS 管理員**中，以滑鼠右鍵按一下 [DNS 伺服器的名稱，然後按一下 [**內容**。
  3. 在 [**轉寄站**] 索引標籤上按一下 [轉寄站的 IP 位址，然後按一下 [**編輯**]。  選取的 IP 位址，然後按一下 [**刪除**]。
  4. 按一下**[確定]**以關閉編輯器] 和 [**確定**] 以關閉 [DNS 伺服器內容。
2. 更新虛擬網路的 DNS 伺服器設定。
  1. 按一下 [**虛擬網路**> 按兩下您所建立的虛擬網路 >**設定** > **DNS 伺服器**，輸入名稱] 和 [其中一個 Vm 執行 DC/DNS 伺服器角色 DIP，然後按一下 [**儲存**。
  2. 選取 VM，然後按一下 [**重新啟動**觸發 VM 設定 DNS 解析程式設定新的 DNS 伺服器的 IP 位址。


## <a name="create-vms-for-domain-members"></a>建立網域成員 Vm

1. 重複建立 Vm 應用程式伺服器以執行下列步驟。 除非建議或需要另一個值，請接受設定的預設值。

    在此精靈] 頁面上...  | 指定這些值
    ------------- | -------------
    **選擇圖像**  | Windows Server 2012 R2 資料中心
    **虛擬機器設定**  | <p>虛擬機器名稱︰ 輸入單張標籤名稱 （例如 AppServer1)。</p><p>新的使用者名稱︰ 輸入使用者名稱。 此使用者會 VM 上的本機管理員群組的成員。 您必須登入 VM 第一次此名稱。 內建的帳戶命名系統管理員將無法運作。</p><p>新密碼與確認︰ 輸入密碼</p>
    **虛擬機器設定**  | <p>雲端服務︰ 第一個 vm 中選擇 [**建立新的雲端服務**，然後選取該相同的雲端服務名稱，當您建立多個 Vm 會裝載應用程式。</p><p>雲端服務的 DNS 名稱︰ 指定的全域唯一的名稱</p><p>地區/相關性群組/虛擬網路︰ 指定虛擬網路名稱 （例如 WestUSVNet)。</p><p>儲存帳戶︰ 第一個 vm 中選擇 [**使用自動產生的儲存空間帳戶**，然後選取該相同的儲存體帳戶名稱當您建立多個 Vm 會裝載應用程式。</p><p>可用性集]: 選擇 [**建立可用性設定**]。</p><p>可用性設定名稱︰ 輸入可用性設定當您建立的第一個 VM，然後選取 [當您建立多個 Vm 相同名稱的名稱。</p>
    **虛擬機器設定**  | <p>選取 [<b>安裝 VM 代理程式</b>及任何其他您需要的副檔名。</p>
2. 每個 VM 已佈建後，請登入，並加入網域。 在 [**伺服器管理員**] 中，按一下 [**本機伺服器** > **工作群組** > **變更...** 然後選取**網域**，並輸入您的內部部署網域的名稱。 提供的網域使用者，認證，然後重新啟動完成網域加入 VM。

若要建立 Vm 使用 Windows PowerShell，而非 UI，請參閱[建立及預先設定 Windows 型虛擬機器使用 Azure PowerShell](../virtual-machines/virtual-machines-windows-classic-create-powershell.md)。

如需有關如何使用 Windows PowerShell 的詳細資訊，請參閱[開始使用 Azure Cmdlet](https://msdn.microsoft.com/library/azure/jj554332.aspx)和[Azure Cmdlet 參考](https://msdn.microsoft.com/library/azure/jj554330.aspx)。


## <a name="see-also"></a>另請參閱

-  [如何安裝新的 Active Directory 樹系 Azure 虛擬網路上](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
-  [部署 Windows Azure 虛擬機器上的伺服器 Active Directory 的指導方針](https://msdn.microsoft.com/library/azure/jj156090.aspx)

-  [設定網站-VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md)
-  [安裝 Azure 虛擬網路中的一個複本 Active Directory 網域控制站](../active-directory/active-directory-install-replica-active-directory-domain-controller.md)
-  [Microsoft Azure IT 專業人員 IaaS: (01) 的虛擬機器基本概念](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
-  [Microsoft Azure IT 專業人員 IaaS: (05) 建立虛擬網路與跨內部部署連線](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
-  [虛擬網路概觀](../virtual-network/virtual-networks-overview.md)
-  [如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)
-  [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
-  [Azure Cmdlet 參考](https://msdn.microsoft.com/library/azure/jj554330.aspx)
-  [設定 Azure VM 靜態 IP 位址](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)
-  [如何指派 Azure VM 靜態 IP](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)
-  [安裝新的 Active Directory 樹系](https://technet.microsoft.com/library/jj574166.aspx)
-  [Active Directory 網域服務 (AD DS) 虛擬化 (層級 100) 簡介](https://technet.microsoft.com/library/hh831734.aspx)

<!--Image references-->
[1]: ./media/active-directory-new-forest-virtual-machine/AD_Forest.png
