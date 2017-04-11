<properties
    pageTitle="資料連線︰ 資料流輸入從事件資料流 |Microsoft Azure"
    description="進一步瞭解如何設定稱為 「 輸入 「 資料流分析的資料連線。 輸入包含資料流從事件，然後也參考資料]。"
    keywords="資料流、 資料連線、 活動串流"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="data-connection-learn-about-data-stream-inputs-from-events-to-stream-analytics"></a>資料連線︰ 深入了解資料串流輸入從事件串流狀況分析

資料流分析的資料連線是事件從資料來源的資料流。 這稱為 「 輸入 」。 資料流分析具有最高級 Azure 資料串流來源事件中心 IoT] 中心內，與 Blob 儲存可從相同或不同 Azure 訂閱，以分析工作的整合。

## <a name="data-input-types-data-stream-and-reference-data"></a>資料輸入類型︰ 資料串流和參考資料
為推入資料至資料來源，它是由資料流分析作業，處理中進行即時。 輸入可分為兩種不同類型︰ 資料串流輸入和參考資料輸入。

### <a name="data-stream-inputs"></a>資料串流輸入
資料流是未繫結即將一段時間的事件順序。 資料流分析工作必須包含至少一個資料串流輸入 consumed 和轉換的工作。 Blob 儲存體、 事件集線器 IoT 集線器支援和當做資料串流輸入來源。 事件集線器用來收集事件資料流來自多個裝置和服務，例如社交媒體活動摘要、 股票交易資訊或感應器的資料。 IoT 集線器進行最佳化，從網際網路的項目 (IoT) 案例中的連線裝置收集資料。  Blob 儲存體可以用於為輸入來源 ingesting 大量資料，以資料流。  

