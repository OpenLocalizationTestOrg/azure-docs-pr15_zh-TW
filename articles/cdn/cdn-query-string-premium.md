<properties
    pageTitle="控制 Verizon 快取的要求與查詢字串的行為從 Azure CDN 進階版 |Microsoft Azure"
    description="Azure CDN 查詢字串快取控制項的查詢字串包含快取檔案的方式。"
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>

#<a name="controlling-caching-behavior-of-cdn-requests-with-query-strings---premium"></a>使用查詢字串-進階版的 CDN 要求控制快取行為

> [AZURE.SELECTOR]
- [標準](cdn-query-string.md)
- [從 Verizon azure CDN 進階版](cdn-query-string-premium.md)

##<a name="overview"></a>概觀

查詢字串快取的控制項的查詢字串包含快取檔案的方式。

> [AZURE.IMPORTANT] 標準和進階版 CDN 產品提供相同的查詢字串快取功能，但不同的使用者介面。  這份文件**從 Verizon Azure CDN 進階**版描述的介面。  查詢字串快取**Azure CDN 標準 Verizon 從** **Azure CDN 標準從 Akamai**與，請參閱[使用查詢字串的 CDN 要求控制快取行為](cdn-query-string.md)。

有三種模式︰

- **標準快取**︰ 這是預設的模式。  Cdn 到底邊緣節點將查詢字串從傳遞要求者到原點第一次要求和快取資產。  所有的後續要求的資產所服務的邊緣節點將會忽略查詢字串，直到快取的資產到期。
- **無快取**︰ 在此模式中，使用查詢字串的要求不會快取的 CDN 邊緣節點。  邊緣節點直接從來源中擷取資產，並將其傳遞給每個要求要求者。
- **唯一快取**︰ 此模式下使用查詢字串的每一條視為使用自己的快取的唯一資產。  例如 [想在邊緣節點快取和後續的快取以該相同的查詢字串傳回*foo.ashx?q=bar*要求的原點的回應。  想要快取*foo.ashx?q=somethingelse*的要求，為自己時間至即時個別的資產。

##<a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>變更查詢字串快取設定進階版 CDN 設定檔。

1. 從 CDN 的設定檔刀中，按一下 [**管理**] 按鈕。

    ![Cdn 到底設定檔刀管理] 按鈕](./media/cdn-query-string-premium/cdn-manage-btn.png)

    隨即會開啟 [CDN 管理入口網站。

2. 將游標移**HTTP 大型**] 索引標籤，然後將游標移**快取設定**彈出式。  按一下 [**查詢字串**快取。

    查詢字串快取選項會顯示。

    ![Cdn 到底查詢字串快取選項](./media/cdn-query-string-premium/cdn-query-string.png)

3. 在之後進行您的選取範圍，請按一下 [**更新**] 按鈕。


> [AZURE.IMPORTANT] 設定變更可能無法立即顯示，，如下所花的時間，才會傳播到 CDN 註冊。  <b>Azure CDN Verizon 從</b>設定檔，傳播通常會 90 分鐘內完成，但在某些情況下可能需要花久。
