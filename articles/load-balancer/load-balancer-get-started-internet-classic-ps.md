<properties
   pageTitle="開始建立網際網路圖示以使用 PowerShell 傳統模式的負載平衡器 |Microsoft Azure"
   description="瞭解如何建立網際網路圖示以使用 PowerShell 傳統模式的負載平衡器"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/05/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-powershell"></a>開始建立網際網路圖示的負載平衡器 （傳統） 的 PowerShell

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]本文涵蓋傳統部署模型。 您也可以[瞭解如何建立網際網路圖示的負載平衡器使用 Azure 資源管理員](load-balancer-get-started-internet-arm-ps.md)。

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]



## <a name="set-up-load-balancer-using-powershell"></a>設定使用 PowerShell 負載平衡器

若要設定使用 powershell 負載平衡器，請遵循下列步驟︰

1. 如果您從未使用 PowerShell 的 Azure，瞭解[如何安裝和設定 Azure PowerShell](../../articles/powershell-install-configure.md) ，並遵循指示到登入 Azure，然後選取您的訂閱結束。


2. 之後建立虛擬機器，您可以使用 PowerShell cmdlet，將負載平衡器新增至虛擬機器中的相同的雲端服務。

在下列範例中，您將加入負載平衡器設定稱為 「 webfarm 「 雲端服務 「 mytestcloud 」 （或 myctestcloud.cloudapp.net），加名為 「 web1 」 和 「 web2 」 的虛擬機器中的負載平衡器的結束點。 負載平衡器會收到連接埠 80 網路流量與負載平衡虛擬機器所定義的本機的結束點 （在此案例連接埠 80） 之間使用 TCP。


### <a name="step-1"></a>步驟 1
建立第一個 vm 「 web1 」 的負載平衡結束點

    Get-AzureVM -ServiceName "mytestcloud" -Name "web1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

### <a name="step-2"></a>步驟 2

使用相同的負載平衡器設定名稱的第二個 VM 」 web2 」 建立另一個端點

    Get-AzureVM -ServiceName "mytestcloud" -Name "web2" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

## <a name="remove-a-virtual-machine-from-a-load-balancer"></a>虛擬機器移除負載平衡器

您可以使用 [移除 AzureEndpoint 虛擬機器端點移除負載平衡器

    Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin| Update-AzureVM

## <a name="next-steps"></a>後續步驟

您也可以[開始建立內部負載平衡器](load-balancer-get-started-ilb-classic-ps.md)及設定何種類型的[通訊群組模式](load-balancer-distribution-mode.md)especific 負載平衡器網路流量行為。

如果您的應用程式需要保留作用之後負載平衡器伺服器的連線，您可以瞭解更多有關[閒置的 TCP 逾時設定的負載平衡器](load-balancer-tcp-idle-timeout.md)。 它會協助瞭解閒置連線行為，當您使用的 Azure 負載平衡器。

