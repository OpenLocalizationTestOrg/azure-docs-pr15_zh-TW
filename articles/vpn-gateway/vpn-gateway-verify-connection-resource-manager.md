<properties
   pageTitle="確認閘道器連線 |Microsoft Azure"
   description="本文將示範如何確認閘道器在資源管理員部署模型連線"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="verify-a-gateway-connection"></a>確認閘道器的連線

您可以驗證您的閘道器連線以數種不同的方法。 本文將說明如何使用 [Azure 入口網站，以及使用 PowerShell 驗證的資源管理員閘道器連線狀態。


## <a name="verify-using-powershell"></a>確認使用 PowerShell

您需要安裝最新版 Azure 資源管理員 PowerShell cmdlet。 安裝 PowerShell cmdlet 的詳細資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。 如需有關如何使用資源管理員 cmdlet 的詳細資訊，請參閱[使用 Windows PowerShell 與資源管理員](../powershell-azure-resource-manager.md)。

### <a name="step-1-log-in-to-your-azure-account"></a>步驟 1︰ 登入您的 Azure 帳戶

1. 開啟您的 PowerShell 主控台以提高權限並連線到您的帳戶。

        Login-AzureRmAccount

2. 核取訂閱的帳戶。

        Get-AzureRmSubscription 

3. 指定您想要使用的訂閱。

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

### <a name="step-2-verify-your-connection"></a>步驟 2︰ 確認您的連線


[AZURE.INCLUDE [verify connection powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)] 


## <a name="verify-using-the-azure-portal"></a>確認使用 Azure 入口網站

[AZURE.INCLUDE [verify connection portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)] 


## <a name="next-steps"></a>後續步驟

- 您可以新增您的虛擬網路虛擬機器。 如需的步驟，請參閱[建立虛擬機器](../virtual-machines/virtual-machines-windows-hero-tutorial.md)。

