<properties
    pageTitle="如何設定 VPN 連線 Azure API 管理"
    description="瞭解如何設定 VPN 連線 Azure API 管理和瀏覽的 access web 服務。"
    services="api-management"
    documentationCenter=""
    authors="antonba"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="antonba"/>

# <a name="how-to-setup-vpn-connections-in-azure-api-management"></a>如何設定 VPN 連線 Azure API 管理

API 管理 VPN 支援可讓您連線至 Azure 虛擬網路 （傳統） API 管理閘道。 這個選項可讓 API 管理客戶可安全地連結內部部署或無法加以公用網際網路存取其後端 web 服務。

>[AZURE.NOTE] Azure API 管理搭配傳統 VNETs。 如需建立傳統的 VNET 資訊，請參閱[建立虛擬網路 （傳統） 使用 Azure 入口網站](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。 在傳統 VNETs 連線至 ARM VNETS 的資訊，請參閱[連線至新的 VNets 的傳統 VNets](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)。

## <a name="enable-vpn"></a>啟用 VPN 連線

>VPN 連線只適用於**進階版**及**開發人員**層。 切換至該[Azure 傳統入口網站][]中開啟您 API 管理的服務，然後開啟 [**縮放比例**] 索引標籤。 在 [**一般**] 區段底下選取 [進階版層，然後按一下 [儲存]。

若要啟用 VPN 連線， [Azure 傳統入口網站][]中開啟您 API 管理的服務並切換到 [**設定**] 索引標籤。 

在 [VPN] 區段中，切換**VPN 連線**至**上**。

![設定 API 管理執行個體] 索引的標籤][api-management-setup-vpn-configure]

您現在會看到您的 API 管理服務佈建後的位置的所有區域的清單。

選取要有 vpn 才能和子網路之每個區域。 Vpn 清單會填入根據虛擬 Azure 訂閱中可用的網路中您要設定的地區設定。

![選取要有 vpn 才能][api-management-setup-vpn-select]

按一下畫面底部的 [**儲存**]。 您無法從 Azure 傳統入口網站執行其他 API 管理服務的作業，並將其更新。 服務閘道器會都會持續為可用，則不受影響執行階段呼叫。

請注意，閘道器的 VIP 位址會變更每次 VPN 已啟用或停用。

## <a name="connect-vpn"></a>前 VPN web 服務的連線

您的 API 管理服務連線到 VPN 後，存取虛擬網路中的 web 服務和並無不同存取公用服務。 只在本機的地址或 web 服務的主機名稱 （如果 Azure 虛擬網路設定 DNS 伺服器） 在欄位中輸入**的 Web 服務 URL**時建立新的 API 或編輯現有。

![從 VPN 新增 API][api-management-setup-vpn-add-api]

## <a name="required-ports-for-api-management-vpn-support"></a>需的 API 管理 VPN 支援的連接埠

當中 VNET 裝載 API 管理服務執行個體時，請使用下表中的連接埠。 如果這些連接埠遭到封鎖，服務可能無法正常運作。 一或多個封鎖下列連接埠是最常見的錯誤設定問題時使用 VNET API 管理。

| 連接埠                      | 方向        | 傳輸通訊協定 | 用途                                                          | 來源 / 目的地              |
|------------------------------|------------------|--------------------|------------------------------------------------------------------|-----------------------------------|
| 80、 443                      | 連入          | TCP                | 用戶端通訊 API 管理                           | 網際網路 / VIRTUAL_NETWORK        |
| 80,443                       | 輸出         | TCP                | Azure 儲存和 Azure 服務匯流排 API 管理相依性 | VIRTUAL_NETWORK / 網際網路        |
| 1433                         | 輸出         | TCP                | SQL API 管理相依性                               | VIRTUAL_NETWORK / 網際網路        |
| 9350 9351、 9352、 9353、 9354 | 輸出         | TCP                | 服務匯流排 API 管理相依性                       | VIRTUAL_NETWORK / 網際網路        |
| 5671                         | 輸出         | AMQP               | API 管理中樞原則的事件記錄檔的相依性            | VIRTUAL_NETWORK / 網際網路        |
| 6381、 6382 6383             | 連入/連出 | UDP                | 在 Redis 快取的 API 管理相依性                       | VIRTUAL_NETWORK / VIRTUAL_NETWORK |
| 445                          | 輸出         | TCP                | 給 Azure 檔案共用上的 API 管理相依性            | VIRTUAL_NETWORK / 網際網路        |

## <a name="custom-dns"></a>自訂 DNS 伺服器設定

API 管理取決於 Azure 服務的數字。 API 管理服務執行個體中使用自訂的 DNS 伺服器位置 VNET 裝載時, 需要能夠解決 hostname 這些 Azure 服務。 請在 [自訂 DNS 設定，依照[本](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)指南。  

## <a name="related-content"></a>相關內容


* [建立與使用 Azure 傳統入口網站的網站-VPN 連線的虛擬網路][]
* [如何使用追蹤 API 檢查通話 Azure API 管理][]

[api-management-setup-vpn-configure]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-configure.png
[api-management-setup-vpn-select]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-add-api.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[Azure 傳統入口網站]: https://manage.windowsazure.com/

[建立與使用 Azure 傳統入口網站的網站-VPN 連線的虛擬網路]: ../vpn-gateway/vpn-gateway-site-to-site-create.md
[如何使用追蹤 API 檢查通話 Azure API 管理]: api-management-howto-api-inspector.md
