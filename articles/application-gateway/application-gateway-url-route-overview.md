<properties
   pageTitle="URL 型內容路由概觀 |Microsoft Azure"
   description="本頁面提供的應用程式閘道器 URL 型傳閱內容以、 UrlPathMap 設定與 PathBasedRouting 規則概觀。"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace"/>

# <a name="url-path-based-routing-overview"></a>URL 路徑以路由概觀

URL 路徑基礎路由可讓您根據要求的 URL 路徑的後端伺服器集區路由流量。 案例的內容之一是不同的內容類型，以不同的後端伺服器集區傳送要求。
在下列範例中，應用程式的閘道器服務的三個後端伺服器集區 contoso.com 流量，例如︰ VideoServerPool ImageServerPool，與 DefaultServerPool。

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

Http://contoso.com/video*傳閱 VideoServerPool，並 http://contoso.com/images*邀請會傳送到 ImageServerPool。 如果路徑圖樣都不相符，已選取 DefaultServerPool。

## <a name="urlpathmap-configuration-element"></a>UrlPathMap 設定項目

UrlPathMap 項目用來指定後端伺服器資料庫對應的路徑模式。 下列範例會從範本檔案 urlPathMap 元素的程式碼片段。

    "urlPathMaps": [
    {
    "name": "<urlPathMapName>",
    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/ urlPathMaps/<urlPathMapName>",
    "properties": {
        "defaultBackendAddressPool": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendAddressPools/<poolName>"
        },
        "defaultBackendHttpSettings": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendHttpSettingsList/<settingsName>"
        },
        "pathRules": [
            {
                "paths": [
                    <pathPattern>
                ],
                "backendAddressPool": {
                    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendAddressPools/<poolName2>"
                },
                "backendHttpsettings": {
                    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendHttpsettingsList/<settingsName2>"
                },

            },

        ],

    }
    }
    

>[AZURE.NOTE] PathPattern︰ 此設定為以符合路徑圖樣的清單。 每個必須開始 / 和唯一 」 * 「 允許位於結束下列 「 / 」。 送至路徑 matcher 字串後第一個不包含任何文字嗎？或 #，這些字元不允許和以下。 

您可以查看[資源管理員範本使用 URL 為基礎的路由](https://azure.microsoft.com/documentation/templates/201-application-gateway-url-path-based-routing)的詳細資訊。

## <a name="pathbasedrouting-rule"></a>PathBasedRouting 規則

型別 PathBasedRouting RequestRoutingRule 用來將接聽繫結至 urlPathMap。 這個接聽程式都會收到的所有要求都傳閱根據 urlPathMap 中指定的原則。
PathBasedRouting 規則的程式碼片段︰

    "requestRoutingRules": [
    {

    "name": "<ruleName>",
    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/requestRoutingRules/<ruleName>",
    "properties": {
        "ruleType": "PathBasedRouting",
        "httpListener": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/httpListeners/<listenerName>"
        },
        "urlPathMap": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/ urlPathMaps/<urlPathMapName>"
        },

    }
    
## <a name="next-steps"></a>後續步驟

學習如何 URL 型傳閱內容以後, 移至使用 URL 路由規則建立應用程式閘道器的 [[建立使用 URL 為基礎的路由應用程式閘道器](application-gateway-create-url-route-portal.md)。