### <a name="reference-data"></a>參考資料
資料流分析支援稱為 [參考資料輸入第二個類型。 這是輔助靜態或緩一段時間，而通常用來執行相互關聯與查詢的資料。 Azure Blob 儲存體目前是參考資料僅支援輸入的來源。 參考資料來源二進位大型物件的大小限制為 100 MB。
若要瞭解如何建立參照資料輸入，請參閱[使用參考資料](stream-analytics-use-reference-data.md)  

## <a name="create-a-data-stream-input-with-an-event-hub"></a>使用 [事件] 中心內建立資料串流輸入

[Azure 事件集線器](https://azure.microsoft.com/services/event-hubs/)是 free 發佈訂閱事件 ingestor。 讓您可以處理程序及分析大量資料所產生的連線的裝置及應用程式，也可以收集數百萬秒的事件。 是其中一個最常用的輸入資料流分析。 事件集線器和串流分析一起提供客戶的端對端解決方案即時分析。 事件集線器讓客戶送入 Azure 中的事件，即時，並串流分析工作中進行即時在處理。 例如，客戶可以傳送 web 點選、 感應器讀取、 線上記錄事件到事件，並建立作為事件集線器輸入的資料流即時篩選、 總結及相關的資料流分析工作。

請務必請注意，來自事件集線器串流分析中的事件的預設時間戳記事件送達時即 EventEnqueuedUtcTime 事件中樞的時間戳記。 若要處理資料，以資料流使用事件內容中的時間戳記，必須使用[時間戳記的](https://msdn.microsoft.com/library/azure/dn834998.aspx)關鍵字。

### <a name="consumer-groups"></a>消費者群組

每個資料流分析事件中心輸入應設定讓它自己的消費者群組。 當工作包含自我聯結或多個輸入時，可能會讀取部分輸入多個讀者傳輸，影響讀者單一消費者群組中的數。 若要避免超過 5 讀者每個每個資料分割的消費者群組事件中心限制，則若要指定每個資料流分析工作的消費者群組的最佳作法。 請注意，也有每事件中心 20 消費者群組的限制。 如需詳細資訊，請參閱[事件集線器程式設計指南](../event-hubs/event-hubs-programming-guide.md)。

### <a name="configure-event-hub-as-an-input-data-stream"></a>設定事件中心以輸入的資料流

下表說明每個屬性，在事件] 中心內輸入描述] 索引標籤︰

| 屬性名稱 | 描述 |
|------|------|
| 輸入的別名 | 將工作查詢中使用參考此輸入好記的名稱 |
| 服務匯流排命名空間 | 服務匯流排命名空間是一組之訊息的實體容器。 當您建立新的事件中心時，也會建立服務匯流排命名空間。 |
| 事件中心 | 輸入您事件中樞的名稱。 |
| 事件中心原則名稱 | 共用的 access 原則，可以建立在 [事件中心設定] 索引標籤。 每個共用的存取原則會有一個名稱，在您的設定，權限，並便捷鍵。 |
| 事件中心原則索引鍵 | 用來驗證服務匯流排命名空間存取共用便捷鍵。 |
| 事件中心消費者群組 （可省略） | 要內嵌資料從 [事件] 中心的消費者群組。 如果未指定資料流分析工作會使用 [預設消費者群組可以內嵌來自 [事件] 中心內的資料。 建議您為每個資料流分析工作使用不同的消費者群組。 |
| 事件序列化格式 | 若要確定您的查詢的運作的方式預期，串流分析需要知道哪些序列化格式 （JSON、 CSV 或 Avro） 您所使用的內送的資料流。 |
| 編碼 | Utf-8 這次是只支援編碼格式。 |

當您的資料來自事件中心來源時，您就可以存取較少的中繼資料欄位至資料流分析查詢。 下表列出的欄位和描述。

| 屬性 | 描述 |
|------|------|
| EventProcessedUtcTime | 日期及時間的事件處理的資料流分析。 |
| EventEnqueuedUtcTime | 日期及時間事件集線器所收到的事件。 |
| PartitionId | 輸入的介面卡的起始分割識別碼。 |

例如，您可能會撰寫的查詢，如下所示︰

````
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
````

## <a name="create-an-iot-hub-data-stream-input"></a>建立 IoT 中樞資料串流輸入

Azure Iot 中心是彈性發行-訂閱事件 ingestor 最佳化 IoT 案例。
請務必請注意，來自 IoT 集線器串流分析中的事件的預設時間戳記事件送達時即 EventEnqueuedUtcTime IoT 中樞的時間戳記。 若要處理資料，以資料流使用事件內容中的時間戳記，必須使用[時間戳記的](https://msdn.microsoft.com/library/azure/dn834998.aspx)關鍵字。

> [AZURE.NOTE] 可以處理 DeviceClient 屬性時所傳送的郵件。

### <a name="consumer-groups"></a>消費者群組

每個資料流分析 IoT 中心輸入應設定讓它自己的消費者群組。 當工作包含自我聯結或多個輸入時，可能會讀取部分輸入多個讀者傳輸，影響讀者單一消費者群組中的數。 若要避免超過 IoT 中樞的每個消費者群組每個資料分割 5 讀者的限制，則若要指定每個資料流分析工作的消費者群組的最佳作法。

### <a name="configure-iot-hub-as-an-data-stream-input"></a>以輸入的資料流設定 IoT 中心

下表說明描述 IoT 中心輸入索引標籤中的每個屬性︰

| 屬性名稱 | 描述 |
|------|------|
| 輸入的別名 | 將工作查詢中用來參照這個輸入好記的名稱。 |
| IoT 中心 | IoT 中心是一組之訊息的實體容器。 |
| 結束點 | 您 IoT 中心結束點名稱。 |
| 共用的 Access 原則名稱 | 若要授與存取權 IoT 中心共用的存取原則。 每個共用的存取原則會有一個名稱，在您的設定，權限，並便捷鍵。 |
| 共用的 Access 原則機碼 | 用來驗證 IoT 中心存取共用便捷鍵。 |
| 消費者群組 （可省略） | 要內嵌資料從 IoT 中樞的消費者群組。 如果未指定資料流分析工作會使用 [預設消費者群組可以內嵌 IoT 中樞的資料。 建議您為每個資料流分析工作使用不同的消費者群組。 |
| 事件序列化格式 | 若要確定您的查詢的運作的方式預期，串流分析需要知道哪些序列化格式 （JSON、 CSV 或 Avro） 您所使用的內送的資料流。 |
| 編碼 | Utf-8 這次是只支援編碼格式。 |

當您的資料來自 IoT 中心來源時，您就可以存取較少的中繼資料欄位至資料流分析查詢。 下表列出的欄位和描述。

| 屬性 | 描述 |
|------|------|
| EventProcessedUtcTime | 日期及時間的事件處理。 |
| EventEnqueuedUtcTime | 日期及時間 IoT 中心所收到的事件。 |
| PartitionId | 輸入的介面卡的起始分割識別碼。 |
| IoTHub.MessageId | 用來建立在 IoT 中心的雙向通訊的關聯。 |
| IoTHub.CorrelationId | 用於訊息回覆和 IoT 中心中的意見反應。 |
| IoTHub.ConnectionDeviceId | 用來傳送此郵件，由 IoT 集線器戳記 servicebound 郵件經過驗證的識別碼。 |
| IoTHub.ConnectionDeviceGenerationId | 用來傳送此郵件，已驗證裝置 generationId 由 IoT 集線器戳記 servicebound 的郵件]。 |
| IoTHub.EnqueuedTime | IoT 中心時收到郵件的時間。 |
| IoTHub.StreamId | 新增寄件者裝置的自訂事件屬性。 |

## <a name="create-a-blob-storage-data-stream-input"></a>建立 Blob 儲存體資料串流輸入

大量儲存在雲端的非結構化資料的情況下，Blob 儲存體提供的效益和擴充的解決方案。 [Blob 儲存體](https://azure.microsoft.com/services/storage/blobs/)中的資料通常會被視為 「 靜止 」 的資料，但可為資料流中處理的資料流分析。 以資料流分析 Blob 儲存體輸入一個常見的情況是記錄處理遙測從系統會擷取及需要剖析和處理以擷取有意義的資料的位置。

請務必請注意，Blob 儲存體中的事件串流分析的預設時間戳記的 blob 上次修改哪些*isBlobLastModifiedUtcTime*的時間戳記。 若要處理資料，以資料流使用事件內容中的時間戳記，必須使用[時間戳記的](https://msdn.microsoft.com/library/azure/dn834998.aspx)關鍵字。

另請注意 CSV 格式輸入**要求**的標題列，以定義資料集的欄位。 進一步的標題列欄位必須都是**唯一**。

> [AZURE.NOTE] 資料流分析不支援新增至現有的 blob 的內容。 一次及所有變更完成後將不會處理此閱讀，串流分析只會檢視 blob。 最佳作法是一次上傳的所有資料，並新增任何其他事件 blob 存放區。

下表說明 Blob 儲存體輸入索引標籤中描述每個屬性︰

<table>
<tbody>
<tr>
<td>屬性名稱</td>
<td>描述</td>
</tr>
<tr>
<td>輸入的別名</td>
<td>將工作查詢中用來參照這個輸入好記的名稱。</td>
</tr>
<tr>
<td>儲存帳戶</td>
<td>Blob 檔案的所在位置的儲存體帳戶名稱。</td>
</tr>
<tr>
<td>儲存帳戶金鑰</td>
<td>儲存帳戶相關聯私密金鑰。</td>
</tr>
<tr>
<td>儲存容器
</td>
<td>容器提供 blob 儲存在 Microsoft Azure Blob 服務中的邏輯群組。 當您上載 blob Blob 服務時，您必須指定為該 blob 的容器。</td>
</tr>
<tr>
<td>[選擇性] 路徑加上字首圖樣</td>
<td>用來找出您的二進位大型物件指定容器中的檔案路徑。
中的路徑，您可以選擇指定下列 3 個變數的一或多個執行個體︰<BR>{日期}、 {時間}<BR>{分割}<BR>範例 1: cluster1 記錄檔 / {日期} {時間} / {分割}<BR>範例 2: cluster1 記錄檔 / {日期}<P>請注意，「 * 」 不允許的 pathprefix 的值。 允許有效<a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Azure blob 的字元</a>。</td>
</tr>
<tr>
<td>[選擇性] 的日期格式</td>
<td>如果前置詞路徑中使用日期權杖，您可以選取組織檔案的 [日期] 格式。 範例︰ YYYY/MM/DD</td>
</tr>
<tr>
<td>[選擇性] 時間格式</td>
<td>如果時間權杖使用前置詞路徑中，指定組織檔案的時間格式。 目前唯一支援的值是 HH。</td>
</tr>
<tr>
<td>事件序列化格式</td>
<td>若要確定您的查詢的運作的方式預期，串流分析需要知道哪些序列化格式 （JSON、 CSV 或 Avro） 您所使用的內送的資料流。</td>
</tr>
<tr>
<td>編碼</td>
<td>CSV，JSON utf-8 是只支援編碼格式這一次。</td>
</tr>
<tr>
<td>分隔符號</td>
<td>資料流分析序列化中的資料 CSV 格式支援常見的分隔符號的數字。 支援的值為逗號、 分號、 空格、] 索引標籤及垂直線。</td>
</tr>
</tbody>
</table>

當您的資料來自 Blob 儲存體來源時，您就可以存取資料流分析查詢幾個中繼資料欄位。 下表列出的欄位和描述。

| 屬性 | 描述 |
|------|------|
| BlobName | 輸入 blob 的事件的來源的名稱。 |
| EventProcessedUtcTime | 日期及時間的事件處理的資料流分析。 |
| BlobLastModifiedUtcTime | 日期及 blob 上次修改時間。 |
| PartitionId | 輸入的介面卡的起始分割識別碼。 |

例如，您可能會撰寫的查詢，如下所示︰

````
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
````


## <a name="get-help"></a>取得說明
進一步協助，請嘗試我們[Azure 資料流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>後續步驟
您已瞭解 Azure 中的資料連線選項，為您的資料流分析工作。 若要進一步瞭解串流分析資料，請參閱︰

- [開始使用 Azure 資料流狀況分析](stream-analytics-get-started.md)
- [不按比例縮放 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure 資料流分析查詢語言參照](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 資料流分析管理 REST API 參照](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
