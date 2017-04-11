<properties 
    pageTitle="休士頓中使用資料 Azure DocumentDB |Microsoft Azure" 
    description="瞭解如何建立、 編製索引及查詢的 Azure DocumentDB 空間的物件。" 
    services="documentdb" 
    documentationCenter="" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="10/25/2016" 
    ms.author="arramac"/>
    
# <a name="working-with-geospatial-data-in-azure-documentdb"></a>使用休士頓 Azure DocumentDB 中的資料

本文是[Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)休士頓功能簡介。 讀取之後此，您可以回答下列問題︰

- 如何在 Azure DocumentDB 儲存空間資料？
- 如何可以查詢 SQL 和 LINQ Azure DocumentDB 休士頓資料？
- 如何啟用或停用空間索引中 DocumentDB？

請參閱此[Github 專案](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs)的程式碼範例。

## <a name="introduction-to-spatial-data"></a>空間資料簡介

空間資料說明的位置和圖案的空間中的物件。 在大多數的應用程式，這些會對應到即休士頓資料地球上的物件。 空間資料可用來代表人員、 感興趣的位置或的城市或湖邊界的位置。 常見的使用案例通常涉及鄰近範圍查詢，例如，「 尋找所有咖啡廳附近我目前的位置]。 

### <a name="geojson"></a>GeoJSON
DocumentDB 支援編製索引和查詢的使用[GeoJSON 規格](http://geojson.org/geojson-spec.html)表示休士頓點資料。 GeoJSON 資料結構一律是有效的 JSON 物件，讓他們可以儲存與不含任何特殊的工具] 或 [文件庫中使用 DocumentDB 查詢。 DocumentDB Sdk 提供協助類別和輕鬆地使用空間資料的方法。 

### <a name="points-linestrings-and-polygons"></a>點、 linestrings 和多邊形
**點**表示空間中的單一位置。 休士頓資料點代表確切的位置，這可能是雜貨店、 在資訊站、 汽車或城市的街道地址。  點會以 GeoJSON （和 DocumentDB） 使用其座標組或經緯度表示。 以下是範例 JSON 點。

**DocumentDB 的資料點**

    {
       "type":"Point",
       "coordinates":[ 31.9, -4.8 ]
    }

>[AZURE.NOTE] GeoJSON 規格指定經度第一個和緯度第二個。 例如在其他應用程式的對應，經緯度角度而以角度表示。 經緯度值子午線作為起點和之間-180 180.0 度和緯度值赤道作為起點和之間-90.0 和 90.0 度。 
>
> DocumentDB 解譯座標，表示每 WGS 84 參照系統。 請參閱下方的更多詳細資料座標參考系統。

這可以內嵌在 DocumentDB 文件在此範例中的使用者設定檔，其中包含位置資料所示︰

**使用儲存在 DocumentDB 位置的設定檔**

    {
       "id":"documentdb-profile",
       "screen_name":"@DocumentDB",
       "city":"Redmond",
       "topics":[ "NoSQL", "Javascript" ],
       "location":{
          "type":"Point",
          "coordinates":[ 31.9, -4.8 ]
       }
    }

除了點，GeoJSON 也會支援 LineStrings 以及多邊形。 **LineStrings**代表一系列的空間和直線線段的連接圖形中的兩個或多個資料點。 休士頓資料 linestrings 常用來代表高速公路或河流。 **多邊形**是表單已關閉的 LineString 連接點的邊界。 多邊形通常用來代表等湖自然陣式或政治轄區城市及狀態等。 以下是在 DocumentDB 多邊形的範例。 

**在 [DocumentDB 多邊形**

    {
       "type":"Polygon",
       "coordinates":[
           [ 31.8, -5 ],
           [ 31.8, -4.7 ],
           [ 32, -4.7 ],
           [ 32, -5 ],
           [ 31.8, -5 ]
       ]
    }

>[AZURE.NOTE] GeoJSON 規格要求有效多邊形，提供的最後一個座標對應該是首先，若要建立封閉的圖形相同。
>
>以逆時針方向順序必須指定多邊形內的點。 順時鐘方向順序指定多邊形代表區域內的反函數值。

除了點、 LineString 以及多邊形，GeoJSON 也表示指定為群組的多個休士頓位置，以及如何關聯做為**功能**的地理位置的任意的屬性。 由於這些物件有效 JSON，他們可以所有儲存及處理 DocumentDB。 不過 DocumentDB 只支援自動編製索引作業的點數。

### <a name="coordinate-reference-systems"></a>參照系統進行共同作業

由於不規則地球的圖案，座標休士頓資料都會在許多的座標參考系統 (CRS)，都有自己的圖文框的參考及的度量單位。 例如，「 國際格線的英國 」 是參照系統是非常正確英國，但不是外。 

使用中最常用的 CRS 今天是全球測地學系統[WGS 84](http://earth-info.nga.mil/GandG/wgs84/)。 GPS 裝置和許多對應服務包括 Google 地圖和 Bing 地圖服務 Api 使用 WGS 84。 DocumentDB 支援編製索引和查詢的使用 WGS 84 CRS 只休士頓資料。 

## <a name="creating-documents-with-spatial-data"></a>建立文件空間資料
當您建立文件若包含 GeoJSON 值時，他們會會自動建立索引的空間索引根據集合的編製索引的原則。 如果您使用動態輸入的語言，例如 Python 或 Node.js DocumentDB SDK，您必須建立有效 GeoJSON。

**建立文件中 Node.js 休士頓資料**

    var userProfileDocument = {
       "name":"documentdb",
       "location":{
          "type":"Point",
          "coordinates":[ -122.12, 47.66 ]
       }
    };

    client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
        // additional code within the callback
    });

如果您使用.NET （或 Java） Sdk，您可以使用新的點及多邊形類別 Microsoft.Azure.Documents.Spatial 命名空間中內嵌您的應用程式物件內的位置資訊。 這些類別有助於簡化序列化和還原到 GeoJSON 序列化的空間資料。

**使用.NET 休士頓資料建立文件**

    using Microsoft.Azure.Documents.Spatial;
    
    public class UserProfile
    {
        [JsonProperty("name")]
        public string Name { get; set; }

        [JsonProperty("location")]
        public Point Location { get; set; }
        
        // More properties
    }
    
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri("db", "profiles"), 
        new UserProfile 
        { 
            Name = "documentdb", 
            Location = new Point (-122.12, 47.66) 
        });

