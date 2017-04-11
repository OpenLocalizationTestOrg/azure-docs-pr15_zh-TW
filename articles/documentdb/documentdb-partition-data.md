<properties 
    pageTitle="分割和縮放比例 Azure DocumentDB |Microsoft Azure"      
    description="深入了解如何分割 Azure DocumentDB、 如何設定分割和分割鍵，以及如何選擇正確的磁碟分割鍵，應用程式中的運作方式。"         
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/> 

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/20/2016" 
    ms.author="arramac"/> 

# <a name="partitioning-and-scaling-in-azure-documentdb"></a>分割和 Azure DocumentDB 中的縮放比例
[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)是設計用來協助您達成快速，如預期呈現效能與完美以及您的應用程式的小數位數的成長。 本文概述 DocumentDB 中的資料分割方式運作，並說明如何設定 DocumentDB 集合有效調整您的應用程式。

閱讀本文之後，您將可以回答下列問題︰   

- 分割 Azure DocumentDB 中的運作方式？
- 如何設定在 DocumentDB 分割
- 什麼是分割鍵，然後如何我的應用程式選擇正確的磁碟分割鍵嗎？

若要開始使用程式碼，請從[DocumentDB 效能測試驅動程式範例](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark)下載專案。 

## <a name="partitioning-in-documentdb"></a>分割 DocumentDB 中

在 DocumentDB，您可以儲存並無結構描述的 JSON 文件，在任何的毫秒數順序回應時間的查詢。 DocumentDB 提供容器儲存資料稱為**集合**。 集合邏輯的資源，而且可以跨越一或多個實體磁碟分割區或伺服器。 根據儲存空間大小和的集合能夠處理量 DocumentDB 取決於磁碟分割區的數目。 DocumentDB 中的每個資料分割有固定的 SSD 備份，相關聯的儲存空間量，而且複寫的可用性。 磁碟分割管理完全受 Azure DocumentDB，而且您不需要撰寫複雜的程式碼或管理您的分割。 DocumentDB 集合是**幾乎沒有限制**儲存及處理量。 

分割是完全透明的應用程式。 DocumentDB 支援快速讀取與寫入、 SQL 和 LINQ 查詢、 JavaScript 基礎交易邏輯、 一致性等級和微調存取控制項，透過 REST API 的來電至單一集合資源。 服務處理散發資料分割以及右邊的磁碟分割路由查詢要求。 

此功能如何運作？ 當您建立集合 DocumentDB 中時，您會發現有**分割鍵值屬性**設定值，您可以指定。 這是 JSON 屬性 （或路徑），您可讓 DocumentDB 散發資料之間的多個伺服器或磁碟分割區的文件中。 DocumentDB 將雜湊分割關鍵值，並使用雜湊的結果決定 JSON 文件會儲存在其中的磁碟分割。 以相同的磁碟分割金鑰的所有文件會儲存在相同的磁碟分割。 

例如，請考慮儲存 DocumentDB 員工與其部門相關資料的應用程式。 讓我們來選擇`"department"`當作分割鍵值屬性，才能藉部門。 每個文件中 DocumentDB 必須包含強制`"id"`屬性必須是唯一的每個文件以相同的磁碟分割關鍵值，例如`"Marketing`」。 每個集合中儲存的文件必須具備唯一組合分割索引鍵和識別碼，例如`{ "Department": "Marketing", "id": "0001" }`， `{ "Department": "Marketing", "id": "0002" }`，及`{ "Department": "Sales", "id": "0001" }`。 換句話說，複合屬性 (磁碟分割按鍵，id) 集合的主索引鍵。

### <a name="partition-keys"></a>分割索引鍵
選擇分割索引鍵是您必須在設計階段進行重要決策。 您必須選取含有大範圍的值，且可能會有平均分散存取模式的 JSON 屬性名稱。 分割索引鍵所指定 JSON 路徑，例如`/department`代表屬性部門。 

下表顯示的磁碟分割索引鍵定義和 JSON 值會對應至每個的範例。

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>分割重要路徑</strong></p></td>
            <td valign="top"><p><strong>描述</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>/department</p></td>
            <td valign="top"><p>對應的文件所在的文件的 doc.department JSON 值。</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ 屬性/名稱</p></td>
            <td valign="top"><p>對應的文件所在的文件 （巢狀屬性） doc.properties.name JSON 值。</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/id</p></td>
            <td valign="top"><p>對應到 doc.id JSON 值 （id 和分割索引鍵是相同的屬性）。</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/ 「 部門名稱"</p></td>
            <td valign="top"><p>對應至文件 [」 部門名稱"] 文件位於文件的何處 JSON 值。</p></td>
        </tr>
    </tbody>
</table>

> [AZURE.NOTE] 分割重要路徑的語法是類似的路徑規格編製索引的主要差異路徑對應至 [，而不是值] 屬性與原則路徑，亦即有沒有萬用字元結尾。 例如，您可以指定/部門/嗎？ 若要編製索引部門] 下的數值，但分割鍵定義為指定 /department。 分割重要路徑隱含地建立索引，並無法從使用編製索引的原則覆寫編製索引作業。

