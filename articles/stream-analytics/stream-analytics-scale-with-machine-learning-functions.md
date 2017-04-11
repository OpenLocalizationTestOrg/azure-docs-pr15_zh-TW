<properties
    pageTitle="不按比例縮放 Azure 電腦自動學習函數與您串流分析工作 |Microsoft Azure"
    description="瞭解如何將正確不按比例縮放串流分析工作 （分割 SU 數量等） 時使用 Azure 電腦自動學習函數。"
    keywords=""
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>

# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-functions"></a>調整您的資料流分析工作與 Azure 電腦自動學習函數

通常是很容易串流分析工作設定，並瀏覽執行一些範例資料。 我們需要以較高的資料量執行相同作業時，我們應該做什麼？ 若要瞭解如何設定資料流分析作業，讓它會在不按比例縮放我們必須。 在此文件中，我們將著重於縮放串流分析工作電腦自動學習函數的特殊的觀點。 若要縮放資料流分析工作的一般資訊請參閱文件[縮放比例工作](stream-analytics-scale-jobs.md)。

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>什麼是資料流分析的 Azure 電腦自動學習函數？

在 [串流分析電腦學習函數可供等一般函數呼叫中的資料流分析查詢語言。 不過，場景，後面函數呼叫的實際 Azure 電腦學習 Web 服務要求。 電腦學習 web 服務支援 」 批次處理 「 多個資料列，就是迷你批次，在同一個 web 服務 API 呼叫，以改善整體處理能力。 請參閱下列文章以取得詳細資料。[Azure 電腦學習串流分析功能](https://blogs.technet.microsoft.com/machinelearning/2015/12/10/azure-ml-now-available-as-a-function-in-azure-stream-analytics/)和[Azure 電腦學習 Web 服務](machine-learning/machine-learning-consume-web-services.md#request-response-service-rrs)。

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>設定資料流分析工作與電腦自動學習函數

在設定電腦自動學習函數串流分析工作時，有兩個參數，需要考慮批次的大小電腦自動學習函數呼叫中，以及串流單位 (SUs) 佈建資料流分析工作。 這些決定適當的值，第一次必須決定之間延遲和處理量，也就是串流分析工作和每個 SU 的延遲。 雖然其他 SUs 增加執行作業的成本 SUs 可能永遠新增到工作來增加處理量，以及分割串流分析、 查詢。

因此務必決定延遲中執行資料流分析作業的*功能*。 執行 Azure 電腦學習服務要求其他延遲自然會增加批次大小，將複合串流分析工作的延遲。 另一方面，增加批次大小可讓資料流分析工作處理程序*與其他事件*同一個數字 * 電腦學習 web 服務要求。 通常電腦學習 web 服務延遲的增加是子線性批次大小的增加，因此請務必考慮在特定的情況下電腦學習 web 服務的成本最有效批次大小。 Web 服務要求的預設批次大小為 1000年，而且可能會使用[資料流分析 REST API](https://msdn.microsoft.com/library/mt653706.aspx "串流分析 REST API")或[PowerShell 用戶端串流分析](stream-analytics-monitor-and-manage-jobs-use-powershell.md "資料流分析 PowerShell 用戶端")修改。

一旦已決定批次大小，串流單位 (SUs) 的量可以判斷，根據函數需要的事件數秒程序。 如需進一步瞭解串流單位，請參閱本文[串流分析小數位數的工作](stream-analytics-scale-jobs.md#configuring-streaming-units)。

一般而言，沒有 20 同時連線至電腦學習 web 服務的每個 6 SUs，但，1 的 SU 工作和 3 的 SU 工作取得 20 同時連線也。  例如，如果輸入的資料速率是 200000 秒的事件，批次大小的剩餘的 1000 之預設 1000年事件迷你批次產生的 web 服務延遲是 200ms年。 這表示每個連接方便 5 要求電腦學習 web 服務中的第二個。 20 連線]，以資料流分析工作可以處理 200ms年中的 20000 事件，因此 100000 事件中第二個。 因此處理 200000 秒的事件，串流分析工作必須 40 同時連線的傳入 12 SUs。 下圖說明電腦學習 web 服務端點串流分析工作要求，每次 6 SUs 最大值在具有 20 同時電腦學習 web 服務連線。

![縮放比例串流分析電腦學習函數 2 工作範例](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "縮放比例串流分析電腦學習函數 2 工作範例")

一般而言， ***B***批次大小，以批次大小以毫秒為單位，B web 服務延遲***L*** ***N*** sus 串流分析工作的是︰

![不按比例縮放串流分析與電腦學習函數的公式](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "不按比例縮放串流分析與電腦學習函數的公式")

其他考量可能 」 最大同時呼叫' 電腦學習 web 服務一邊，建議您將此值設為最大的值 (目前 200)。

如需有關此設定，請檢閱[電腦學習 Web 服務的縮放比例文件](../machine-learning/machine-learning-scaling-webservice.md)的詳細資訊。

## <a name="example--sentiment-analysis"></a>範例 – 舉動分析

下列範例會包含舉動分析電腦自動學習函數、 串流分析工作[串流分析電腦學習整合教學課程](stream-analytics-machine-learning-integration-tutorial.md)中所述。

查詢會是一個簡單的分割完全查詢，後面接著**舉動**函數，如下所示︰

    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery

請考慮下列情況中;使用 10000 傳出 tweets 秒的處理能力串流分析工作必須建立執行舉動分析的推文 （事件）。 使用 1 SU，此串流分析工作能夠處理流量嗎？ 使用批次的預設大小 1000年工作應該能夠趕上輸入。 進一步新增的電腦自動學習函數應該不超過延遲，也就是一般] 的第二個產生舉動分析電腦學習 web 服務的預設的延遲 （含預設批次大小 1000年）。 資料流分析作業的**整體**或端對端延遲通常是幾秒鐘。 看詳細到這個資料流分析作業，*尤其是*學習函數呼叫的電腦。 為 1000年有批次的大小的 10000 事件處理能力會需要瞭解的 10 個要求 web 服務。 即使使用 1 SU，有足夠的同時連線以容納此輸入的流量。

但如果輸入的事件工資率 100 的 x，藉此增加，現在串流分析工作需要 1000000 傳出 tweets 每第二個 [處理程序？ 有兩個選項︰

1.  放大批次，或
2.  分割輸入資料流處理平行事件

使用第一個選項時，會增加工作**延遲**。

第二個選項，以更多 SUs 需要會佈建後，因此產生更多的同時電腦學習 web 服務要求。 這表示工作**成本**會增加。


假設舉動分析電腦學習 web 服務的延遲是 200ms 1000年事件批次或下方，250ms 5000 事件批次，300ms 10000 事件批次或 500 25000 事件批次。

1. 使用第一個選項，（****提供更多 SUs），可以到**25000**增加批次的大小。 這在可讓工作流程 1000000 事件 20 同時電腦學習 web 服務連線 （使用的每個呼叫 500年延遲）。 因此其他的延遲時間，因為舉動函數對要求電腦學習 web 服務要求的資料流分析工作會從**200ms年**增加，以**500年**。 不過，請注意，批次大小**無法**為提高無限電腦學習 web 服務需要要求的內容大小為 4 MB 或更小 web 服務要求之後作業的 100 秒的逾時。
2. 使用 [第二個選項，批次大小就會留在 1000年 200ms web 服務延遲，每 20 的同時連線至 web 服務就無法處理 1000年*20* 5 事件 = 100000 秒。 因此，若要程序 1000000 秒的事件，工作需要 60 SUs。 相較於第一個選項，串流分析工作會使其他 web 服務批次要求，產生更高的成本。

以下是資料表的資料流分析作業的不同 SUs 和批次 （在數秒事件）。

| 批次大小 （毫升延遲）  | 500 (200ms) | 1000 (200ms) | 5000 (250ms) | 10000 (300ms) | 25000 (500) |
|--------|-------------------------|---------------|---------------|----------------|----------------|
| **1 SU** | 2500 | 5000 | 20000 | 30000 | 50000 |
| **3 SUs** | 2500 | 5000 | 20000 | 30000 | 50000 |
| **6 SUs** | 2500 | 5000 | 20000 | 30000 | 50000 |
| **12 SUs** | 5000 | 10000 | 40000 | 60000 | 100000 |
| **18 SUs** | 7500 | 15000 | 60000 | 90000 | 150000 |
| **24 SUs** | 10000 | 20000 | 80000 | 120000 | 200000 |
| **…** | … | … | … | … | … |
| **60 SUs** | 25000 | 50000 | 200000 | 300000 | 500000 |

現在，您應該已經瞭解電腦學習串流分析功能的運作方式。 您可能也了解從資料來源的資料流分析工作 」 提取 「 資料和每個 「 提取 」 會傳回一個批次的事件處理資料流分析工作。 此擷取模型影響電腦學習如何 web 服務要求？

一般而言，我們要為電腦自動學習函數批次大小完全無法整除事件 」 提取 」 的每個資料流分析工作所傳回的數字。 這會發生電腦學習 web 服務會被稱為 「 部分 」 批次使用時。 這是為了不導致聯合事件中的其他工作延遲負荷提取擷取。

## <a name="new-function-related-monitoring-metrics"></a>新函數相關的監控指標

在 [串流分析工作的 [監視器] 區域中，已新增三個額外的函數相關指標。 他們的年齡函數要求函數事件與失敗函數的要求下, 圖所示。

![不按比例縮放與電腦學習函數指標串流狀況分析](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "不按比例縮放與電腦學習函數指標串流狀況分析")

是的定義如下︰

**函數的要求**︰ 函數要求的數目。

**函數事件**︰ 在函數邀請中的數字事件。

**無法函式的要求**︰ 失敗的函數要求的數目。

## <a name="key-takeaways"></a>重要心得  

若要重點，摘要才能不按比例縮放與電腦自動學習函數的資料流分析作業，必須考慮下列項目︰

1.  輸入的事件工資率
2.  執行資料流分析作業 （及電腦學習 web 服務要求批次大小） tolerated 的延遲
3.  提供的資料流分析 SUs 及電腦學習 web 服務要求 （其他函數相關的成本） 的數目

完整資料分割的資料流分析查詢所使用的範例。 如果需要較複雜的查詢[Azure 資料流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)是很實用的資源，為從資料流分析小組取得其他協助。

## <a name="next-steps"></a>後續步驟

若要進一步瞭解串流分析資料，請參閱︰

- [開始使用 Azure 資料流狀況分析](stream-analytics-get-started.md)
- [不按比例縮放 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure 資料流分析查詢語言參照](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 資料流分析管理 REST API 參照](https://msdn.microsoft.com/library/azure/dn835031.aspx)
