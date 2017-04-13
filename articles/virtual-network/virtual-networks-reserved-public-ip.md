<properties
   pageTitle="保留 IP |Microsoft Azure"
   description="瞭解如何管理其保留的 IPs"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/10/2016"
   ms.author="jdial" />

# <a name="reserved-ip-overview"></a>保留的 IP 概觀
Azure 中的 IP 位址分為兩種類別︰ 動態和保留。 Azure 所管理的公用 IP 位址是預設的動態。 這表示的 IP 位址用於指定的雲端服務 (VIP) 或存取 VM 或角色執行個體直接 (ILPIP) 可以變更的時候，資源可關閉或解除配置。

若要防止變更 IP 位址，您可以保留 IP 位址。 保留的 IPs 只能作為 VIP，確保雲端服務的 IP 位址會相同即使資源關閉或解除配置。 此外，您可以將現有的動態 IPs 用來做為要保留的 IP 位址 VIP 轉換。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]瞭解如何將保留使用[資源管理員部署模型](virtual-network-ip-addresses-overview-arm.md)靜態公用 IP 位址。

請確定您瞭解[IP 位址](virtual-network-ip-addresses-overview-classic.md)Azure 中的運作方式。

## <a name="when-do-i-need-a-reserved-ip"></a>我何時需要保留的 IP？
- **您想要確定 IP 會保留在您的訂閱**。 如果您想要保留的 IP 位址，不會釋放從您的訂閱在任何情況下，您應該使用保留的公用 IP。  
- **您想要保持雲端服務已停止或解除配置狀態 (Vm) 甚或您 IP**。 如果您想要使用的 IP 位址，不會存取您的服務，甚至雲端服務中的 Vm 時停止或解除配置。
- **您想要確保從 Azure 的外寄流量使用如預期呈現的 IP 位址**。 您可能需要您的內部部署防火牆設定為允許來自特定 IP 位址的流量。 保留 IP，您就會知道來源 IP 位址，並不會更新您的防火牆規則，因為 IP 變更。

## <a name="faq"></a>常見問題集
1. 我可以使用所有 Azure 服務保留的 IP 嗎？  
  - 保留的 IPs 只用於 Vm 和雲端服務的執行個體角色透過 VIP 公開。
1. 我能有多少保留的 IPs？  
  - 此時，所有 Azure 訂閱授權使用 20 保留的 IPs。 不過，您可以要求其他保留的 IPs。 [[訂閱及服務限制](../azure-subscription-service-limits.md)的頁面，如需詳細資訊，請參閱。
1. 為保留 IPs 上有免費嗎？
  - 價格資訊請參閱[保留 IP 位址定價詳細資料](http://go.microsoft.com/fwlink/?LinkID=398482)。
1. 我要如何保留 IP 位址？
  - 您可以使用 PowerShell 或[Azure 管理 REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx) ，若要保留的特定區域中的 IP 位址。 此保留的 IP 位址是您的訂閱相關聯。 您無法使用 [管理入口網站，以保留 IP 位址。
1. 我可以使用這與相關性群組依據 VNets 嗎？
  - 保留的 IPs 只支援地區 VNets。 不支援相關性群組相關聯的 VNets。 如需有關 VNet 關聯地區或相關性群組的詳細資訊，請參閱[關於地區 VNets 與相關性群組。](virtual-networks-migrate-to-regional-vnet.md)

## <a name="how-to-manage-reserved-vips"></a>如何管理保留的 Vip

您可以使用保留的 IPs 之前，您必須將其新增至您的訂閱。 若要建立保留的 IP 集區中的公用 IP 位址可在 [*美國中部*位置，請執行下列 PowerShell 命令︰

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

請注意，不過，您無法指定 IP 正被保留。 若要檢視您的訂閱中保留哪些 IP 位址，請執行以下的 PowerShell 命令，並注意值*ReservedIPName*和*地址*︰

    Get-AzureReservedIP

    ReservedIPName       : MyReservedIP
    Address              : 23.101.114.211
    Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
    Label                :
    Location             : Central US
    State                : Created
    InUse                : False
    ServiceName          :
    DeploymentName       :
    OperationDescription : Get-AzureReservedIP
    OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
    OperationStatus      : Succeeded

一旦 IP 會保留，它會保持至您的訂閱相關聯，直到您將其刪除。 若要刪除保留的 IP 如上所示，請執行下列 PowerShell 命令︰

    Remove-AzureReservedIP -ReservedIPName "MyReservedIP"

## <a name="how-to-reserve-the-ip-address-of-an-existing-cloud-service"></a>若要保留現有的雲端服務的 IP 位址的方式

您可以藉由新增*-ServiceName*參數保留現有的雲端服務的 IP 位址。 若要保留在雲端服務*TestService*在 [*美國中部*位置中的 IP 位址，執行下列 PowerShell 命令︰

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService


## <a name="how-to-associate-a-reserved-ip-to-a-new-cloud-service"></a>如何建立新的雲端服務的保留的 IP 之間的關聯
建立新的保留的 IP 下方的指令碼，然後將其關聯到新名為*TestService*雲端服務。

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"

>[AZURE.NOTE] 與雲端服務搭配使用的保留的 IP 建立後，您仍需要使用參照 VM *VIP:&lt;連接埠號碼 >*連入通訊。 預約 IP 不表示您可以直接連接到 VM。 保留的 IP 指派給 VM 已部署至雲端服務。 如果您想要直接連接到 ip VM，您必須設定的執行個體層級的公用 IP。 執行個體層級的公用 IP 是一種公用 IP （稱為 ILPIP） 的直接指派給您 VM。 無法保留。 如需詳細資訊，請參閱[執行個體層級公用 IP (ILPIP)](virtual-networks-instance-level-public-ip.md) 。

## <a name="how-to-remove-a-reserved-ip-from-a-running-deployment"></a>如何從執行的部署移除保留的 IP
若要移除保留的 IP 新增至新的服務建立上述的指令碼，請執行下列 PowerShell 命令︰

    Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService

>[AZURE.NOTE] 從執行的部署移除保留的 IP 並不會從您的訂閱移除保留項目。 只要釋出您的訂閱中的另一個資源所使用的 IP。

## <a name="how-to-associate-a-reserved-ip-to-a-running-deployment"></a>如何建立與執行的部署保留的 IP 之間的關聯
下方的指令碼會建立新的雲端服務，名為*TestService2*與新名為*TestVM2*，VM，然後將現有的保留的 IP 命名*MyReservedIP*雲端服務相關聯。

    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| New-AzureVM -ServiceName TestService2 -Location "Central US"
    Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2

## <a name="how-to-associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>如何使用 [服務設定檔關聯到雲端服務的保留的 IP
您也可以使用 [服務設定 (CSCFG) 檔案關聯到雲端服務的保留的 IP。 下列範例 xml 示範如何設定在雲端服務，使用名為*MyReservedIP*保留的 VIP:

    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <ReservedIPs>
           <ReservedIP name="MyReservedIP"/>
          </ReservedIPs>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## <a name="next-steps"></a>後續步驟

- 了解如何[IP 位址](virtual-network-ip-addresses-overview-classic.md)傳統部署模型中的運作。

- 深入了解[保留私用的 IP 位址](virtual-networks-reserved-private-ip.md)。

- 瞭解[執行個體層級公用 IP (ILPIP) 地址](virtual-networks-instance-level-public-ip.md)。
