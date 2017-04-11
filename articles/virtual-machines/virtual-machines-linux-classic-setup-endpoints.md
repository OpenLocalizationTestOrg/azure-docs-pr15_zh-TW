<properties
    pageTitle="設定結束點，在 [傳統 Linux VM |Microsoft Azure"
    description="瞭解如何設定為允許與 Linux 虛擬機器通訊 Azure 中 Linux vm Azure 傳統入口網站中的端點"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/13/2016"
    ms.author="cynthn"/>

# <a name="how-to-set-up-endpoints-on-a-linux-classic-virtual-machine-in-azure"></a>如何設定 Linux 傳統虛擬機器 Azure 中的端點

您建立使用傳統的部署模型 Azure 中的所有 Linux 虛擬機器自動可以透過使用相同的雲端服務或虛擬網路中其他虛擬機器私人網路頻道進行都通訊。 不過，在網際網路或其他虛擬網路上的電腦需要將虛擬機器輸入的網路流量導向的結束點。 本文也適用於[Windows 虛擬機器](virtual-machines-windows-classic-setup-endpoints.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]**資源管理員**部署模型中的端點使用**網路安全性群組 (NSGs)**設定。 如需詳細資訊，請參閱 [開啟連接埠和結束點] (虛擬-機器-linux-nsg-quickstart.md)。

當您在 Azure 傳統入口網站中建立 Linux 虛擬機器時，端點的安全命令介面 (SSH) 通常是為您自動。 視需要建立虛擬機器時或之後，您可以設定其他的結束點。
 

[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>後續步驟

* 您也可以使用[Azure 命令列介面](../virtual-machines-command-line-tools.md)建立 VM 結束點。 執行**azure vm 端點建立**的命令。

* 如果您建立虛擬機器中的資源管理員部署模型時，您可以使用 Azure CLI 資源管理員模式，來控制流量的 vm[建立網路安全性群組](../virtual-network/virtual-networks-create-nsg-arm-cli.md)。