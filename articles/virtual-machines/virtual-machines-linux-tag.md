<properties
   pageTitle="如何標記 Linux 虛擬機器 |Microsoft Azure"
   description="深入了解標記 Linux 虛擬機器 Azure 使用資源管理員部署模型中所建立。"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="07/05/2016"
   ms.author="memccror"/>

# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>若要標記 Linux 虛擬機器 Azure 中的方式

本文將說明在 Azure 中標記 Linux 虛擬機器，透過資源管理員部署模型的不同方式。 標籤都可以直接放置在資源或資源群組的使用者定義的金鑰/值組。 Azure 目前支援最多為 15 的標籤，每個資源與資源群組。 標記可能會放置在資源時建立或新增至現有的資源。 請注意，資源建立資源管理員部署模型只支援標籤。

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Azure CLI 與標記

若要開始，請[安裝和設定 Azure CLI](../xplat-cli-azure-resource-manager.md) ，並確定在資源管理員模式 (`azure config mode arm`)。

您可以指定虛擬機器，包括標籤中，使用這個命令來檢視所有屬性︰

        azure vm show -g MyResourceGroup -n MyTestVM

若要新增新 VM 標記透過 Azure CLI，您可以使用`azure vm set`以及標籤參數**-t**命令︰

        azure vm set -g MyResourceGroup -n MyTestVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

若要移除所有的標籤，您可以使用**– T**參數中的`azure vm set`] 命令。

        azure vm set – g MyResourceGroup –n MyTestVM -T


現在，我們已套用標籤我們 Azure CLI 和入口網站的資源，讓我們來看看使用方式詳細資料，請參閱在 [帳單] 入口網站中的標籤。

[AZURE.INCLUDE [virtual-machines-common-tag-usage](../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>後續步驟

* 若要進一步瞭解標記 Azure 資源，請參閱[Azure 資源管理員概觀][]與[使用的標籤，來組織 Azure 資源][]。
* 若要查看標籤如何協助您管理資源 Azure 減少使用，請參閱[瞭解 Azure 帳單][]和[獲得深入見解到消耗 Microsoft Azure 資源][]。





[Azure CLI environment]: ./xplat-cli-azure-resource-manager.md
[Azure 資源管理員的概觀]: ../azure-resource-manager/resource-group-overview.md
[使用標籤以組織 Azure 資源]: ../resource-group-using-tags.md
[了解 Azure 帳單]: ../billing/billing-understand-your-bill.md
[獲得深入見解到消耗 Microsoft Azure 資源]: ../billing-usage-rate-card-overview.md
