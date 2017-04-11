<properties
    pageTitle="設定 web 應用程式的自訂網域名稱用於流量管理員負載平衡 Azure 應用程式服務。"
    description="使用自訂網域名稱中包含流量管理員負載平衡 Azure 應用程式服務的 web 應用程式。"
    services="app-service\web"
    documentationCenter=""
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robmcm"/>

# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>設定 web 應用程式的自訂網域名稱中使用流量管理員 Azure 應用程式服務

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[AZURE.INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

本文提供用於流量管理員負載平衡與 Azure 應用程式服務中使用自訂網域名稱的一般指示進行。

[AZURE.INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>
## <a name="understanding-dns-records"></a>瞭解 DNS 記錄

[AZURE.INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>
## <a name="configure-your-web-apps-for-standard-mode"></a>設定 web 應用程式的 [標準模式

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>
## <a name="add-a-dns-record-for-your-custom-domain"></a>新增您的自訂網域的 DNS 記錄

> [AZURE.NOTE] 如果您已購買網域透過 Azure 應用程式服務 Web 應用程式然後略過步驟，並參考文章[的 Web 應用程式的購買網域](custom-dns-web-site-buydomains-web-app.md)的最後一個步驟。

Azure 應用程式服務中的 web 應用程式中與您的自訂網域，您必須新增新的項目 DNS 資料表中的自訂網域使用提供的網域註冊機構購買的網域名稱的工具。 使用下列步驟，找出並使用 [DNS 工具。

1. 登入您的網域註冊機構，您的帳戶，並尋找頁面以管理 DNS 記錄。 尋找連結或標示為 「**網域名稱**、 **DNS**或**名稱伺服器管理**網站的區域。 通常可以找到此頁面的連結可檢視您的帳戶資訊，然後尋找的連結，例如 [**我的網域**。

1. 當您找到您的網域名稱的 [管理] 頁面時，尋找連結，可讓您以編輯 DNS 記錄。 這可能會列出**區域檔案****的 DNS 記錄**，或為**進階**設定連結。

    * 頁面很可能會有幾個記錄已經建立項目建立關聯，例如 「**@**'\*」 的 「 網域停車 」 頁面。 也可能包含常見的子網域，例如**www**記錄。
    * 頁面會提及**CNAME 記錄**，或提供下拉式選取的記錄類型。 它也可能會提及**記錄**等**MX 記錄**其他記錄。 在某些情況下，會由其他名稱，例如**別名記錄**呼叫 CNAME 記錄。
    * 頁面也會有欄位可讓您將**地圖****主機名稱**或**網域名稱**至另一個網域名稱。

1. 每個註冊機構的細節會有所不同，一般您對應*從*您的自訂網域名稱 （例如**contoso.com**，)*若要*用於您的 web 應用程式的流量管理員網域名稱 (**contoso.trafficmanager.net**)。

    > [AZURE.NOTE] 或者，如果記錄已在使用，您需要將您的應用程式主動繫結到它，您可以建立其他的 CNAME 記錄。 例如，至主動將**www.contoso.com**繫結到您的 web 應用程式，建立一筆 CNAME 記錄從**awverify.www** **contoso.trafficmanager.net**。 然後，您可以新增至您的 Web 應用程式 」 www.contoso.com 」 而不變更 「 www 」 CNAME 記錄。 如需詳細資訊，請參閱[建立 web 應用程式的自訂網域的 DNS 記錄][CREATEDNS]。

1. 一旦您完成新增或修改您的註冊機構的 DNS 記錄，以儲存變更。

<a name="enabledomain"></a>
## <a name="enable-traffic-manager"></a>啟用流量管理員

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱[Node.js 開發人員中心](/develop/nodejs/)。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
