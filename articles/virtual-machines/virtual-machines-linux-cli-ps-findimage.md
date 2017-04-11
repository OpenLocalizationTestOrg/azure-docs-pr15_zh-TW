<properties
   pageTitle="選取 Azure CLI Linux VM 影像 |Microsoft Azure"
   description="瞭解如何決定 publisher、 提供的服務，以及 SKU 的圖像時建立 Linux 虛擬機器資源管理員部署模型。"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   />

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="08/23/2016"
   ms.author="rasquill"/>

# <a name="select-linux-vm-images-with-the-azure-cli"></a>選取 [使用 Azure CLI Linux VM 圖像

本主題說明如何尋找發行者、 優惠、 sku 及版本可能您部署每個位置。 若要提供範例，一些常用的 Linux VM 圖像是︰

**常用的 Linux 圖像的表格**


| PublisherName                        | 優惠                                 | Sku                         |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| RedHat                           | RHEL                                       | 7.2                              |
| credativ                         | Debian                                     | 8                                | 
| SUSE                             | openSUSE                                   | 13.2                             |
| SUSE                             | SLES                                       | 12 SP1                           |
| OpenLogic                        | CentOS                                     | 7.1                              |
| 標準                        | UbuntuServer                               | 14.04.4-LTS                      |
| CoreOS                           | CoreOS                                     | 穩定                           |


[AZURE.INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]
