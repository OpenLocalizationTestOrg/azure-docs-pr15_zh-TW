<properties
    pageTitle="JSON 輸出資料流分析 |Microsoft Azure"
    description="瞭解如何串流分析可以針對 Azure DocumentDB JSON 輸出] 清單中的資料封存與非結構化 JSON 資料低延遲查詢。"
    keywords="JSON 輸出"
    documentationCenter=""
    services="stream-analytics,documentdb"
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

# <a name="target-azure-documentdb-for-json-output-from-stream-analytics"></a>從資料流分析 JSON 輸出的目標 Azure DocumentDB

資料流分析可以啟用非結構化 JSON 資料的資料封存和低延遲查詢輸出、 JSON 的目標[Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 。 瞭解如何最佳實作這項整合。

對於熟悉 DocumentDB，看看[DocumentDB 的學習路徑](https://azure.microsoft.com/documentation/learning-paths/documentdb/)，即可開始使用。

## <a name="basics-of-documentdb-as-an-output-target"></a>為輸出目標 DocumentDB 的基本概念
Azure DocumentDB 中的輸出資料流分析可讓您的資料流處理作為 JSON 輸出的結果，將您 DocumentDB 某個或撰寫。 資料流分析不會建立集合資料庫，請改為需要事前建立。 這是與讓帳單 DocumentDB 集合的成本透明的好讓您可以調整的效能、 一致性和直接使用[DocumentDB Api](https://msdn.microsoft.com/library/azure/dn781481.aspx)集合的容量。 我們建議使用一個 DocumentDB 資料庫，每個串流作業邏輯分隔串流工作的集合。

部分 DocumentDB 集合選項的詳細資訊] 下方。

## <a name="tune-consistency-availability-and-latency"></a>調整的一致性、 可用性和延遲

若要符合您的應用程式的需求，DocumentDB 可讓您要調整的資料庫及集合，然後進行折衷方案之間的一致性、 可用性和延遲。 哪些層級讀的一致性根據針對您案例的需求讀取和寫入延遲，您可以選擇資料庫帳戶上的一致性層級。 根據預設，DocumentDB 還有同步至您的集合每個 CRUD 作業編製索引。 這是另一個有用的選項，以控制 DocumentDB 寫入/已讀取的效能。 如需進一步瞭解此主題，檢閱[變更資料庫及查詢的一致性等級](../documentdb/documentdb-consistency-levels.md)文章。

## <a name="choose-a-performance-level"></a>選擇 [效能層級

可以在 3 個不同的效能層級 （S1、 S2 或 S3） 決定可用的處理能力 CRUDs 到該集合建立 DocumentDB 集合。 此外，以在您集合上的一致性編製索引作業層級會受到影響效能。 請參閱[這篇文章](../documentdb/documentdb-performance-levels.md)瞭解這些的效能層級的詳細資料。

## <a name="upserts-from-stream-analytics"></a>從資料流分析 Upserts

資料流分析整合 DocumentDB 可讓您插入或更新記錄，將您根據指定的 [文件識別碼] 資料行的 DocumentDB 集合。 這也稱為**。

資料流分析利用位置更新只完成插入失敗，因為衝突的文件識別碼時樂觀工期了方法。 此更新做為執行串流分析來更新程式，讓它可以讓文件，也就是加入的新內容] 或 [取代現有的屬性逐步執行的部分更新。 請注意，在 JSON 文件結果整個陣列中快速覆寫，也就是陣列中的陣列屬性的值變更為不合併。

## <a name="data-partitioning-in-documentdb"></a>在 DocumentDB 分割的資料

DocumentDB 集合允許您分割資料的查詢模式和應用程式的效能需求。 每個集合可能包含 10 GB 的資料 （最大值） 和目前有任何不按比例縮放 （或浮動） 集合。 延展，串流分析可讓您指定的前置詞與多個集合撰寫 （請參閱下方的使用狀況詳細資料）。 資料流分析會使用一致的[雜湊磁碟分割解析程式](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx)策略根據使用者提供 PartitionKey 欄分割它輸出的記錄。 以指定的前置詞串流工作的開始時間的集合數會用來作為輸出資料分割計數的工作撰寫平行 (DocumentDB 集合 = 輸出磁碟分割區)。 麻煩編製索引的實際操作的單一 S3 集合只會插入，瞭解 0.4 可以預期 MB/s 寫入處理能力。 使用多個集合，可讓您獲得更高的輸出量和提高的生產力。

如果您想要在未來增加的磁碟分割計算，您可能需要停止您的工作，重新分割成新集合現有的集合中的資料，然後重新啟動串流分析工作。 待處理的文章中，將會包含使用 PartitionResolver 及重新分割範例碼的更多詳細資料。 [分割 DocumentDB 中](../articles/documentdb-partition-data.md#developing-a-partitioned-application)的文章︰ 在此也提供詳細資料。

## <a name="documentdb-settings-for-json-output"></a>JSON 輸出 DocumentDB 設定

建立 DocumentDB 作為輸出資料流分析中會產生的資訊如下所示的提示。 本節提供的屬性的定義的說明。

![documentdb 串流分析輸出畫面](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output.png)  

-   **輸出別名**– 別名參照 ASA 查詢輸出  
-   **帳戶名稱**– 端點 URI DocumentDB 帳戶的名稱。  
-   **[帳戶金鑰]** – DocumentDB 帳戶的共用的便捷鍵。  
-   **資料庫**– DocumentDB 資料庫名稱。  
-   **集合名稱模式**– 使用集合的集合名稱模式。 您可使用選擇性 {分割} 權杖，從 0 的磁碟分割區位置開始建構集合姓名格式。 以下是範例有效的輸入︰  
   1\) MyCollection – 必須有一個名為 「 MyCollection 」 的集合。  
   2\) MyCollection {分割}-這類集合必須存在於 – 」 MyCollection0 「，」 MyCollection1 」、 「 MyCollection2 」 等。  
-   **分割索引鍵**– 中輸出事件用來指定分割集合輸出的索引鍵欄位的名稱。 單一集合輸出的任何任意輸出資料行可使用例如 PartitionId。  
-   **文件識別碼**– 選用。 在 [輸出事件用來指定哪些插入或更新作業所依據的主索引鍵的欄位名稱。  
