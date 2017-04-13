<properties
   pageTitle="手動安裝 SAP HANA Azure Vm 上的快速入門指南 |Microsoft Azure"
   description="手動安裝 SAP HANA Azure Vm 上快速入門指南"
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

# <a name="quickstart-guide-for-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>手動安裝單一執行個體 SAP HANA Azure Vm 上的快速入門指南

## <a name="introduction"></a>簡介

此快速入門指南可協助設定單一執行個體 SAP HANA 原型/示範系統上 Azure Vm 手動安裝 SAP NetWeaver 7.5 和 SAP HANA SP12。
本指南假設讀者都熟悉 Azure IaaS 等如何部署虛擬機器或 [透過 Azure 入口網站或 Powershell/CLI 包括使用 json 範本] 選項的虛擬網路的基本知識。 此外預期讀者是熟悉 SAP HANA、 SAP NetWeaver，以及如何將其內部部署安裝。

預期讀者並知道在文件結尾處的 [一般資訊] 區段中所提及的一般 SAP Azure 文件。

因為非生產系統的限制本指南將不包含 HA，例如主題備份，DR、 高效能或特殊的安全性考量。

已完成的範例設定使用兩個虛擬機器完成分散式的 SAP NetWeaver 安裝透過 Azure 資源管理員模型 SAP-Linux-Azure 上僅支援 Azure 資源管理員和不傳統的模型。 關於 Azure 資源管理員的詳細資訊的連結可以在本文結尾的一般資訊] 區段中找到。

以下是兩個測試 Vm 用於範例安裝︰

* hana appsrv 裝載 NW 7.5 ASCS 執行個體 + PAS 的 （類型 DS3）
* hana-dbsrv （類型 GS4） 到主機 HANA SP12
* 兩個 Vm 屬於一 Azure 虛擬網路 (azure-hana-測試-vnet)
* 在這兩種情況下 OS 已 SLES 12 SP1


但是請注意事實的年 7 月 2016 SAP HANA 只完全支援 OLAP (BW) 生產系統上 Azure VM 類型 GS5。 測試其中一個並不會收到正式 SAP 支援是都使用等例如 GS4 較小的項目。
什麼應該一律使用 SAP HANA Azure 上為 Azure 進階版儲存 HANA 資料和記錄檔-請參閱 「 磁碟設定 」 一節。 關於進一步瞭解哪些 SAP 產品都支援 Azure 的詳細資訊，請檢查本文結尾的一般資訊] 區段。


本指南將說明若要手動安裝 Azure Vm SAP HANA 兩種不同的方式︰

* 在 「 資料庫執行個體 」 步驟分散式 NetWeaver 安裝的一部分安裝 SAP HANA 透過 SAP 軟體佈建管理員 (SWPM)
* 安裝 SAP HANA 使用 HANA 生命週期管理員工具 hdblcm，然後再安裝 NetWeaver 之後

您也可使用 SWPM 及一個單一 VM 安裝所有元件 (SAP HANA，SAP 應用程式伺服器、 ASCS 執行個體，SAP GUI)。 這個選項不本文中所述，但，必須考慮的項目都相同。

在開始安裝前應該瞭解之區段後 Azure 測試 Vm 設定的相關的檢查清單下方避免使用只預設 Azure VM 設定時，會發生的幾個基本錯誤。


## <a name="checklist-sap-hana-installation-via-sap-swpm"></a>透過 SAP SWPM 的檢查清單 SAP HANA 安裝

這是透過 SAP SWPM 執行分散式的 SAP NW 7.5 示範或原型 pursposes 手動單一執行個體 SAP HANA 安裝相關的項目安裝索引鍵的簡單的檢查清單。 說明並的螢幕擷取畫面中更多詳細資料，請參閱在表單中顯示的個別項目︰

