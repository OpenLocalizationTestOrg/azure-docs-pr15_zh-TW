<properties
    pageTitle="Azure 應用程式服務︰ 縮放應用程式服務應用程式"
    description="了解性，調整應用程式服務中的應用程式。"
    keywords="應用程式 azure 服務應用程式服務，可調整應用程式服務方案、 小數位數，應用程式服務成本"
    services="app-service"
    documentationCenter=""
    authors="btardif"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/07/2016"
    ms.author="byvinyal"/>

# <a name="azure-app-service-scaling-app-service-applications"></a>Azure 應用程式服務︰ 縮放應用程式服務應用程式

裝載於 Azure 應用程式服務應用程式可以[獲得 kevin](https://azure.microsoft.com/blog/canadian-broadcasting-corporation-radio-canada-leverage-azure-for-smooth-election-coverage/)。
不過，調整應用程式是複雜的問題不具有 「 一個大小符合所有 」 的解決方案。 為正確的比例有應用程式是會影響您的應用程式成功的 3 個主要領域︰

1. 瞭解您的應用程式結構和缺點。
    * 是您的應用程式狀態？ 無狀態？
    * 有哪些應用程式的所有元件？
        * 在哪裡瓶頸在應用程式？
    * 當載入套用至您的應用程式時，什麼會中斷第一個項目？
2. 了解預期的載入和效能需求。
    * 應用程式需要服務一千使用者嗎？或一百萬？
    * 流量是從單一地理位置或全域？
    * 是否有季節性變化？尖峰流量，則嗎？
    * 如何快速回應應用程式？ 1 的第二個嗎？ 1 毫秒嗎？
3. 瞭解及正確運用裝載您的應用程式的平台。
    * 以達到我比例目標應該運用哪些功能？

本節將協助您瞭解所有的因素與您設計策略，會利用必要的應用程式服務功能，以達到延展性目標的說明。

[AZURE.INCLUDE [app-service-blueprint-scaling-app-service-applications](../../includes/app-service-blueprint-scaling-app-service-applications.md)]
