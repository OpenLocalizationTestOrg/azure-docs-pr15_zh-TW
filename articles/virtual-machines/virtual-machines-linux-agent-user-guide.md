<properties 
    pageTitle="Linux 代理程式使用者指南 |Microsoft Azure" 
    description="瞭解如何安裝和設定來管理您的虛擬機器互動 Azure 布料的轉印圖樣控制器 Linux 代理程式 (waagent)。" 
    services="virtual-machines-linux" 
    documentationCenter="" 
    authors="szarkos" 
    manager="timlt" 
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="szark"/>



# <a name="azure-linux-agent-user-guide"></a>Azure Linux 代理程式使用者指南

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="introduction"></a>簡介

Microsoft Azure Linux 代理程式 (waagent) 管理 Linux 與 FreeBSD 佈建和 Azure 布料的轉印圖樣控制站的 VM 互動。 它會提供下列功能 Linux 和 FreeBSD IaaS 部署︰

> [AZURE.NOTE] 請參閱 Azure Linux 代理程式[讀我檔案](https://github.com/Azure/WALinuxAgent/blob/master/README.md)，如需詳細資料。

* **佈建圖像**
  - 建立使用者帳戶
  - 設定 SSH 驗證類型
  - 部署 SSH 公開金鑰和組
  - 設定主機名稱
  - 發佈的平台的 DNS 主機名稱
  - 報表 SSH 主機指紋平台
  - 資源磁碟管理
  - 格式設定及裝載資源磁碟
  - 設定交換空間

* **網路**
  - 管理路由改善與平台 DHCP 伺服器的相容性
  - 可確保網路介面名稱的穩定性

* **核心**
  - 設定虛擬 NUMA (停用核心 < 2.6.37)
  - 會耗費 /dev/random HYPER-V 亂的度
  - 設定根裝置 （這可能是遠端） SCSI 逾時

* **診斷**
  - 主控台重新導向到序列連接埠

* **SCVMM 部署**
    - 偵測並在系統管理中心虛擬機器管理員 2012 R2 環境中執行時 bootstraps Linux 的 VMM 代理程式

* **VM 副檔名**
    - 插入由 Microsoft 和協力廠商撰寫 Linux VM (IaaS) 若要啟用軟體和設定自動化的元件
    - 在[https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions) VM 副檔名參考實作


## <a name="communication"></a>通訊
透過兩個頻道，會發生的平台資訊流程代理程式︰

* 啟動時間附加 DVD IaaS 部署。 此 DVD 包含 OVF 相容的設定檔，其中包含實際 SSH 金鑰組以外的所有佈建資訊。
* TCP 端點公開 REST API 用來取得部署和拓撲設定。


## <a name="requirements"></a>需求
下列系統測試，然後使用 Azure Linux 代理程式已知會︰

> [AZURE.NOTE] 這份清單，可能會有所不同 Microsoft Azure 平台上的支援系統的正式清單如下所示︰ [http://support.microsoft.com/kb/2805216](http://support.microsoft.com/kb/2805216)

* CoreOS
* CentOS 6.3 +
* 紅色的角色企業 Linux 6.7 +
* Debian 7.0 +
* Ubuntu 12.04 +
* openSUSE 12.3 +
* SLES 11 SP3 +
* Oracle Linux 6.4 +

其他支援的系統︰

* FreeBSD 10 + （Azure Linux 代理程式 v2.0.10 +）

Linux 代理程式而定一些系統套件才能正常運作︰

* Python 2.6 +
* OpenSSL 1.0 +
* OpenSSH 5.3 +
* 檔案系統公用程式︰ sfdisk fdisk，mkfs，不會在意
* 密碼工具︰ chpasswd，sudo
* 處理工具的文字︰ sed，grep
* 網路工具︰ ip 路由
* 核心支援裝載 UDF filesystems。

## <a name="installation"></a>安裝
使用您的散發套件存放庫 RPM 或 DEB 套件的安裝是慣用的安裝及升級 Azure Linux 代理程式的方法。 所有[背書分配提供者](virtual-machines-linux-endorsed-distros.md)整合 Azure Linux 代理程式套件的圖像和存放庫。

請參閱[Azure Linux 代理程式 repo Github 上](https://github.com/Azure/WALinuxAgent)的進階的安裝選項，例如安裝來源的資料，或若要自訂的位置] 或 [前置詞中的文件。


## <a name="command-line-options"></a>命令列選項

### <a name="flags"></a>旗標

- 詳細資訊︰ 增加詳細等級指定] 命令
- 強制︰ 略過互動式確認的一些指令

### <a name="commands"></a>命令

- 協助︰ 列出支援的命令和標幟。

- deprovision︰ 嘗試清除系統，並使其更適合重新佈建。 這項作業刪除下列動作︰
 * 所有 SSH 主索引鍵 （如果 Provisioning.RegenerateSshHostKeyPair 設定檔中的 「 y 」）
 * /Etc/resolv.conf 中的名稱伺服器設定
 * 根密碼 /etc/shadow （如果 Provisioning.DeleteRootPassword 設定檔中的 「 y 」）
 * 快取的 DHCP 用戶端租用
 * 重設 localhost.localdomain 主機名稱


> [AZURE.WARNING] 取消提供不保證圖像是已清除所有的機密資訊且適用於發佈。


- deprovision + 使用者︰ 執行下-deprovision （上方） 的所有項目以及刪除 （取自 /var/lib/waagent） 的最後一個提供的使用者帳戶和相關的資料。 取消佈建先前佈建上 Azure，以便可以擷取並重複使用的圖像時，則此參數。

- 版本︰ 顯示 waagent 的版本

- serialconsole︰ 設定標示 ttyS0 幼蟲 （第一個序列連接埠） 為啟動主控台。 這樣核心開機記錄會傳送至序列連接埠和提供偵錯。

- 精靈︰ 執行 waagent 為協助程式來管理與平台的互動。 此引數被指定至 waagent waagent 初始化指令碼中。

- 開始︰ 執行 waagent 為背景的程序


## <a name="configuration"></a>設定

設定檔案 (/ etc/waagent.conf) 控制項 waagent 的動作。 如下所示的範例設定檔案︰

    Provisioning.Enabled=y
    Provisioning.DeleteRootPassword=n
    Provisioning.RegenerateSshHostKeyPair=y
    Provisioning.SshHostKeyPairType=rsa
    Provisioning.MonitorHostName=y
    Provisioning.DecodeCustomData=n
    Provisioning.ExecuteCustomData=n
    Provisioning.PasswordCryptId=6
    Provisioning.PasswordCryptSaltLength=10
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.MountOptions=None
    ResourceDisk.EnableSwap=n
    ResourceDisk.SwapSizeMB=0
    LBProbeResponder=y
    Logs.Verbose=n
    OS.RootDeviceScsiTimeout=300
    OS.OpensslPath=None
    HttpProxy.Host=None
    HttpProxy.Port=None

以下詳細說明各種不同的設定選項。 設定選項是三種類型;布林值、 字串或整數。 布林值設定選項可以指定為"y"或"n"。 [無] 可用於某些字串類型設定項目詳細下列特殊關鍵字。

**Provisioning.Enabled:**  
類型︰ 布林值  
預設︰ y

這個選項可讓使用者，以啟用或停用代理程式中提供的功能。 有效的值是"y"或"n"。 如果佈建已停用，會保留在圖像中的 SSH host （主機） 與使用者鍵，然後指定佈建 API Azure 中的任何設定會忽略。

> [AZURE.NOTE] `Provisioning.Enabled`值將用於雲端初始化佈建的 Ubuntu 雲端圖像上的"n"。

  
**Provisioning.DeleteRootPassword:**  
類型︰ 布林值  
預設︰ n

如果設定，根密碼/等/陰影檔案中會清除提供程序。


**Provisioning.RegenerateSshHostKeyPair:**  
類型︰ 布林值  
預設︰ y

如果設定，所有 SSH 主索引鍵的組合 （ecdsa 來、 dsa 和 rsa） 會刪除從 /etc/ssh/提供程序。 並產生單一的全新組。

全新的金鑰的加密類型是由 Provisioning.SshHostKeyPairType 項目設定的。 請注意一些散佈會重新建立的任何缺少的加密類型 SSH 組重新啟動 SSH 精靈時 （例如，在重新啟動電腦）。


**Provisioning.SshHostKeyPairType:**  
類型︰ 字串  
預設︰ rsa

這可以設定為支援的虛擬機器上的 SSH 精靈的加密演算法類型。 一般支援的值為 「 rsa 」、 「 dsa 」 和 「 ecdsa 來 」。 請注意，在 Windows 上的 「 putty.exe 」 不支援 「 ecdsa 來 」。 因此，如果您想要在 Windows 上使用 putty.exe 連線至 Linux 部署，請使用 「 rsa 」 或 「 dsa 」。


**Provisioning.MonitorHostName:**  
類型︰ 布林值  
預設︰ y

如果設定，waagent 會監控 Linux 虛擬機器主機名稱變更 （如下所傳回的 「 主機名稱] 命令） 和自動更新以反映的變更圖像網路的設定。 以推的 DNS 伺服器的名稱變更，請將虛擬機器中重新啟動網路。 這會簡短失去網際網路連線。


**Provisioning.DecodeCustomData**  
類型︰ 布林值  
預設︰ n

如果設定，waagent 會解碼從 Base64 CustomData。


**Provisioning.ExecuteCustomData**  
類型︰ 布林值  
預設︰ n

如果設定，waagent 執行 CustomData 佈建之後。


**Provisioning.PasswordCryptId**  
字串類型︰  
預設︰ 6

產生密碼雜湊時使用窖演算法。  
 1-MD5  
 2a-Blowfish  
 5-SHA-256  
 6-SHA-512  


**Provisioning.PasswordCryptSaltLength**  
字串類型︰  
預設︰ 10

產生密碼雜湊時所使用的隨機 salt 的長度。


**ResourceDisk.Format:**  
類型︰ 布林值  
預設︰ y

如果設定，請提供的平台的資源磁碟會格式化並裝載 waagent，如果要求使用者在 「 ResourceDisk.Filesystem 」 中的檔案系統類型是 「 ntfs 」 以外的任何項目。 單一類型 Linux (83) 的磁碟分割將可供磁碟上。 請注意，是否可以順利裝載它，不會格式化資料分割。


**ResourceDisk.Filesystem:**  
類型︰ 字串  
預設︰ ext4

指定資源磁碟的檔案系統類型。 支援的值會因 Linux 通訊群組。 如果字串的 X，然後 mkfs。X 應該出現在 Linux 圖像。 SLES 11 圖像應該通常會使用 「 ext3 」。 FreeBSD 圖像應該使用 「 ufs2 」 以下所示。


**ResourceDisk.MountPoint:**  
類型︰ 字串  
預設: / mnt/資源 

指定裝載資源磁碟時的路徑。 請注意，資源磁碟是一個*暫時性*的磁碟可能已清空時 VM 會取消提供。


**ResourceDisk.MountOptions**  
類型︰ 字串  
預設︰ 無

指定要傳遞給裝載 command 和 o] 命令的磁碟裝載選項。 這是逗點分隔清單的值，例如。 「 nodev nosuid 」。 請參閱 mount(8) 如需詳細資訊。


