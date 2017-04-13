<properties
   pageTitle="測試 Microsoft Azure SUSE Linux Vm 上的 SAP NetWeaver |Microsoft Azure"
   description="測試 Microsoft Azure SUSE Linux Vm 上的 SAP NetWeaver"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="hermanndms"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/15/2016"
   ms.author="hermannd"/>

# <a name="running-sap-netweaver-on-microsoft-azure-suse-linux-vms"></a>Microsoft Azure SUSE Linux Vm 上執行的 SAP NetWeaver


本文將說明各種 SAP NetWeaver Microsoft Azure SUSE Linux 虛擬機器 (Vm) 上執行時的考量的事項。 可能 19 2016年齡 SAP NetWeaver 正式支援 SUSE Linux Vm Azure 上。 有關 Linux 版本、 SAP 核心版本及其他所有的詳細資訊，請參閱 SAP 筆記 1928533 」 上 Azure SAP 應用程式︰ 支援產品和 Azure VM 類型 」。
可以在這裡找到有關上 Linux Vm SAP 進一步的文件︰ [Linux 虛擬機器 (Vm) 上的使用 SAP](virtual-machines-linux-sap-get-started.md)。


以下資訊應能助您避免一些潛在問題。

## <a name="suse-images-on-azure-for-running-sap"></a>Azure 執行 SAP SUSE 影像

