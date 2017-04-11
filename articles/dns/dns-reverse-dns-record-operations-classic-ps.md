<properties
   pageTitle="管理反向 DNS 記錄，您使用 PowerShell 的 Azure （傳統） 服務 |Microsoft Azure"
   description="如何管理反向 DNS 記錄或在傳統的部署模型中使用 PowerShell 的 Azure 服務的 PTR 記錄。 "
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/28/2016"
   ms.author="smalone" />

# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-classic-using-azure-powershell"></a>如何管理使用 PowerShell 的 Azure Azure 服務 （傳統） 的反向 DNS 記錄

[AZURE.INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]
<BR>
[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]瞭解如何[執行這些步驟後，使用資源管理員模型](dns-reverse-dns-record-operations-ps.md)。

## <a name="validation-of-reverse-dns-records"></a>驗證的反向 DNS 記錄
若要確保協力廠商無法建立反向對應到您的 DNS 網域的 DNS 記錄，Azure 只允許建立下列其中一項是 true 反向 DNS 記錄︰

- 反向 DNS FQDN 用它已指定，雲端服務的名稱或任何雲端服務名稱相同的訂閱中例如，反向 DNS 是 「 contosoapp1.cloudapp.net 」。。
- 反向 DNS FQDN 轉寄解析為名稱或 IP 的雲端服務的它已指定，或任何雲端服務名稱，或是 IP 同一份訂閱中例如反向 DNS 是 「 app1.contoso.com 」。 這是 contosoapp1.cloudapp.net CName 別名。

設定或修改雲端服務的反向 DNS 屬性時，只會執行驗證檢查。 不會執行定期重新驗證。

## <a name="add-reverse-dns-to-existing-cloud-services"></a>反向 DNS 新增至現有的雲端服務
您可以新增現有的雲端服務使用 「 設定 AzureService 」 cmdlet 反向 DNS 記錄︰

    PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## <a name="create-a-cloud-service-with-reverse-dns"></a>使用反向 DNS 建立雲端服務
您可以使用 「 設定 AzureService 」 指令程式來指定反向 DNS 屬性新增新的雲端服務︰

    PS C:\> New-AzureService –ServiceName “contosoapp1” –Location “West US” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## <a name="view-reverse-dns-for-existing-cloud-services"></a>檢視反向 DNS 現有的雲端服務
您可以檢視現有的雲端服務使用 「 取得 AzureService 」 指令程式的設定的值︰

    PS C:\> Get-AzureService "contosoapp1"

## <a name="remove-reverse-dns-from-existing-cloud-services"></a>反向 DNS 移除現有的雲端服務
您可以將反向 DNS 屬性移除現有的雲端服務使用 「 設定 AzureService 」 指令程式。 這是設定空白反向 DNS 屬性值︰

    PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “”

[AZURE.INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[AZURE.INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-asm-include.md)]