讓我們來看看如何分割索引鍵的選擇會影響應用程式的效能。

### <a name="partitioning-and-provisioned-throughput"></a>分割及能夠傳輸量
DocumentDB 被專為預期的效能。 當您建立的集合時，您會保留處理**[要求單位](documentdb-request-units.md)(RU) 秒**的能力。 每個要求會被指派是 CPU 和 IO 由作業等系統資源的數量等比例要求單位收費。 1 kB 文件的工作階段的一致性讀取會耗費 1 要求單位。 讀取為 1 RU 無論儲存的項目數目或同時的要求，同時執行的數目。 較大的文件需要較高的要求單位，根據大小。 如果您知道您的實體和讀取您需要您的應用程式支援的數字的大小，您可以提供確實所需的所需的應用程式的需求閱讀處理量。 

當 DocumentDB 儲存文件時，其分散給平均磁碟分割區以分割鍵值。 處理量也平均分佈之間使用分割也就是每個資料分割的處理能力 = （每個集合總處理量） / （數字的磁碟分割區）。 

>[AZURE.NOTE] 以達到完整的處理量的集合，您必須選擇資料分割索引鍵，可讓您平均分散之間的不同的磁碟分割關鍵值數字的要求。

## <a name="single-partition-and-partitioned-collections"></a>單一資料分割和分割的集合
DocumentDB 支援建立的單一資料分割和分割的集合。 

- **分割集合**可以橫跨多個資料分割及支援] 有大量的儲存空間及處理量。 您必須指定集合的資料分割索引鍵。
- **單一資料分割集合**有較低的價格選項和查詢，以及跨集合的所有資料執行交易的能力。 他們擁有單一資料分割延展性和儲存空間的限制。 您沒有指定這些集合分割索引鍵。 

![分割的 DocumentDB 集合][2] 

不需要大量的儲存空間或處理量的情況下，單一磁碟分割集合相當適合。 請注意，單一資料分割集合延展性和儲存空間限制單一分割的資料，亦即 10 GB 的儲存空間，以及最秒的 10000 要求單位。 

分割的集合，可支援有大量的儲存空間及處理量。 預設優惠不過設定儲存 250 GB 的儲存空間，並不按比例縮放秒的最 250000 要求單位。 如果您需要較高的儲存空間或每個集合處理量，請連絡[Azure 支援](documentdb-increase-limits.md)有這些提升您的帳戶。

下表列出使用單一資料分割和分割的集合中的差異︰

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>單一資料分割集合</strong></p></td>
            <td valign="top"><p><strong>分割的集合</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>分割索引鍵</p></td>
            <td valign="top"><p>無</p></td>
            <td valign="top"><p>所需</p></td>
        </tr>
        <tr>
            <td valign="top"><p>文件的主索引鍵</p></td>
            <td valign="top"><p>「 識別碼 」</p></td>
            <td valign="top"><p>複合索引鍵&lt;分割索引鍵&gt;和 「 識別碼 」</p></td>
        </tr>
        <tr>
            <td valign="top"><p>最小值的儲存空間</p></td>
            <td valign="top"><p>0 GB</p></td>
            <td valign="top"><p>0 GB</p></td>
        </tr>
        <tr>
            <td valign="top"><p>最大值的儲存空間</p></td>
            <td valign="top"><p>10 GB</p></td>
            <td valign="top"><p>無限制 (依預設 250 GB)</p></td>
        </tr>
        <tr>
            <td valign="top"><p>最小的處理量</p></td>
            <td valign="top"><p>每秒 400 要求單位</p></td>
            <td valign="top"><p>每秒的 10000 要求單位</p></td>
        </tr>
        <tr>
            <td valign="top"><p>最大處理量</p></td>
            <td valign="top"><p>每秒的 10000 要求單位</p></td>
            <td valign="top"><p>無限制 （依預設秒的 250000 要求單位）</p></td>
        </tr>
        <tr>
            <td valign="top"><p>API 版本</p></td>
            <td valign="top"><p>所有</p></td>
            <td valign="top"><p>API 2015 年 12-16 及更新版本</p></td>
        </tr>
    </tbody>
