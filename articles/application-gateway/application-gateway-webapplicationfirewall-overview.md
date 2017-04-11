<properties
   pageTitle="應用程式的閘道器的 Web 應用程式防火牆 (WAF) 簡介 |Microsoft Azure"
   description="此頁面將概要的 Web 應用程式防火牆 (WAF) 應用程式的閘道器 "
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

# <a name="application-gateway-web-application-firewall-preview"></a>應用程式閘道器 Web 應用程式防火牆 （預覽版本）

Web 應用程式防火牆 (WAF) 是 azure 應用程式的閘道器之提供保護運用標準的應用程式傳送控制項 (ADC) 函數的應用程式閘道器的 web 應用程式的功能。 Web 應用程式防火牆的運作方式保護其針對大部分的 OWASP 前 10 個常見網頁的弱點。 Web 應用程式會逐漸常見的已知的弱點惡意攻擊的目標。 這些利用之間的常見 SQL 注入，跨網站指令碼攻擊等等。 防止應用程式碼中的這類攻擊可能相當困難，可能會要求嚴格維護]，[修補和監視的應用程式拓撲多重層級。 集中式的 web 應用程式防火牆來防止 web 攻擊使安全性管理更容易，並提供更佳的保證威脅侵入的應用程式。 更快速的安全性威脅也可以回應 WAF 解決方案以修補已知的弱點與保護每個個別的 web 應用程式的中央位置。 現有的應用程式閘道器可以輕鬆地轉換為 web 應用程式防火牆應用程式閘道。

![imageURLroute](./media/application-gateway-webapplicationfirewall-overview/WAF1.png)

應用程式的閘道器的應用程式傳送控制站的運作方式，並提供 SSL 終止、 cookie 為基礎的工作階段相關性循環載入分配，內容路由]，來管理多個網站及安全性的增強功能的功能。 所提供的應用程式的閘道器的安全性增強功能包括 SSL 原則管理，端對端 SSL 支援。 我們會介紹 WAF （web 應用程式防火牆） 直接整合提供 ADC 加強我們的服務應用程式的安全性功能。 這會提供輕鬆設定可用於管理及保護常見的 web 漏洞 web 應用程式的中央位置。

在應用程式閘道設定 WAF 給您提供下列好處︰

- 保護您的 web 應用程式網頁弱點和攻擊，不需要修改後端的程式碼。
- 使用應用程式閘道後方同時保護多個 web 應用程式。 應用程式閘道支援主機可能所有受 web 攻擊的單一閘道前 20 個網站。
- 監控您的 web 應用程式，使用應用程式閘道器 WAF 記錄產生的即時報表攻擊。
- 某些法規遵循控制項要求所有網際網路具結束點到受保護的 WAF 解決方案。 藉由使用應用程式的閘道器與 WAF 啟用，您可以符合這些規範要求。

## <a name="overview"></a>概觀

應用程式閘道器 WAF 所提供的新 SKU (WAF SKU) 中，也可以自行與 ModSecurity OWASP 核心規則集提供的 OWASP 前 10 個常見網頁的弱點大部分的比較基準防護。

- SQL 注入保護
- 跨網站指令碼保護
- 一般 Web 攻擊保護] 命令插入、 HTTP 要求 smuggling，HTTP 回應分割，等遠端檔案包含攻擊
- 保護 HTTP 通訊協定衝突
- 保護 HTTP 通訊協定異常，例如遺失主控使用者代理程式，並接受標題
- 包括 HTTP 癱瘓和變得很慢 HTTP DoS 防止 HTTP DoS 保護
- 防止依據 bot、 尋及掃描器
- 偵測的常見的應用程式錯誤設定 （亦即 Apache、 IIS 等）

## <a name="waf-modes"></a>WAF 模式

若要在下列兩種模式中執行，您可以設定應用程式閘道器 WAF:

- **偵測模式**– 設定為在應用程式閘道器 WAF 偵測模式中執行時監視與記錄檔將記錄所有威脅通知。 您需要確認該應用程式的閘道器的診斷記錄開啟使用 [診斷] 區段。 您也必須確定已選取且已開啟 WAF 記錄。
- **防止模式**– 當正在執行防止模式，應用程式的閘道器設定會封鎖入侵和攻擊偵測到它的規則。 攻擊者會收到 403 未經授權的存取例外狀況的連線已中斷。 防止模式繼續登這類攻擊 WAF 記錄中。

## <a name="application-gateway-waf-reports"></a>應用程式閘道器 WAF 報表

應用程式閘道器 WAF 提供詳細報告偵測到每個威脅。 記錄整合 Azure 診斷記錄及通知記錄 json 格式。

![imageURLroute](./media/application-gateway-webapplicationfirewall-overview/waf2.png)

    {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<applicationGatewayName>",
        "operationName": "ApplicationGatewayFirewall",
        "time": "2016-09-20T00:40:04.9138513Z",
        "category": "ApplicationGatewayFirewallLog",
        "properties":     {
            "instanceId":"ApplicationGatewayRole_IN_0",
            "clientIp":"108.41.16.164",
            "clientPort":1815,
            "requestUri":"/wavsep/active/RXSS-Detection-Evaluation-POST/",
            "ruleId":"OWASP_973336",
            "message":"XSS Filter - Category 1: Script Tag Vector",
            "action":"Logged",
            "site":"Global",
            "message":"XSS Filter - Category 1: Script Tag Vector",
            "details":{"message":" Warning. Pattern match "(?i)(<script","file":"/owasp_crs/base_rules/modsecurity_crs_41_xss_attacks.conf","line":"14"}}
    }

## <a name="application-gateway-waf-sku-pricing"></a>應用程式閘道器 WAF SKU 價格

在預覽中，有應用程式閘道器 WAF 使用量沒有額外的費用。 您繼續現有的基本 SKU 費用會薪資。 我們將 GA 次通訊 WAF SKU 費用。 選擇要部署 WAF SKU 應用程式閘道器的客戶累算 WAF SKU 價格 GA 公告後才開始。

## <a name="next-steps"></a>後續步驟

進一步了解 WAF 的功能之後，請造訪[如何設定 Web 應用程式防火牆上應用程式的閘道器](application-gateway-web-application-firewall-portal.md)。
