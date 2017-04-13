<properties
   pageTitle="重設為 Azure VPN 閘道 |Microsoft Azure"
   description="本文會引導您重設您的 Azure VPN 閘道器。 本文適用於 VPN 傳統，與資源管理員部署模型中的閘道器。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="cherylmc"/>

# <a name="reset-an-azure-vpn-gateway-using-powershell"></a>重設為使用 PowerShell 的 Azure VPN 閘道


本文會引導您重設 Azure VPN 閘道使用 PowerShell cmdlet。 這些指示包含傳統部署模型與資源管理員部署模型。

重設 Azure VPN 閘道器是如果遺失了一或多個 S2S VPN 通道上的跨內部部署 VPN 連線還是有幫助的。 在此情況下，您的內部部署 VPN 裝置所有運作正常，但是無法建立與 Azure VPN 閘道 IPsec 通道。 

在 [作用中待命設定中執行的兩個 VM 執行個體被由每個 Azure VPN 閘道器。 當您使用 PowerShell cmdlet 來重設閘道器時，它重新啟動閘道器，，然後重新套用以便跨內部部署設定。 閘道器會保留它既有的公用 IP 位址。 這表示您不需要新的公用 IP 位址 Azure VPN 閘道器的更新 VPN 路由器設定。  

一旦發出命令，會立即重新啟動 Azure VPN 閘道器目前使用中的執行個體。 在作用中的執行個體 （要重新啟動），備用的執行個體的容錯移轉時，將會有簡短的間距。 間距應一分鐘之內。

如果第一個重新啟動後無法還原連線時，會發出相同的命令來重新啟動第二個 VM 執行個體 （新作用中的閘道）。 如果的兩個重新啟動要求回到上一步，會有稍微長兩個 VM 執行個體 （作用中和備用） 位置正在重新啟動。 這樣就會更長的時間間隔 VPN 連線，最多 2 到 4 分鐘 Vm 完成重新啟動電腦上。

在兩個重新開機後, 如果仍然發生跨內部部署連線問題，請開啟支援要求從 Azure 入口網站。

## <a name="before-you-begin"></a>開始之前

您重設您的閘道之前，請針對每個 IPsec-網站 (S2S) VPN 通道下方所列的重要項目。 任何不相符的項目中會產生的 S2S VPN 通道中斷。 驗證並修正您的內部部署和 Azure VPN 閘道器設定會儲存您不需要重新啟動和中斷的其他工作連線的閘道器。

重設您的閘道之前，請確認下列項目︰

- Azure 與內部部署 VPN 原則中正確設定的網際網路 IP 位址 (Vip) Azure VPN 閘道器] 及 [內部部署 VPN 閘道器。
- 預先共用的金鑰必須是 Azure 與內部部署 VPN 閘道器都相同。
- 如果您套用特定 IPsec/IKE 設定，例如加密雜湊演算法及 PFS （完整轉寄密碼），請確定 Azure 與內部部署 VPN 閘道器都有相同的設定。

## <a name="reset-a-vpn-gateway-using-the-resource-management-deployment-model"></a>重設使用資源管理部署模型 VPN 閘道器

重設閘道器 PowerShell 資源管理員 cmdlet 是`Reset-AzureRmVirtualNetworkGateway`。 下列範例會重設 Azure VPN 閘道器 」 VNet1GW 」，在 [資源群組 」 TestRG1 」。

    $gw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroup TestRG1
    Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw

## <a name="reset-a-vpn-gateway-using-the-classic-deployment-model"></a>重設為使用傳統的部署模型的 VPN 閘道

適用於重設 Azure VPN 閘道器的 PowerShell cmdlet 是`Reset-AzureVNetGateway`。 下列範例會重設 Azure VPN 閘道器的虛擬網路稱為 「 ContosoVNet 」。
 
    Reset-AzureVNetGateway –VnetName “ContosoVNet” 

結果︰

    Error          :
    HttpStatusCode : OK
    Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
    Status         : Successful
    RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
    StatusCode     : OK


## <a name="next-steps"></a>後續步驟
    
請參閱[PowerShell 服務管理 cmdlet 參考](https://msdn.microsoft.com/library/azure/mt617104.aspx)和[PowerShell 資源管理員 cmdlet 參考](http://go.microsoft.com/fwlink/?LinkId=828732)如需詳細資訊。






