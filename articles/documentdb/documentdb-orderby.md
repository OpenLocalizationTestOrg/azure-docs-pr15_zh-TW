<properties 
    pageTitle="排序順序來使用 DocumentDB 資料 |Microsoft Azure" 
    description="瞭解如何使用順序，請在 LINQ 和 SQL，DocumentDB 查詢中以及如何指定順序來查詢的編製索引原則。" 
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="cgronlun" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="arramac"/>

# <a name="sorting-documentdb-data-using-order-by"></a>排序順序來使用 DocumentDB 資料
Microsoft Azure DocumentDB 支援 JSON 文件透過使用 SQL 查詢的文件。 SQL 查詢陳述式中使用 ORDER BY 子句可以排序查詢結果。

閱讀本文之後，您便可以回答下列問題︰ 

- 如何查詢的方式？
- 如何設定編製索引的原則的方式？
- 什麼即將下一個項目？

也會提供[範例](#samples)和[常見問題集](#faq)。

完成的 SQL 查詢參照，請參閱[DocumentDB 查詢教學課程](documentdb-sql-query.md)。

## <a name="how-to-query-with-order-by"></a>如何排序依據的查詢
例如，您可以在現在包含選擇性 Order By 子句 SQL 陳述式中的查詢 DocumentDB 時。 子句可以包含指定的順序必須擷取結果中的選擇性遞增/遞減引數。 

### <a name="ordering-using-sql"></a>使用 SQL 的順序
例如，以下是查詢擷取前 10 活頁簿以遞減順序的標題。 

    SELECT TOP 10 * 
    FROM Books 
    ORDER BY Books.Title DESC

### <a name="ordering-using-sql-with-filtering"></a>排序與篩選使用 SQL
您可以使用任何等 Books.ShippingDetails.Weight，文件中的巢狀的屬性來排序，您可以指定其他篩選器的順序來搭配 WHERE 子句像在此範例中︰

    SELECT * 
    FROM Books 
    WHERE Books.SalePrice > 4000
    ORDER BY Books.ShippingDetails.Weight

### <a name="ordering-using-the-linq-provider-for-net"></a>使用.NET LINQ 提供者的順序
使用.NET SDK 版本 1.2.0，您也可以在此範例使用 OrderBy() 或 OrderByDescending() 子句，例如 LINQ 查詢中的更高，︰

    foreach (Book book in client.CreateDocumentQuery<Book>(UriFactory.CreateDocumentCollectionUri("db", "books"))
        .OrderBy(b => b.PublishTimestamp)
        .Take(100))
    {
        // Iterate through books
    }

DocumentDB 支援即將推出的其他查詢類型的順序與單一數字、 字串或每個查詢，則為 True 的屬性。 請參閱[什麼接下來的](#Whats_coming_next)如需詳細資訊。

## <a name="configure-an-indexing-policy-for-order-by"></a>設定編製索引的原則的方式

回收 DocumentDB 支援兩種類型的索引 （雜湊和範圍），可以設定的特定路徑/屬性，資料類型 （字串/數字），然後在不同的精確度值 （最大的精確度或固定的精確度值）。 因為 DocumentDB 使用雜湊編製索引作業成預設值，您必須建立自訂數字、 字串或兩個範圍編製索引原則新集合才能使用方式。 

>[AZURE.NOTE] 在 2015 年 7 月 7 日到以 REST API 版本 2015年-06-03 上帶來字串範圍索引。 順序來建立原則，針對字串，您必須使用 SDK 1.2.0.NET sdk，您可以或 Python、 Node.js 或 Java SDK 1.1.0 版的版本。
>
>之前 REST API 版本 2015年-06-03，預設集合編製索引原則會被雜湊字串] 及 [數字。 此已變更為雜湊的字串與範圍的數字。 

如需詳細資訊，請參閱[DocumentDB 編製索引的原則](documentdb-indexing-policies.md)。

### <a name="indexing-for-order-by-against-all-properties"></a>編製索引的順序來針對所有屬性
以下方式可以建立集合與 「 所有範圍 」 編製都索引的順序來針對任何/所有數字或字串內的 JSON 文件中顯示的內容。 以下我們會覆寫字串值為範圍]，並在 [最大整數位數 (-1) 的預設索引類型。
                   
    DocumentCollection books = new DocumentCollection();
    books.Id = "books";
    books.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    
    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), books);  

