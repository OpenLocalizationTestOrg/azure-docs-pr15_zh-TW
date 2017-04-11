<properties
    pageTitle="使用狀況相關的常見問題集 |Microsoft Azure"
    description="Azure 堆疊公尺、 Azure 的使用方式的 API 使用狀況時間，報告的時間，出現錯誤碼比較清單。"
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

# <a name="azure-stack-usage-api-faqs"></a>Azure 堆疊的使用狀況 API 常見問題集
本文將回答有關 Azure 堆疊使用 API 一些常見問題集的問題。

## <a name="what-meter-ids-can-i-see"></a>我可以看到哪些量表識別碼？

目前，使用情況報告網路、 儲存及計算資源的提供者。

| **資源提供者** | **量表識別碼** |**量表名稱** | **單位** | **其他資訊** |
| --------------------------- | --------------------------------------- | -------------------------- | ---------------------------- | ----------------------------------------- |
| **網路** | f114cb19-ea64-40b5-bcd7-aee474b62853 | 公用 IP 位址的使用狀況 | IP 位址 |                    
| **儲存空間**  | B4438D5D-453B-4EE1-B42A-DC72E377F1E4 | TableCapacity | GB\*小時 | 由 [資料表的容量總數 |
|              | B5C15376-6C94-4FDD-B655-1A69D138ACA3 | PageBlobCapacity | GB\*小時 | 由頁面 blob 的容量總數 |
|              | B03C6AE7-B080-4BFA-84A3-22C800F315C6 | QueueCapacity  | GB\*小時  | 佇列中所使用的容量總數 |
| | 09F8879E-87E9-4305-A572-4B7BE209F857 | BlockBlobCapacity | GB\*小時  | 由區塊 blob 的容量總數 |
| | B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90 | TableTransactions  | 邀請中 10,000s 的字數統計   | 資料表 （在 10,000s) 的服務要求 |
| | 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D | TableDataTransIn | 輸入資料以 GB | 在 [GB 表格服務資料輸入 |
| | 1B8C1DEC-EE42-414B-AA36-6229CF199370 | TableDataTransOut | Outgress gb | 表格服務資料出口 gb |
| | 43DAF82B-4618-444A-B994-40C23F7CD438 | BlobTransactions | 要求計算中 10,000s | Blob 服務要求 （在 10,000s) |
| | 9764F92C-E44A-498E-8DC1-AAD66587A810   | BlobDataTransIn    | 輸入資料以 GB          | 在 [GB blob 服務資料輸入 
| | 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8   | BlobDataTransOut   | Outgress gb              | Blob 服務資料出口 gb 
| | EB43DD12-1AA6-4C4B-872C-FAF15A6785EA   | QueueTransactions  | 要求計算中 10,000s   | 佇列中 （在 10,000s) 的服務要求 
| | E518E809-E369-4A45-9274-2017B29FFF25   | QueueDataTransIn          | 輸入資料以 GB         | 在 [GB 佇列中服務資料輸入 
| | DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2   | QueueDataTransOut         | Outgress gb  | 佇列中服務資料出口 gb 
| **計算** | 6DAB500F-A4FD-49C4-956D-229BB9C8C793 | VM 大小的時數 | 虛擬機器大小 |



## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>Azure 堆疊使用 Api 比較對[Azure 使用 API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) （目前的公用預覽版本） 的方式？

-   租用戶使用 API 是一致和 Azure API，有一個例外狀況︰ *showDetails*旗標目前不支援 Azure 堆疊。

-   提供者使用 API 僅適用於 Azure 堆疊。

-   目前，用於 Azure [RateCard API](https://msdn.microsoft.com/en-us/library/azure/mt219004.aspx)不適用於 Azure 堆疊。

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>使用階段和流程回報時間之間的差異為何？

使用情況資料報告中有兩個主要的時間值︰

-   **報告的時間**。 使用事件時輸入使用系統的時間

-   **使用時間**。 Azure 堆疊資源的使用狀況時間

您可能會看到使用階段和流程回報時間的值不一致的特定使用事件。 延遲可在任何環境中的多個小時。

目前，您可以查詢*只報告的時間*。

## <a name="what-do-these-usage-api-error-codes-mean"></a>使用 API 錯誤代碼有何意義？

| **HTTP 狀態碼** | **錯誤碼** | **描述** |
| ---------------------- | ------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| 400/錯誤的要求        | *NoApiVersion*     | 遺漏*api 版本*查詢參數。
| 400/錯誤的要求        | *InvalidProperty*  | 屬性遺失或不正確的值。 回應本文中的錯誤碼中識別遺失的屬性。
| 400/錯誤的要求        | *RequestEndTimeIsInFuture*  | *ReportedEndTime*的值就是在未來。 在未來不允許值的引數。
| 400/錯誤的要求        | *SubscriberIdIsNotDirectTenant*    | 提供者 API 打電話用的不是有效的租用戶的來電者之間的訂閱識別碼。
| 400/錯誤的要求        | *SubscriptionIdMissingInRequest*   | 遺漏的來電者之間的訂閱識別碼。
| 400/錯誤的要求        | *InvalidAggregationGranularity*   | 要求無效的彙總資料粒度。 有效的值為每日和下限。
| 503                    | *ServiceUnavailable*   | 重試錯誤而發生，因為服務正在忙碌，或通話的經流速控制。 |

## <a name="next-steps"></a>後續步驟
[帳單寄送的客戶及 chargeback Azure 堆疊中](azure-stack-billing-and-chargeback.md)

[提供者資源使用狀況 API](azure-stack-provider-resource-api.md)

[租用戶資源使用狀況 API](azure-stack-tenant-resource-usage-api.md)
