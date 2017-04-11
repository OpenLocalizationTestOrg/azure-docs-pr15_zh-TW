<properties
    pageTitle="建立虛擬機器縮放比例設定使用 Azure 入口網站 |Microsoft Azure"
    description="部署使用 Azure 入口網站的縮放設定。"
    keywords="虛擬機器縮放設定" 
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gatneil"
    manager="madhana"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/15/2016"
    ms.author="gatneil"/>

# <a name="create-a-virtual-machine-scale-set-using-the-azure-portal"></a>建立虛擬機器縮放比例設定使用 Azure 入口網站

本教學課程教您如何輕鬆使用來建立虛擬機器縮放比例設定在幾分鐘 Azure 入口網站。 如果您沒有安裝 Azure 的訂閱，建立一個[免費的帳戶](https://azure.microsoft.com/free/)，才能開始。

## <a name="choose-the-vm-image-from-the-marketplace"></a>選擇 [從服務商場 VM 圖像

從入口網站，您可以輕鬆地部署設定 CentOS、 CoreOS、 Debian、 開啟 Suse、 紅色角色企業 Linux、 SUSE Linux Enterprise Server、 Ubuntu 伺服器或 Windows Server 圖像的比例。

首先，請瀏覽至網頁瀏覽器中[Azure 入口網站](https://portal.azure.com)。 按一下 [ `New`，搜尋`scale set`，然後選取 [`Virtual machine scale set`項目︰

![ScaleSetPortalOverview](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalOverview.PNG)

## <a name="create-the-linux-virtual-machine"></a>建立 Linux 虛擬機器

現在您可以使用的預設設定，並快速建立虛擬機器。

* 在`Basics`刀，輸入縮放比例設定的名稱。 此名稱就會變成對數的 FQDN 負載平衡器前面的縮放比例設定，以確定所有 Azure 是唯一的名稱。

* 選取您想要的 OS 輸入，請輸入您想要的使用者名稱，然後選取 [驗證輸入您想要。 如果您選擇的密碼時，必須至少 12 個字元長，而且符合三種四個下列複雜度需求︰ 一個小寫字元、 一個大寫字元、 一個數字、 和一個特殊字元。 請參閱深入瞭解[使用者名稱和密碼的需求](../virtual-machines/virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm)。 如果您選擇`SSH public key`，請務必只貼上您公開金鑰中，不是您私人的金鑰︰

![ScaleSetPortalBasics](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalBasics.PNG)

* 輸入您想要的資源群組名稱] 和 [位置]，然後按 [ `OK`。

* 在`Virtual machine scale set service settings`刀︰ 輸入您想要的網域名稱標籤 （底數的負載平衡器縮放比例設定前面的 FQDN）。 在所有 Azure，此標籤必須都是唯一的。

* 選擇您想要的作業系統磁碟映像、 執行個體計數與電腦大小。

* 啟用或停用自動調整大小並設定如果啟用︰

![ScaleSetPortalService](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalService.PNG)

* 在`Summary`刀驗證完成時，按一下 [ `OK`。

* 最後，在`Purchase`刀，按一下 [`Purchase`開始縮放比例設定部署。

## <a name="connect-to-a-vm-in-the-scale-set"></a>在縮放比例設定 vm 連線

瀏覽至您的縮放比例設定為部署， `Inbound NAT Rules` ] 索引標籤的縮放比例設定負載平衡器︰

![ScaleSetPortalNatRules](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalNatRules.PNG)

您可以連線至每個 VM 中使用這些 NAT 的規則集小數位數。 舉例來說，Windows 縮放比例組，向內連接埠 50000 NAT 規則時您無法連線至 RDP 透過該電腦上`<load-balancer-ip-address>:50000`。 Linux 比例組，您會使用連線命令`ssh -p 50000 <username>@<load-balancer-ip-address>`。

## <a name="next-steps"></a>後續步驟

如需如何部署從 CLI 的縮放比例集的文件，請參閱[此文件](./virtual-machine-scale-sets-cli-quick-create.md)。

如需如何部署從 PowerShell 比例集的文件，請參閱[此文件](./virtual-machine-scale-sets-windows-create.md)。

如需如何部署從 Visual Studio 的縮放設定，請參閱[本文件](./virtual-machine-scale-sets-vs-create.md)的文件。

一般的文件，請參閱[文件概觀] 頁面的 [縮放比例設定](./virtual-machine-scale-sets-overview.md)。

一般資訊，請參閱[縮放比例設定為主要的登陸頁面](https://azure.microsoft.com/services/virtual-machine-scale-sets/)。

