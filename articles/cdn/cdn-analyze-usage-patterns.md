<properties
    pageTitle="分析 Azure CDN 使用模式 |Microsoft Azure"
    description="您可以使用下列報告您 CDN 檢視使用模式︰ 頻寬資料傳輸、 點擊、 快取的狀態、 快取點擊比率、 IPV4/IPV6 傳送的資料。"
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

# <a name="analyze-azure-cdn-usage-patterns"></a>分析 Azure CDN 使用模式

[AZURE.INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

您可以使用下列報告您 CDN 檢視使用模式︰

- 頻寬
- 傳送的資料
- 瀏覽人數
- 快取的狀態
- 快取點擊比率
- 傳輸 IPV4/IPV6 資料

## <a name="accessing-advanced-http-reports"></a>存取進階的 HTTP 報表

1. 從 CDN 的設定檔刀中，按一下 [**管理**] 按鈕。

    ![Cdn 到底設定檔刀管理] 按鈕](./media/cdn-reports/cdn-manage-btn.png)

    隨即會開啟 [CDN 管理入口網站。

2. 暫留在 [**分析**] 索引標籤，然後將游標移**核心報表**延伸。  按一下所要的報表，在功能表中。

    ![Cdn 到底管理入口網站-核心報表] 功能表](./media/cdn-reports/cdn-core-reports.png)


## <a name="bandwidth"></a>頻寬

頻寬報表包含在特定時間週期表示頻寬使用量 HTTP 和 HTTPS 的圖形和資料表格。 您可以檢視頻寬使用量過所有 CDN 取出或特定的快顯。 這個選項可讓您檢視的流量特殊圖文集和發佈 CDN 會顯示在 Mbps。

- 選取所有的邊緣節點，請參閱從所有節點，或從下拉式清單中選擇特定地區/節點。
- 選取您要檢視此今天/週/本月份的資料，或輸入自訂的日期，然後按一下 [執行]，請確定您的選取範圍會更新的日期範圍。
- 您可以匯出，並按一下位於 [go] 旁的 excel 工作表圖示下載的資料。

報表每 5 分鐘更新。

