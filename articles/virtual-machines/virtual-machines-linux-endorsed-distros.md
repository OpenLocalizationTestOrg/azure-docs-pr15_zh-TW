<properties
    pageTitle="背書 Linux 散發 |Microsoft Azure"
    description="瞭解 Linux 上 Azure 背書散佈，包括 Ubuntu、 OpenLogic、 Oracle，及 SUSE 的指導方針。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management,azure-resource-manager"
    />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="szark"/>



#<a name="linux-on-azure-endorsed-distributions"></a>在 Azure 背書散佈 Linux

> [AZURE.NOTE] 如果您有幾分鐘的時間，請協助我們改進 Azure Linux VM 文件進行[快速問卷](https://aka.ms/linuxdocsurvey)的體驗。 每個答案可幫助幫助您完成工作。

Linux 圖像中的 Azure 圖庫或服務商場所提供的數字的合作夥伴，我們會使用各種 Linux 社群背書通訊群組清單中新增更多的類別。 同時，從圖庫無法使用的散發您一律都能一位您-擁有-Linux 遵循[此頁面](virtual-machines-linux-classic-create-upload-vhd.md)上。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="supported-distributions--versions"></a>支援的散佈與版本 ##

下表列出 Linux 散佈和 Azure 都支援的版本。 請也請參閱[支援的 Microsoft Azure 中 Linux 圖像](https://support.microsoft.com/en-us/kb/2941892)如需詳細資訊。

HYPER-V 和 Azure Linux 整合服務 （清單） 驅動程式是 Microsoft 佔直接到上游 Linux 核心核心模組。  清單驅動程式 [內建通訊群組的核心根據預設，或針對較舊的 RHEL/CentOS 基礎發佈的個別下載[以下](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)。  請參閱[本文](virtual-machines-linux-create-upload-generic.md#linux-kernel-requirements)的清單驅動程式的相關詳細資訊。

Azure Linux 代理程式已預先安裝 Azure 庫圖像，通常是可以使用從通訊群組的套件存放庫。  原始程式碼位於[GitHub](https://github.com/azure/walinuxagent)。

通訊群組|版本|驅動程式|代理程式
---|---|---|---
依 OpenLogic centOS | CentOS 6.3 + 7.0 + | CentOS 6.3︰[清單下載](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)<p>核心中 centOS 6.4 +: | 在 「 WALinuxAgent 」 下[OpenLogic repo](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/)套件︰ <br/>程式碼︰ [GitHub](https://github.com/Azure/WALinuxAgent)
[CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) | 494.4.0+ | 核心 | 原始程式碼︰ [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent)
Debian | Debian 7.9 +、 8.2 + | 核心 | 在 「 waagent 」 下 repo 套件︰ <br/>原始程式碼︰ [GitHub](https://github.com/Azure/WALinuxAgent)
Oracle Linux | 6.4 +、 7.0 + | 核心 | 在 「 WALinuxAgent 」 下 repo 套件︰ <br/>程式碼︰ [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
紅色的角色企業 Linux | RHEL 6.7 + 7.1 + | 核心|在 「 WALinuxAgent 」 下 repo 套件︰ <br/>程式碼︰ [GitHub](https://github.com/Azure/WALinuxAgent)
SUSE Linux 企業版 | SLES 11 SP4、 SLES 12 SP1 + 和 <p> SLES 的 SAP 11 SP3 + | 核心 | 在 「 python azure-代理程式] 底下的[雲端︰ 工具](https://build.opensuse.org/project/show/Cloud:Tools)repo 套件︰ <br/>程式碼︰ [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
openSUSE | openSUSE 13.2 + | 核心 | 在 「 python azure-代理程式] 底下的[雲端︰ 工具](https://build.opensuse.org/project/show/Cloud:Tools)repo 套件︰ <br/>程式碼︰ [GitHub](https://github.com/Azure/WALinuxAgent)
Ubuntu|Ubuntu 12.04，14.04、 16.04 16.10 | 核心 | 在 「 walinuxagent 」 下 repo 套件︰ <br/>程式碼︰ [GitHub](https://github.com/Azure/WALinuxAgent)


## <a name="partners"></a>合作夥伴

### <a name="openlogic"></a>OpenLogic
[http://www.openlogic.com/azure](http://www.openlogic.com/azure)

OpenLogic 是雲端和資料中心企業開啟來源解決方案的前置提供者。 OpenLogic 可協助下數百個前置企業廣泛的行業安全地取得、 支援，並控制開啟來源軟體。 OpenLogic 提供商業級的技術支援人員，以及 indemnification 600 開啟來源套件由 OpenLogic 專家社群，包括企業層級 CentOS 支援，以及在啟動合作夥伴提供 Azure CentOS 為基礎的圖像。

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

從 CoreOS 網站︰

*安全性、 一致性和可靠性的設計 CoreOS。而不是安裝套件透過 yum 或引起，CoreOS 會使用 Linux 容器，來管理您的抽象較高層級的服務。單一服務的程式碼和所有的相依性被封裝容器內，可以在一或多個 CoreOS 電腦上執行。*


### <a name="credativ"></a>Credativ
[http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ 是獨立諮詢服務的公司，於 [在開發和實作專業的方案，透過使用免費的軟體。 前置字元的開啟來源專家，為 Credative 會有許多使用支援的 IT 部門以國際辨識。 與 Microsoft 搭配使用，Credativ 正在目前準備對應 Debian 圖像 Debian 8 （潔） 和 Debian 7 (Wheezy)，特別設計用來在 Azure 上執行，並透過平台可輕鬆地管理之前。 長期維護和更新的 Debian 圖像 Azure 其開啟來源支援中心，也會支援 Credativ。

### <a name="oracle"></a>Oracle
[http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Oracle 的策略是時選擇及具彈性部署中 Oracle 雲朵，以及其他雲朵 Oracle 軟體的方式，讓客戶提供的公開及私密金鑰雲朵解決方案的主要產品組合。  Microsoft oracle 的合作關係可讓客戶部署中的憑證放心 Microsoft 公開及私密金鑰雲朵 Oracle 軟體和支援的 Oracle。  Oracle 的認可與投資 Oracle 公用與私人雲端解決方案不變。

### <a name="red-hat"></a>紅色的角色
[http://www.redhat.com/en/partners/strategic-alliance/microsoft](http://www.redhat.com/en/partners/strategic-alliance/microsoft)

世界前置的開啟來源解決方案提供者，紅色角色協助 90%以上 Fortune 500 大企業解決商務問題、 對齊其 IT 和商務策略，並且準備未來的技術。 紅色角色的運作方式提供安全的方案，透過開啟商務模型和實惠地，如預期呈現訂閱模型。

### <a name="suse"></a>SUSE
[http://www.suse.com/suse-linux-enterprise-server-on-azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server Azure 上是經過驗證的平台，提供更好的可靠性與安全性雲端運算。 Azure 雲端服務進行輕鬆地管理雲端環境完美整合 SUSE 的多功能 Linux 平台。 並使用多個 9,200 有 1800 獨立軟體廠商的 SUSE Linux Enterprise Server 認證應用程式，確保工作負載的資料中心執行支援可以充滿信心地部署上 Azure SUSE。

### <a name="canonical"></a>標準
[http://www.ubuntu.com/cloud/azure](http://www.ubuntu.com/cloud/azure)

標準工程和開放性社群控管磁碟機中用戶端與伺服器雲端運算，包括個人的雲端服務的消費者 Ubuntu 的成功。 標準的願景統一免費的平台中 Ubuntu，在雲端，使用一系列的電話、 平板電腦、 電視及桌面，連貫介面電話讓 Ubuntu 公用雲端提供者各種不同的機構的消費者 electronics 製造商和之間個別的措辭為最愛的第一個選擇。

開發人員及世界各地的工程中心，Canonical 擔負合作夥伴與硬體製造商、 內容提供者軟體開發人員，使 Ubuntu 解決方案來推廣，從伺服器和掌上型裝置的電腦。

