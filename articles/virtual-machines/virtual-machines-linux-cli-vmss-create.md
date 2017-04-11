<properties
    pageTitle="什麼是 VM 縮放比例設定？ |Microsoft Azure"
    description="深入了解 VM 縮放比例設定。"
    keywords="linux 虛擬機器，虛擬機器縮放設定" 
    services="virtual-machines-linux"
    documentationCenter=""
    authors="gatneil"
    manager="madhana"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machine-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/24/2016"
    ms.author="gatneil"/>

# <a name="what-are-virtual-machine-scale-sets"></a>什麼是虛擬機器縮放比例設定？

虛擬機器縮放比例設定可讓您管理多個 Vm 作為一組。 在高的層級，縮放比例設定有下列優缺點︰

專業人員︰

1. 高的可用性。 每個縮放比例設定放其 Vm 5 故障網域 (FDs) 及 5 更新網域 (UDs) 若要確保設定的顯示狀態 （適用於開啟 FDs 和 UDs，請參閱[VM 可用性](./virtual-machines-linux-manage-availability.md)的詳細資訊）。 
2. 輕鬆地整合 Azure 負載平衡器與應用程式的閘道器。
3. 輕鬆地整合 Azure 自動調整大小的詳細資訊。
4. 簡化的部署，管理及清理 Vm。
5. 支援一般的 Windows 和 Linux 類別，以及自訂的圖像。

缺點︰

1. 無法在縮放比例設定 VM 執行個體附加資料磁碟。 不過，必須使用 Blob 儲存體、 Azure 檔案、 Azure 資料表或其他儲存空間方案。

## <a name="quick-create-using-azure-cli"></a>快速建立使用 Azure CLI

[AZURE.INCLUDE [cli-vmss-quick-create](../../includes/virtual-machines-linux-cli-vmss-quick-create-include.md)]

## <a name="next-steps"></a>後續步驟

一般資訊，請參閱[縮放比例設定為主要的登陸頁面](https://azure.microsoft.com/services/virtual-machine-scale-sets/)。

更多的文件，請參閱[主要文件頁面的 [縮放比例設定](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)。

資源管理員使用小數位數集的範本，例如搜尋 「 vmss 」 中[Azure 快速入門範本 github repo](https://github.com/Azure/azure-quickstart-templates)。

