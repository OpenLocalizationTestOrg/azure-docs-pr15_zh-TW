<properties 
   pageTitle="失敗後的 VMware 虛擬機器和內部部署網站的實體伺服器 |Microsoft Azure"
   description="深入了解後移轉後的 VMware Vm 回內部部署網站失敗，且 Azure 實體伺服器。" 
   services="site-recovery" 
   documentationCenter="" 
   authors="ruturaj" 
   manager="mkjain" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required" 
   ms.date="08/22/2016"
   ms.author="ruturajd"/>

# <a name="fail-back-vmware-virtual-machines-and-physical-servers-to-the-on-premises-site"></a>返回 VMware 虛擬機器和內部部署網站的實體伺服器失敗

> [AZURE.SELECTOR]
- [Azure 入口網站](site-recovery-failback-azure-to-vmware.md)
- [Azure 傳統入口網站](site-recovery-failback-azure-to-vmware-classic.md)
- [Azure 傳統入口網站 （舊版）](site-recovery-failback-azure-to-vmware-classic-legacy.md)



本文說明如何失敗回 Azure 虛擬機器 Azure 從內部部署網站。 當您準備好要回復 VMware 虛擬機器或 Windows/Linux 實體伺服器之後他們已移轉來自內部部署網站以使用此[教學課程](site-recovery-vmware-to-azure-classic.md)的 Azure 時，請遵循本文中的指示進行。



## <a name="overview"></a>概觀

此圖顯示回復架構，這種情況。

當程序伺服器內部部署，而且您使用的 ExpressRoute 時，請使用此架構。

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

當程序伺服器上 Azure，而且必須要有 vpn 才能或 ExpressRoute 連線時，請使用此架構。

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)

若要查看完整的連接埠和回復清單 architechture 圖表請參閱下方的圖像

