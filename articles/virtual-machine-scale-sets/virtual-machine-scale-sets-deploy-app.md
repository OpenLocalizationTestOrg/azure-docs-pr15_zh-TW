<properties
    pageTitle="部署虛擬機器比例集應用程式 |Microsoft Azure"
    description="部署虛擬機器比例集應用程式"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="guybo"/>

# <a name="deploy-an-app-on-virtual-machine-scale-sets"></a>部署虛擬機器比例集應用程式

通常是三種方法的其中一種部署上 VM 縮放比例設定執行應用程式︰

- 部署時，在平台圖像上安裝新的軟體。 在本文中的平台圖像是從 Azure Marketplace，例如 Ubuntu 16.04、 Windows Server 2012 R2 等作業系統圖像。

您可以使用[VM 副檔名](../virtual-machines/virtual-machines-windows-extensions-features.md)的平台圖像上安裝新的軟體。 副檔名為 VM 是執行部署 VM 時的軟體。 您可以執行任何您要部署時使用副檔名為自訂指令碼的程式碼。 [以下](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale)是範例 Azure 資源管理員範本具有兩個 VM 副檔名︰ 安裝 Apache 並 PHP，Linux 自訂指令碼副檔名和診斷副檔名發出使用 Azure 自動調整大小的效能資料。

這種方法的優點是分隔的，您有應用程式碼和 OS，之間的層級，並可以分別維護您的應用程式。 當然所代表的意義有還有更多移動的組件，與 VM 部署時間可能是較長，如果有很多下載和設定的指令碼。

**如果您在您的自訂指令碼副檔名命令 （例如密碼） 傳遞機密資訊，請務必指定`commandToExecute`中`protectedSettings`屬性的自訂指令碼副檔名，而不是`settings`屬性。**

- 建立包含單一 VHD OS 和應用程式的自訂 VM 圖像。 縮放比例設定以下包含 Vm 從所必須若要維持所建立的影像複製一組。 這種方式不需要額外設定 VM 部署次。 不過，在`2016-03-30`版本的 VM 縮放比例設定 （及舊版） 在縮放比例設定 Vm OS 磁碟限於單一儲存帳戶。 因此，您可以在 [縮放比例設定中有 40 Vm 最大值，而不是每個小數位數的 100 VM 設定與平台圖像的限制。 如需詳細資訊，請參閱[縮放比例設定設計概觀](./virtual-machine-scale-sets-design-overview.md)。

- 部署平台或自訂的圖像即基本上容器 host （主機），並安裝應用程式為您管理使用 orchestrator 或設定管理工具的一或多個容器。 這種方法的好處是您有區隔您從應用程式層級的雲端基礎架構，並可以分別維護它們。

## <a name="what-happens-when-a-vm-scale-set-scales-out"></a>設定 VM 縮放比例縮放出時，會發生什麼情況？

當您新增至來增加容量設定小數位數的一個或多個 Vm – 是否手動或自動調整大小 – 透過應用程式會自動安裝。 範例的小數位數，如果有定義的副檔名，他們在上執行新 VM 每次建立時。 如果縮放比例設定為基礎的自訂圖像，任何新 VM 是來源自訂圖像的複本。 如果的小數位數 Vm 是容器主機]，然後您可能需要啟動載入在 [自訂指令碼副檔名容器的程式碼或副檔名可能會安裝代理程式 （例如 Azure 容器服務） 叢集 orchestrator 使用註冊的。

## <a name="how-do-you-manage-application-updates-in-vm-scale-sets"></a>您要如何管理應用程式更新 VM 比例集？

VM 比例組中的應用程式更新，三個主要的方法，請遵循三個的上一個應用程式部署方法︰

* 更新 VM 副檔名。 任何 VM 副檔名所定義的 VM 縮放比例設定新 VM 部署時，現有 VM 每次執行的建立映像，或 VM 副檔名為會更新。 如果您需要更新您的應用程式，直接更新延伸到應用程式是可行的方法，您只要更新擴充定義。 一個簡單的方法是變更為指向新的軟體 fileUris。

* 不變的自訂圖像的方法。 當您將 VM 圖像試吃應用程式 （或應用程式元件） 您可以專注於建置可靠的管線自動建立、 測試和部署的圖像。 您可以設計您架構以幫助您快速交換生產分段的縮放比例設定。 例如這種方法， [Azure Spinnaker 驅動程式運作](https://github.com/spinnaker/deck/tree/master/app/scripts/modules/azure) - [http://www.spinnaker.io/](http://www.spinnaker.io/)。

Packer 和 Terraform 也支援 Azure 資源管理員，請讓您也可以定義圖像 」 作為程式碼 」，並建立 Azure 中，然後 VHD 中使用您的縮放設定。 不過，如此一來，就會成為服務商場圖像位置副檔名/自訂指令碼變得更重要的是因為您沒有直接管理服務商場位元的問題。

* 更新容器。 將容器抽象，例如封裝的應用程式，與應用程式的元件至層雲端基礎架構的應用程式生命週期管理及管理這些容器透過容器 orchestrators 和像主廚/傀儡組態管理員]。

縮放比例設 Vm 成為容器的穩定基底，而只需要偶爾發生的安全性與 OS 相關更新。 如上所述，Azure 容器服務是很好的範例這種方法，以及建置周圍的服務。

## <a name="how-do-you-roll-out-an-os-update-across-update-domains"></a>您該如何更新網域推行 OS 更新？

假設您想要更新時保留 VM 縮放比例設定執行的 OS 圖像。 若要執行此作業的其中一個方法是更新 VM 圖像一個 VM 一次。 您可以執行 PowerShell 的 Azure CLI。 有另一個命令更新 VM 縮放比例設定模型 （如何設定定義），並發出個別 Vm 上的 [手動升級 「 呼叫。

[以下](https://github.com/gbowerman/vmsstools)是範例自動化程序一次更新 VM 縮放比例設定一個更新網域的 Python 指令碼。 (要注意︰ 很多個比強化生產準備解決方案的概念證明 – 您可能會想要新增一些錯誤檢查等等。)。
