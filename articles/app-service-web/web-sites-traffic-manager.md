<properties
    pageTitle="控制 Azure web 應用程式流量與 Azure 流量管理員"
    description="本文會提供為 Azure 流量管理員的摘要資訊與相關 Azure web 應用程式。"
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    writer="cephalin"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/25/2016"
    ms.author="cephalin"/>

# <a name="controlling-azure-web-app-traffic-with-azure-traffic-manager"></a>控制 Azure web 應用程式流量與 Azure 流量管理員

> [AZURE.NOTE] 本文針對 Microsoft Azure 流量管理員的摘要資訊與相關 Azure 應用程式服務 Web 應用程式。 瀏覽在本文結尾的連結可以找到更多關於 Azure 流量管理員資訊本身。

## <a name="introduction"></a>簡介
您可以使用 Azure 流量管理員來控制要求網頁用戶端分佈 Azure 應用程式服務中的 web 應用程式的方式。 當 web 應用程式端點加入至 Azure 流量管理員的設定檔時，Azure 流量管理員會持續追蹤的 web 應用程式 （執行，停止或刪除） 的狀態，讓它可以決定哪一項那些端點應該接收流量。

## <a name="load-balancing-methods"></a>負載平衡的方法
Azure 流量管理員會使用三個不同的負載平衡方法。 這些說明下列清單與 Azure web 應用程式。

* **容錯移轉**︰ 如果您在不同區域中有 web 應用程式複製，您可以使用這個方法來設定一個 web 應用程式服務所有網頁用戶端流量，並設定另一個 web 應用程式服務的流量，以避免第一個 web 應用程式會變成無法使用不同區域。

* **循環**︰ 如果您在不同區域中有 web 應用程式複製，您可以使用這個方法來流量平均分散在不同區域內的 web 應用程式。

* **效能**︰ 效能方法將用戶端最短的來回時間為基礎的流量。 效能方法可以用於在同一個區域中，或在不同區域內的 web 應用程式。

##<a name="web-apps-and-traffic-manager-profiles"></a>Web 應用程式與流量管理員設定檔
若要設定控制項的 web 應用程式流量，您會建立設定檔中 Azure 流量管理員，使用其中一個三載入平衡之前所述的方法，然後再新增您要控制流量的設定檔的結束點 （在此例中，web 應用程式）。 定期，設定檔來傳達您的 web 應用程式狀態 （執行、 停止或刪除），以便 Azure 流量管理員可根據這一點來將流量導向。

時使用 Azure Azure 流量管理員，請謹記下列重點︰

* 針對 web 應用程式只的部署相同的區域內，Web 應用程式已提供錯誤移轉及花費 web 應用程式模式的循環功能。

* 在相同地區另一個 Azure 雲端服務搭配使用 Web 應用程式的部署，您可以結合這兩種類型的端點，讓混合式案例。

* 您只可以指定一個 web 應用程式端點，每個地區設定檔中。 選取一個區域端點的 web 應用程式，在該區域剩餘的 web 應用程式會變成無法使用的設定檔的選取範圍。

* Azure 流量管理員設定檔中所指定的 web 應用程式結束點就會出現在 web 應用程式的設定檔中的 [設定] 頁面上的 [**網域名稱**] 區段下，但不是會在哪個有。

* 在 web 應用程式新增至的設定檔之後，web 應用程式的入口網站頁面的儀表板上的**網站 URL**會顯示 web 應用程式的自訂網域的 URL，如果您已經設定一個。 否則，它會顯示流量管理員設定檔 URL (例如， `contoso.trafficmgr.com`)。 兩個直接網域名稱的 web 應用程式和流量管理員的 URL 會顯示在 web 應用程式的設定] 頁面的**網域名稱**] 區段下。

* 您的自訂網域名稱運作正常，但除了將其新增至您的 web 應用程式，您也必須設定 DNS 地圖上，以指向流量管理員 URL。 如需如何設定 Azure web 應用程式的自訂網域的資訊，請參閱[設定 Azure 網站的自訂網域名稱](web-sites-custom-domain-name.md)。

* 您只可以新增至 Azure 流量管理員的設定檔的標準模式中的 web 應用程式。

## <a name="next-steps"></a>後續步驟

如需概念性和技術概觀的 Azure 流量管理員，請參閱[流量管理員的概觀](../traffic-manager/traffic-manager-overview.md)。

如需有關如何使用 Web 應用程式的流量管理員的詳細資訊，請參閱部落格文章[使用 Azure 流量管理員與 Azure 網站](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx)和[Azure 流量管理員現在可以整合與 Azure 網站](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/)。