![頻寬報表](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>傳送的資料

此報表包含在特定時間週期表示 HTTP 和 HTTPS 的流量使用圖形和資料表格。 您可以過所有 CDN 取出或特定的快顯檢視流量使用方式。 這個選項可讓您檢視的流量特殊圖文集和發佈 CDN 會顯示在 GB。

- 選取所有的邊緣節點，請參閱從所有的筆記，或從下拉式清單中選擇特定地區/節點。
- 選取您要檢視此今天/週/本月份的資料，或輸入自訂的日期，然後按一下 [執行]，請確定您的選取範圍會更新的日期範圍。
- 您可以匯出，並按一下位於 [go] 旁的 excel 工作表圖示下載的資料。

報表每 5 分鐘更新。

![傳送報表的資料](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>點擊 （狀態碼）

此報表說明您內容的邀請狀態碼分配。 每個要求的內容會產生 HTTP 狀態碼。 狀態碼說明邊緣的 Pop 要求的處理方式。 例如，2xx 狀態碼會表示的要求已成功提供給用戶端，而 4xx 狀態碼表示發生錯誤。 如需 HTTP 狀態碼的詳細資訊，請參閱[狀態碼](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)。

- 選取您要檢視此今天/週/本月份的資料，或輸入自訂的日期，然後按一下 [執行]，請確定您的選取範圍會更新的日期範圍。
- 您可以匯出並下載時，按一下位於 [go] 旁的 excel 工作表資料。

![點擊報表](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>快取的狀態

此報表說明快取點擊和用戶端要求的快取遺漏的分配。 最快的效能來自快取點擊率，因為您可以最佳化至快取遺漏和過期的快取點擊資料傳遞速度。 設定您的來源伺服器，以避免指派 「 無快取 」 回應標頭、 避免嚴格有需要，除了快取查詢字串和避免非快取回應代碼時，會降低快取遺失。 可避免點擊進行資產的過期快取盡可能最小化的原始伺服器要求數的最大值年齡。

![快取的狀態報表](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>主要的快取的狀態包含︰

- TCP_HIT︰ 服務從邊緣。 物件快取中，有超過的最大時間。
- 從來源，提供 TCP_MISS:。 物件不是在 [快取中，已回到原始的回應。
- TCP_EXPIRED _MISS︰ 從來源服務後的原點重新確認。 物件快取中但已超出的最大時間。 重新驗證與原始導致快取物件取代原始從新的回應。
- TCP_EXPIRED _HIT︰ 從 Edge 服務後的原點重新確認。 物件快取中但已超出的最大時間。 重新驗證原始伺服器導致快取物件正在修改。

- 選取您要檢視此今天/週/本月份的資料，或輸入自訂的日期，然後按一下 [執行]，請確定您的選取範圍會更新的日期範圍。
- 您可以匯出，並按一下位於 [go] 旁的 excel 工作表圖示下載的資料。

### <a name="full-list-of-cache-statuses"></a>完整的快取的狀態清單

- TCP_HIT-時要求提供直接 POP 用戶端，報告此狀態。 資產立即來自 POP 當其快接近用戶端的快取，並且有有效的時間存留或 [TTL]。 [TTL] 是由下列回應標頭標準判定的︰

    - 快取控制項︰ s maxage
    - 快取控制項︰ max 年齡
    - 到期

- TCP_MISS-的要求資產的快取的版本上找不到用戶端最接近的快指出此狀態。 將要求從原始伺服器或原始盾伺服器的資產。 如果來源伺服器或原始盾伺服器傳回資產，它會提供用戶端並快取的用戶端和 edge server 上。 否則，非 200 狀態碼 (例如 403 禁止，404 找不到，等) 會傳回。

- TCP_EXPIRED _HIT-時要求的目標資產過期的 ttl，例如當資產的最大值年齡已過期、 已直接從快顯 served 用戶端，報告此狀態。

    過期的邀請通常會重新驗證要求原始伺服器。 為了讓發生 TCP_EXPIRED _HIT，原始伺服器必須指出較新版的資產不存在。 該資產的快取控制項和到期日標頭，通常會更新這種情況。

- TCP_EXPIRED _MISS-時較新版的過期的快取資產提供 POP 用戶端，報告此狀態。 這會發生的快取的資產 TTL 已到期時 （例如過期 max 年齡） 原始伺服器傳回該資產的較新版本。 將這個新版本的資產提供用戶端，而非快取的版本。 此外，它將會快取 edge server 與用戶端上。

- CONFIG_NOCACHE-的客戶特定上的設定 POP 我們邊緣禁止資產快取指出此狀態。

- 無-此狀態會指出快取內容的有效期限核取無法執行。

- TCP_ CLIENT_REFRESH _MISS-HTTP 用戶端 （例如，在瀏覽器） 強制邊緣 POP 從原始伺服器擷取過期的資產的新版本時，報告此狀態。

    根據預設，我們的伺服器，防止 HTTP 用戶端強制邊緣伺服器從原始伺服器擷取資產的新版本。

- TCP_ PARTIAL_HIT-位元組範圍要求所產生的部分的快取的資產點擊報告此狀態。 要求的位元組範圍立即來自 POP 用戶端。

- 快取-當資產的快取控制] 和 [到期日標題指示，它應該不快取上以 POP 或 HTTP 用戶端報告此狀態。 從原始伺服器提供這些類型的要求

## <a name="cache-hit-ratio"></a>快取點擊比率

此報表會指出快取直接從快取服務的要求的百分比。

報表提供的下列詳細資料︰

- 要求的內容是在要求者最接近的快快取。
- 要求已提供服務，直接從我們的網路的邊緣。
- 要求不需要重新驗證原始伺服器。

不包含報表︰

- 因為篩選選項的國家/地區拒絕要求。
- 其標題指示他們應該不能快取的資產的要求。 快取，例如控制︰ 私人的快取控制項︰ 沒有快取或 Pragma︰ 無快取標題會防止資產快取。
- 位元組範圍要求部分的快取的內容。

公式是: (TCP_ 點擊 / (TCP_ 點擊 + TCP_MISS)) * 100

- 選取您要檢視此今天/週/本月份的資料，或輸入自訂的日期，然後按一下 [執行]，請確定您的選取範圍會更新的日期範圍。
- 您可以匯出，並按一下位於 [go] 旁的 excel 工作表圖示下載的資料。


![快取點擊比報表](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>傳送 IPV4/IPV6 資料

此報表示範 IPV4 與 IPV6 流量使用通訊群組。

![傳送 IPV4/IPV6 資料](./media/cdn-reports/cdn-ipv4-ipv6.png)

- 選取您要檢視此今天/週/本月份的資料，或輸入自訂日期的日期範圍。
- 接著，按一下 [執行] 來確認會更新您的選取範圍。


## <a name="considerations"></a>考量

只會在最後一個 18 個月內產生報告。
