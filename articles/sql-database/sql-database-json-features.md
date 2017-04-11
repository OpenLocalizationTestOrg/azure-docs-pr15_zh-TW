<properties
    pageTitle="Azure SQL 資料庫 JSON 功能 |Microsoft Azure"
    description="Azure SQL 資料庫可讓您以分析、 查詢和 JavaScript 物件標記法 (JSON) 標記法中資料的格式。"
    services="sql-database"
    documentationCenter=""
    authors="jovanpop-msft"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="08/17/2016"
    ms.author="jovanpop"
   ms.workload="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>



# <a name="getting-started-with-json-features-in-azure-sql-database"></a>開始使用 JSON 功能 Azure SQL 資料庫

Azure SQL 資料庫可讓您剖析和查詢 JavaScript Object Notation [(JSON)](http://www.json.org/)格式中表示的資料，並將關聯式資料匯出為 JSON 文字。

JSON 是用於交換現代化的網頁和行動應用程式中的資料的常用的資料格式。 JSON 也可用於儲存記錄檔或像[Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)NoSQL 資料庫中的半結構化的資料。 許多 REST web 服務傳回結果格式化為 JSON 文字或接受資料格式化為 JSON。 最 Azure 服務，例如[Azure 搜尋](https://azure.microsoft.com/services/search/)、 [Azure 儲存體](https://azure.microsoft.com/services/storage/)] 和 [ [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)有傳回或使用 JSON 其餘結束點。

Azure SQL 資料庫可讓您輕鬆地使用 JSON 資料，並使用新的服務整合您的資料庫。

## <a name="overview"></a>概觀

Azure SQL 資料庫使用 JSON 資料提供下列功能︰

![JSON 函數](./media/sql-database-json-features/image_1.png)

如果您有 JSON 文字時，您可以從 JSON 擷取資料，或驗證 JSON 使用內建函數[JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx) [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx)，與[ISJSON](https://msdn.microsoft.com/library/dn921896.aspx)正確格式。 [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx)函數可讓您更新 JSON 文字內的值。 更多進階查詢及分析， [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx)函數可以轉換 JSON 物件的陣列，將一組資料列。 傳回的結果集都能執行任何 SQL 查詢。 最後，還有一個[FOR JSON](https://msdn.microsoft.com/library/dn921882.aspx)子句，可讓您以 JSON 文字儲存在關聯式表格中的資料格式化。

## <a name="formatting-relational-data-in-json-format"></a>設定格式化的關聯式資料 JSON 格式
如果您有 web 服務的圖層資料庫中的資料，並提供在 JSON 回應的時間設定格式，或用戶端 JavaScript 架構或文件庫，接受資料格式設為 JSON，則您可以直接在 SQL 查詢中來格式化 json 資料庫內容。 您不再需要撰寫格式從 Azure SQL 資料庫 json，結果的應用程式碼，或包含轉換表格的查詢結果，然後將序列化物件 JSON 格式一些 JSON 序列化文件庫。 不過，您可以使用 FOR JSON 子句 SQL 查詢結果的格式設定為 JSON Azure SQL 資料庫中，並直接在您的應用程式中使用。

在下列範例中，Sales.Customer 資料表資料列格式設定為 JSON 使用 FOR JSON 子句︰

```
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

針對 JSON 路徑子句為 JSON 文字格式查詢的結果。 資料行名稱作為索引鍵，而儲存格的值會產生 JSON 值為︰

```
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

結果會格式化為 JSON 陣列，其中每個資料列會格式化為個別 JSON 物件。

路徑會指出您可以使用點標記法欄別名中自訂 JSON 結果的輸出格式。 下列查詢輸出 JSON 格式時，「 客戶名稱 」 索引鍵的名稱變更，並將 「 連絡人 」 的子物件中的電話和傳真號碼︰

```
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

此查詢的輸出看起來像這樣︰

```
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

在此範例中，我們會傳回單一 JSON 物件，而不是陣列指定[WITHOUT_ARRAY_WRAPPER](https://msdn.microsoft.com/library/mt631354.aspx)選項。 如果您知道您傳回當做查詢的單一物件，您可以使用這個選項。

FOR JSON 子句的主值就是它可以讓您從資料庫將格式化為巢狀的 JSON 物件或陣列傳回複雜階層式資料。 下列範例會示範如何包含訂單屬於客戶的訂單以巢狀陣列︰

```
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER

```

而非傳送分隔查詢，以取得客戶資料，然後以擷取相關的訂單的清單，您可以取得使用單一查詢，所有必要的資料，輸出下列範例所示︰

```
{
  "Name":"Nada Jovanovic",
  "Phone":"(215) 555-0100",
  "Fax":"(215) 555-0101",
  "Orders":[
    {"OrderID":382,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":395,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":1657,"OrderDate":"2013-01-31","ExpectedDeliveryDate":"2013-02-01"}
]
}
```

## <a name="working-with-json-data"></a>使用 JSON 資料

如果您沒有嚴格結構化的資料，如果您有複雜的子物件、 陣列或階層式資料，或您的資料結構會隨著時間，JSON 格式可協助您用來代表任何複雜的資料結構。

JSON 是可像其他任何字串類型 Azure SQL 資料庫中的文字格式。 您可以傳送或儲存 JSON 資料為標準的 NVARCHAR:

```
CREATE TABLE Products (
  Id int identity primary key,
  Title nvarchar(200),
  Data nvarchar(max)
)
go
CREATE PROCEDURE InsertProduct(@title nvarchar(200), @json nvarchar(max))
AS BEGIN
    insert into Products(Title, Data)
    values(@title, @json)
END
```

此範例中使用 JSON 資料會以使用 nvarchar （max） 類型。 JSON 可以插入此表格或依現狀使用標準的 SQL 語法，如下列範例所示的預存程序的引數︰

```
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

任何用戶端語言或文件庫，可以使用 Azure SQL 資料庫中的字串資料也會使用 JSON 資料。 JSON 可以儲存在支援 NVARCHAR 類型，例如記憶體最佳化表格或系統建立版本的表格的任何資料表中。 JSON 不會產生任何限制式，在用戶端程式碼或資料庫的圖層。

## <a name="querying-json-data"></a>查詢 JSON 資料

如果您有格式化為 JSON 儲存在 SQL Azure 資料表中的資料，JSON 函數可讓您在任何 SQL 查詢中使用此資料。

JSON 函數所提供的 Azure SQL 資料庫可讓您將資料格式化為 JSON 為任何其他 SQL 資料類型。 您可以輕鬆從 JSON 文字，擷取的值，而且任何查詢中使用 JSON 資料︰

```
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

JSON_VALUE 函數起 JSON 文字資料行中儲存的值。 此函數會使用 JavaScript 類似的路徑，參照擷取 JSON 文字中的值。 在任何部分中的 SQL 查詢可所擷取的值。

JSON_QUERY 函數很類似 JSON_VALUE。 不同於 JSON_VALUE，此函數會擷取複雜的子物件，例如陣列或會放在 JSON 文字的物件。

JSON_MODIFY 函數可讓您指定的值路徑中應該會更新，JSON 文字，將會覆寫舊的新值。 如此一來您可以輕鬆更新 JSON 文字不 reparsing 整個結構。

由於 JSON 儲存在標準的文字，並沒有儲存文字資料行中值的正確格式任何保證。 您可以驗證儲存 JSON 欄中的文字具有正確的格式使用標準 Azure SQL 資料庫核取限制和 ISJSON 函數︰

```
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

如果輸入的文字格式正確 JSON，ISJSON 函數會傳回數值 1。 在每個插入或 JSON 資料行的更新，這項限制式將新的文字值不正確的 JSON 來確認。

## <a name="transforming-json-into-tabular-format"></a>將 JSON 轉換成表格的格式

Azure SQL 資料庫也可讓您轉換成表格的格式並載入或查詢 JSON 資料 JSON 集合。

OPENJSON 是陣列的陣列的剖析 JSON 文字、 找出第三四 JSON 物件的陣列，逐一查看項目的，並傳回一列輸出結果的每個項目中的資料表值函式。

![JSON 表格式](./media/sql-database-json-features/image_2.png)

在上述範例中，我們可以指定要找出 JSON 陣列應該開啟的 （$。訂單路徑） 的資料行應傳回結果，以及將會傳回儲存格 JSON 值的位置。

我們可以轉換為 JSON 陣列中的@orders變數將一組資料列，分析此結果集，或插入標準資料表中的資料列︰

```
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )

END
```
訂單的集合格式化為 JSON 陣列，並提供為可以剖析和 [訂單] 資料表中插入預存程序參數。

## <a name="next-steps"></a>後續步驟

若要瞭解如何將 JSON 整合到您的應用程式，請參閱下列資源︰

- [TechNet 部落格](https://blogs.technet.microsoft.com/dataplatforminsider/2016/01/05/json-in-sql-server-2016-part-1-of-4/)
- [MSDN 文件](https://msdn.microsoft.com/library/dn921897.aspx)
- [頻道 9 視訊](https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-JSON-Support)

若要瞭解將 JSON 整合到您的應用程式的各種情況，請參閱在此[視訊頻道 9](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds)中的示範或尋找符合您的使用案例中[JSON 部落格文章](http://blogs.msdn.com/b/sqlserverstorageengine/archive/tags/json/)的情況。
