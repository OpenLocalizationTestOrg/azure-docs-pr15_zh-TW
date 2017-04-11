<properties
    pageTitle="什麼是流量管理員 |Microsoft Azure"
    description="本文可協助您瞭解流量管理員是什麼，以及是否為您的應用程式的右流量路由選擇"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="overview-of-traffic-manager"></a>流量管理員的概觀

Microsoft Azure 流量管理員可讓您控制使用者流量不同的資料中心的服務端點的通訊群組。 服務端點流量管理員支援包含 Azure Vm，Web 應用程式]，然後雲端服務]。 您也可以使用流量管理員與外部、 非 Azure 結束點。

流量管理員會使用網域名稱系統 (DNS)，將[流量路由方法](traffic-manager-routing-methods.md)和健康狀況的結束點的最適合端點的用戶端要求。 流量管理員提供傳輸路由的方法，以符合不同的應用程式的需求、 端點狀況[監控](traffic-manager-monitoring.md)和自動容錯移轉的範圍。 流量管理員會失敗，包括整個 Azure 區域的失敗。

## <a name="traffic-manager-benefits"></a>流量管理員優點

流量管理員可以協助您︰

- **改善重要的應用程式的可用性**

    流量管理員提供您的應用程式的可用性監控您的結束點，並提供自動容錯移轉，當端點當機。

- **改善高效能應用程式回應能力**

    Azure 可讓您位於世界各地的資料中心的執行雲端服務或網站。 流量管理員會導向至端點的流量最低網路延遲，用戶端來改善應用程式回應能力。

- **執行服務不停機時間進行的維修作業**

    您可以執行您的應用程式，而不中斷預定進行的維護作業。 在進行進行的維修作業時，流量管理員會指導流量替代的結束點。

- **合併了內部部署和雲端應用程式**

    流量管理員支援外部，讓它搭配混合式部署的非 Azure 端點雲端和內部部署，包括 「 尖峰-至-雲端，」 「 移轉-至-雲端，」 和 「 容錯移轉至雲端 」 案例。

- **發佈流量大型的複雜的部署**

    使用[巢狀的流量管理員設定檔](traffic-manager-nested-profiles.md)，流量路由方法可以結合以建立複雜且有彈性的規則，以支援更大、 更複雜的部署的需求。

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="next-steps"></a>後續步驟

- 進一步瞭解[流量管理員的運作方式](traffic-manager-how-traffic-manager-works.md)。

- 瞭解如何開發高可用性使用[流量管理員端點監視](traffic-manager-monitoring.md)的應用程式。

- 深入瞭解支援由流量管理員中的[傳輸路由的方法](traffic-manager-routing-methods.md)。

- [建立流量管理員設定檔](traffic-manager-manage-profiles.md)。

