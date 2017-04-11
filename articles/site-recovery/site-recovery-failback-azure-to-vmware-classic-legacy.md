<properties 
   pageTitle="失敗後的 VMware 虛擬機器和 azure 實體伺服器 VMware （舊版） |Microsoft Azure" 
   description="本文將說明如何回復已複寫到 Azure 網站復原 Azure 虛擬機器 VMware。" 
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
   ms.workload="storage-backup-recovery" 
   ms.date="10/05/2016"
   ms.author="ruturajd@microsoft.com"/>

# <a name="fail-back-vmware-virtual-machines-and-physical-servers-from-azure-to-vmware-with-azure-site-recovery-legacy"></a>失敗後的 VMware 虛擬機器和 azure 實體伺服器 VMware Azure 網站復原 （舊版）

> [AZURE.SELECTOR]
- [Azure 入口網站](site-recovery-failback-azure-to-vmware.md)
- [Azure 傳統入口網站](site-recovery-failback-azure-to-vmware-classic.md)
- [Azure 傳統入口網站 （舊版）](site-recovery-failback-azure-to-vmware-classic-legacy.md)


Azure 網站復原服務佔業務連續性和損壞復原 (BCDR) 策略來協調複寫、 錯誤移轉及復原的虛擬機器和實體伺服器。 Azure，或第二個內部部署資料中心，可將複寫機器。 概述讀[什麼是 Azure 網站復原？](site-recovery-overview.md)

## <a name="overview"></a>概觀

本文將說明如何失敗返回 VMware 虛擬機器和 Windows/Linux 實體伺服器 azure 至您的內部部署網站之後已從您的內部部署網站複寫至 Azure。

>[AZURE.NOTE] 本文將說明舊版的案例。 您只應該使用本文中的指示，如果您將複寫到 Azure 使用[這些舊版的指示進行](site-recovery-vmware-to-azure-classic-legacy.md)。 如果您設定使用[增強型的部署](site-recovery-vmware-to-azure-classic-legacy.md)的試驗，請遵循[本文](site-recovery-failback-azure-to-vmware-classic.md)中的指示，回失敗。 


## <a name="architecture"></a>架構

此圖表代表錯誤移轉及回復案例。 藍色行會容錯移轉時所用的連線。 紅線是回復時所用的連線。 以箭號線條移透過網際網路。

![](./media/site-recovery-failback-azure-to-vmware/vconports.png)

## <a name="before-you-start"></a>在您開始之前 

- 您應該移到您的 VMware Vm 或實體伺服器失敗，他們應該執行 Azure 中。
- 請注意，您可以只失敗返回 VMware 虛擬機器和 Windows/Linux 實體伺服器 azure VMware 虛擬機器中的內部部署的主要網站。  如果您失敗實體機器，容錯移轉至 Azure 會將它轉換成 Azure VM，並以 VMware 回復會將它轉換成 VMware VM。

以下是設定回復︰

1. **設定回復元件**︰ 您必須設定 vContinuum 伺服器於內部部署，並指向設定伺服器 VM Azure 中。 您會也設定程序伺服器為 Azure VM 傳送資料至內部部署主要目標伺服器。 您可以註冊程序伺服器與處理容錯移轉設定伺服器。 您安裝的內部部署主要目標伺服器。 如果您需要 Windows 主要目標伺服器它會自動設定當您安裝 vContinuum。 如果您需要 Linux 必須進行設定以手動方式在不同的伺服器上。
2. **啟用保護與回復**︰ 您已設定元件之後，在 vContinuum 中必須啟用透過 Azure Vm 失敗的保護。 您會執行 Vm 的整備檢查，並執行 Azure 容錯移轉至您的內部部署網站。 回復完成後您重新開始複寫到 Azure 將內部部署電腦。



## <a name="step-1-install-vcontinuum-on-premises"></a>步驟 1︰ 安裝 vContinuum 內部部署

您需要安裝 vContinuum 伺服器內部部署，並指向設定伺服器。