如果您沒有緯度和經度資訊，但有實體地址或位置名稱，例如 [城市] 或 [國家/地區，您可以使用的地理位置編碼服務，例如 Bing 地圖服務其餘服務查閱實際座標。 深入瞭解 Bing 地圖服務的地理位置編碼[以下](https://msdn.microsoft.com/library/ff701713.aspx)。

## <a name="querying-spatial-types"></a>查詢 [空間類型

現在我們已經看看如何插入休士頓資料，讓我們來看看如何使用 DocumentDB 使用 SQL 和 LINQ 此資料的查詢。

### <a name="spatial-sql-built-in-functions"></a>空間 SQL 內建函數
DocumentDB 支援下列開啟休士頓協會 (OGC) 內建函數休士頓查詢。 如需一組完整的 SQL 語言的內建函數的詳細資訊，請參閱[查詢 DocumentDB](documentdb-sql-query.md)。

<table>
<tr>
  <td><strong>使用方式</strong></td>
  <td><strong>描述</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE （point_expr、 point_expr）</td>
  <td>傳回之間的距離兩個 GeoJSON 點運算式。</td>
</tr>
<tr>
  <td>ST_WITHIN （point_expr、 polygon_expr）</td>
  <td>會傳回指示中的第一個引數指定 GeoJSON 點是否在第二個引數中 GeoJSON 多邊形的布林運算式。</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>會傳回一個 Boolean 值，表示指定的 GeoJSON 點或多邊形運算式是否有效。</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>會傳回 JSON 值，其中包含一個 Boolean 值，如果指定的 GeoJSON 點或多邊形運算式是否有效，而且不正確，此外字串值的原因。</td>
</tr>
</table>

空間函數可用於執行鄰近範圍空間資料查詢。 例如，以下是位置的查詢會傳回所指定使用 ST_DISTANCE 內建函數 30 金鑰管理中的所有家庭文件。 

**查詢**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**結果**

    [{
      "id": "WakefieldFamily"
    }]

如果您加入空間索引編製索引原則中，然後 「 距離查詢 」 將提供有效率地透過索引。 如需空間編製索引作業的詳細資訊，請參閱下方的一節。 如果您沒有安裝空間索引針對指定的路徑，您仍然可以執行空間查詢指定`x-ms-documentdb-query-enable-scan`要求標題為 「 true 」 設定的值。 在.NET 中，進行選擇性**FeedOptions**引數傳遞[EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery)設定為 true 的查詢。 

若要檢查是否點所在多邊形內可 ST_WITHIN。 經常多邊形用來代表界限郵遞區號、 狀態界限或自然陣式等。 一次如果包含空間索引編製功能在您編製索引的原則，然後 「 中 」 查詢將提供有效率地透過索引。 

ST_WITHIN 多邊形引數可以包含單一撥打給，亦即多邊形不能包含在這些文章。 

**查詢**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**結果**

    [{
      "id": "WakefieldFamily",
    }]
    
>[AZURE.NOTE] 類似如何不相符類型運作在 DocumentDB 查詢中，如果在 [引數不正確或不正確，然後它會評估**未定義**和評估文件的查詢結果會略過指定的 [位置] 的值。 如果您的查詢會不傳回任何結果，執行 ST_ISVALIDDETAILED 至偵錯 spatail 類型不正確的原因。     

DocumentDB 也支援執行反查詢，也就是您可以在 [多邊形或行 DocumentDB，索引，然後查詢包含指定的點的區域。 邏輯經常使用這個模式來識別例如當車進入或離開的指定的區域。 

**查詢**

    SELECT * 
    FROM Areas a 
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)


**結果**

    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon", 
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]

ST_ISVALID 和 ST_ISVALIDDETAILED 可用來檢查是否有效空間的物件。 例如，下列查詢會檢查不足的範圍緯度值 (-132.8) 點的有效性。 ST_ISVALID 傳回只是一個 Boolean 值，而 ST_ISVALIDDETAILED 傳回布林及字串，包含為何被視為不正確的原因。

**查詢**

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**結果**

    [{
      "$1": false
    }]

這些函數也會用於驗證多邊形。 例如，以下我們使用 ST_ISVALIDDETAILED 驗證不關閉多邊形。 

**查詢**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**結果**

    [{
       "$1": { 
          "valid": false, 
          "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points." 
        }
    }]
    
### <a name="linq-querying-in-the-net-sdk"></a>LINQ.NET sdk 查詢

DocumentDB.NET SDK 也提供者虛設常式方法`Distance()`和`Within()`LINQ 運算式中使用。 DocumentDB LINQ 提供者翻譯相當於 SQL 內建函數呼叫這些方法呼叫 (ST_DISTANCE 和 ST_WITHIN 分別)。 

以下是 LINQ 查詢，以尋找 「 位置 」 值為 30 金鑰管理的指定半徑內指向使用 LINQ DocumentDB 集合中的所有文件的範例。

**LINQ 查詢的距離**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(u => u.ProfileType == "Public" && a.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

同樣地，以下是查詢尋找 「 位置 」 是指定的方塊/多邊形內的所有文件。 

**LINQ 查詢中**

    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }


現在我們已經看看如何使用 LINQ 和 SQL 的文件的查詢，讓我們來看看如何設定 DocumentDB 空間編製索引作業。

## <a name="indexing"></a>編製索引

如[架構診斷編製索引作業 Azure DocumentDB 與](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)本文所述，我們設計 DocumentDB 的資料庫引擎是真正架構診斷並提供第一本課程支援 JSON。 最佳化寫入資料庫引擎 DocumentDB 的原生瞭解空間資料 （點與多邊形線） 表示 GeoJSON 標準。

簡而言之，幾何有投射從測地學 2D 平面的座標，然後逐漸分成使用**quadtree**的儲存格。 這些儲存格對應到 1d 根據**Hilbert 空間填滿曲線**，都能保留位置的點內的儲存格的位置。 此外位置資料索引時，它會稱為**鑲嵌**程序，也就是所有的儲存格相交位置識別並儲存為 DocumentDB 索引鍵。 在查詢時，引數，例如點和多邊形也是 tessellated 擷取相關的儲存格識別碼範圍，，然後用來擷取索引中的資料。

如果您指定包含空間索引編製索引原則 / * （所有路徑），然後找到集合內的所有點都索引為有效的空間查詢 （ST_WITHIN 和 ST_DISTANCE）。 空間索引沒有精確度值，並使用預設的精確度值。

>[AZURE.NOTE] DocumentDB 支援的資料點，LineStrings 多邊形自動編製索引

下列 JSON 程式碼片段顯示已啟用的空間索引編製索引的原則，亦即編製索引的空間查詢找到文件中任何 GeoJSON 點。 如果您要修改的編製索引的原則，使用 [Azure 入口網站，您可以指定下列 JSON 編製索引的原則的空間索引編製功能在您集合上啟用。

**使用空間啟用點和多邊形集合編製索引作業原則 JSON**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Range",
                   "dataType":"String",
                   "precision":-1
                },
                {
                   "kind":"Range",
                   "dataType":"Number",
                   "precision":-1
                },
                {
                   "kind":"Spatial",
                   "dataType":"Point"
                },
                {
                   "kind":"Spatial",
                   "dataType":"Polygon"
                }                
             ]
          }
       ],
       "excludedPaths":[
       ]
    }