Azure 上執行 SAP NetWeaver，只 SUSE Linux Enterprise Server SLES 12 (SPx)，請參閱也 SAP 筆記 1928533。 特殊的 SUSE 圖像位於 Azure Marketplace (「 SLES 11 SP3 的 SAP CAL 」)，但這不是用於一般的使用方式。 不要使用此圖像，因為它會保留[SAP 雲端應用程式庫](https://cal.sap.com/)方案。  

您應該使用 Azure 資源管理員的所有新測試和 Azure 上的安裝。 若要查詢的 SUSE SLES 圖像與版本使用 PowerShell 的 Azure 或 Azure 的命令列介面 (CLI)，請使用下列命令。 您可以再用於輸出，，例如定義 OS 圖像部署新的 SUSE Linux VM JSON 範本中。
這些 PowerShell 命令是有效的 PowerShell 的 Azure 版 1.0.1 及更新版本。

* 尋找現有的發行者，包括 SUSE:

   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```

* 尋找 SUSE 從現有的方案︰

   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```

* 尋找 SUSE SLES 方案︰

   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   CLI : azure vm image list-skus westeurope SUSE SLES
   ```

* 針對特定版本 SLES SKU 的外觀︰

   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12"
   CLI : azure vm image list westeurope SUSE SLES 12
   ```

## <a name="installing-walinuxagent-in-a-suse-vm"></a>安裝 WALinuxAgent SUSE VM 中

代理程式稱為 WALinuxAgent 是在 Azure Marketplace SLES 圖像。 安裝，以手動方式 （例如，當傳 SLES OS 虛擬硬碟 (VHD) 從內部部署） 的相關資訊，請參閱︰

- [OpenSUSE](http://software.opensuse.org/package/WALinuxAgent)

- [Azure](虛擬-機器-linux-背書-distros.md)

- [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## <a name="sap-enhanced-monitoring"></a>SAP 「 增強型監控 」

SAP 「 增強型監控 」 會強制執行 Azure SAP 必要條件。 請檢查 SAP 中的詳細資料，請注意 2191498 「 SAP 上 Linux 與 Azure:: 增強型監控 」。

## <a name="attaching-azure-data-disks-to-an-azure-linux-vm"></a>Azure Linux vm 附加 Azure 資料磁碟

您應該永遠不會裝載 Azure 資料磁碟 Azure Linux vm 使用裝置識別碼。 請改用通用唯一識別碼 (UUID)。 當您使用圖形的工具來裝載 Azure 資料磁碟，例如，則請小心。 請仔細檢查 /etc/fstab 中的項目。

裝置識別碼的問題是，可能會變更，並在然後 Azure VM 可能會在啟動程序。 若要降低問題，您可以新增 nofail 參數 /etc/fstab 中。 但是，小心 nofail 因為應用程式可能會使用之前，以連接點，以避免外部 Azure 資料磁碟不裝載在啟動時，可能會寫入根檔案系統。

下一節所述，透過 UUID 裝載唯一的例外附加疑難排解，OS 的磁碟。

## <a name="troubleshooting-a-suse-vm-that-isnt-accessible-anymore"></a>疑難排解無法再存取 SUSE VM

可能的情況 SUSE VM 上 Azure 程序停滯不前中啟動程序 （例如，使用相關裝載磁碟的錯誤）。 您可以使用 Azure 入口網站中的 Azure 虛擬機器 v2 開機診斷功能來驗證此問題。 如需詳細資訊，請參閱 [開機診斷] (https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)。

若要解決此問題的其中一個方法是從損毀 VM 附加 OS 磁碟，另一個 SUSE vm Azure 上。 下一節所述，然後進行編輯 /etc/fstab 或移除網路 udev 規則，例如適當的變更。

有考慮重點。 部署從相同的 Azure Marketplace 圖像 (例如，SLES 11 SP4) 的數個 SUSE Vm 會永遠裝載以相同的 UUID OS 磁碟。 因此，若要從不同的 VM 部署使用相同的 Azure Marketplace 圖像附加 OS 磁碟使用 UUID 會造成兩個相同 Uuid。 這就會發生問題，可能是指的是用來進行疑難排解 VM 會事實上從附加和損毀 OS 磁碟開機，而不是原始項目。

有兩種方式若要避免此問題︰

* 使用不同的 Azure Marketplace 圖像疑難排解 vm (例如，SLES 11 SPx，而不是 SLES 12)。
* 從另一個 VM 不附加的損毀的 OS 磁碟使用 UUID-使用其他項目。

## <a name="uploading-a-suse-vm-from-on-premises-to-azure"></a>上傳至 Azure SUSE VM 來自內部部署

若要上傳至 Azure SUSE VM 來自內部部署步驟的說明，請參閱 [準備 Azure SLES 或 openSUSE 虛擬機器] (virtual-machines-linux-suse-create-upload-vhd.md)。

如果您想要上載 VM 沒有結尾 deprovision 步驟 （例如，若要保留現有的 SAP 安裝，以及主機名稱），請檢查下列項目︰

* 請確定裝載 OS 磁碟時使用 UUID 和不裝置識別碼。 僅在 /etc/fstab 變更 UUID 不足 OS 磁碟。 此外，別忘了配合開機載入器透過 YaST 或編輯 /boot/grub/menu.lst。
* 如果您使用 SUSE OS 磁碟 VHDX 格式，並將它轉換成 VHD 中上傳至 Azure 的就很可能是在網路裝置會變更從 eth0 eth1。 若要避免發生問題，您稍後開機 Azure 上時，變更回 eth0 [修正 eth0 中複製 SLES 11 VMware] 所述 (https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/)。

除了描述文件中，我們建議您移除這個︰

   /lib/udev/rules.d/75-persistent-net-generator.rules

您也可以安裝可協助您避免潛在問題，只要並不會有多個 Nic Azure Linux 代理程式 (waagent)。

## <a name="deploying-a-suse-vm-on-azure"></a>部署上 Azure SUSE VM

您應該使用 JSON 新 Azure 資源管理員模型中的範本檔案，以建立新的 SUSE Vm。 建立 JSON 範本檔案之後，您就可以使用下列 CLI 命令 PowerShell 另一種部署 VM:

   ```
   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ```
如需 JSON 範本檔案的詳細資訊，請參閱 [撰寫 Azure 資源管理員樣板] (資源-群組-撰寫-templates.md) 和 [Azure 快速入門樣板] (https://azure.microsoft.com/documentation/templates/)。

如需詳細瞭解 CLI 和 Azure 資源管理員的詳細資訊，請參閱 [使用 Azure CLI for Mac、 Linux 和 Windows Azure 資源管理員使用的商務連絡人] (xplat-cli-azure-資源-manager.md)。

## <a name="sap-license-and-hardware-key"></a>SAP 授權及硬體索引鍵

正式的 SAP Azure 認證，新機制引進計算用於 SAP 授權的 SAP 硬體鍵。 SAP 核心傳達必須使用這個屬性。 Linux 離職 SAP 核心版本不包含此程式碼的變更。 因此，在某些情況下 (例如，Azure VM 調整大小)、 變更 SAP 硬體索引鍵和 SAP 授權已不再有效。 在最新的 SAP Linux 核心解決此問題。 如需詳細資訊，請檢查 SAP 筆記 1928533。

## <a name="suse-sapconf-package--tuned-adm"></a>SUSE sapconf 套件 / 調整 adm

SUSE 提供套件，稱為 「 sapconf 「 管理一組 SAP 特定設定。 如需詳細瞭解這個套件的功能，以及如何安裝並使用它，請參閱 [使用 sapconf 準備要執行 SAP 系統 SUSE Linux 企業伺服器] 的 (https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) 和 [什麼是 sapconf 或準備 SUSE Linux Enterprise Server 執行 SAP 系統如何？](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems)。

同時有取代 sapconf-微調 adm.的工具 其中一個可以找到有關追蹤下列兩個連結此工具的更多詳細資料。

您可以找到 SLES 文件中的有關調整 adm 設定檔 sap hana[以下](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html) 

您可以找到調整 adm-的 SAP 工作負載的調整系統[以下](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf)章節 6.2 中


## <a name="nfs-share-in-distributed-sap-installations"></a>NFS 共用在分散式 SAP 安裝

如果您有您要在個別的 Vm-安裝資料庫及 SAP 應用程式伺服器分散式的安裝，例如，您可以共用 /sapmnt 目錄透過網路檔案系統 (NFS)。 如果您有安裝步驟的問題，您建立 /sapmnt NFS 共用之後，檢查是否為共用設定 「 no_root_squash 」。

## <a name="logical-volumes"></a>邏輯區

過去的視其中一個邏輯大量跨多個 Azure 資料磁碟 （例如，SAP 資料庫），它所建議使用 mdadm lvm 未完全上 Azure 尚未驗證。 若要瞭解如何使用 mdadm 設定 Linux 襲擊 Azure，請參閱[設定軟體 Linux 襲擊](virtual-machines-linux-configure-raid.md)。 到開頭可能 2016年同時也 lvm 完全支援上 Azure，而且可供 mdadm 另一種。 關於 lvm Azure 上的其他資訊，請參閱[在 Linux VM Azure 中的設定 LVM](virtual-machines-linux-configure-lvm.md)。


## <a name="azure-suse-repository"></a>Azure SUSE 存放庫

如果您具備標準 Azure SUSE 存放庫的存取問題，您可以使用簡單的指令重設 pin。 如果您建立的私人 OS 圖像 Azure 區域中，然後將圖像複製到不同的區域，您要部署新 Vm 根據此私人 OS 圖像的位置可能會發生這種情況。 只要執行 VM 內部下列命令︰

   ```
   service guestregister restart
   ```

## <a name="gnome-desktop"></a>Gnome 桌面

如果您想要安裝完整的 SAP 示範系統包括 SAP GUI，在單一 VM-內部使用 Gnome 桌面瀏覽器和 SAP 管理主控台-使用此提示來將其安裝於 Azure SLES 圖像︰

   針對 SLES 11:

   ```
   zypper in -t pattern gnome
   ```

   針對 SLES 12:

   ```
   zypper in -t pattern gnome-basic
   ```

## <a name="sap-support-for-oracle-on-linux-in-the-cloud"></a>SAP 支援的 Oracle Linux 在雲端上

這是在虛擬環境中的 [從 Oracle linux 有支援限制。 雖然這不是 Azure 特定主題，請務必瞭解。 SAP 不支援 Oracle SUSE 或紅色角色，例如 Azure 公用雲端上。 若要討論此主題，請直接連絡 Oracle。