</table>

## <a name="working-with-the-sdks"></a>使用 Sdk

Azure DocumentDB 新增自動分割以[REST API 版本 2015 年 12-16](https://msdn.microsoft.com/library/azure/dn781481.aspx)的支援。 建立分割的集合，您必須下載 SDK 版本 1.6.0 或更新版本中其中一個支援 SDK 平台 （.NET Node.js、 Java、 Python）。 

### <a name="creating-partitioned-collections"></a>建立分割的集合

下列範例會顯示建立每秒的處理量 20000 要求單位的裝置遙測資料的儲存集合.NET 程式碼片段。 SDK 設定 OfferThroughput 值 (其中將`x-ms-offer-throughput`REST API 邀請標頭)。 以下我們設定`/deviceId`分割索引鍵。 分割索引鍵的選擇會儲存以及集合中繼資料名稱等編製索引原則的其餘部分。

此範例中，我們挑選`deviceId`我們知道 （a） 之後有大量的裝置，因為寫入由磁碟分割區平均並讓我們要內嵌龐大的大量資料內容的資料庫不按比例縮放 （b） 有許多像取得最新的讀取裝置的相關的要求都只限於單一 deviceId，可以從單一磁碟分割擷取。

    DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
    await client.CreateDatabaseAsync(new Database { Id = "db" });

    // Collection for device telemetry. Here the JSON property deviceId will be used as the partition key to 
    // spread across partitions. Configured for 10K RU/s throughput and an indexing policy that supports 
    // sorting against any number or string property.
    DocumentCollection myCollection = new DocumentCollection();
    myCollection.Id = "coll";
    myCollection.PartitionKey.Paths.Add("/deviceId");

    await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri("db"),
        myCollection,
        new RequestOptions { OfferThroughput = 20000 });
        

> [AZURE.NOTE] 建立分割的集合，您必須指定秒 > 10000 要求單位處理量值。 由於處理量的 100 的多重圖表中，這必須是 10,100 或更新版本。

這個方法可讓您撥 DocumentDB，REST API，服務會佈建數字的磁碟分割區以要求的處理量。 您可以變更您的效能需要的集合。 如需詳細資訊，請參閱[效能等級](documentdb-performance-levels.md)。

### <a name="reading-and-writing-documents"></a>閱讀及撰寫文件

現在讓我們來插入 DocumentDB 中的資料。 以下是包含讀取，請在裝置的範例類別及 CreateDocumentAsync 呼叫以插入新的裝置讀取成集合。

    public class DeviceReading
    {
        [JsonProperty("id")]
        public string Id;

        [JsonProperty("deviceId")]
        public string DeviceId;

        [JsonConverter(typeof(IsoDateTimeConverter))]
        [JsonProperty("readingTime")]
        public DateTime ReadingTime;

        [JsonProperty("metricType")]
        public string MetricType;

        [JsonProperty("unit")]
        public string Unit;

        [JsonProperty("metricValue")]
        public double MetricValue;
      }

    // Create a document. Here the partition key is extracted as "XMS-0001" based on the collection definition
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new DeviceReading
        {
            Id = "XMS-001-FE24C",
            DeviceId = "XMS-0001",
            MetricType = "Temperature",
            MetricValue = 105.00,
            Unit = "Fahrenheit",
            ReadingTime = DateTime.UtcNow
        });


現在就讓我們閱讀文件的磁碟分割鍵] 及 [識別碼的更新，並最後一個步驟中，然後將其刪除以分割索引鍵和 id。 請注意，讀取加 PartitionKey 值 (對應至`x-ms-documentdb-partitionkey`REST API 邀請標頭)。

    // Read document. Needs the partition key and the ID to be specified
    Document result = await client.ReadDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

    DeviceReading reading = (DeviceReading)(dynamic)result;

    // Update the document. Partition key is not required, again extracted from the document
    reading.MetricValue = 104;
    reading.ReadingTime = DateTime.UtcNow;

    await client.ReplaceDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      reading);

    // Delete document. Needs partition key
    await client.DeleteDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });



### <a name="querying-partitioned-collections"></a>查詢分割的集合

當您查詢分割集合中的資料時，DocumentDB 自動將查詢傳送至對應的磁碟分割鍵值 （如果有的話），在篩選中指定的磁碟分割。 例如，此查詢會路由至只包含分割索引鍵 」 XMS 0001 」 的磁碟分割。

    // Query using partition key
    IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"))
        .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");