* 建立會包含兩個測試 Vm Azure 虛擬網路 
* 部署 OS SLES 12 sp1 的兩個 Azure Vm 透過 Azure 資源管理員模型 
* 將兩個標準的儲存空間的磁碟附加至應用程式伺服器 VM （例如 75 GB 和 500 GB）
* 附加至 HANA 資料庫伺服器 VM-的四個磁碟 2 標準的儲存空間的磁碟等應用程式伺服器 VM + 2 的進階版儲存磁碟 (例如 2x512GB)
* 根據大小及/或處理量需求附加多個磁碟和 [建立條紋的區使用 lvm 或 mdadm VM 內部 OS 層級
* 建立 XFS 檔案系統的連接的磁碟 / 邏輯區
* 裝載 OS 層級的新 XFS 檔案系統。 使用一個檔案系統，讓所有的 SAP 軟體和，另一個例如 sapmnt 目錄和也許備份。 在 SAP HANA DB 伺服器裝載 XFS 檔案系統為 /hana 和 /usr/sap 這是為了避免根檔案系統不太大無法上 Linux Azure Vm 填滿所有必要的進階版儲存空間的磁碟上
* 在 [/ 等/主機中輸入測試 Vm 本機 ip 的位址
* 在 [/etc/fstab 中輸入 nofail 參數
* 設定核心參數，根據 HANA-SLES-12 SAP 附註 （請參閱下進一步核心參數區段中的詳細資料）
* 新增交換空間
* 如果想要的，安裝圖形桌面測試 Vm。 使用遠端 sapinst 安裝
* 從 SAP 服務商場下載 SAP 軟體
* SAP ASCS 執行個體的伺服器上安裝應用程式 VM
* 共用之間測試 Vm 透過 NFS sapmnt 目錄 （應用程式伺服器 VM 是 NFS 伺服器）
* 安裝在資料庫伺服器 VM 包括透過 SWPM HANA 資料庫執行個體
* PAS 在伺服器上安裝應用程式 VM
* 啟動 SAP MC，例如連線透過 SAP GUI / HANA Studio 



## <a name="checklist-sap-hana-installation-via-hdblcm"></a>透過 hdblcm 的檢查清單 SAP HANA 安裝

這是透過 SAP SWPM 執行分散式的 SAP NW 7.5 示範或原型 pursposes 手動單一執行個體 SAP HANA 安裝相關的項目安裝索引鍵的簡單的檢查清單。 說明並的螢幕擷取畫面中更多詳細資料，請參閱在表單中顯示的個別項目︰

* 建立會包含兩個測試 Vm Azure 虛擬網路 
* 部署 OS SLES 12 sp1 的兩個 Azure Vm 透過 Azure 資源管理員模型 
* 將兩個標準的儲存空間的磁碟附加至應用程式伺服器 VM （例如 75 GB 和 500 GB）
* 附加至 HANA 資料庫伺服器 VM-2 標準的儲存空間等應用程式伺服器 VM + 2 的進階版儲存磁碟 (例如 2x512GB) 的四個磁碟
* 根據大小及/或處理量需求附加多個磁碟和 [建立條紋的區使用 lvm 或 mdadm VM 內部 OS 層級
* 建立 XFS 檔案系統的連接的磁碟 / 邏輯區
* 裝載 OS 層級的新 XFS 檔案系統。 使用一個檔案系統，讓所有的 SAP 軟體和，另一個例如 sapmnt 目錄和也許備份。 在 SAP HANA DB 伺服器裝載 XFS 檔案系統為 /hana 和 /usr/sap 這是為了避免根檔案系統不太大無法上 Linux Azure Vm 填滿所有必要的進階版儲存空間的磁碟上
* 在 [/ 等/主機中輸入測試 Vm 本機 ip 的位址
* 在 /etc/fstab 輸入 nofail 參數
* 設定核心參數，根據 HANA-SLES-12 SAP 附註 （請參閱下進一步核心參數區段中的詳細資料）
* 新增交換空間
* 如果想要的，安裝圖形桌面測試 Vm。 使用遠端 sapinst 安裝
* 從 SAP 服務商場下載 SAP 軟體
* 使用群組識別碼 1001 HANA DB 伺服器 VM 上建立群組 」 sapsys 」
* SAP HANA 在伺服器上安裝 DB VM 使用 hdblcm
* SAP ASCS 執行個體的伺服器上安裝應用程式 VM
* 共用之間測試 Vm 透過 NFS sapmnt 目錄 （應用程式伺服器 VM 是 NFS 伺服器）
* 安裝在資料庫伺服器 VM 包括透過 SWPM HANA 資料庫執行個體
* PAS 在伺服器上安裝應用程式 VM
* 啟動 SAP MC，例如連線透過 SAP GUI / HANA Studio 




## <a name="prepare-azure-vms-for-manual-installation-of-sap-hana"></a>Azure Vm 準備 SAP HANA 手動安裝

此章節的相關準備手動安裝 SAP HANA Azure Vm 包含五個章節涵蓋下列主題︰

* 磁碟設定
* 核心參數
* Filesystems
* / 等/主機
* / 等/fstab


### <a name="disk-setup"></a>磁碟的設定

根檔案系統中 Linux VM Azure 上為大小限制。 因此就必須執行 SAP VM 附加額外的磁碟空間。 若是 VM 環境中使用單純的原型示範 SAP 應用程式伺服器是使用 Azure 標準的儲存空間的磁碟問題。 而 SAP HANA DB 資料和記錄檔-Azure 進階版儲存空間的磁碟應即使是在非生產橫向。

請參閱如何將附加 Linux VM 磁碟相關詳細資料[以下](virtual-machines-linux-add-disk.md)

Azure 磁碟快取-當您必須使用 「 無 」 的磁碟會用來儲存 HANA 交易記錄檔。 您可以使用的資料檔案 HANA 閱讀快取。 HANA 現狀依存的整體使用模式多少讀取快取 Azure 磁碟層級的記憶體內資料庫會改善效能 （例如啟動 HANA 和從磁碟讀取資料，將記憶體）。

請參閱 Azure 進階版存放裝置的相關詳細資料[以下](../storage/storage-premium-storage.md)

[在這裡](https://github.com/Azure/azure-quickstart-templates)尋找範例 json 範本來建立 Vm。
「 101-vm-簡單-linux 」 會顯示如何為基本範本看起來像包括加 100 GB 資料磁碟的 [儲存] 區段。

[本文](virtual-machines-linux-sap-on-suse-quickstart.md)包含以下部分若要瞭解如何尋找透過 Powershell 或 CLI SUSE 圖像和要附加的磁碟透過 UUID 重要性。


根據系統和處理量需求的大小，可能需要附加而不是一個多個磁碟並稍後建立這些磁碟 OS 層級設定等量。 為什麼一會建立等量在多個 Azure 磁碟組的兩個外觀如下︰

* 增加處理量
* 需要單一檔案系統 > 1 TB，為目前的 Azure 磁碟大小限制為 1 TB （如年 7 月 2016)


可以在這裡找到更多關於設定等量的兩個主要的工具︰

[瞭解如何使用 mdadm 設定 Linux 軟體襲擊 Azure VM 文章](virtual-machines-linux-configure-raid.md)

[在 Linux Azure VM 設定邏輯大量管理員的相關文章︰](virtual-machines-linux-configure-lvm.md)



![](./media/virtual-machines-linux-sap-hana-get-started/image003.jpg)

測試環境兩個 Azure 標準的儲存空間的磁碟已經連接到 SAP 應用程式伺服器 VM。 其中一個用來儲存所有的 SAP 軟體安裝 (例如 NetWeaver 7.5，SAP GUI SAP HANA... )，另一個有足夠的空間上述程序可能需要 （例如備份，測試資料） 以及其中屬於相同的 SAP 橫向所有 Vm 之間共用 sapmnt 目錄 （例如 SAP 設定檔）。

![](./media/virtual-machines-linux-sap-hana-get-started/image004.jpg)

應用程式伺服器相反 VM 四磁碟已連接到 SAP HANA 伺服器 VM。 等前兩個磁碟用於維持 SAP 軟體 （其中一個也無法共用透過 NFS SAP 軟體磁碟），並有足夠的空間，例如的備份。 其他兩個磁碟的 Azure 進階版儲存空間的磁碟保留 SAP HANA 資料和記錄檔，以及 /usr/sap 目錄。


### <a name="kernel-parameters"></a>核心參數


SAP HANA 需要特定 Linux 核心設定，不是標準的 Azure 庫圖像的一部分，必須手動設定。 有特定的說明設定的 SAP 筆記。 


SAP 注意 SAP HANA DB: OS 的建議設定 SLES 12 / SLES 的 SAP 應用程式 12: [SAP 筆記 2205917](https://launchpad.support.sap.com/#/notes/2205917)

您可以找到其他主題 reagrding 頁面快取相關 SLES 上執行 SAP HANA 一個[以下](https://www.suse.com/documentation/sles_for_sap/singlehtml/sles_for_sap_guide/sles_for_sap_guide.html#sec.s4s.configure.page-cache)的章節 6.1 核心︰ 頁面快取限制

此外，還有相關的頁面快取限制[SAP 筆記 1557506](https://service.sap.com/sap/support/notes/1557506) SAP 附註

SLES 12 有取代舊的 sapconf 公用程式的新工具。 「 調整 adm 」 並有特殊的 SAP HANA 設定檔。 其中一個可以找到有關追蹤下列兩個連結此工具的更多詳細資料。

您可以找到 SLES 文件中的有關調整 adm 設定檔 sap hana[以下](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html)

關於調整 adm SLES 文件的設定檔 sap hana-微調 adm-的 SAP 工作負載的章節 6.2 調整系統可以找到[以下](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf)


![](./media/virtual-machines-linux-sap-hana-get-started/image005.jpg)

以下其中一個可以看到 「 調整 adm 」 變更 transparent_hugepage 為根據的必要的 SAP HANA 設定 numa_balancing 值的方式。


若要永久 SAP HANA 核心設定一個有 SLES 12 上使用 grub2。 進一步瞭解 grub2 的資訊，請參閱[以下](https://www.suse.com/documentation/sled-12/book_sle_admin/data/sec_grub2_file_structure.html)


![](./media/virtual-machines-linux-sap-hana-get-started/image006.jpg)

此螢幕擷取畫面顯示如何核心設定已變更設定檔中，然後 「 編譯 」 透過 grub2 mkconfig


![](./media/virtual-machines-linux-sap-hana-get-started/image007.jpg)

若要變更 Yast 透過設定] 和 [開機 Loader 核心參數設定會另一個選項。


### <a name="filesystems"></a>Filesystems 

![](./media/virtual-machines-linux-sap-hana-get-started/image008.jpg)

以下其中一個可以看到的兩個檔案系統 SAP 應用程式伺服器 VM 上方的兩個連接的 Azure 標準存放磁碟上所建立的。 兩個 filesystems 的類型 XFS，並且裝載 /sapdata 和 /sapsoftware。

不強制執行這種方式。 有不同的選項如何結構的磁碟空間。
若要避免根的磁碟空間不足是最重要的長寬。 


![](./media/virtual-machines-linux-sap-hana-get-started/image009.jpg)

有關 SAP HANA DB VM 很重要事項，透過 sapinst (swpm) 資料庫安裝期間，只使用簡單的 「 一般 」 安裝選項，將會安裝內容 /hana 與 /usr/sap] 下的預設。 預設設定的 SAP HANA 記錄備份是例如 /usr/sap 底下。
類似之前避免根的磁碟空間不足的金鑰。 因此其中一個應該要確定透過 swpm 安裝 SAP HANA 之前有足夠的空間 /hana 與 /usr/sap] 下。

從 SAP[本文](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm)將說明 SAP HANA 的標準檔案系統版面配置 


![](./media/virtual-machines-linux-sap-hana-get-started/image010.jpg)

在標準的 SLES 12 Azure 庫安裝 SAP NetWeaver 時會有沒有交換空間的訊息。 若要移除這封郵件的其中一個可能例如手動新增交換檔案透過 dd mkswap 與 swapon 這份文件中所述。 只搜尋[本文](https://www.suse.com/documentation/sled-12/book_sle_deployment/data/sec_yast2_i_y2_part_expert.html)中的 [的 「 新增交換檔案手動 」

另一個選項是設定交換透過 Linux VM 代理程式的空間。 詳細資訊，請參閱[以下](virtual-machines-linux-agent-user-guide.md)


### <a name="etchosts"></a>/ 等/主機

![](./media/virtual-machines-linux-sap-hana-get-started/image011.jpg)

開始安裝 SAP 之前，先另一個重點是包含 [/etc/hosts 檔案中的 [主機名稱和 SAP Vm 的 IP 位址。 其中一個應該部署 Azure 虛擬網路中的所有 SAP Vm，然後使用 [內部的 IP 位址。

### <a name="etcfstab"></a>/ 等/fstab

![](./media/virtual-machines-linux-sap-hana-get-started/image000c.jpg)

測試過程事實上，建議您先將 nofail 參數新增至 fstab。 如果發生錯誤的磁碟然後 VM 想仍得到並不會在啟動程序。 但其中一個監看在此情況下可能無法使用額外的磁碟空間程序可能填滿根檔案系統。 萬一 /hana 就會遺失 SAP HANA 不會啟動，雖然完全。


## <a name="install-graphical-gnome-desktop-on-sles-12"></a>在 [SLES 12 上安裝圖形 Gnome 桌面

此章節包含兩個 secitions 其中涵蓋下列主題︰

* 安裝 Gnome 桌面和 xrdp 上 SLES 12
* 執行 java SAP MC SLES 12 上使用 Firefox

一個可能也會使用 Xterminal，VNC 等的替代方案，但年 9 月 2016年本文僅說明 xrdp。

### <a name="installation-of-gnome-desktop-and-xrdp-on-sles-12"></a>安裝 Gnome 桌面和 xrdp 上 SLES 12

特別是有 Microsoft Windows 的背景，並且想要使用圖形直接在 SAP Linux Vm 桌面執行 Firefox、 Sapinst、 SAP GUI、 SAP MC 或 HANA Studio，以及可能透過 RDP VM 從電腦連線到 Microsoft Windows 的人有一個簡單的方法達到此目標。 這可能不是如適合生產資料庫伺服器是確定純粹的原型示範環境。 以下是安裝 Azure SLES 12 VM Gnome 桌面步驟︰

安裝 gnome 桌面 （例如在 putty 視窗） 下列命令︰

   在-t 圖樣 gnome basic zypper

再安裝 xrdp 為允許透過 RDP VM 連線︰

   在 [xrdp zypper

編輯 /etc/sysconfig/windowmanager 並設定預設 windows 管理員 Gnome 至︰

   DEFAULT_WM = 「 gnome 」

執行 chkconfig，請確定該 xrdp 會自動啟動後重新啟動︰ 

  chkconfig-階層 3 xrdp 上

萬一應該 RDP 連線發生問題，嘗試重新啟動 （是不是出在 putty 視窗）︰

  /etc/xrdp/xrdp.sh 重新啟動

萬一為 xrdp 重新啟動前述不.pid 檔案時，使用核取，並將它移除︰

  檢查 /var/run 並尋找 xrdp.pid   
  移除它，然後再次嘗試重新啟動



### <a name="sap-mc"></a>SAP MC


若要開始圖形 java SAP MC 不在執行中 Azure SLES 12 VM 安裝 Gnome 後的 Firefox 前一節所述的桌面會收到錯誤，因為缺少 Java 瀏覽器增益集。

若要開始 SAP MC URL 是<server>: 5 < instance_number > 13

您可以找到更多詳細資料[以下](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm)


![](./media/virtual-machines-linux-sap-hana-get-started/image013.jpg)

在上述的螢幕擷取畫面上人都無法檢視如何錯誤訊息看起來應該像 Java 瀏覽器增益集時遺失。 

![](./media/virtual-machines-linux-sap-hana-get-started/image014.jpg)

若要解決此問題的其中一個選項是只安裝缺少的外掛程式透過 Yast。

![](./media/virtual-machines-linux-sap-hana-get-started/image015.jpg)

重複 SAP MC URL 會顯示一些] 對話方塊的第一個時間要求啟動外掛程式。


可能會出現一個其他問題會遺失的檔案相關的錯誤訊息︰ 這是很可能 Java 1.8 SAP GUI 7.4 所需要的安裝相關的 javafx.properties

透過 Yast 看到 IBM Java 版本不包含此檔案。 解決方法是從 Oracle Java 下載。
您可以找到文章說話此特定問題，[以下](https://scn.sap.com/thread/3908306)



## <a name="manual-sap-hana-installation-via-swpm-as-part-of-a-netweaver-75-installation"></a>手動 SAP HANA 安裝透過 SWPM NetWeaver 7.5 安裝的一部分


下列清單的螢幕擷取畫面會顯示安裝 SAP NetWeaver 7.5 和 SAP HANA SP12 透過 SWPM (sapinst) 的主要步驟。 做為 NW 7.5 的一部分安裝 SWPM 有也為單一的執行個體安裝 HANA 資料庫的功能。


![](./media/virtual-machines-linux-sap-hana-get-started/image012.jpg)

範例測試安裝環境只要 ABAP 應用程式伺服器。 「 分散式系統 」 的選項來安裝 ASCS 執行個體，以及主應用程式伺服器執行個體中一個 Azure VM 和 SAP HANA 另一個 Azure VM 資料庫系統。


![](./media/virtual-machines-linux-sap-hana-get-started/image016.jpg)

一旦 ASCS 執行個體應用程式伺服器 VM 上已安裝並設定為 [綠色 」 sapmnt 目錄包括例如 SAP 設定檔目錄具有共用與 SAP HANA DB 伺服器 VM 中 SAP MC。
DB 安裝步驟都需要此資訊的存取權。 最佳方式就是使用 NFS 可使用 Yast 設定。


![](./media/virtual-machines-linux-sap-hana-get-started/image017b.jpg)

在應用程式上伺服器 VM sapmnt 目錄應該共用 NFS 透過使用選項 」 rw 」 以及 「 no_root_squash 」。 預設值為 「 ro 」 和 「 root_squash 」，這可能會導致問題時安裝資料庫執行個體。


![](./media/virtual-machines-linux-sap-hana-get-started/image018b.jpg)

SAP HANA DB 伺服器 VM sapmnt 上共用應用程式伺服器 VM 具有透過 NFS 用戶端] 設定 （例如 Yast 說明）


![](./media/virtual-machines-linux-sap-hana-get-started/image019.jpg)

然後一個有 SAP HANA DB 伺服器 VM 登入並開始 SWPM 完成分散式 NW 7.5 安裝 「 資料庫執行個體 」 的下一個步驟。


![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

相關沒有就要選取 「 一般 」 安裝之後輸入太多 SAP HANA 安裝。 除了 installatiom 媒體一個路徑有輸入 DB SID、 主機名稱、 執行個體數目和 DB 系統管理員的密碼。

 
![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

下一步是 DBACOCKPIT 結構描述中輸入密碼。



![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

接著是 SAPABAP1 結構描述密碼問題。


![](./media/virtual-machines-linux-sap-hana-get-started/image023.jpg)

結尾應為只前面 DB 安裝程序的每個階段綠色核取記號，小型的訊息方塊會顯示哪些應會顯示 「 執行的 [...] 資料庫執行個體已完成]。

 
![](./media/virtual-machines-linux-sap-hana-get-started/image024.jpg)

成功安裝後 SAP MC 也應該會顯示為 「 綠色 」 和 SAP HANA 程序 （例如 hdbindexserver、 hdbcompileserver） 的完整清單的 DB 執行個體


![](./media/virtual-machines-linux-sap-hana-get-started/image025.jpg)

此螢幕擷取畫面顯示部分下 /hana/shared SWPM HANA 安裝期間所建立的檔案結構。 沒有任何選項，指定不同的路徑。 因此，是很重要裝載下 SAP HANA 安裝，以避免根檔案系統執行可用的空間 SWPM 透過前 /hana 額外的磁碟空間。


![](./media/virtual-machines-linux-sap-hana-get-started/image026.jpg)

以下其中一個可以看到相同的動作，如 /usr/sap 目錄之前所述。


![](./media/virtual-machines-linux-sap-hana-get-started/image027b.jpg)

分散 ABAP 安裝的最後一個步驟是 「 主要應用程式伺服器執行個體 」


![](./media/virtual-machines-linux-sap-hana-get-started/image028b.jpg)

取得安裝 PAS 以及 SAP GUI 後一個可以檢查透過交易 「 dbacockpit 」 的所有項目權限與 SAP HANA 安裝。

 
![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

作為最後無法安裝 SAP 應用程式伺服器 VM SAP HANA Studio 其中一個步驟，並將其連線至資料庫伺服器 VM 上執行的 SAP HANA 執行個體中。




## <a name="manual-sap-hana-installation-via-hana-life-cycle-manager-tool-hdblcm"></a>透過 HANA 生命週期管理員工具 hdblcm 手動 SAP HANA 安裝


除了透過 SWPM 分散式安裝的一部分安裝 SAP HANA 也是第一次安裝 HANA 獨立使用 hdblcm，然後再安裝在例如 SAP NetWeaver 7.5 在之後 possibe。 螢幕擷取畫面下方的清單會顯示方式。

以下是三個來源 HANA hdblcm 工具的相關資訊︰

[選擇工作的正確 SAP HANA HDBLCM](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)

[SAP HANA 生命週期管理工具](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)

[SAP HANA 伺服器安裝和更新指南](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)



![](./media/virtual-machines-linux-sap-hana-get-started/image030.jpg)

若要避免稍後問題的預設群組識別碼設定\<HANA SID\>adm 使用者 （hdblcm 工具所建立） 一應該定義安裝 SAP HANA 使用 hdblcm 之前稱為 「 sapsys 」 群組識別碼 1001年新群組。




![](./media/virtual-machines-linux-sap-hana-get-started/image031.jpg)

開始會有一個有選取項目位置的簡單開始] 功能表的 hdblcm 第一次 1 「 安裝新的系統 」



![](./media/virtual-machines-linux-sap-hana-get-started/image032.jpg)

在此螢幕擷取畫面上一個可以看到所有之前所輸入的金鑰選項。 重要事項-目錄的已命名 HANA 記錄與資料區以及安裝路徑 （/hana/在這個範例中共用） 和/usr/sap 不應該根檔案系統的部分，但屬於 Azure VM 設定一節所述，是附加到 VM Azure 資料磁碟。 這樣可避免根檔案系統可能空間不足的風險。
其中一個也可以查看 HANA 管理員使用者的使用者識別碼 1005年，而且是安裝前所定義的 sapsys 群組 (id 1001) 的一部分。



![](./media/virtual-machines-linux-sap-hana-get-started/image033.jpg)

其中一個可以檢查 HANA \<HANA SID\>adm (在此範例中 azdadm) 中/等/密碼的使用者詳細資料



![](./media/virtual-machines-linux-sap-hana-get-started/image034.jpg)

安裝 SAP HANA 使用 hdblcm 後，可以看到 SAP HANA Studio 中。 包括例如所有 SAP NetWeaver 資料表的 SAPABAP1 結構描述尚不提供。



![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

安裝 SAP HANA 後可以使用其安裝 SAP NetWeaver。 在這個範例中，已完成使用透過 SWPM 對應節所述的 「 分散式的安裝 」。
當剛安裝的資料庫執行個體，透過 SWPM 一個具有輸入相同資料為之前 hdblcm （例如主機名稱，HANA SID 執行個體的數字）。 SWPM 然後會使用現有的 HANA 安裝，或新增其他結構描述。



![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

這是一個有輸入 DBACOCKPIT 結構描述相關資料的位置 SWPM 安裝步驟的圖片。


![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

然後 SWPM 預期輸入 SAPABAP1 結構描述的相關資料。


![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

一旦完成 SWPM 資料庫執行個體安裝人都無法檢視 SAPABAP1 中的結構描述 HANA Studio。



![](./media/virtual-machines-linux-sap-hana-get-started/image039b.jpg)

最後的 [SAP 應用程式伺服器] 和 [SAP GUI 安裝後一個應會以驗證與交易 「 dbacockpit 」 的 HANA DB 執行個體。




## <a name="general-information-related-to-sap-azure-certifications-running-sap-hana-on-azure-and-sap-software-download"></a>SAP Azure 認證，Azure 及 SAP 軟體下載上執行 SAP HANA 相關的一般資訊

* 關於 Azure Windows 作業系統上執行 SAP，在 [標準模式中的一般 SAP Azure 記錄︰[使用 Windows Azure 虛擬機器上的 SAP](virtual-machines-windows-classic-sap-get-started.md)

* 客戶所使用量的現有 SAP 範本的相關資訊︰ [SAP Azure 快速入門範本](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/)

* 關於 Azure Linux 作業系統上執行 SAP，Azure 資源管理員模型中的一般 SAP Azure 記錄︰ [Linux 虛擬機器 (Vm) 上的使用 SAP](virtual-machines-linux-sap-get-started.md)

* 通過認證的 SAP HANA 硬體目錄會列出生產支援哪些 Azure VM 類型︰[認證 SAP HANA® 硬體目錄](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)

* 虛擬機器大小，尤其是對於 Linux 工作負載的資訊︰[在 Azure 虛擬機器的大小](virtual-machines-linux-sizes.md)

* SAP 記事會列出所有支援 Azure SAP 產品及 SAP 的支援 Azure VM 類型︰ [SAP 筆記 1928533](https://launchpad.support.sap.com/#/notes/1928533/E)

* SAP SAP 附註 」 增強型監控 」 與 Linux Vm 上 Azure: [SAP 筆記 2191498](https://launchpad.support.sap.com/#/notes/2191498/E)

* SAP HANA 提供在 Azure 」 大型的執行個體 」。 請務必瞭解這不是 Azure Vm 上執行 SAP HANA 相關，但在混合式環境 SAP 應用程式伺服器執行 Azure Vm 但 SAP HANA 的位置上執行，答對金屬伺服器︰ [SAP 筆記 2316233](https://launchpad.support.sap.com/#/notes/2316233/E)

* 相關資訊 SAPOSCOL linux SAP 附註︰ [SAP 筆記 1102124](https://launchpad.support.sap.com/#/notes/1102124/E)

* 關鍵 Microsoft Azure 上 SAP 監視指標︰ [SAP 筆記 2178632](https://launchpad.support.sap.com/#/notes/2178632/E)

* Azure 資源管理員] 的相關資訊︰ [Azure 資源管理員的概觀](../azure-resource-manager/resource-group-overview.md)

* 部署 Linux Vm 透過範本的相關資訊︰[部署及管理虛擬機器使用 Azure 資源管理員範本和 Azure CLI](virtual-machines-linux-cli-deploy-templates.md)

* 比較的部署模型之間 Azure 資源管理員] 及 [傳統︰[與傳統部署 Azure 資源管理員︰ 瞭解部署模型，以及您資源的狀態](../resource-manager-deployment-model.md)

* 下載適用於 Linux/HANA 從 SAP 服務商場 NetWeaver 7.5:![](./media/virtual-machines-linux-sap-hana-get-started/image001.jpg)

* 從 SAP 服務商場下載 HANA SP12 平台版︰![](./media/virtual-machines-linux-sap-hana-get-started/image002.jpg)

