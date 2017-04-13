<properties
   pageTitle="建立一個虛擬網路手臂範本 |Microsoft Azure"
   description="瞭解如何建立使用 ARM 範本的虛擬網路 |資源管理員。"
   services="virtual-network"
   documentationCenter=""
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial"/>

# <a name="create-a-virtual-network-by-using-an-arm-template"></a>使用 ARM 範本來建立虛擬網路

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnet-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]這份文件包含使用資源管理員部署模型建立 VNet。 您也可以[建立虛擬網路傳統部署模型中](virtual-networks-create-vnet-classic-pportal.md)。

您將瞭解如何下載並修改以及 GitHub，從現有的手臂範本及部署從 GitHub、 PowerShell 和 Azure CLI 範本。

如果您只要部署 ARM 範本，直接從 GitHub，不進行任何變更，略過部署[github 範本](#deploy-the-arm-template-by-using-click-to-deploy)。

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-include](../../includes/virtual-networks-create-vnet-arm-template-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-ps-include](../../includes/virtual-networks-create-vnet-arm-template-ps-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-cli-include](../../includes/virtual-networks-create-vnet-arm-template-cli-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-click-include](../../includes/virtual-networks-create-vnet-arm-template-click-include.md)]