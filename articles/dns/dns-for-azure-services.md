<properties
  pageTitle="與其他 Azure 服務使用 Azure DNS |Microsoft Azure"
  description="瞭解如何使用 Azure DNS 解析為其他 Azure 服務名稱"
  services="dns"
  documentationCenter="na"
  authors="sdwheeler"
  manager="carmonm"
  editor=""
  tags="azure dns"
/>
<tags
  ms.service="dns"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="infrastructure-services"
  ms.date="09/21/2016"
  ms.author="sewhee"
/>

# <a name="using-azure-dns-with-other-azure-services"></a>Azure DNS 使用其他 Azure 服務

Azure DNS 是主控的 DNS 管理及名稱解析服務。 這個選項可讓您建立 Azure 中的其他應用程式和服務已部署公用 DNS 名稱。 建立您的自訂網域中的 Azure 服務名稱是非常簡單，新增您的服務的正確類型的記錄。

* 動態配置的 IP 位址，您必須建立對應至 Azure 建立您的服務的 DNS 名稱 DNS CNAME 記錄。 DNS 標準可讓您無法從使用區域 apex 一筆 CNAME 記錄。
* 靜態配置的 IP 位址，您可以建立使用任何的名稱，包括在區域 apex_裸網域_名稱的 DNS 記錄。

下表描述可供各種 Azure 服務的支援的記錄類型。 您可以看到這個資料表中，Azure DNS 只支援的 DNS 記錄的具網際網路網路資源。 無法使用 azure DNS 名稱解析內部、 私人的地址。

| Azure 服務 | 網路介面 | 描述 |
|---------------|-------------------|-------------|
| 應用程式的閘道器 | 前端的公用 IP | 您可以建立 DNS A 或 CNAME 記錄。 |
| 負載平衡器 | 前端的公用 IP | 您可以建立 DNS A 或 CNAME 記錄。 負載平衡器可以有動態指派的 IPv6 公用 IP 位址。 因此，您必須建立 IPv6 位址的 CNAME 記錄。 |
| 流量管理員 | 公用名稱 | 您只可以建立 CNAME 對應至指派給您的資料傳輸管理員設定檔的 trafficmanager.net 名稱。 如需詳細資訊，請參閱[如何流量管理員運作](../traffic-manager/traffic-manager-how-traffic-manager-works.md#traffic-manager-example)。 |
| 雲端服務 | 公用 IP | 靜態配置的 IP 位址，您可以建立的 DNS 記錄。 動態配置的 IP 位址，您必須建立對應到_cloudapp.net_名稱的 CNAME 記錄。 此規則適用於 Vm 部署與雲端服務，因此，在 [傳統] 入口網站中建立。 如需詳細資訊，請參閱[設定雲端服務中的自訂網域名稱](../cloud-services/cloud-services-custom-domain-name-portal.md)。 |
| 應用程式服務 | 外部 IP | 針對外部的 IP 位址，您可以建立的 DNS 記錄。 否則，您必須建立對應到 azurewebsites.net 名稱的 CNAME 記錄。 如需詳細資訊，請參閱[對應至 Azure 應用程式的自訂網域名稱](../app-service-web/web-sites-custom-domain-name.md) |
| 資源管理員 Vm | 公用 IP | 資源管理員 Vm 可以有公用 IP 位址。 使用的公用 IP 位址 VM 也可能前負載平衡器。 您可以建立公用位址的 DNS A 或 CNAME 記錄。 若要略過負載平衡器上的 VIP 可這個自訂的名稱。 |
| 傳統 Vm | 公用 IP | 使用 PowerShell 建立傳統 Vm 或 CLI 可以設定的動態或靜態 （保留） 虛擬地址。 您可以建立 DNS CNAME 或記錄中，分別。 |
