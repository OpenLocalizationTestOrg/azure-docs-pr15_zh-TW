<properties
   pageTitle="修改區域網路閘道器 IP 位址首碼和閘道器 IP |Microsoft Azure"
   description="本文會引導您完成變更您的區域網路閘道器的 IP 位址首碼"
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
   ms.date="08/08/2016"
   ms.author="cherylmc"/>

# <a name="modify-local-network-gateway-settings-using-powershell"></a>修改區域網路使用 PowerShell 的閘道器設定

有時候您的區域網路的閘道器 AddressPrefix 或 GatewayIPAddress 變更的設定。 下列程序將協助您修改您的區域網路的閘道器設定。 您也可以修改 Azure 入口網站中的這些設定。

## <a name="before-you-begin"></a>開始之前
    
您需要安裝最新版 Azure 資源管理員 PowerShell cmdlet。 如需安裝的 PowerShell 指令程式的相關資訊，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md) 。

## <a name="to-modify-ip-address-prefixes"></a>若要修改的 IP 位址的開頭

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="to-modify-the-gateway-ip-address"></a>若要修改的閘道器 IP 位址

[AZURE.INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>後續步驟

您可以驗證閘道器的連線。 請參閱[驗證的閘道器的連線](vpn-gateway-verify-connection-resource-manager.md)。