以下是程式碼片段.net，顯示如何建立具有開啟包含點的所有路徑的空間索引的集合。 

**建立使用空間編製索引的集合**

    DocumentCollection spatialData = new DocumentCollection()
    spatialData.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point)); //override to turn spatial on by default
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), spatialData);

以下說明如何修改現有的集合，以善用空間編製索引作業儲存在文件內任何點上。

**修改現有的集合使用空間編製索引**

    Console.WriteLine("Updating collection with spatial indexing enabled in indexing policy...");
    collection.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point));
    await client.ReplaceDocumentCollectionAsync(collection);

    Console.WriteLine("Waiting for indexing to complete...");
    long indexTransformationProgress = 0;
    while (indexTransformationProgress < 100)
    {
        ResourceResponse<DocumentCollection> response = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
        indexTransformationProgress = response.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromSeconds(1));
    }

> [AZURE.NOTE] 如果不正確或無效的文件中的 GeoJSON 值的位置，然後它會取得建立索引的空間的查詢。 您可以驗證使用 ST_ISVALID 和 ST_ISVALIDDETAILED 位置值。
>
> 如果您的集合定義包含資料分割索引鍵，不報告轉換進度編製索引作業。 

## <a name="next-steps"></a>後續步驟
現在，您已經學會如何開始使用 DocumentDB 休士頓支援，您可以︰

- 開始撰寫程式碼[上 Github 休士頓.NET 程式碼範例](https://github.com/Azure/azure-documentdb-dotnet/blob/fcf23d134fc5019397dcf7ab97d8d6456cd94820/samples/code-samples/Geospatial/Program.cs)
- 在上取得手休士頓查詢在[DocumentDB 查詢遊樂場](http://www.documentdb.com/sql/demo#geospatial)
- 深入瞭解[DocumentDB 查詢](documentdb-sql-query.md)
- 深入瞭解[DocumentDB 編製索引的原則](documentdb-indexing-policies.md)