下列查詢並沒有篩選器的磁碟分割鍵 (DeviceId)，並執行的磁碟分割索引針對所在的所有磁碟分割區以扇形。 請注意，您必須指定 EnableCrossPartitionQuery (`x-ms-documentdb-query-enablecrosspartition` REST API 中) 有 SDK 跨磁碟分割區執行查詢。

    // Query across partition keys
    IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"), 
        new FeedOptions { EnableCrossPartitionQuery = true })
        .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);

### <a name="parallel-query-execution"></a>平行查詢

DocumentDB Sdk 1.9.0 支援平行查詢執行選項，可讓您執行分割的集合，低延遲查詢，即使他們需要觸控大量的磁碟分割區的上方。 例如，下列查詢會設為同時在多個執行磁碟分割區。

    // Cross-partition Order By Queries
    IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"), 
        new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
        .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
        .OrderBy(m => m.MetricValue);

您可以管理查詢執行平行調整下列參數︰

- 藉由設定`MaxDegreeOfParallelism`，您可以控制平行即集合的磁碟分割區的同時網路連線的最大數目度。 如果您設定為-1，平行度由 SDK 管理。 如果`MaxDegreeOfParallelism`未指定或設定以 0，這是預設值] 會有集合的磁碟分割區的單一網路連線。
- 藉由設定`MaxBufferedItemCount`，您可以交換查詢延遲和用戶端側邊的記憶體使用率。 如果您省略此參數，或將此值設為-1，由 SDK 管理緩衝平行查詢執行時的項目數目。

指定集合中的相同的狀態，平行查詢會傳回結果，與序列執行相同的順序。 執行時跨分割查詢包含排序 （ORDER BY 及/或上方），DocumentDB SDK 跨磁碟分割區問題平行查詢，並合併以產生全域排序的結果的用戶端中的部分排序的結果。

### <a name="executing-stored-procedures"></a>執行 [預存程序

您也可以例如執行最小的異動，針對文件的相同的裝置識別碼，如果您維護彙總或單一文件中的裝置的最新狀態。 

    await client.ExecuteStoredProcedureAsync<DeviceReading>(
        UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
        new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
        "XMS-001-FE24C");

在下一個區段中，我們看看如何移動至分割集合從單一資料分割集合。

<a name="migrating-from-single-partition"></a>
### <a name="migrating-from-single-partition-to-partitioned-collections"></a>從單一資料分割升級至分割的集合
使用單一資料分割集合應用程式時需要較高的處理量 (> 10000 RU/s) 或更大的資料儲存區 (> 10 GB)，您可以使用[DocumentDB 資料移轉工具](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d)，將資料從單一資料分割集合移轉至分割集合。 

若要從單一資料分割集合移轉至分割集合

