<properties
    pageTitle="Azure 中 Linux 簡介 |Microsoft Azure"
    description="瞭解如何使用 Linux 虛擬機器上 Azure。"
    services="virtual-machines-linux"
    documentationCenter="python"
    authors="szarkos"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="szark"/>

#<a name="introduction-to-linux-on-azure"></a>在 Azure Linux 簡介

本主題提供一些使用 Linux 虛擬機器 Azure 雲端中的概觀。 部署 Linux 虛擬機器是直接從圖庫中使用圖像的程序。


## <a name="authentication-usernames-passwords-and-ssh-keys"></a>驗證︰ 使用者名稱、 密碼和 SSH 索引鍵

在建立 Linux 虛擬機器使用 Azure 傳統入口網站時，會要求您提供使用者名稱、 密碼或 SSH 公開金鑰。 部署 Linux 上 Azure 虛擬機器中的使用者名稱的選擇受到下列限制式︰ 系統帳戶的名稱 (UID < 100) 存在於虛擬機器不允許，「 根 」，例如。


 - 請參閱[建立執行 Linux 虛擬機器](virtual-machines-linux-quick-create-cli.md)
 - 瞭解[如何使用 SSH Linux Azure 上使用](virtual-machines-linux-mac-create-ssh-keys.md)


## <a name="obtaining-superuser-privileges-using-sudo"></a>取得使用 Superuser 權限`sudo`

在 Azure 虛擬機器執行個體部署期間所指定的使用者帳戶是權限的帳戶。 此帳戶設定 Azure Linux 代理程式都能提高權限根 （superuser 帳戶） 使用`sudo`公用程式。 一旦登入此使用者帳戶，您將能夠使用命令語法根身分執行] 命令

    # sudo <COMMAND>

您可以選擇性地取得使用**sudo s**根命令介面。

- 請參閱[使用根 Linux 虛擬機器中 Azure 權限](virtual-machines-linux-use-root-privileges.md)


## <a name="firewall-configuration"></a>防火牆設定

Azure 提供限制 Azure 傳統入口網站中指定的連接埠連線的輸入封包篩選器。 根據預設，只允許的連接埠是 SSH。 您可能的存取權額外的連接埠上開啟 Linux 虛擬機器藉由設定 Azure 傳統入口網站中的結束點︰

 - 請參閱︰[如何設定虛擬機器中的端點](virtual-machines-windows-classic-setup-endpoints.md)

Linux 圖像 Azure 圖庫中的不會依預設啟用*iptables*防火牆。 如有需要，可能會提供其他篩選設定防火牆。


## <a name="hostname-changes"></a>主機名稱變更

當您初次部署 Linux 圖像的執行個體時，您必須提供的虛擬機器主機名稱。 後虛擬機器執行時，此主機名稱已發佈的平台 DNS 伺服器，讓多個虛擬機器連線至其他可以執行 IP 位址查閱使用 [主機名稱。

如果需要主機名稱變更部署虛擬機器之後，請使用命令

    # sudo hostname <newname>

Azure Linux 代理程式包含可以自動偵測此名稱變更，並適當地設定虛擬機器保存這項變更並發佈的平台 DNS 伺服器這項變更的功能。

 - [Azure Linux 代理程式使用者指南](virtual-machines-linux-agent-user-guide.md)

### <a name="cloud-init"></a>雲端初始化
**Ubuntu**和**CoreOS**圖像運用雲端初始化 Azure 提供其他功能來啟動虛擬機器上。

 - [插入自訂資料的方式](virtual-machines-windows-classic-inject-custom-data.md)
 - [自訂資料和雲端初始化 Microsoft Azure 上](https://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/)
 - [建立使用雲端初始化 Azure 交換磁碟分割區](https://wiki.ubuntu.com/AzureSwapPartitions)
 - [如何使用 CoreOS Azure 上](https://coreos.com/os/docs/latest/booting-on-azure.html)


## <a name="virtual-machine-image-capture"></a>虛擬機器圖像擷取畫面

Azure 提供就可以用來部署額外的虛擬機器執行個體的圖像將擷取的現有的虛擬機器狀態的能力。 Azure Linux 代理程式可能會用來復原自訂佈建程序時所執行的部分。 您可能會遵循下列步驟來擷取虛擬機器為圖像︰

1. 執行**waagent-deprovision**復原佈建自訂。 或者**waagent-deprovision + 使用者**或者，您可以刪除佈建和所有相關的資料時所指定的使用者帳戶。

2. 關閉 [向下/power 虛擬機器。

3. 按一下 [Azure 傳統入口網站中的 [*擷取*，或使用 Powershell 或 CLI 工具來擷取虛擬機器為圖像。

 - 請參閱︰[如何擷取 Linux 虛擬機器使用另存為範本](virtual-machines-linux-classic-capture-image.md)


## <a name="attaching-disks"></a>附加磁碟

每個虛擬機器具有暫存的本機連接的*資源磁碟*。 資源磁碟上的資料可能不是長期重新開機，因為它會經常使用應用程式與執行資料的暫時性與**暫存**的儲存空間的虛擬機器中的程序。 它也用來儲存頁面或交換檔案的作業系統。

在 Linux，會資源磁碟通常是由 Azure Linux 代理程式管理，且自動裝載**/mnt/resource** （或**/mnt** Ubuntu 圖像上）。


>[AZURE.NOTE] 請注意的資源磁碟是一個**暫時性**的磁碟可能會刪除和重新格式化時重新 VM。

可能會由為核心命名資料磁碟上 Linux `/dev/sdc`，，使用者必須以分割、 格式化和裝載的資源。 這涵蓋逐步教學課程︰[如何將附加至虛擬機器資料磁碟](virtual-machines-linux-classic-attach-disk.md)。

 - **另請參閱︰**[設定軟體襲擊 Linux](virtual-machines-linux-configure-raid.md) & [設定 LVM 上 Linux VM Azure 中](virtual-machines-linux-configure-lvm.md)