>[AZURE.NOTE] 請注意，順序，只會傳回的資料類型 （字串和數字） 會使用 RangeIndex 索引的結果。 例如，如果您有預設的原則只在數字有 RangeIndex 編製索引作業，Order By 針對字串值路徑會傳回沒有任何文件。

### <a name="indexing-for-order-by-for-a-single-property"></a>編製索引的方式的單一屬性
以下是您如何建立與編製索引的順序來只會將標題屬性，這是字串針對集合。 有兩個路徑，其中一個標題屬性 （「 / 標題 /？ 」） 與範圍編製索引，為具有預設編製索引的配置，即雜湊的字串與範圍的數字的每個其他屬性。                    
    
    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 } } 
            });
    
    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), booksCollection);  


## <a name="samples"></a>範例
查看示範如何使用順序，包括建立的原則編製索引和分頁順序來使用此[Github 範例專案](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries)。 範例是開啟的來源，我們建議您提交提取要求與可以利用其他 DocumentDB 開發人員的比重。 請參閱如何提供的指導方針的[比重指導方針](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md)。  

## <a name="faq"></a>常見問題集

**什麼是預期要求單位 (RU) 耗用的順序來查詢？**

Order By 利用查閱 DocumentDB 索引，因為的順序來查詢所使用的要求單位數會類似 Order By 不相等的查詢。 在 DocumentDB 上任何其他作業，例如要求單位數量而定大小/圖形的文件，以及複雜的查詢。 


**什麼是預期編製索引作業負荷的方式？**

編製索引的儲存空間成本會相稱數個屬性。 表現最差案例情節，索引開銷會變成 100%的資料。 有處理量 （要求的單位） 開銷範圍/Order By 編製索引和預設雜湊編製索引之間沒有差別。

**如何查詢中使用順序 DocumentDB 我現有的資料？**

才能排序順序所使用的查詢結果，您必須修改編製索引的原則的使用範圍索引類型針對用來排序的屬性的集合。 請參閱[修改原則編製索引作業](documentdb-indexing-policies.md#modifying-the-indexing-policy-of-a-collection)。 

**目前的順序來限制是什麼？**

順序，您可以指定只針對屬性時，[數字或字串時，它範圍索引以最大的精確度 (-1)。

您無法執行下列動作︰
 
- Order By 內部字串屬性，例如識別碼]、 [_rid 及 [_self （即將推出）。
- Order By 屬性衍生自 （即將推出） 的文件內部聯結的結果。
- 依多個屬性 （即將推出）。
- Order By 資料庫、 集合、 使用者、 權限或附件 （即將推出） 的查詢。
- 依計算屬性與例如運算式或 UDF/built 的函數的結果。

Order By 目前不支援跨分割查詢時 Azure 入口網站中使用查詢檔案總管。

## <a name="troubleshooting"></a>疑難排解

如果您收到錯誤的方式不受支援，則會檢查並確定您使用的支援 Order By [SDK](documentdb-sdk-dotnet.md)的版本。 

## <a name="next-steps"></a>後續步驟

分叉[Github 範例專案](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries)，然後開始使用排序資料 ！ 

## <a name="references"></a>參照
* [DocumentDB 查詢參照](documentdb-sql-query.md)
* [DocumentDB 編製索引作業原則參照](documentdb-indexing-policies.md)
* [DocumentDB SQL 參考](https://msdn.microsoft.com/library/azure/dn782250.aspx)
* [範例 DocumentDB 排序](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries)
 