1. 匯出資料的單一資料分割集合 JSON。 如需詳細資訊，請參閱[匯出 JSON 檔案](documentdb-import-data.md#export-to-json-file)。
2. 將資料匯入資料分割集合分割鍵定義和第二個處理量，每超過 10000 個要求單位建立下列範例所示。 如需詳細資訊，請參閱[DocumentDB 匯入](documentdb-import-data.md#DocumentDBSeqTarget)。

![移轉至分割集合 DocumentDB 中的資料][3]  

>[AZURE.TIP] 更快速地匯入的時間，請考慮增加數字的平行要求 100 位或更新版本才能利用適用於分割集合較高的處理量。 

現在，我們已完成的基本概念，我們來看看幾個重要的設計考量時使用的磁碟分割 DocumentDB 鍵。

## <a name="designing-for-partitioning"></a>分割的設計
選擇分割索引鍵是您必須在設計階段進行重要決策。 本節說明一些必須做的取捨中選取您的集合資料分割索引鍵。

### <a name="partition-key-as-the-transaction-boundary"></a>為交易界限分割索引鍵
您所選擇的磁碟分割金鑰應該平衡需要啟用異動實體分散多個資料分割鍵，以確保可調整解決方案的需求。 其中一種方式，您無法將相同的分割索引鍵的所有文件，但這可能會限制您的方案延展性。 其他時，您可以指定唯一的磁碟分割鍵每份文件，就會彈性，但想要防止使用跨文件交易透過預存程序與引動程序。 適合分割索引鍵是指，可讓您使用有效的查詢，並有足夠的基數，以確保您的解決方案是可調整。 

### <a name="avoiding-storage-and-performance-bottlenecks"></a>避免儲存和效能瓶頸 
務必也選取屬性可寫入分佈唯一值的數目。 要求的相同的磁碟分割鍵不能超過的單一資料分割，並將經流速控制。 因此請務必挑選資料不會導致**「 作用點 」**應用程式中的分割索引鍵。 以相同的磁碟分割金鑰的文件的總儲存空間大小也不能超過 10 GB 的儲存空間。 

### <a name="examples-of-good-partition-keys"></a>建議的磁碟分割鍵的範例
以下是如何選擇資料分割鍵，應用程式的一些範例︰

* 如果您實作使用者設定檔後端，則的使用者識別碼會為適合分割索引鍵。
* 如果您儲存 IoT 資料例如裝置狀態，裝置識別碼是適合分割索引鍵。
* 如果您使用的記錄時間序列資料 DocumentDB，主機名稱或程序 ID 是適合分割索引鍵。
* 如果您有多個租用戶架構，租用戶 ID 是適合分割索引鍵。

請注意，在 （例如 IoT] 和 [使用者設定檔上述） 一些使用情況下，分割索引鍵可能會與您的識別碼 （文件索引） 相同。 在其他類似時間數列資料，您可能有不同的識別碼資料分割索引鍵。

### <a name="partitioning-and-loggingtime-series-data"></a>分割和記錄/時間數列的資料
其中一個最常用的使用案例的 DocumentDB 適用於記錄及遙測。 請務必挑選以良好的磁碟分割金鑰，因為您可能需要讀/寫大量的大量資料內容。 選擇會取決於您讀取及撰寫工資率和您要執行的查詢類型。 以下是一些秘訣，瞭解如何選擇建議的磁碟分割鍵。

- 如果您使用案例涉及小型率一段時間，覆寫 acculumating，需要的時間戳記的範圍以查詢和其他篩選]，然後使用例如的彙總套件的時間戳記的日期資料分割索引鍵是很好的方法如下。 這可讓您查詢的所有資料日期的單一資料分割。 
- 如果您的工作量寫入大量，也就是常見通常，您應該使用不根據時間戳記，好讓 DocumentDB 可以分散寫入平均的磁碟分割之數字資料分割索引鍵。 以下是主機名稱、 程序識別碼、 活動 ID 或高基數的另一個屬性是很不錯的選擇。 
- 第三種方法是混合式其中一個位置您有多個集合] 中，為每日/月和分割索引鍵是細微的屬性，例如主機名稱。 這是您可以設定不同的效能層級是根據 [時間] 視窗的優勢，例如本月份的集合會佈建與較高的處理量因為它是讀取和寫入，而使用前一個月降低處理量，因為他們只能處理讀取。

### <a name="partitioning-and-multi-tenancy"></a>分割和多重 tenancy
如果您在實作使用 DocumentDB 的多租用戶應用程式，有兩個主要的搜尋模式實作與 DocumentDB – tenancy 的每一租用戶的磁碟分割索引鍵和每個租用戶的一個集合。 以下是每個優缺點︰

* 每個租用戶的一個分割索引鍵︰ 此模型的租用戶的組單一集合中。 但針對單一磁碟分割，則可以執行查詢及插入的單一租用戶中的文件。 您也可以實作交易邏輯租用戶中的所有文件。 多個租用戶共用集合，因為您可以在單一集合內的租用戶共用資源，而不是佈建額外發揮空間，每個租用戶儲存儲存及處理量的成本。 缺點是您沒有效能隔離每一租用戶。 套用至整個集合與效能/處理量增加租用戶的目標的增加。
* 每個租用戶的一個集合︰ 每個租用戶有它自己的集合。 在此模式中，您可以保留每一租用戶的效能。 此模型 DocumentDB 的新消耗基礎價格的模型，就加值的租用戶少數多租用戶應用程式。

您也可以使用的組合/階層的方法，collocates 小型的租用戶，並會較大的租用戶移轉至自己的集合。

## <a name="next-steps"></a>後續步驟
在本文中，我們已來描述中 Azure DocumentDB 如何分割的運作方式，您可以建立分割的集合，與如何您可以選擇以良好的磁碟分割金鑰應用程式的。 

-   執行縮放比例和效能與 DocumentDB 測試。 範例，請參閱[效能及使用 Azure DocumentDB 測試的小數位數](documentdb-performance-testing.md)。
-   開始使用[Sdk](documentdb-sdk-dotnet.md)或[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx)撰寫程式碼
-   瞭解如何[在 DocumentDB 能夠處理量](documentdb-performance-levels.md)
-   如果您想要自訂您的應用程式執行分割的方式，您可以插入您自己的用戶端分割實作。 請參閱[用戶端分割支援](documentdb-sharding.md)。

[1]: ./media/documentdb-partition-data/partitioning.png
[2]: ./media/documentdb-partition-data/single-and-partitioned.png
[3]: ./media/documentdb-partition-data/documentdb-migration-partitioned-collection.png  

 
