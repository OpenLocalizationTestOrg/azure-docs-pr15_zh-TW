<properties 
    pageTitle="要求 DocumentDB 單位 |Microsoft Azure" 
    description="學習如何瞭解、 指定，以及估計 DocumentDB 要求單位需求。" 
    services="documentdb" 
    authors="syamkmsft" 
    manager="jhubbard" 
    editor="mimig" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/29/2016" 
    ms.author="syamk"/>

#<a name="request-units-in-documentdb"></a>要求 DocumentDB 單位
現在提供︰ DocumentDB[要求單位計算機](https://www.documentdb.com/capacityplanner)。 如需[Estimating 您處理量需要](documentdb-request-units.md#estimating-throughput-needs)深入瞭解。

![處理量計算機][5]

##<a name="introduction"></a>簡介
本文提供[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)要求單位的概觀。 

閱讀本文之後，您便可以回答下列問題︰  

-   什麼是要求單位，並要求費用嗎？
-   我要如何指定要求單位容量集合？
-   如何估計我的應用程式要求單位需求？
-   如果超過要求單位容量集合，會發生什麼情況？


##<a name="request-units-and-request-charges"></a>要求單位，並要求費用
DocumentDB 遞送*預約*資源以符合您的應用程式處理量需要快速，如預期呈現效能。  載入應用程式，並存取模式變更一段時間，因為 DocumentDB 可讓您輕鬆地增加或減少的應用程式可使用的保留處理量。

使用 DocumentDB，保留處理量指定秒處理要求單位。  您可以將要求單位為處理量貨幣，也就是容量的 [*保留*保證要求單位秒為基礎的應用程式可使用。  CPU、 記憶體和 IOPS，則會耗費 DocumentDB-撰寫文件、 執行查詢，更新文件-在每一項作業。  也就是每一項作業進行時*邀請收費*，這以*要求單位*表示。  了解因素的影響要求單位費用，以及您的應用程式處理量需求，可讓您以成本儘可能有效率地執行您的應用程式。 

##<a name="specifying-request-unit-capacity"></a>指定要求單位容量
建立 DocumentDB 集合時，您可以指定每秒 (RUs) 您想要保留集合的要求單位數量。  集合建立後，RUs 指定完整配置會保留集合的使用中。  每個集合保證專用及隔離處理量特性。  

請務必注意 DocumentDB 運作上的預約功能模型。即是向您收費處理量*保留*集合，無論有多少的該處理量積極*使用*的時數。  請記住，不過，為您的應用程式載入、 資料及使用方式圖樣變更，您可以輕鬆地不按比例縮放的上下保留 RUs DocumentDB Sdk，或是使用[Azure 入口網站](https://portal.azure.com)。  如需哪上下比例處理量的詳細資訊，請參閱[DocumentDB 效能層級](documentdb-performance-levels.md)。

##<a name="request-unit-considerations"></a>要求單位考量
估計時保留 DocumentDB 集合要求單位數量，請務必考慮下列變數︰

- **文件大小**。 文件大小會增加讀取或寫入也會增加資料使用的單位。
- **文件內容計數**。 假設預設編製索引的所有內容，若要撰寫文件中使用的單位會增加屬性的計數增加。
- **資料的一致性**。 時使用資料的強式或限制過時的一致性層級，將會用其他單位，閱讀文件。
- **索引屬性**。 每個集合上的索引原則會決定哪些內容已編製索引預設。 藉由限制索引屬性，或者啟用麻煩編製索引作業，您可以縮減您要求單位消耗。
- **文件編製索引作業**。 根據預設，每份文件會自動建立索引，您會使用較少的要求單位，如果您選擇不編製索引的文件部分。
- **查詢模式**。 複雜的查詢會影響作業使用要求單位數量。 述詞數目、 性質述詞、 計帳、 Udf，數及所有來源資料集的大小會影響查詢作業的成本。
- **指令碼的使用方式**。  查詢，使用預存程序與觸發程序取用要求單位根據所執行的作業的複雜性。 當您開發應用程式，檢查 [若要進一步瞭解如何每一項作業分成要求單位容量要求收費標頭]。

##<a name="estimating-throughput-needs"></a>估計處理量需求
要求單位是要求的標準化處理成本。 單一要求單位表示 （透過自我連結或識別碼） 讀取單一 1 KB JSON 文件包含 10 個唯一的屬性值 （不包括 [系統內容]） 的所需的處理容量。 建立 （插入）、 取代或刪除同一份文件的要求將會耗費更多處理從服務，藉此邀請單位。   

> [AZURE.NOTE] 以自動連結] 或 [文件識別碼簡單取得與相對應的比較基準 1 要求單位 1 KB 文件。

###<a name="use-the-request-unit-calculator"></a>使用要求單位計算機
若要協助客戶關係微調其處理量數量的估計值，有是網頁式[要求單位計算機](https://www.documentdb.com/capacityplanner)協助估計一般的操作，包括要求單位需求︰

- 文件建立 （寫入）
- 讀取文件
- 刪除文件
- 更新文件

工具也包含支援評估根據您所提供的文件範例的資料儲存需求。

使用工具十分簡單︰

1. 上傳一或多個代表 JSON 文件。

    ![上傳文件的邀請單位計算機][2]

2. 若要估計資料儲存需求，輸入您要儲存的文件的總數。

3. 輸入文件數建立、 讀取、 更新及刪除的作業，您需要 （基礎秒）。 若要估計的文件更新作業的要求單位費用上, 傳上述步驟 1，其中包含一般功能變數更新的範例份文件的複本。  比方說，如果文件更新通常是修改 「 lastLogin userVisits 的兩個屬性，然後只要複製樣本文件，更新這兩個的屬性的值並上傳複製文件。

    ![在 [要求單位計算機中輸入處理量需求][3]

4. 按一下計算，並檢查結果。

    ![要求單位計算機的結果][4]

>[AZURE.NOTE]如果您有大幅會大小和索引屬性的數目不同的文件類型，然後上傳至工具的一般的文件的每一種*類型*的範例，然後計算出的結果。

###<a name="use-the-documentdb-request-charge-response-header"></a>使用 DocumentDB 要求收費回應標頭
每個回應 DocumentDB 服務包含自訂頁首 （x-ms-要求-收費），包含要求單位耗用的要求。 這個標題是也可透過 DocumentDB Sdk 存取。 .NET sdk，您可以在 RequestCharge 會是 ResourceResponse 物件的屬性。  查詢 Azure 入口網站中的 [DocumentDB 查詢總管會提供要求收費資訊執行的查詢。

![檢查查詢檔案總管中的 RU 費用][1]

此記住、 評估保留您的應用程式所需的處理量的錄製與針對代表應用程式使用文件中執行一般的作業相關聯的要求單位收費，然後評估運算的數目的其中一個方法在您將會執行每秒。  請務必量值，包含一般查詢及 DocumentDB 指令碼用法。

>[AZURE.NOTE]如果您有大幅會大小和索引屬性的數目不同的文件類型，然後記錄的一般的文件的每個*類型*相關聯的適用作業要求單位收費。

例如︰

1. 錄製 （插入） 所建立的邀請單位收費一般文件。 
2. 記錄讀取一般文件的邀請單位收費。
3. 記錄更新一般文件的邀請單位收費。
3. 記錄的一般的常見文件查詢要求單位收費。
4. 錄製要求單位任何的收費自訂指令碼 （預存程序、 引動程序，使用者定義函數） 利用應用程式
5. 計算指定估計的數字，您將會執行每秒的作業的必要的要求單位。

##<a name="a-request-unit-estimation-example"></a>要求單位評估範例
請考慮下列 ~ 1 篇知識庫文章文件︰

    {
     "id": "08259",
    "description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
    "tags": [
        {
        "name": "cereals ready-to-eat"
        },
        {
        "name": "kellogg"
        },
        {
        "name": "kellogg's crispix"
        }
    ],
    "version": 1,
    "commonName": "Includes USDA Commodity B855",
    "manufacturerName": "Kellogg, Co.",
    "isFromSurvey": false,
    "foodGroup": "Breakfast Cereals",
    "nutrients": [
        {
        "id": "262",
        "description": "Caffeine",
        "nutritionValue": 0,
        "units": "mg"
        },
        {
        "id": "307",
        "description": "Sodium, Na",
        "nutritionValue": 611,
        "units": "mg"
        },
        {
        "id": "309",
        "description": "Zinc, Zn",
        "nutritionValue": 5.2,
        "units": "mg"
        }
    ],
    "servings": [
        {
        "amount": 1,
        "description": "cup (1 NLEA serving)",
        "weightInGrams": 29
        }
    ]
    }

>[AZURE.NOTE]系統會計算上述文件的大小會稍有小於 1 KB，被 minified DocumentDB，在文件。


下表顯示大約要求的一般運算 （大約要求單位收費假設帳戶一致性層級的設定為 「 活動 」 和所有文件會自動編製索引） 這份文件的單位費用︰

作業|要求單位費用 
---|---
建立文件|~ 15 RU 
閱讀文件|~ 1 RU
查詢文件識別碼|~2.5 RU

此外，本表格顯示大約要求單位費用所用的應用程式的一般查詢︰

查詢|要求單位收費|# 傳回的文件
---|---|--- 
選取食物識別碼|~2.5 RU|1 
選取 [食物製造商|~ 7 RU|7
選取食物] 群組中的順序粗細|~ 70 RU|100
選取 [食物] 群組中的 [前 10 個食物|~ 10 RU|10

>[AZURE.NOTE]RU 費用會根據文件傳回數目而定。

此資訊，我們可以估計的作業與我們預期秒的查詢數目此應用程式的 RU 需求︰

作業/查詢|每秒的估計的數目|必要的 RUs 
---|---|--- 
建立文件|10|150 
閱讀文件|100|100 
選取 [食物製造商|25|175 
選取食物] 群組|10|700 
選取 [前 10 個|15|150 總計|155|1275

在此案例中，我們預期平均處理量的需求 1,275 RU/s。  四捨五入進位至最接近的 100，我們會佈建 1300 RU/s 此應用程式的集合。

##<a id="RequestRateTooLarge"></a>超過保留的處理限制
回收要求單位消耗會評估為秒。 對於超過集合能夠的要求單位率的應用程式，直到工資率低於保留層級，將會降低要求該集合。 流速發生時，伺服器將主動結束 RequestRateTooLargeException （HTTP 狀態碼 429） 邀請，並傳回指出所需的時間，以毫秒為單位，使用者必須等待正在重新嘗試要求的 x-ms-重試-之後-ms 頁首。

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

如果您使用.NET 用戶端 SDK 和 LINQ 查詢，然後大多數情況下您永遠不必自行處理此例外狀況，目前版本的.NET 用戶端 SDK 隱含攔截此回應時，會採用伺服器指定重試之後標題和重試要求。 除非您的帳戶存取同時以多個用戶端下, 一步重試就會失敗。

如果您有一個以上的用戶端累積起來作業系統上方要求率預設重試行為不可以解決問題，，而且用戶端會以狀態碼 429 DocumentClientException 應用程式。 在這種情況下，您可以考慮處理重試行為與您的應用程式錯誤處理常式或增加保留處理量，集合中的邏輯。

##<a name="next-steps"></a>後續步驟

若要進一步瞭解保留處理量 Azure DocumentDB 資料庫時，請探索這些資源︰
 
- [DocumentDB 價格](https://azure.microsoft.com/pricing/details/documentdb/)
- [管理 DocumentDB 容量](documentdb-manage.md) 
- [模型 DocumentDB 中的資料](documentdb-modeling-data.md)
- [DocumentDB 效能層級](documentdb-partition-data.md)

若要進一步瞭解 DocumentDB 」 的資訊，請參閱 Azure DocumentDB[文件](https://azure.microsoft.com/documentation/services/documentdb/)。 

若要開始使用小數位數和效能與 DocumentDB 測試，請參閱[效能和 Azure DocumentDB 測試小數位數](documentdb-performance-testing.md)。


[1]: ./media/documentdb-request-units/queryexplorer.png 
[2]: ./media/documentdb-request-units/RUEstimatorUpload.png
[3]: ./media/documentdb-request-units/RUEstimatorDocuments.png
[4]: ./media/documentdb-request-units/RUEstimatorResults.png
[5]: ./media/documentdb-request-units/RUCalculator2.png
