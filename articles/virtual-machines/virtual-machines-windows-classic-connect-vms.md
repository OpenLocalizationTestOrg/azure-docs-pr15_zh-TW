<properties
    pageTitle="連接雲端服務中的 Windows Vm |Microsoft Azure"
    description="連線] 以傳統部署模型至 Azure 雲端服務或虛擬網路建立 Windows 虛擬機器。"
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

# <a name="connect-windows-virtual-machines-created-with-the-classic-deployment-model-with-a-virtual-network-or-cloud-service"></a>連線建立使用傳統的部署模型虛擬網路或雲端服務的 Windows 虛擬機器

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

建立使用傳統的部署模型 Windows 虛擬機器會放置在雲端服務。 雲端服務做為容器，並提供唯一的公用 DNS 名稱與公用的 IP 位址]，在網際網路上存取虛擬機器端點的一組。 雲端服務可在虛擬網路中，但這不是一項需求。 您也可以[將 Linux 虛擬機器虛擬網路或雲端服務的連線](virtual-machines-linux-classic-connect-vms.md)。

如果在雲端服務不在虛擬網路，然後按一下 [*獨立*雲端服務。 獨立雲端服務中的虛擬機器只可以與其他虛擬機器通訊使用其他虛擬機器的公用 DNS 名稱，並在網際網路上移動流量。 如果在雲端服務虛擬網路，虛擬機器中的雲端服務可以與其他所有虛擬網路的虛擬機器而不透過網際網路傳送資料傳輸通訊。

如果您將您的虛擬機器中相同的獨立雲端服務時，您還是可以使用負載平衡與可用性集。 如需詳細資訊，請參閱[負載平衡虛擬機器](virtual-machines-windows-load-balance.md)和[管理的虛擬機器可用性](virtual-machines-windows-manage-availability.md)。 不過，您無法組織子網路上的虛擬機器或連線到您的內部網路的獨立雲端服務。 以下是範例︰

[AZURE.INCLUDE [virtual-machines-common-classic-connect-vms](../../includes/virtual-machines-common-classic-connect-vms.md)]

## <a name="next-steps"></a>後續步驟

建立虛擬機器之後，則若要[新增資料磁碟](virtual-machines-windows-classic-attach-disk.md)建議您先讓您的服務與負載來儲存資料的位置。 