<properties
   pageTitle="閘道器 WebSocket 支援應用程式 |Microsoft Azure"
   description="本頁面提供應用程式閘道器 WebSocket 支援的概觀。"
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/16/2016"
   ms.author="amsriva"/>

# <a name="application-gateway-websocket-support"></a>閘道器 WebSocket 支援應用程式

應用程式的閘道器提供原生支援 WebSocket 過所有閘道器的大小。 沒有選擇性地啟用或停用 WebSocket 支援使用者設定設定。 您可以繼續使用標準的 HTTPListener 的連接埠 80/443 接收 WebSocket 流量。 然後 WebSocket 流量會導向 WebSocket 啟用後端伺服器所指定的應用程式閘道器規則中使用適當的後端資料庫。 WebSocket 通訊協定中[RFC6455](https://tools.ietf.org/html/rfc6455)標準化讓伺服器與用戶端之間的雙向通訊透過長時間執行的 TCP 連線。 此功能可讓更多的互動之間的通訊網頁伺服器，以及用戶端，可以是不需要為必要的 HTTP 為基礎的實作投票的雙向。  WebSocket 有低負荷與 HTTP 不同，而且可以重複使用相同的 TCP 連接的多個要求/回應]，並產生更有效率的使用情況的資源。 WebSocket 通訊協定的設計工作傳統 HTTP 連接埠 80 和 443。

後端伺服器，必須回應應用程式閘道器探查[狀況探查概觀](application-gateway-probe-overview.md)一節所述。 應用程式閘道器狀況探查只在 HTTP/HTTPS，這表示必須回應 HTTP 探查，若要將 WebSocket 流量路由傳送至伺服器的應用程式閘道器的每個後端伺服器。

## <a name="listener-configuration-element"></a>接聽設定項目

現有 HTTPListener 可支援 WebSocket。 以下是從範例範本檔案 HttpListeners 元素的片段。 您必須同時 HTTP 和 HTTPS 接聽支援 WebSocket 及保護 WebSocket 流量。 同樣地使用[入口網站](application-gateway-create-gateway-portal.md)或[PowerShell](application-gateway-create-gateway-arm.md)以接聽程式建立應用程式閘道的連接埠 80/443 支援 WebSocket 流量。


    "httpListeners": [
                {
                    "name": "appGatewayHttpsListener",
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
                    }
                },
                {
                    "name": "appGatewayHttpListener",
                    "properties": {
                        "FrontendIPConfiguration": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
                        },
                        "FrontendPort": {
                            "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
                        },
                        "Protocol": "Http",
                    }
                }
            ],

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>BackendAddressPool、 BackendHttpSetting 和路徑規則設定

BackendAddressPool 應用於定義與 WebSocket 啟用伺服器後端資料庫。 應該使用後端定義 BackendHttpSetting 連接埠 80/443 只。 WebSocket 流量無關 cookie 為基礎的相關性和 requestTimeouts 的屬性。 沒有任何路由規則中所需的變更。 基本' 應該繼續要用來將適當的相對應的後端位址集區接聽項路由的規則。 

    "requestRoutingRules": [{
        "name": "<ruleName1>",
        "properties": {
            "RuleType": "Basic",
            "httpListener": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener')]"
            },
            "backendAddressPool": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
            },
            "backendHttpSettings": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
            }
        }

    }, {
        "name": "<ruleName2>",
        "properties": {
            "RuleType": "Basic",
            "httpListener": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener')]"
            },
            "backendAddressPool": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
            },
            "backendHttpSettings": {
                "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
            }

        }
    }]

## <a name="websocket-enabled-backend"></a>啟用 WebSocket 後端

您的後端必須執行上設定的 HTTP/HTTPS 網頁伺服器連接埠 (通常是 80/443)，用 WebSocket 搭配使用。 這是因為 WebSocket 通訊協定需要為 HTTP 進行升級為標題欄位的 WebSocket 通訊協定的初始信號。

    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: http://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13

另一個原因是該應用程式閘道器後端狀況探查支援只 HTTP/HTTPS 通訊協定。 如果後端伺服器沒有回應 HTTP/HTTPS 探查，它會被引導登出後端資料庫，包括 WebSocket 要求沒有要求，要達到此後端]。

## <a name="next-steps"></a>後續步驟

學習如何 WebSocket 支援後, 請移至[建立應用程式閘道](application-gateway-create-gateway.md)WebSocket 啟用 web 應用程式快速入門。