![](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

回復的運作方式如下︰

- 您已無法透過 Azure 之後，會失敗返回您內部部署的網站，在幾個階段︰
    - **階段 1**︰ 開始複寫回到 VMware Vm 執行您的內部部署網站中，重新將 Azure Vm。 啟用 reprotection VM 進入回復保護群組您最初建立容錯移轉保護群組時自動建立的。 如果您新增到復原方案然後回復保護群組容錯移轉保護群組也會自動新增到計劃。  在重新將您指定如何規劃回失敗。
    - **階段 2**︰ 您 Azure Vm 複寫至您的內部部署網站之後，您會失敗透過執行從 Azure 失敗。
    - **階段 3**︰ 回失敗資料後，您重新將您無法以，內部部署 Vm 開始複寫到 Azure。

> [AZURE.VIDEO enhanced-vmware-to-azure-failback]


### <a name="failback-to-the-original-or-alternate-location"></a>回復到原來或其他位置

如果您無法透過 VMware VM，您可能會失敗回到相同的來源 VM 如果仍然存在內部部署。 在這個案例中 delta 變更將回失敗。 請注意︰

- 如果您無法在實體伺服器然後回復永遠是新的 VMware VM。
    - 回失敗實體機器前請注意，
    - 傳回的虛擬機器時無法透過從 Azure VMware 是受保護的實際機器
    - 請確定您探索至少有一個主要目標伺服器，以及您需要以回復需 ESX/ESXi 主機。
- 如果您無法回到原始的 VM 是必要︰
    - 如果 VM 由 vCenter 伺服器管理然後母片目標 ESX 主機應有存取權 Vm 資料存放區。
    - 如果 VM ESX 主機上，但不由 vCenter 管理 VM 的硬碟必須在資料存放區存取細明體的主機。
    - 如果您 VM ESX 主機上，而不是使用 vCenter 您應該完成 ESX 主機細明體的探索您重新將之前。 這適用於您正在太失敗回實體伺服器。
    - 其他選項 （如果存在的內部部署 VM） 是回復前請先將其刪除。 然後回復則會建立新的 VM 主機為主要目標 ESX 主機上。
    
- 當您回復到另一個位置資料將會還原成相同的資料存放區，並為內部部署主要目標伺服器使用相同的 ESX 主機。 


## <a name="prerequisites"></a>必要條件

- 您必須以失敗 VMware 環境回復 VMware Vm 和實體伺服器。 無法在實體伺服器不支援。
- 若要回復應該有時所建立 Azure 網路您剛開始設定保護。 回復必須要有 vpn 才能或 ExpressRoute 從 Azure 中 Azure Vm 位於至內部部署網站的網路連線。
- 如果您想要回復至受 vCenter 伺服器，必須先確定 Vm 必須必要的權限的探索 Vm vCenter 伺服器上。 [閱讀更多](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)。
- 如果快照上 VM reprotection 將會失敗。 您可以刪除快照或磁碟。 
- 您在回失敗之前必須建立多個元件︰
    - **建立 Azure 中的程序伺服器**。 這是您必須建立並繼續執行期間回復 Azure VM。 回復完成後，您可以刪除電腦。
    - **建立主版目標伺服器**︰ 主要目標伺服器傳送及接收回復資料。 管理伺服器建立內部部署具有預設安裝的主要目標伺服器。 不過，視的失敗返回流量而定，您可能需要建立另一個主要目標伺服器回復的。
    - 如果您想要建立 Linux 上執行的其他主要目標伺服器，，必須設定 Linux VM 安裝主要目標伺服器，如下所述。
- 設定伺服器執行回復時所需的內部部署。 期間回復，虛擬機器必須存在於失敗的無法回復會成功設定 server 資料庫。 因此確保您一般排定的備份您的伺服器。 若損毀，您必須將它還原有相同的 IP 位址，以便回復都將能夠運作。

## <a name="set-up-the-process-server-in-azure"></a>將 Azure 中的程序伺服器設定

您必須安裝 Azure 中的程序伺服器，以便 Azure Vm 可以將資料傳送回內部部署的主要目標伺服器。 

1.  在網站復原入口網站 >**設定伺服器**選取您要新增新的程序伺服器。

    ![](./media/site-recovery-failback-azure-to-vmware-classic/ps1.png)

2.  指定程序的伺服器名稱，然後輸入名稱和密碼，您會使用連線至 Azure VM 以系統管理員。 在 [**設定伺服器**選取內部部署管理伺服器，並指定應該部署程序 server Azure 網路。 應該是 Azure Vm 位於網路。 指定唯一的 IP 位址，從選取的子網路，並開始部署]。

    ![](./media/site-recovery-failback-azure-to-vmware-classic/ps2.png)

    將會觸發部署程序伺服器工作

    ![](./media/site-recovery-failback-azure-to-vmware-classic/ps3.png)

    Azure 中部署程序伺服器之後您可以登入並使用您所指定的認證。 第一次您登入程序伺服器] 對話方塊便會執行。 輸入內部部署管理伺服器與其複雜密碼的 IP 位址。 保留預設連接埠 443 設定。 您可以也將預設資料複寫 9443 連接埠除非您特別修改這項設定，當您將內部部署管理伺服器設定。 

    >[AZURE.NOTE] 伺服器不會顯示在**VM 屬性**] 下。 它只會顯示在 [管理伺服器的已登錄中的 [**伺服器**] 索引標籤下。 需要有關顯示程序伺服器 10-15 分鐘。


## <a name="set-up-the-master-target-server-on-premises"></a>設定主要目標 server 內部部署

主要目標伺服器接收回復資料。 主要目標伺服器會自動安裝內部部署管理伺服器上，但如果回失敗大量資料您可能需要設定的其他主要目標伺服器。 執行下列步驟，如下所示︰

>[AZURE.NOTE] 如果您想要安裝 Linux 母片的目標伺服器，請依照下一個程序中的指示進行。

1. 如果您在 Windows 上安裝主要目標伺服器，請從 VM 要在其安裝在主要目標伺服器，並將安裝檔案下載 Azure 網站修復整合安裝精靈中開啟 [快速入門] 頁面。
2. 執行安裝程式，並在**您開始之前，**選取 [**新增額外的程序伺服器擴充部署**。
3. 當您所做的相同方式完成精靈您[設定 「 管理伺服器](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server)。 在 [**設定伺服器詳細資料**] 頁面中，指定這個主要目標伺服器，以及存取 VM 複雜密碼的 IP 位址。

### <a name="set-up-a-linux-vm-as-the-master-target-server"></a>Linux VM 設為主要目標伺服器
若要設定為您需要安裝西非中部 Linux VM 執行主要目標伺服器管理伺服器） S 6.6 最小作業系統，擷取 SCSI 識別碼，每個 SCSI 硬碟、 安裝一些額外的套件，並套用一些自訂的變更。

#### <a name="install-centos-66"></a>安裝 CentOS 6.6

1.  管理伺服器 VM 安裝 CentOS 6.6 最小的作業系統。 保留 ISO DVD 磁碟機中，並啟動系統。 略過媒體測試，選取 [英文 （美國） 在語言、 選取**基本存放裝置**，檢查硬碟不任何重要資料，並按一下 [**是**]，捨棄任何資料。 輸入 「 管理伺服器主機名稱，然後選取 [伺服器網路介面卡。  在 [**編輯系統**] 對話方塊中選取**自動連線**並新增的靜態 IP 位址、 網路和 DNS 設定。 指定時區，以及根密碼來存取 [管理伺服器。 
2.  當詢問您的安裝類型時您想要的磁碟分割選取**建立的自訂版面配置**。 按一下後**下一步**選取 [**無限制**] 並按一下 [建立]。 建立**/**， **/var/crash**和**/ 首頁磁碟分割區**以**FS 類型︰** **ext4**。 建立為交換 partion **FS 類型︰ 交換**。
3.  如果找不到會出現警告訊息的現有的裝置。 按一下 [**格式**]，以設定格式的磁碟分割設定的磁碟機]。 按一下 [**撰寫變更到磁碟**分割變更套用]。
4.  選取 [**安裝開機載入器** > **下一步**根磁碟分割上安裝開機載入器。
5.  安裝完成時請按一下 [**重新啟動**]。


#### <a name="retrieve-the-scsi-ids"></a>擷取 SCSI 識別碼

1. 安裝後擷取 SCSI 識別碼，每個 SCSI 硬碟 VM 中。 若要執行此管理伺服器 VM 關機]，在 VMware VM 屬性中以滑鼠右鍵按一下 VM 項目 >**編輯設定** > **選項**。
2. 選取 [**進階** > **一般的項目**，按一下 [**設定參數**。 當電腦正在執行時，此選項會 de-active。 若要使其成為使用電腦必須關閉。
3. 如果列**磁碟。EnableUUID**存在確認設定值，則為**True** （區分大小寫）。 如果已為您可以取消並測試內來賓作業系統 SCSI 命令後開機。 
4.  如果現有的資料列無法按一下 [**新增列**– 並將其新增**為 True**的值。 不要使用雙引號中。

#### <a name="install-additional-packages"></a>安裝其他套件

您需要下載並安裝一些額外的套件。 

1.  請確定主要目標伺服器連線至網際網路。
2.  執行此命令，以從下載並安裝 15 封包 CentOS 存放庫: **# yum 安裝 – y xfsprogs perl lsscsi rsync wget kexec-工具**。
3.  如果您要保護的來源電腦執行的 Linux 機智 Reiser 或 XFS 檔案系統根或開機裝置，您應該下載並安裝其他封包，如下所示︰

    - # <a name="cd-usrlocal"></a>cd /usr/local
    - # <a name="wget-httpelrepoorglinuxelrepoel6x8664rpmskmod-reiserfs-00-1el6elrepox8664rpmhttpelrepoorglinuxelrepoel6x8664rpmskmod-reiserfs-00-1el6elrepox8664rpm"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
    - # <a name="wget-httpelrepoorglinuxelrepoel6x8664rpmsreiserfs-utils-3621-1el6elrepox8664rpmhttpelrepoorglinuxelrepoel6x8664rpmsreiserfs-utils-3621-1el6elrepox8664rpm"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
    - # <a name="rpm-ivh-kmod-reiserfs-00-1el6elrepox8664rpm-reiserfs-utils-3621-1el6elrepox8664rpm"></a>轉速 – ivh kmod reiserfs-0.0 1.el6.elrepo.x86_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm
    - # <a name="wget-httpmirrorcentosorgcentos66osx8664packagesxfsprogs-311-16el6x8665rpmhttpmirrorcentosorgcentos66osx8664packagesxfsprogs-311-16el6x8665rpm"></a>wget [http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
    - # <a name="rpm-ivh-xfsprogs-311-16el6x8664rpm"></a>轉速 – ivh xfsprogs 3.1.1 16.el6.x86_64.rpm

#### <a name="apply-custom-changes"></a>套用自訂的變更

執行下列動作，以套用自訂的變更之後您已, 完成安裝後的步驟，安裝套件︰

1.  複製 VM RHEL 6-64 整合代理程式二進位。 執行此命令以 untar 二進位︰ **tar – zxvf <file name> **
2.  執行此命令授與權限: **# chmod 755./ApplyCustomChanges.sh**
3.  執行指令碼: **#./ApplyCustomChanges.sh**。 您只一次執行指令碼。 順利執行指令碼之後，請重新啟動伺服器。


## <a name="run-the-failback"></a>執行回復

### <a name="reprotect-the-azure-vms"></a>重新將 Azure Vm

1.  在網站復原入口網站 >**機器**] 索引標籤上選取的移轉，然後按一下 [**重新保護**VM。
2.  在 [**主要目標伺服器**及**程序伺服器**選取內部部署的主要目標伺服器和 Azure VM 程序伺服器。
3.  選取您設定的 vm 連線的帳戶。
4.  選取 [保護] 群組的回復版本。 範例如果 VM 受到保護的 PG1 然後您必須選取 PG1_Failback。
5.  如果您想要復原到另一個位置，選取保留磁碟機和設定主要目標伺服器的資料存放區。 當您無法回內部部署網站 VMware Vm 回復保護計劃中，會使用相同的資料存放區作為主要目標伺服器。 如果您想要復原複本至相同的 Azure VM 內部部署 VM 內部部署 VM 應該已經是作為主要目標伺服器相同的資料存放區中。 如果不有任何 VM 內部部署 reprotection 期間會建立一個新。

    ![](./media/site-recovery-failback-azure-to-vmware-classic/failback1.png)

6.  之後您按一下**[確定**] 以開始 reprotection 工作開始 VM 從 Azure 複寫內部部署網站。 在 [**工作**] 索引標籤上，您可以追蹤進度。

    ![](./media/site-recovery-failback-azure-to-vmware-classic/failback2.png)

### <a name="run-a-failover-to-the-on-premises-site"></a>執行容錯移轉至內部網站

之後 reprotection VM 會移至其 [保護] 群組的回復版本，並會自動新增至您用於在容錯移轉至 Azure 如果有的話復原計劃。

1.  在 [**復原方案**] 頁面選取復原計劃包含回復群組並按一下**容錯移轉** > **意外的容錯移轉**。
2.  在 [**確認容錯移轉**驗證的容錯移轉方向 （Azure)，然後選取您想要使用的容錯移轉 （最新） 的復原點。 如果您設定複寫屬性時，啟用**多重 VM**就可以復原的最新的應用程式或損毀一致的復原點。 按一下 [核取記號，以開始容錯移轉]。
3.  在容錯移轉網站復原會關閉 Azure Vm。 您核取該回復已完成，如您預期後可以您可以檢查的 Azure Vm 關閉如預期般。

### <a name="reprotect-the--on-premises-site"></a>重新將內部部署網站

回復完成後資料回內部部署網站上，但不會受到保護。 若要開始複寫到 Azure 再次執行下列動作︰

1.  在網站復原入口網站 > 返回**機器**] 索引標籤選取 Vm 失敗，然後按一下 [**重新保護**。 
2.  驗證該複寫之後 Azure 未能正常運作，您可以刪除 Azure Vm （目前未執行） 已回失敗 Azure 中。


### <a name="common-issues-in-failback"></a>在 [回復常見的問題

1. 如果您執行唯讀使用者 vCenter 探索及保護虛擬機器，成功和容錯移轉能夠運作。 重新將時，它將會失敗之後無法找到 datastores。 為症狀不會看到列出時重新保護 datastores。 若要解決這個問題，您可以使用適當權限的帳戶更新 vCenter 認證，並再次工作。 [了解更多](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)
2. 當您回復 Linux VM 並執行內部部署，您會看到網路管理員套件，從電腦解除安裝。 這是因為網路管理員套件時 VM 復原 Azure 中時，會移除。
3. 當 VM 靜態 IP 位址的設定，並無法透過 Azure 時，透過 DHCP 取得 IP 位址。 當您無法移回內部部署時，VM 繼續使用 DHCP 取得 IP 位址。 您必須以手動方式登入電腦和 IP 位址改回為靜態位址，如有必要的設定。
4. 如果您使用 ESXi 5.5 免費 edition 或 vSphere 6 Hypervisor 免費版，會成功容錯移轉，但回復會失敗。 您將會升級至啟用回復任一評估授權 ned。

## <a name="failing-back-with-expressroute"></a>故障後的 ExpressRoute

您可以返回透過 VPN 連線或 Azure ExpressRoute 失敗。 如果您想要使用 ExpressRoute 注意下列動作︰

- ExpressRoute 應該會在哪一個來源機器會失敗，以及哪些 Azure Vm 位於之後發生錯誤後移轉 Azure 虛擬網路上的設定。
- Azure 儲存體帳戶公用的端點上複製的資料。 您應該設定與使用 ExpressRoute 網站復原複寫的目標資料中心的 ExpressRoute 公用外面。



