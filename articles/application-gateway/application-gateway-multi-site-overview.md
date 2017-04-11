<properties
   pageTitle="管理多個網站上的應用程式的閘道器 |Microsoft Azure"
   description="本頁面提供應用程式閘道多網站支援的概觀。"
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="amsriva"/>

# <a name="application-gateway-multiple-site-hosting"></a>應用程式閘道主機服務的多個網站

多個網站主機服務可讓您在相同的應用程式閘道執行個體上設定一個以上的 web 應用程式。 此功能可讓您以 20 個網站加上一個應用程式的閘道器設定您的部署更有效率拓撲。 每個網站可以會導向至自己的後端資料庫。 在下列範例中，應用程式的閘道器服務 contoso.com 和 fabrikam.com 從兩個 ContosoServerPool] 和 [FabrikamServerPool 的後端伺服器集區的流量。

![imageURLroute](./media/application-gateway-multi-site-overview/multisite.png)

要求 http://contoso.com 路由至 ContosoServerPool，並 http://fabrikam.com 路由至 FabrikamServerPool。

同樣的兩個相同的上層網域的子網域，可以會裝載於相同的應用程式閘道部署。 使用 [子網域的範例可能包含 http://blog.contoso.com 和 http://app.contoso.com 裝載於單一應用程式閘道部署。

## <a name="host-headers-and-server-name-indication-sni"></a>主機標題和伺服器名稱的指示 (SNI)

有啟用多個網站主機服務上相同的基礎結構的三個常見機制。

1. 主控多個網頁上的應用程式每個唯一的 IP 位址。
2. 使用 [主機名稱裝載相同的 IP 位址的多個 web 應用程式。
3. 使用不同的連接埠裝載相同的 IP 位址的多個 web 應用程式。

目前的應用程式閘道取得其接聽流量的單一公用 IP 位址。 多個應用程式，因此支援，各有其專屬 IP 位址，目前不支援。 應用程式閘道支援裝載多個應用程式在不同的連接埠上的每個接聽，但這種情況需要接受非標準的連接埠上流量的應用程式和通常不想要的設定。 應用程式閘道依賴 HTTP 1.1 主機標頭裝載相同的公用 IP 位址和連接埠上的多個網站。 裝載於應用程式的閘道器的網站也可支援 SSL 卸載伺服器名稱的指示 (SNI) TLS 副檔名。 這種情況表示的用戶端瀏覽器和後端網頁伺服器陣列必須支援 HTTP/1.1 與 TLS 分機號碼 RFC 6066 中所定義。

## <a name="listener-configuration-element"></a>接聽設定項目

現有 HTTPListener 設定項目增強支援主機名稱和伺服器名稱指示項目，用來路由流量至適當的後端資料庫應用程式閘道器。 下列範例會從範本檔案 HttpListeners 元素的程式碼片段。

    "httpListeners": [
                {
                    "name": "appGatewayHttpsListener1",
                    "properties": {
                        "FrontendIPConfiguration": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
                        },
                        "FrontendPort": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
                        },
                        "Protocol": "Https",
                        "SslCertificate": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
                        },
                        "HostName": "contoso.com",
                        "RequireServerNameIndication": "true"
                    }
                },
                {
                    "name": "appGatewayHttpListener2",
                    "properties": {
                        "FrontendIPConfiguration": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
                        },
                        "FrontendPort": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
                        },
                        "Protocol": "Http",
                        "HostName": "fabrikam.com",
                        "RequireServerNameIndication": "false"
                    }
                }
            ],




您可以瀏覽的端對端以範本為基礎的部署[使用多個網站裝載的資源管理員範本](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting)。

## <a name="routing-rule"></a>路由規則

沒有任何路由規則中所需的變更。 路由規則 'Basic' 應繼續選擇繫結至對應的後端地址資料庫的適當網站接聽程式。

    "requestRoutingRules": [
    {
        "name": "<ruleName1>",
        "properties": {
            "RuleType": "Basic",
            "httpListener": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener1')]"
            },
            "backendAddressPool": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
            },
            "backendHttpSettings": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
            }
        }

    },
    {
        "name": "<ruleName2>",
        "properties": {
            "RuleType": "Basic",
            "httpListener": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener2')]"
            },
            "backendAddressPool": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/FabrikamServerPool')]"
            },
            "backendHttpSettings": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
            }
        }

    }
    ]

## <a name="next-steps"></a>後續步驟

之後學習多個網站主機服務，請移至[建立使用多個網站主機服務應用程式閘道器](application-gateway-create-multisite-azureresourcemanager-powershell.md)建立應用程式閘道支援一個以上的 web 應用程式的能力。
