<properties
   pageTitle="多個 Vip 雲端服務"
   description="MultiVIP 及如何設定在雲端服務上的多個 Vip 概觀"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="configure-multiple-vips-for-a-cloud-service"></a>設定多個 Vip 雲端服務

您可以使用 Azure 所提供的 IP 位址透過公用網際網路存取 Azure 雲端服務。 這個公用 IP 位址指 VIP (虛擬 IP) 因為它連結至 Azure 負載平衡器，並不虛擬機器 (VM) 執行個體中的雲端服務。 您可以使用單一 VIP 存取雲端服務中的任何 VM 執行個體。

然而，有可能需要中一個以上的項目為 VIP 指向相同的雲端服務的案例。 比方說，您的雲端服務可能會裝載需要使用的預設連接埠 443、 SSL 連線，為每個網站裝載的不同的客戶，或租用戶的多個網站。 在此案例中，您需要有不同公用具的 IP 位址的每個網站。 下圖說明一般多租用戶虛擬主機需要多個 SSL 憑證相同的公用連接埠。

![多重 VIP SSL 案例](./media/load-balancer-multivip/Figure1.png)

在上述範例中，所有 Vip 都使用相同的公用連接埠 (443) 和流量重新導向至一或多負載平衡 Vm 內部 IP 位址的主機服務所有網站的雲端服務的唯一私人連接埠。

>[AZURE.NOTE] 需要使用多個裡的另一種情況裝載相同的虛擬機器上多個 SQL AlwaysOn 可用性群組接聽。

Vip 是動態根據預設，這表示一段時間，可能會變更指派到雲端服務的實際 IP 位址。 若要防止發生此狀況，您可以保留 VIP 服務。 若要進一步瞭解保留 Vip，請參閱[保留的公用 IP](../virtual-network/virtual-networks-reserved-public-ip.md)。

>[AZURE.NOTE] 請參閱在 Vip 價格和保留的 IPs 相關資訊的[IP 位址價格](https://azure.microsoft.com/pricing/details/ip-addresses/)。

您可以使用 PowerShell 來確認裡使用雲端服務，以及新增或移除 Vip、 建立關聯 VIP 端點，以及設定負載平衡與特定 VIP 上。

## <a name="limitations"></a>限制

此時，多重 VIP 功能僅限於下列情況︰

- **僅限 IaaS**。 您只可以啟用多重 VIP 包含 Vm 雲端服務。 您無法使用多重 VIP PaaS 案例的角色執行個體。
- **僅限 PowerShell**。 您只能使用 PowerShell 來管理多重 VIP。

這些限制會暫時，並在任何時候，可能會變更。 請確定重新瀏覽此頁面，以確認未來的變更。


## <a name="how-to-add-a-vip-to-a-cloud-service"></a>如何將 VIP 新增至雲端服務

若要新增 VIP 您的服務，請執行下列 PowerShell 命令︰

    Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService

這個命令將結果顯示為類似下面的範例︰

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>如何移除 VIP 來自雲端服務

若要移除 VIP 新增至您在上述範例中的服務，請執行下列 PowerShell 命令︰

    Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService

>[AZURE.IMPORTANT] 如果不有任何其相關聯的端點，您只能移除 VIP。

## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>如何從雲端服務擷取 VIP 資訊

若要擷取 Vip 雲端服務相關聯，請執行下列 PowerShell 指令碼︰

    $deployment = Get-AzureDeployment -ServiceName myService
    $deployment.VirtualIPs

指令碼將結果顯示為類似下面的範例︰

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

此範例中，在雲端服務會有 3 Vip:

- **Vip1**預設 VIP 時，您知道因為 IsDnsProgrammedName] 的值設定為 true。
- 沒有任何 IP 位址時，不會使用**Vip2**和**Vip3** 。 他們會只能用於如果您將 vip 端點。

>[AZURE.NOTE] 您的訂閱只需要付費額外 Vip 後端點與相關聯。 如需有關價格的詳細資訊，請參閱[價格 IP 位址](https://azure.microsoft.com/pricing/details/ip-addresses/)。

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>如何建立 VIP 端點之間的關聯

若要建立關聯 VIP 雲端服務的端點上，執行下列 PowerShell 命令︰

    Get-AzureVM -ServiceName myService -Name myVM1 `
  	| Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 `
  	| Update-AzureVM

命令會建立連結的連接埠*80*和它的 vm 命名為*myVM1*名為*myService*使用*TCP*連接埠*8080*雲端服務中的連結呼叫*Vip2* vip 端點。

若要確認設定，請執行下列 PowerShell 命令︰

    $deployment = Get-AzureDeployment -ServiceName myService
    $deployment.VirtualIPs

輸出看起來就像以下範例︰

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         : 191.238.74.13
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>如何啟用負載平衡上特定的 VIP

您可以將單一 VIP 關聯負載平衡用途的多個虛擬機器。 例如，您有雲端服務，名為*myService*，以及 「 *myVM1* *myVM2*的兩個虛擬機器。 而且您的雲端服務有多個 Vip，有一個名為*Vip2*。 如果您想要確保所有流量*Vip2*連接埠*81*之間*myVM1*和連接埠*8181*，執行下列 PowerShell 指令碼上*myVM2*都平均︰

    Get-AzureVM -ServiceName myService -Name myVM1 `
  	| Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet `
        -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe `
  	| Update-AzureVM

    Get-AzureVM -ServiceName myService -Name myVM2 `
  	| Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet `
        -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe `
  	| Update-AzureVM

您也可以更新您的負載平衡器，若要使用不同的 VIP。 例如，如果您執行以下的 PowerShell 命令，將會變更負載平衡設定為使用名為 Vip1 VIP:

    Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1

## <a name="next-steps"></a>後續步驟

[Azure 負載平衡記錄狀況分析](load-balancer-monitor-log.md)

[網際網路具負載平衡器概觀](load-balancer-internet-overview.md)

[開始使用網際網路對負載平衡器](load-balancer-get-started-internet-arm-ps.md)

[虛擬網路概觀](../virtual-network/virtual-networks-overview.md)

[保留的 IP REST Api](https://msdn.microsoft.com/library/azure/dn722420.aspx)