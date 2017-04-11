<properties
    pageTitle="設計虛擬機器比例縮放比例設定 |Microsoft Azure"
    description="進一步瞭解如何設計您的小數位數的虛擬機器縮放比例設定"
    keywords="linux 虛擬機器，虛擬機器縮放設定" 
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gatneil"
    manager="madhana"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="gatneil"/>

# <a name="designing-vm-scale-sets-for-scale"></a>設計 VM 縮放比例設定的小數位數

本主題探討虛擬機器比例集的設計的考量。 有關虛擬機器縮放比例設定為何，請參閱[虛擬機器縮放比例設定概觀](virtual-machine-scale-sets-overview.md)。


## <a name="storage"></a>儲存空間

縮放比例設定會使用儲存的帳戶，在設定儲存的 Vm OS 磁碟。 我們建議您 20 Vm 每個儲存帳戶或更少的比例。 我們也建議，您分散到字母表儲存體帳戶名稱的開頭字元。 這樣有助於負荷分散到不同的內部系統。 舉例來說，在下一個範本，我們使用 uniqueString 資源管理員範本函數來產生其前面加上儲存的帳戶名稱的前置詞雜湊︰ [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)。


## <a name="overprovisioning"></a>Overprovisioning

開始使用 「 2016年-03-30 」 API 版本，VM 縮放比例設定預設值為 「 overprovisioning 」 Vm。 使用 overprovisioning 開機，縮放比例設定實際更多 Vm 超過，要求您設定的旋轉，然後刪除額外 Vm 的最後一次開始。 Overprovisioning 皆可改善佈建成功率。 您不付費的這些額外的 Vm，並不會計配額限制。

雖然 overprovisioning 並協助改善佈建成功率，它可能會導致混亂行為的不設計用來處理 Vm 消失未宣告的應用程式。 若要開啟關閉 overprovisioning，請確定您在範本中有下列字串: 「 overprovision 」:"false"。 可以[VM 縮放比例設定 REST API 文件](https://msdn.microsoft.com/library/azure/mt589035.aspx)中找到更多詳細資料。

如果您關閉 overprovisioning，您可以立即取得 Vm 較大比例，每個儲存帳戶，但我們不建議移 40 上方。


## <a name="limits"></a>限制
內建在自訂的圖像 （其中一個內建您） 的縮放比例設定，必須建立一個儲存帳戶內的所有 OS 磁碟 Vhd。 因此，建議 Vm 的內建的自訂圖像上的縮放比例設定中的數字的最大值為 20。 如果您關閉 overprovisioning，您可以前往最 40。

內建在平台圖像縮放比例設定為目前僅適用於 100 Vm （我們建議此刻度 5 儲存帳戶）。

更多 Vm 的非允許這些限制，您需要部署多小數位數組，[此範本](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale)所示。