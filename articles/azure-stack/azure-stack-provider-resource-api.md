<properties
    pageTitle="提供者資源使用狀況 API |Microsoft Azure"
    description="參照的資源使用狀況 API，這擷取 Azure 堆疊的使用狀況資訊。"
    services="azure-stack"
    documentationCenter=""
    authors="AlfredoPizzirani"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="alfredop"/>

# <a name="provider-resource-usage-api"></a>提供者資源使用狀況 API

服務管理員以及任何委派的提供者，適用於字詞提供者。 服務管理員和委派的提供者可以使用提供者使用 API 來檢視其直接租用戶的使用方式。 例如 [P0 打電話提供者 API P1 的和 P2 的直接流量的使用狀況資訊和 P1 打電話第 3 頁和第 4 頁上的使用情況資訊。

![提供者階層的概念模型](media/azure-stack-provider-resource-api/image1.png)


## <a name="api-call-reference"></a>API 通話參考

### <a name="request"></a>要求

要求取得消耗詳細資料的要求的訂閱和要求的時間範圍。 沒有邀請內文。

此 API 的使用方式是一個提供者的 API，來電者必須被指派提供者的訂閱中的擁有者 」、 「 參與者或 「 閱讀程式角色。

| **方法**  | **要求 URI** |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
|  取得        | https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&subscriberId={sub1.1}&api-version=2015-06-01-preview&continuationToken={token-value} |

### <a name="arguments"></a>引數

| **引數**              | **描述** |
| -------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| *armendpoint*             | Azure 資源管理員 Azure 堆疊環境的結束點。 Azure 堆疊慣例是 ARM 端點的名稱格式 https://api 中。{網域名稱}'。 例如，如果 azurestack.local 的網域名稱，然後 ARM 端點會 https://api.azurestack.local。 |
| *subId*                   | 訂閱 ID 進行通話的使用者。 |
| *reportedStartTime*       | 開始時間的查詢。 *DateTime*值應該是以 utc 表示以及小時，例如 13:00 的開頭。 每日的彙總，將此值設為 UTC 午夜。 格式是*逸出字元*ISO 8601，例如 2015年-06-16t18%3a53%3a11%2b00 %3a00z，以 %3a 逸出冒號和加號，讓它 URI 易記逸出至 %2b。 |
| *reportedEndTime*         | 查詢的結束時間。 條件約束套用至*reportedStartTime*也適用於此引數。 無法在未來*reportedEndTime* ] 的值。 |
| *aggregationGranularity*  | 有兩個不連續的可能值的選擇性參數︰ 每日和每小時。 為建議值，其中一個每日的資料點，在傳回的資料，另一個是每小時的解析度。 預設為 [每日] 選項。 |
| *subscriberId*            | 訂閱識別碼。 若要篩選的資料，直接租用戶的提供者的訂閱 ID 是必要。 如果沒有訂閱 ID 指定參數，電話會傳回使用情況資料的提供者的所有直接租用戶。 |
| *api 版本*             | 用來進行此要求的通訊協定版本。 您必須使用 2015年 06-01-預覽。 |
| *continuationToken*       | 權杖擷取的使用狀況 API 提供者，最後一個呼叫。 這被需要回應大於 1000 的線條。 這是書籤的進度。 如果不存在，一天開始擷取資料或小時，根據資料粒度傳入。 |



### <a name="response"></a>回應

取得 /subscriptions/sub1/providers/Microsoft.Commerce/subscriberUsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00 與 reportedEndTime = 2015年-06-01t00%3a00%3a00%2b00 %3a00 與 aggregationGranularity = 每天 & subscriberId = sub1.1 & api 版本 = 1.0

{

「 值 」:\[

{

「 識別碼 」: 「 /subscriptions/sub1.1/providers/Microsoft.Commerce/UsageAggregate/sub1.1-

meterID1 」，

「 名稱 」: 「 sub1.1-meterID1 」

「 type 」: 「 Microsoft.Commerce/UsageAggregate 」，

[內容]: {

「 subscriptionId 」: 「 sub1.1 」，

「 usageStartTime 」: 「 2015年-03-03T00:00:00 + 00:00 」，

「 usageEndTime 」: 「 2015年-03-04T00:00:00 + 00:00 」，

「 instanceData 」: 「 {\\」 Microsoft.Resources\\」: {\\」 resourceUri\\」:\\」 resourceUri1\\」，\\」 的位置\\

「:\\」 阿拉斯加\\」，\\」 標記\\」: null，\\」 additionalInfo\\」: null}} 」，

「 數量 」:2.4000000000

「 meterId 」: 「 meterID1 」

}

},

…

### <a name="response-details"></a>回應詳細資料


| **引數**       | **描述**
| ------------------ | ------------------------------------------------------------------------------------------------------------- |
| *識別碼*               | 使用彙總的唯一識別碼。
| *名稱*             | 使用彙總的名稱
| *類型*             | 資源定義
| *subscriptionId*   | Azure 堆疊使用者訂閱識別碼
| *usageStartTime*   | UTC 的開始時間此使用彙總所屬的使用狀況值區
| *usageEndTime*     | 此使用彙總所屬的使用狀況值區 UTC 結束時間
| *instanceData*     | 關鍵值組的執行個體的詳細資料 （在新的格式）︰<br> *resourceUri*︰ 完整的 [資源識別碼，包括資源群組以及執行個體名稱 <br> *位置*︰ 在其中執行這項服務的區域 <br> *標籤*︰ 資源標籤所指定的使用者 <br> *additionalInfo*︰ 更多詳細資料所使用的資源，例如作業系統版本或圖像類型 |
| *數量*         | 這個時段的資源耗用的數量 |
| *meterId*          | 唯一識別碼是資源耗用 （也稱為的*預設*） |

## <a name="next-steps"></a>後續步驟

[租用戶資源使用狀況 API 參考](azure-stack-tenant-resource-usage-api.md)

[使用狀況相關的常見問題集](azure-stack-usage-related-faq.md)
