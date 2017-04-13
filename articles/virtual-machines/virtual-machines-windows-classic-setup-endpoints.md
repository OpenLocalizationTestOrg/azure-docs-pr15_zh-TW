<properties
    pageTitle="設定結束點，在 [傳統的 Windows VM |Microsoft Azure"
    description="瞭解如何設定為允許與 Windows 虛擬機器通訊 Azure 中 Windows vm Azure 傳統入口網站中的端點。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="how-to-set-up-endpoints-on-a-classic-windows-virtual-machine-in-azure"></a>如何設定在傳統的 Windows 虛擬機器 Azure 中的端點


透過私人通訊的所有的 Windows Azure 使用傳統的部署模型中建立的虛擬機器自動可以使用相同的雲端服務或虛擬網路中其他虛擬機器網路頻道。 不過，在網際網路或其他虛擬網路上的電腦需要將虛擬機器輸入的網路流量導向的結束點。 本文也適用於[Linux 虛擬機器](virtual-machines-linux-classic-setup-endpoints.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]**資源管理員**部署模型中的端點使用**網路安全性群組 (NSGs)**設定。 如需詳細資訊，請參閱 [允許外部存取您 VM 使用 Azure 入口網站] (virtual-machines-windows-nsg-quickstart-portal.md)。

當您建立 Windows 虛擬機器 Azure 傳統入口網站中時，類似於遠端桌面和 Windows PowerShell 遠端處理常見的結束點會通常是為您自動建立。 視需要建立虛擬機器時或之後，您可以設定其他的結束點。



[AZURE.INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>後續步驟

* 若要使用 PowerShell 的 Azure cmdlet 設定 VM 端點，請參閱[新增 AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx)。

* 若要使用 Azure PowerShell cmdlet 來管理 ACL 端點，請參閱[管理存取控制清單 (Acl) 使用 PowerShell 端點](../virtual-network/virtual-networks-acl-powershell.md)。

* 如果您建立虛擬機器中的資源管理員部署模型時，您可以使用 PowerShell 的 Azure 來控制流量的 vm[建立網路安全性群組](../virtual-network/virtual-networks-create-nsg-arm-ps.md)。