1.  [下載 vContinuum](http://go.microsoft.com/fwlink/?linkid=526305)。 
2.  然後，下載[vContinuum 更新](http://go.microsoft.com/fwlink/?LinkID=533813)版本。
3. 安裝最新版的 vContinuum。 在 [**歡迎**] 頁面中，按一下 [**下一步**]。
    ![](./media/site-recovery-failback-azure-to-vmware/image2.png)
4.  在精靈的第一頁上指定 CX 伺服器 IP 位址和 CX 伺服器連接埠。 選取 [**使用 HTTPS**。

    ![](./media/site-recovery-failback-azure-to-vmware/image3.png)

5.  Azure 中的 [**儀表板**] 索引標籤的 [設定伺服器 VM 上找到設定伺服器 IP 位址。
    ![](./media/site-recovery-failback-azure-to-vmware/image4.png)

6.  Azure 中尋找設定伺服器設定伺服器 VM 的**結束點**] 索引標籤上的 HTTPS 公用連接埠。

    ![](./media/site-recovery-failback-azure-to-vmware/image5.png)

7. 在 [ **CS 複雜密碼的詳細資料**頁面上指定登錄設定伺服器時，您情形下複雜密碼。 如果您不記得將其存回其**c:\\Program Files (x86)\\InMage 系統\\私人\\connection.passphrase** VM 設定伺服器上。

    ![](./media/site-recovery-failback-azure-to-vmware/image6.png)

8.  在 [**選取目的地位置]**頁面上指定您要安裝 vContinuum 伺服器，並按一下 [**安裝**]。

    ![](./media/site-recovery-failback-azure-to-vmware/image7.png)

9. 安裝完成後，便可啟動 vContinuum。
    ![](./media/site-recovery-failback-azure-to-vmware/image8.png)


## <a name="step-2-install-a-process-server-in-azure"></a>步驟 2: Azure 中安裝程序伺服器 

您必須安裝 Azure 中的程序伺服器，以便 Azure 中的 Vm 可以將資料傳送回內部部署的主要目標伺服器。 

1.  Azure 中的 [**設定伺服器**] 頁面上選取您要新增新的程序伺服器。

    ![](./media/site-recovery-failback-azure-to-vmware/image9.png)

2.  指定程序的伺服器名稱，以及名稱和密碼連線至虛擬機器以系統管理員身分。 選取您要註冊程序伺服器的設定伺服器。 應該是您用來保護並無法透過虛擬機器相同的伺服器。
3.  指定應該部署程序伺服器的 Azure 網路。 它必須設定伺服器相同的網路。 指定唯一的 IP 位址選取子網路，並開始部署]。

    ![](./media/site-recovery-failback-azure-to-vmware/image10.png)

4.  部署程序伺服器觸發工作。

    ![](./media/site-recovery-failback-azure-to-vmware/image11.png)

5.  Azure 中部署程序伺服器之後您可以登入伺服器使用您所指定的認證。 註冊程序伺服器註冊內部部署的程序伺服器的方式相同。 

    ![](./media/site-recovery-failback-azure-to-vmware/image12.png)

>[AZURE.NOTE] 註冊回復期間的伺服器不會顯示在網站復原 VM 屬性] 下。 他們的年齡只顯示其正在註冊設定伺服器的**伺服器**] 索引標籤下。 需要瞭解 10-15 分鐘直到這些程序伺服器會出現在索引標籤。


## <a name="step-3-install-a-master-target-server-on-premises"></a>步驟 3︰ 安裝主要目標 server 內部部署

根據您來源虛擬機器的作業系統，您必須安裝 Linux 或 Windows 主要目標 server 內部部署。

### <a name="deploy-a-windows-master-target-server"></a>部署 Windows 主要目標伺服器

主要目標已經一起 vContinuum 安裝程式在 Windows。 當您安裝 vContinuum 時，主要伺服器也是部署在同一部電腦上，而且註冊設定伺服器。

1.  若要開始部署，建立空電腦內部部署在您要從 Azure 復原 Vm ESX 主機上。

2.  請確定有兩個以上的磁碟連接到 VM – 使用的作業系統和其他用於保留磁碟機。

3.  安裝作業系統。

4.  在伺服器上安裝 vContinuum。 這也會完成安裝母片的目標伺服器。

### <a name="deploy-a-linux-master-target-server"></a>部署 Linux 主要目標伺服器

1.  若要開始部署，建立空電腦內部部署在您要從 Azure 復原 Vm ESX 主機上。

2.  請確定有兩個以上的磁碟連接到 VM – 使用的作業系統和其他用於保留磁碟機。

3.  安裝 Linux 作業系統。 Linux 母片的目標系統不應使用 LVM 根或保留儲存空間。 若要避免 LVM 磁碟分割區/磁碟探索預設設定 Linux 主要目標伺服器。
4.  您可以建立的磁碟分割︰

    ![](./media/site-recovery-failback-azure-to-vmware/image13.png)

5.  執行以下開始主要目標安裝之前張貼安裝步驟。


#### <a name="post-os-installation-steps"></a>張貼 OS 安裝步驟

若要取得 SCSI ID SCSI 硬碟 Linux 虛擬機器中的每個，啟用參數 」 磁碟。EnableUUID = TRUE 」，如下所示︰

1. 關閉您的虛擬機器。
2. 以滑鼠右鍵按一下 VM 中的項目左側面板 >**編輯設定**。
3. 按一下 [**選項**] 索引標籤。 選取 [**進階\>一般的項目** > **設定參數**。 關閉電腦時，只提供**設定參數**選項。

    ![](./media/site-recovery-failback-azure-to-vmware/image14.png)

4. 檢查是否具有**磁碟的資料列。EnableUUID**存在。 如果它會並設定為 [ **False**然後將其設**為 True** （不區分大小寫）。 如果存在，且設定為 true，按一下 [**取消**] 然後測試內來賓作業系統 SCSI 命令後開機設定。 如果不存在按一下 [**新增列**。
5. 新增磁碟。在 [**名稱**] 欄中的 EnableUUID。 設定其值為 TRUE。 不要加上雙引號以及上述的值。

    ![](./media/site-recovery-failback-azure-to-vmware/image15.png)

#### <a name="download-and-install-the-additional-packages"></a>下載並安裝的其他套件

注意︰ 請確定系統下載並安裝其他封包之前有網際網路連線。

\#yum 安裝-y xfsprogs perl lsscsi rsync wget kexec-工具

這個命令從 CentOS 6.6 存放庫下載這些 15 套件，並安裝︰

bc-1.06.95-1.el6.x86\_64.轉速

busybox-1.15.1-20.el6.x86\_64.轉速

elfutils 程式庫-0.158 3.2.el6.x86\_64.轉速

kexec 工具-2.0.0 280.el6.x86\_64.轉速

lsscsi-0.23-2.el6.x86\_64.轉速

lzo-2.03-3.1.el6\_5.1.x86\_64.轉速

perl-5.10.1-136.el6\_6.1.x86\_64.轉速

perl-模組-隨插即用-3.90-136.el6\_6.1.x86\_64.轉速

perl-Pod-逸出字元-1.04-136.el6\_6.1.x86\_64.轉速
    
perl-Pod-簡單-3.13-136.el6\_6.1.x86\_64.轉速

perl 程式庫-5.10.1 136.el6\_6.1.x86\_64.轉速

perl 版本-0.77 136.el6\_6.1.x86\_64.轉速

rsync-3.0.6-12.el6.x86\_64.轉速

俏皮 1.1.0 1.el6.x86\_64.轉速

wget-1.12-5.el6\_6.1.x86\_64.轉速

注意︰ 如果來源電腦使用 Reiser 或 XFS 檔案系統的根或啟動裝置，然後下列封裝應該會下載並安裝上保護之前的 Linux 主要目標。

\#cd /usr/local

\#wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm>

\#wget <http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm>

\#轉速-ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.轉速 reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.轉速

\#wget <http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_64.rpm>

\#轉速-ivh xfsprogs 3.1.1 16.el6.x86\_64.轉速

#### <a name="apply-custom-configuration-changes"></a>套用自訂的設定變更

套用這些變更之前先確定您已完成前一節，，然後遵循下列步驟︰


1. 將 RHEL 6-64 整合代理程式二進位複製到新建 OS 中。

2. 執行此命令以 untar 二進位︰ **tar-zxvf\<檔案名稱\>**

3. 執行此命令授與權限︰ \# **chmod 755./ApplyCustomChanges.sh**

4. 執行指令碼︰ ** \# ./ApplyCustomChanges.sh**。 在伺服器上執行的指令碼一次。 執行指令碼之後，請重新啟動伺服器。



### <a name="install-the-linux-server"></a>安裝 Linux 伺服器


1. [下載](http://go.microsoft.com/fwlink/?LinkID=529757)安裝檔案。
2. 將檔案複製到 Linux 母片目標虛擬機器使用您所選擇的 sftp 用戶端公用程式。 或者，您便可以登入 Linux 母片的目標虛擬機器上，並使用 wget 下載安裝套件中提供的連結
3. 登入 Linux 主要目標伺服器虛擬機器使用 ssh 您所選擇的用戶端
4. 如果您連線到您要在其部署 Linux 主要目標伺服器透過 VPN 連線 Azure 網路然後使用內部的 IP 位址的伺服器，您可以找到的虛擬機器**儀表板**] 索引標籤和連接埠 22 連線到 Linux 主要目標伺服器使用安全命令介面。
5. 如果您透過公用網際網路連線連線到 Linux 主要目標伺服器使用 Linux 主要目標伺服器的公用虛擬 IP 位址 （從虛擬機器**儀表板**] 索引標籤），且公用端點建立 ssh 登入 Linux servder。
6. 將檔案從 gzipped Linux 主要目標 Server 安裝程式 tar 封存解壓縮執行︰ *」 tar – xvzf Microsoft ASR\_UA\_8.2.0.0\_RHEL6 64\ 」*安裝程式檔案所在的目錄。

    ![](./media/site-recovery-failback-azure-to-vmware/image16.png)

7. 如果擷取檔案到不同的目錄變更目錄的安裝程式已擷取其中的 tar 內容封存。 從執行此目錄路徑 」 sudo。 / install.sh 」。

    ![](./media/site-recovery-failback-azure-to-vmware/image17.png)

8. 當系統提示您選擇的主要角色選取**2 （母片目標）**]。 將其他互動式的安裝選項保留為預設值。
9. 若要繼續的安裝及顯示主機設定介面等。 Linux 母片 sarget 伺服器主機設定公用是命令列公用程式。 不調整大小 ssh 用戶端公用程式視窗。 使用方向鍵選取**[通用**] 選項，然後按下鍵盤上的 ENTER。

    ![](./media/site-recovery-failback-azure-to-vmware/image18.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image19.png)


10. 在 [功能變數**IP**輸入 [內部伺服器 IP 位址設定 （您可以找到設定伺服器 VM 的**儀表板**] 索引標籤中），然後按 enter 鍵。 在 [**連接埠**輸入**22** ，然後按 ENTER。 
11.  離開**使用 HTTPS**為 [ **]**，然後按 ENTER。
12.  輸入所產生的複雜密碼設定伺服器上。 如果您使用的 PUTTY 用戶端從 Windows 電腦 ssh Linux 虛擬機器您可以使用 Shift + Insert 貼上剪貼簿的內容。 到本機剪貼簿]，並使用 CTRL-C 複製複雜密碼，然後使用 Shift + Insert 貼上。 按下 ENTER。
13.  使用 [向右箭號鍵瀏覽至結束，然後按 enter 鍵。 安裝完成，請等候。

    ![](./media/site-recovery-failback-azure-to-vmware/image20.png)

如果基於某種原因，您無法註冊您 Linux 主要目標的伺服器設定伺服器您可以，因此一次從 /usr/local/ASR/Vx/bin/hostconfigcli （第一次必須設定此目錄的存取權限的進階使用者身分執行 chmod） 執行主機設定公用程式。


#### <a name="validate-master-target-registration"></a>驗證主要目標註冊

您可以在驗證的主要目標伺服器已成功註冊 Azure 網站復原保存庫中設定伺服器 >**設定伺服器** > **伺服器詳細資料**。

>[AZURE.NOTE] 在註冊之後主要目標伺服器，如果您收到設定錯誤虛擬機器可能已經從 Azure 或結束點設定不正確，這是因為主要目標設定會偵測到 Azure dndpoints 主要目標部署 Azure 中時，雖然這不為 true，內部部署主要目標 server 內部部署。 這樣就不會影響回復，您可以略過這些錯誤。 



## <a name="step-4-protect-the-virtual-machines-to-the-on-premises-site"></a>步驟 4︰ 保護內部部署網站的虛擬機器

您需要保護內部部署網站 Vm 之前回會失敗。

### <a name="before-you-begin"></a>開始之前

當 VM 無法透過 Azure 時，它會加入頁面檔案額外 temp 磁碟機。 這是額外的磁碟機，通常是不是由您失敗 VM，因為它可能已經有專用的頁面檔案的磁碟機上。 虛擬機器反向保護之前，必須先確定的磁碟機離線時，讓它不會不會受到保護。 執行下列步驟，如下所示︰

1.  開啟電腦管理]，然後選取儲存管理列出的磁碟線上，使其連接至電腦。
2.  選取連接至電腦的暫時磁碟，然後選擇將其開啟離線。 

### <a name="protect-the-vms"></a>保護 Vm

1. 在 Azure 入口網站中，核取的狀態，以確保他們無法透過虛擬機器。

    ![](./media/site-recovery-failback-azure-to-vmware/image21.png)

2. 啟動您的電腦上的 vContinuum。

    ![](./media/site-recovery-failback-azure-to-vmware/image8.png)

3. 按一下 [**新的保護**]，然後選取作業系統類型] 中， 

4.  在新視窗中開啟選取**OS 類型** > 您想要回復的 Vm**取得詳細資料**。 在**主要伺服器詳細資料**，找出並選取您要保護的虛擬機器。 Vm 會列在進行容錯移轉前 vCenter 主機名稱。
5.  當您選取要保護的虛擬機器 （和其已經無法透過 Azure 時） 快顯視窗會提供兩個項目虛擬機器。 這是因為設定伺服器偵測到的虛擬機器註冊，讓它的兩個執行個體。 您需要移除的內部部署 VM 項目，好讓您可以保護正確 VM。 若要識別正確 Azure VM 的項目這裡，您可以登入 Azure VM，然後移至 C:\Program Files (x86) \Microsoft Azure Site Recovery\Application Data\etc。在檔案 drscout.conf 中，找出主機 id。 在 [vContinuum] 對話方塊中保留的項目，您必須找到主機識別碼 VM。 刪除所有其他項目。 若要選取正確的 VM，您可以參照為其 IP 位址。 IP 位址範圍內部部署會內部部署 VM。

    ![](./media/site-recovery-failback-azure-to-vmware/image22.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image23.png)

6. VCenter 伺服器上停止虛擬機器。 您也可以刪除 Vm 內部。
7. 然後指定您要保護 Vm 的內部部署細明體伺服器。 若要這麼做，連線到您要回復 vCenter 伺服器。

    ![](./media/site-recovery-failback-azure-to-vmware/image24.png)

8. 選取 [主要目標伺服器會根據您要復原 VM 的主機。

    ![](./media/site-recovery-failback-azure-to-vmware/image24.png)

9. 提供每個虛擬機器複寫的選項。 若要這麼做，您需要選取復原 Vm 復原側資料存放區。 表摘要列出您需要的每個 VM 提供不同的選項。

    ![](./media/site-recovery-failback-azure-to-vmware/image25.png)

    **選項** | **建議值的選項**
    ---|---
    處理程序的伺服器 IP 位址 | 選取部署 Azure 中的程序伺服器
    保留大小 (mb)| 
    保留值 | 1
    日期/時間 | 天
    一致性間隔 | 1
    選取目標資料存放區 | 資料存放區使用復原網站上。 資料儲存區應該有足夠的空間，並可供您要還原虛擬機器 ESX 主機。


10. 設定虛擬機器會擷取後至內部部署網站的移轉後的屬性。 下表摘要列出屬性。

    ![](./media/site-recovery-failback-azure-to-vmware/image26.png)

    **屬性** | **詳細資料**
    ---|---
    網路設定| 每個偵測到的網路介面卡，加以選取，然後按一下 [**變更**設定虛擬機器回復 IP 位址。 
    硬體設定| 指定 VM CPU 和記憶體。 設定可套用至您要保護的所有 Vm。 CPU 和記憶體識別正確的值，您可以參照 IAAS Vm 角色大小，並查看數核心和記憶體指派。
    顯示名稱 | 之後回復內部部署，您可以重新命名虛擬機器中 vCenter 庫存會顯示。 預設名稱是虛擬機器電腦主機名稱。 若要識別 VM 名稱，您可以參照至 [保護] 群組中的 [VM] 清單。
    NAT 設定 | 討論的下列詳細資料

    ![](./media/site-recovery-failback-azure-to-vmware/image27.png)

#### <a name="configure-nat-settings"></a>設定 NAT 設定

1. 若要啟用的虛擬機器保護，就需要建立兩個通訊通道。 第一個頻道是虛擬機器與程序伺服器之間。 此通道 VM 收集資料，並將其傳送至主版目標伺服器傳送資料的程序伺服器。 如果程序伺服器和受保護的虛擬機器 Azure 虛擬網路上不需要使用 NAT 設定。 否則指定 NAT 設定。 檢視 Azure 中的程序伺服器的公用 IP 位址。 

    ![](./media/site-recovery-failback-azure-to-vmware/image28.png)

2. 第二個頻道介於程序伺服器和主要目標伺服器。 使用 NAT，或不的選項取決於您是使用根據 VPN 連線或透過網際網路通訊。 不要選取 NAt，如果您使用的 VPN，但只有當您使用的連線至網際網路。

    ![](./media/site-recovery-failback-azure-to-vmware/image29.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image30.png)

3. 如果您沒有刪除指定內部部署虛擬機器，而且您會回到仍然失敗資料存放區包含舊的 VMDK 您會需要確認回復 VM 取得在中建立新的位置。 若要這麼做選取 [**進階**設定]，然後指定的**資料夾名稱設定**還原至其他資料夾。 [離開預設設定的其他的選項。 資料夾名稱將設定套用到所有的伺服器。

    ![](./media/site-recovery-failback-azure-to-vmware/image31.png)

4. 執行整備檢查以確保虛擬機器準備好要保護回內部部署。 

    ![](./media/site-recovery-failback-azure-to-vmware/image32.png)


5. 請等候，才能完成。 如果是成功上所有的 Vm 您可以指定保護計劃的名稱。 然後按一下 [**保護**開始。

    ![](./media/site-recovery-failback-azure-to-vmware/image33.png)


6. 您可以監視 vContinuum 的進度。

    ![](./media/site-recovery-failback-azure-to-vmware/image34.png)

7. 在**啟動保護規劃**完成步驟之後，您可以監視 VM 網站復原入口網站中的保護。

    ![](./media/site-recovery-failback-azure-to-vmware/image35.png)

8. 您可以看到 VM 上按一下，並監控其進度的確切的狀態。

    ![](./media/site-recovery-failback-azure-to-vmware/image36.png)

## <a name="prepare-the-failback-plan"></a>準備回復計劃

您可以準備回復計劃利用 vContinuum 應用程式可以隨時無法回內部部署網站。 這些修復計劃，兩者十分相似網站修復復原方案。

1.  啟動 vContinuum，然後選取 [**管理計劃** > **復原。** 您可以看到的容錯 Vm 用過的所有方案的清單。 若要復原，您可以使用相同的方案。

    ![](./media/site-recovery-failback-azure-to-vmware/image37.png)

2. 選取 [保護計劃和所有您想要復原的 Vm。 當您選取每個 VM 時，您可以看到更多詳細資料，包括目標 ESX 伺服器及來源 VM 磁碟。 按一下 [**下一步**開始 [復原] 精靈，然後選取您想要復原的 Vm。

    ![](./media/site-recovery-failback-azure-to-vmware/image38.png)

3. 您可以復原根據多個選項，但我們建議您可以使用**最新的標籤**，並選取**的所有 Vm 套用**]，以確保虛擬機器的最新資料將會用於。
4. 執行**整備核取。** 這會檢查是否正確的參數會設定為啟用 VM 復原。 如果是成功的所有檢查項目，請按一下 [**下一步**]。 如果不核取登入並解決錯誤。

    ![](./media/site-recovery-failback-azure-to-vmware/image39.png)

8.  在**VM 設定**確定復原設定已正確設定。 如果您需要您可以變更 VM 設定。

    ![](./media/site-recovery-failback-azure-to-vmware/image40.png)

9. 檢閱虛擬機器，將會還原，並指定復原順序的清單。 請注意 Vm 會列出使用電腦主機名稱。 可能會很難電腦主機名稱對應到虛擬機器。
若要對應的名稱，請移至 [**儀表板**中 Azure 虛擬機器並檢查 VM 主機名稱。

    ![](./media/site-recovery-failback-azure-to-vmware/image41.png)

10. 指定的方案名稱，然後選取 [**復原更新版本**。 我們建議您稍後復原之後可能無法完成初始的保護。 
11. 按一下 [儲存方案，或如果您已選取 [復原現在並不稍後觸發復原的 [**復原**]。 您可以檢查復原狀態，請參閱是否計劃的儲存。

    ![](./media/site-recovery-failback-azure-to-vmware/image42.png)

    ![](./media/site-recovery-failback-azure-to-vmware/image43.png)

## <a name="recover-virtual-machines"></a>復原虛擬機器

計劃的建立之後，您可以復原虛擬機器。 之前您檢查虛擬機器已完成同步處理。 如果複寫狀態] 顯示的 [確定]，保護已完成，並已符合 RPO 閥值。 您可以驗證在 VM 屬性的狀況。

![](./media/site-recovery-failback-azure-to-vmware/image44.png)

啟動復原之前，請關閉 Azure 虛擬機器。 這可確保沒有分割大腦，而且使用者只會存取應用程式的一份。 


1.  開始儲存的方案。 在 [vContinuum 選取**監視器**的方案。 這會列出已執行的所有方案。

    ![](./media/site-recovery-failback-azure-to-vmware/image45.png)

2.  **復原**選取的方案，然後按一下 [**開始**]。 您可以監視復原。 Vm 已之後，在您可以連線到其 vCenter 中。

    ![](./media/site-recovery-failback-azure-to-vmware/image46.png)

## <a name="protect-to-azure-again-after-failback"></a>Azure 回復後再保護

回復完成後您可能需要一次保護虛擬機器。 執行下列步驟，如下所示︰

1.  檢查正在虛擬機器內部部署及應用程式的連線。
2.  在 Azure 網站復原入口網站中，選取的虛擬機器並刪除這些頁面。 選取 [停用的虛擬機器保護。 如此一來，可確保 Vm 不會受到保護。
3.  Azure 中刪除失敗透過 Azure 虛擬機器
4.  刪除舊的虛擬機器上 vSpehere。 這些是您先前無法透過 Azure Vm。
5.  在網站復原入口網站保護最近移轉 Vm。 他們受保護之後，您可以將其新增至復原計劃。
 
## <a name="next-steps"></a>後續步驟



- 將 VMware 虛擬機器和實體伺服器複寫到 Azure 使用增強型的部署[閱讀的相關資訊](site-recovery-vmware-to-azure-classic.md)。

 
