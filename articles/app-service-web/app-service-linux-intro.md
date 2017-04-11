<properties 
    pageTitle="簡介 Linux 上的應用程式服務 |Microsoft Azure" 
    description="深入了解 Linux 上的應用程式服務。" 
    keywords="azure 應用程式服務、 linux os"
    services="app-service" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="naziml"/>

# <a name="introduction-to-app-service-on-linux"></a>Linux 上的應用程式服務簡介
Linux 上的應用程式服務目前位於公用預覽和 linux 原本即支援執行 web 應用程式。 

## <a name="overview"></a>概觀 ##
客戶可以使用應用程式服務上 Linux 原本在 Linux 主機 web 應用程式支援的應用程式堆疊。 下列功能] 區段會列出目前支援的應用程式堆疊。

## <a name="features"></a>功能 ##
Linux 服務應用程式目前支援下列應用程式堆疊

- Node.js
- PHP

客戶可以部署使用其應用程式

- FTP。
- 本機給。
- GitHub 或 BitBucket。

調整應用程式


- 客戶可以向上和向下調整其 web 應用程式，藉由變更其應用程式服務計劃中的層。 
- 客戶可以擴充查看其應用程式，並執行多個執行個體範圍內的其 SKU 的其應用程式。

Kudu 的一些基本功能都將能夠運作

- 環境。
- 部署。
- 基本主控台。

## <a name="limitations"></a>限制 ##

Azure 管理入口只會顯示目前支援的功能 Linux 上的應用程式服務，並隱藏其餘。 為我們啟用其他功能的小組我們會保留反映這管理入口網站上。 某些功能等 VNET 整合和 AAD/第三方驗證或 Kudu 網站副檔名執行目前無法運作。 但我們會使用這些會更新我們的說明文件和部落格的變更。

此公用 preview 目前僅提供的在下列區域內

-   西美國。
-   西歐。
-   東南亞。

只有支援專用的應用程式服務方案 Linux 上的 web 應用程式，而且也沒有免費或共用層。 此外，一般和 Linux web 應用程式的應用程式服務方案互斥，因此您無法建立 Linux web app 非 Linux 應用程式服務計劃中。

資源群組不包含在同一個區域中的非 Linux web 應用程式中，必須建立 Linux 上的 web 應用程式。

因為缺少重疊回收 web 應用程式，客戶應該會在 web 應用程式時的小型停機時間有重新啟動。 

## <a name="next-steps"></a>後續步驟 ##

請遵循下列連結，即可開始使用 Linux 上的應用程式服務。 請在[我們論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)上張貼問題與疑慮。

* [建立 Web 應用程式中上 Linux 的應用程式服務](./app-service-linux-how-to-create-a-web-app.md)
* [Node.js linux 的 Web 應用程式中使用 PM2 設定](./app-service-linux-using-nodejs-pm2.md)