**ResourceDisk.EnableSwap:**  
類型︰ 布林值  
預設︰ n

如果設定，交換檔案 (/ 交換檔) 資源磁碟上建立並新增至系統交換空間。


**ResourceDisk.SwapSizeMB:**  
類型︰ 整數  
預設︰ 0

以百萬位元組交換檔案的大小。


**Logs.Verbose:**  
類型︰ 布林值  
預設︰ n

如果提高集，記錄詳細等級。 Waagent 登 /var/log/waagent.log，並利用旋轉記錄系統 logrotate 功能。


**作業系統。EnableRDMA**  
類型︰ 布林值  
預設︰ n

如果設定，請代理程式將會嘗試安裝，然後將載入符合的基礎硬體上韌體版本 RDMA 核心驅動程式。


**作業系統。RootDeviceScsiTimeout:**  
類型︰ 整數  
預設︰ 300

在 OS 磁碟和資料磁碟機上的秒數，這就會設定 SCSI 逾時。 如果沒有設定的系統會使用預設值。


**作業系統。OpensslPath:**  
類型︰ 字串  
預設︰ 無

這可以用於指定二進位用於加密作業 openssl 替代路徑。


**HttpProxy.Host HttpProxy.Port**  
類型︰ 字串  
預設︰ 無

如果設定，請代理程式會使用此 proxy 伺服器存取網際網路。 


## <a name="ubuntu-cloud-images"></a>Ubuntu 雲端圖像

請注意 Ubuntu 雲端圖像，利用[雲端初始化](https://launchpad.net/ubuntu/+source/cloud-init)執行許多否則想管理 Azure Linux 代理程式的設定工作。  請注意差異如下︰


- **Provisioning.Enabled**預設值為"n"在使用雲端初始化執行佈建工作的 Ubuntu 雲端圖像。

- 下列設定參數有不會影響使用雲端初始化管理資源磁碟和交換空間的 Ubuntu 雲端圖像︰

 - **ResourceDisk.Format**
 - **ResourceDisk.Filesystem**
 - **ResourceDisk.MountPoint**
 - **ResourceDisk.EnableSwap**
 - **ResourceDisk.SwapSizeMB**

- 請參閱下列資源設定資源磁碟連接點，並提供時交換空間 Ubuntu 雲端圖像︰

 - [Ubuntu Wiki︰ 設定交換磁碟分割區](http://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
 - [插入自訂資料到 Azure 虛擬機器](virtual-machines-windows-classic-inject-custom-data.md)

