<properties 
    pageTitle="SQL 語法和 SQL 查詢 DocumentDB |Microsoft Azure" 
    description="進一步瞭解 SQL 語法資料庫概念與 SQL 查詢 for DocumentDB，NoSQL 資料庫。 SQL 可以作為 DocumentDB JSON 查詢語言。" 
    keywords="sql 語法的 sql 查詢、 sql 查詢、 json 查詢語言、 資料庫概念與 sql 查詢，彙總函數"
    services="documentdb" 
    documentationCenter="" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="arramac"/>

# <a name="sql-query-and-sql-syntax-in-documentdb"></a>SQL 查詢和 DocumentDB 中的 SQL 語法
Microsoft Azure DocumentDB 支援 JSON 查詢語言使用 SQL （結構化查詢語言） 的查詢文件。 DocumentDB 是真正的結構描述空閒。 直接在資料庫引擎 JSON 資料模型的承諾，一定會提供自動編製索引的 JSON 文件而不需要明確結構描述或建立次要索引。 

DocumentDB 的設計查詢語言時必須注意的兩個目標︰

-   而非 ¬ 新 JSON 查詢語言，我們要支援 SQL。 SQL 是一種最熟悉且最常用的查詢語言。 DocumentDB SQL 提供豐富的查詢的正式的程式設計模型透過 JSON 文件。
-   為 JSON 文件資料庫能夠執行 JavaScript 直接在資料庫引擎，我們要使用 JavaScript 的程式設計模型為基礎，我們查詢語言。 DocumentDB SQL 被根目錄 JavaScript 的類型系統、 運算式評估，和呼叫的函數。 此中開啟提供自然程式設計的模型關聯式計帳、 階層式導覽 JSON 文件、 自我聯結、 空間的查詢，以及引動完全撰寫 JavaScript] 之間其他功能的使用者定義函數 (Udf)。 

我們相信，這些功能鍵以減少此應用程式與資料庫人事而很重要的開發人員生產力。

我們建議您快速入門觀看以下影片，其中 Aravind Ramachandran 顯示 DocumentDB 的查詢功能，及瀏覽我們[查詢遊樂場](http://www.documentdb.com/sql/demo)，您可以嘗試 DocumentDB 及對資料集執行 SQL 查詢的位置。

> [AZURE.VIDEO dataexposedqueryingdocumentdb]

然後，返回本文中，我們會在位置開始逐步引導您進行一些簡單的 JSON 文件和 SQL 命令的 SQL 查詢教學課程。

## <a name="getting-started-with-sql-commands-in-documentdb"></a>快速入門中 DocumentDB SQL 命令
若要查看 DocumentDB SQL 工作，現在就讓我們先幾個簡單的 JSON 文件，以及逐步瞭解一些簡單查詢。 請考慮兩個系列瞭解這些兩個 JSON 文件。 請注意，與 DocumentDB，我們不需要明確地建立任何結構描述或次要索引。 我們只需要插入 DocumentDB 集合 JSON 文件及之後的查詢。 以下我們有簡單的 JSON 文件的 Andersen 系列、 父系、 子系 （和其寵物），地址和註冊資訊。 文件有字串、 數字、 布林值、 陣列和巢狀的屬性。 

**文件**  

    {
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }


以下是 [第二個文件的一個細微差異 –`givenName`和`familyName`而不是使用`firstName`和`lastName`。

**文件**  

    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                "givenName": "Jesse", 
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
                "familyName": "Miller", 
                 "givenName": "Lisa", 
                 "gender": "female", 
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "creationDate": 1431620462,
        "isRegistered": false
    }



現在讓我們來嘗試幾個此資料至了解重要層面的 DocumentDB SQL 查詢。 例如，下列查詢會傳回文件符合 [識別碼] 欄位的位置`AndersenFamily`。 因為它是`SELECT *`，查詢的輸出已完成的 JSON 文件︰

**查詢**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**結果**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]


現在請考慮大小寫，我們需要重新格式化 JSON 輸出中不同圖案的位置。 此查詢的專案新 JSON 物件的兩個所選欄位名稱和縣 / 市時的地址的城市狀態中有相同的名稱。 在此情況下，比對 「 NY，NY 」。

**查詢**   

    SELECT {"Name":f.id, "City":f.address.city} AS Family 
    FROM Families f 
    WHERE f.address.city = f.address.state

**結果**

    [{
        "Family": {
            "Name": "WakefieldFamily", 
            "City": "NY"
        }
    }]


下一步查詢傳回其識別碼符合的系列中的子系的所有指定名稱`WakefieldFamily`排序我的城市。

**查詢**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC

**結果**

    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]


我們想要強調一些值得一看層面 DocumentDB 查詢語言透過為止看到我們的範例︰  
 
-   DocumentDB SQL 運作 JSON 值，因為它處理樹狀形狀，而不是列和欄的項目。 因此，語言可讓您要參照的任意深度，在樹狀節點`Node1.Node2.Node3…..Nodem`，就像參照的兩個部分參照的關聯式 SQL `<table>.<column>`。   
-   結構化的查詢語言搭配無結構描述的資料。 因此，類型系統需要動態繫結。 相同的運算式可能會產生上不同的文件的不同類型。 查詢的結果是有效的 JSON 值，但不是一定會固定的結構描述。  
-   DocumentDB 只支援嚴格 JSON 文件。 這表示類型系統和運算式的限制只有處理 JSON 類型。 請參閱[JSON 規格](http://www.json.org/)如需詳細資訊。  
-   DocumentDB 集合是 JSON 文件的結構描述空閒容器。 隱含擷取資料實體且所有集合中的文件中的關聯，內含項目而不是主索引鍵和外部索引鍵關聯。 這是值得根據本文稍後所述的文件內部聯結，重要。

## <a name="documentdb-indexing"></a>DocumentDB 編製索引

我們 DocumentDB SQL 語法之前，值得探索中 DocumentDB 編製索引的設計。 

資料庫索引的目的，是使用最小的資源使用量 （例如 CPU 或輸入輸出） 提供各種表單及圖形中的查詢時提供建議處理量和低延遲。 通常，選擇正確的索引的查詢資料庫需要太多的 「 規劃 」 和試驗。 此方法均會造成的挑戰無結構描述的資料庫的資料不符合嚴格的結構描述和快速發展的位置。 

因此，當我們設計編製索引作業子系統 DocumentDB，我們會設定下列目標︰

-   索引的文件，而不需要結構描述︰ 編製索引的子系統不需要任何結構描述資訊或假設結構描述的文件。 

-   支援有效、 豐富的階層式，和關聯式查詢︰ 索引支援 DocumentDB 查詢語言有效率地，包括支援階層式和關聯式預測。

-   支援的寫入持續大量 in face of 一致的查詢︰ 高寫入處理量工作負載一致的查詢，更新索引從屬參照、 有效率地，與線上面對寫入持續音量。 一致索引更新是很重要作為使用者設定文件服務的一致性層級的查詢。

-   支援多重 tenancy︰ 提供在租用戶資源管理保留基礎模型，index 會執行更新系統資源 （CPU、 記憶體和秒的輸入輸出作業） 配置複本的預算。 

-   儲存效率︰ 成本效益索引的磁碟上儲存負擔是受限制，如預期呈現。 這是很重要，因為 DocumentDB 允許開發人員索引的查詢效能的費用相對於之間的成本基礎折衷。  

如需顯示如何設定集合編製索引的原則的範例，請參閱 MSDN 上[DocumentDB 範例](https://github.com/Azure/azure-documentdb-net)。 現在就讓我們立即取得 DocumentDB SQL 語法的詳細資料。


## <a name="basics-of-a-documentdb-sql-query"></a>DocumentDB SQL 查詢的基本概念
每個查詢包含一個 SELECT 子句和選擇性的 FROM 和 WHERE 子句，每個 ANSI SQL 標準。 一般而言，每個查詢，列舉 FROM 子句的來源。 然後在 WHERE 子句中的篩選會套用至要擷取子集 JSON 文件的來源。 最後，SELECT 子句會用於要求的 JSON 值，選取清單中的專案。
    
    SELECT [TOP <top_expression>] <select_list> 
    [FROM <from_specification>] 
    [WHERE <filter_condition>]
    [ORDER BY <sort_specification]    


## <a name="from-clause"></a>FROM 子句
`FROM <from_specification>`除非來源經過篩選或更新版本中查詢預計是選用子句。 此子句的目的是指定查詢必須操作結合的資料來源。 整個集合通常是來源，但其中一個可以改為指定的集合子集。 

查詢等`SELECT * FROM Families`表示整個系列集合的列舉來源。 特殊的識別碼根可用來表示的集合，而非集合名稱。 下列清單包含每個查詢便會強制執行的規則︰

- 集合可別名，例如`SELECT f.id FROM Families AS f`或只是`SELECT f.id FROM Families f`。 以下`f`相當於`Families`。 `AS`是選擇性的關鍵字別名的識別碼。

-   請注意，一次別名，原始來源無法繫結。 例如，`SELECT Families.id FROM Families f`的語法無效，因為無法再解析識別碼 」 系列]。

-   參照的所有屬性必須都是完整名稱。 嚴格的結構描述遵循沒有，這會強制執行以避免任何不明確的繫結。 因此，`SELECT id FROM Families f`的語法是無效的屬性自`id`未繫結。
    
### <a name="sub-documents"></a>子文件
來源也可以縮減較小的子集合。 舉例來說，來列舉子樹狀目錄中每個文件，子根可能就會變成來源，如下列範例所示。

**查詢**

    SELECT * 
    FROM Families.children

**結果**  

    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]

上述範例使用做為來源的陣列，也無法用物件做為來源，也就是在下列範例顯示的內容。 有效 JSON 的任何值 （不未定義），請參閱來源會被視為納入查詢的結果。 如果沒有部分系列`address.state`值，它們會排除在查詢結果中。

**查詢**

    SELECT * 
    FROM Families.address.state

**結果**

    [
      "WA", 
      "NY"
    ]


## <a name="where-clause"></a>WHERE 子句
WHERE 子句 (**`WHERE <filter_condition>`**) 為選用步驟。 它會指定條件的 JSON 文件提供的來源必須符合才有結果的一部分。 任何 JSON 文件都必須評估為 「 true 」 會視為結果指定的條件。 在 [圖層索引使用 WHERE 子句來判斷最小的絕對來源文件可結果中的子集。 

下列查詢要求文件若包含值的名稱屬性`AndersenFamily`。 沒有名稱屬性時，任何其他文件或值不符合`AndersenFamily`排除。 

**查詢**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**結果**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


上一個範例顯示簡單相等查詢。 DocumentDB SQL 也支援各種不同的純量運算式。 最常用的二進位與一元運算式。 從來源 JSON 物件的屬性參照，也會有效的運算式。 

目前支援下列二進位運算子，並可以在查詢中使用，如下列範例所示︰  
<table>
<tr>
<td>算術</td> 
<td>+,-,*,/,%</td>
</tr>
<tr>
<td>位元</td>    
<td>|，&，^，<<, >>，>>> （零填滿右移） </td>
</tr>
<tr>
<td>邏輯</td>
<td>或不</td>
</tr>
<tr>
<td>比較</td> 
<td>=, !=, &lt;, &gt;, &lt;=, &gt;=, <></td>
</tr>
<tr>
<td>字串</td> 
<td>||（串連）</td>
</tr>
</table>  

讓我們來看看一些使用二進位運算子的查詢。

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1
    
    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5
    
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5     -- matching grades == 5


一元運算子 +、-，~ 不也受到支援，並可以使用查詢在下列範例所示︰

    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1
    
    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5



除了二進位和一元運算子，也可以屬性參照。 例如，`SELECT * FROM Families f WHERE f.isRegistered`會傳回包含屬性的 JSON 文件`isRegistered`屬性的值等於 JSON`true`值。 任何其他的值 (false，null，定義， `<number>`， `<string>`， `<object>`， `<array>`，等等) 潛在客戶來源文件要排除的結果。 

### <a name="equality-and-comparison-operators"></a>相等和比較運算子
下表會顯示在 DocumentDB SQL 任何兩個 JSON 類型之間相等比較的結果。
<table style = "width:300px">
   <tbody>
      <tr>
         <td valign="top">
            <strong>作業</strong>
         </td>
         <td valign="top">
            <strong>定義</strong>
         </td>
         <td valign="top">
            <strong>Null</strong>
         </td>
         <td valign="top">
            <strong>布林值</strong>
         </td>
         <td valign="top">
            <strong>數字</strong>
         </td>
         <td valign="top">
            <strong>字串</strong>
         </td>
         <td valign="top">
            <strong>物件</strong>
         </td>
         <td valign="top">
            <strong>陣列</strong>
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>定義<strong>
         </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Null<strong>
         </td>
         <td valign="top">
定義 </td>
         <td valign="top">
            <strong>[確定]</strong>
         </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>布林值<strong>
         </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
         <td valign="top">
            <strong>[確定]</strong>
         </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>數字<strong>
         </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
         <td valign="top">
            <strong>[確定]</strong>
         </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>字串<strong>
         </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
         <td valign="top">
            <strong>[確定]</strong>
         </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>物件<strong>
         </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
         <td valign="top">
            <strong>[確定]</strong>
         </td>
         <td valign="top">
定義 </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>陣列<strong>
         </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
         <td valign="top">
定義 </td>
         <td valign="top">
            <strong>[確定]</strong>
         </td>
      </tr>
   </tbody>
</table>

針對其他比較運算子，例如 >，> =、 ！ =、 < 和 < =，下列規則套用︰   

-   跨類型比較的結果未定義。
-   比較兩個物件，或兩個陣列中未定義結果。   

如果在篩選純量運算式的結果會是未定義，對應的文件就不會包含在結果中，由於未定義邏輯不等於 「 true 」。

### <a name="between-keyword"></a>之間關鍵字
您也可以使用之間關鍵字表達範圍中的值例如 ANSI SQL 查詢。 之間來字串或數字。

例如，此查詢會傳回所有家庭的文件中的第一個子成績介於 1-5 （兩者均 （含））。 

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5

不同於中，您也可以使用之間子句的 FROM 子句像在下列範例。

    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c

更快速地查詢的執行時間，請記得建立編製索引的原則，使用對任何數值屬性/路徑的之間子句中篩選範圍索引類型。 

使用 DocumentDB 和 ANSI SQL 之間的主要差異是，您可以表示範圍的混合型別屬性的查詢 – 例如，您可能需要 「 成績 」 是一個數字 (5)，在某些文件及其他人 (「 grade4 」) 中的字串。 在下列情況下，例如在 [JavaScript] 比較兩個不同類型會導致 「 未定義 」，與文件會略過。

### <a name="logical-and-or-and-not-operators"></a>邏輯 (AND、 OR 和不) 運算子
布林值邏輯運算子操作。 下表顯示邏輯運算子真實資料表。

OR|為 true|False|定義
---|---|---|---
為 true|為 true|為 true|為 true
False|為 true|False|定義
定義|為 true|定義|定義

與|為 true|False|定義
---|---|---|---
為 true|為 true|False|定義
False|False|False|False
定義|定義|False|定義

不|  |
---|---
為 true|False
False|為 true
定義|定義

### <a name="in-keyword"></a>關鍵字
在關鍵字可用來檢查指定的值是否符合清單中的任何值。 例如，此查詢會傳回所有的家庭文件 id 在哪裡其中一個 「 WakefieldFamily 」 或 「 AndersenFamily 」。 
 
    SELECT *
    FROM Families 
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')

此範例會傳回所有文件位置的狀態是任何指定的值。

    SELECT *
    FROM Families 
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")

### <a name="ternary--and-coalesce--operators"></a>三元 （？） 和聯合 （？） 運算子
三元和聯合運算子可用於建立條件運算式，類似於 C# 和 JavaScript 等常用的程式設計語言。 

建構即時新 JSON 屬性時，可以是非常好用三元 （？） 運算子。 例如，您現在可以撰寫分類的類別層級轉換為可讀取的人力表單，例如初級/中繼/進階]，如下所示的查詢。
 
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel 
     FROM Families.children[0] c

您也可以巢狀的來電至] 等下列查詢中的運算子。
 
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel 
    FROM Families.children[0] c

為其他查詢運算子，如果在任何文件中遺失條件運算式中參考的屬性，或如果進行比較類型不同，然後這些文件會排除查詢結果中。

聯合 （？） 運算子可用於有效率地檢查存在的屬性 （又名 定義） 文件中。 針對半結構化查詢時，這是很有用或混合類型的資料。 例如，此查詢會傳回 「 姓氏 」 如果有的話或 「 姓氏 」 如果很難簡報。

    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f

### <a name="quoted-property-accessor"></a>引號括住的屬性存取子
您也可以存取內容使用引號括住的屬性運算子`[]`。 例如，`SELECT c.grade`和`SELECT c["grade"]`相等。 這個語法時，必須先逸出包含空格或特殊字元，或共用相同名稱的 SQL 關鍵字或保留的字發生的屬性。

    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"


## <a name="select-clause"></a>SELECT 子句
SELECT 子句 (**`SELECT <select_list>`**) 是必要欄位，並指定哪些值會擷取從查詢，就像在 ANSI SQL 中。 已篩選的來源文件頂端的子集合傳遞到投影階段，其中會擷取指定的 JSON 值，而新 JSON 物件公式的結構，傳遞到其上每個輸入。 

下列範例顯示一般的選取查詢。 

**查詢**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**結果**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


### <a name="nested-properties"></a>巢狀的屬性
在下列範例中，我們會將兩個巢狀的屬性`f.address.state`和`f.address.city`。

**查詢**

    SELECT f.address.state, f.address.city
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**結果**

    [{
      "state": "WA", 
      "city": "seattle"
    }]


預測也支援 JSON 運算式，如下列範例所示。

**查詢**

    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**結果**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle", 
        "name": "AndersenFamily"
      }
    }]


我們來看看的角色`$1`以下。 `SELECT`子句必須建立 JSON 物件，因為沒有金鑰會提供，我們會使用隱含的引數的變數名稱開頭`$1`。 例如，此查詢會傳回兩個的隱含的引數變數標示為 [`$1`和`$2`。

**查詢**

    SELECT { "state": f.address.state, "city": f.address.city }, 
           { "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**結果**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]


### <a name="aliasing"></a>別名
現在讓我們來擴充上述範例使用明確的別名的值。 會使用的別名的關鍵字。 請注意，如下圖所示，將第二個值選用`NameInfo`。 

查詢具有相同名稱的兩個屬性，以防別名必須用於重新命名一或兩個屬性，使它們明示預期的結果。

**查詢**

    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo, 
           { "name": f.id } NameInfo
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**結果**

    [{
      "AddressInfo": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]


### <a name="scalar-expressions"></a>純量運算式
除了屬性參照 SELECT 子句也支援純量運算式，例如常數、 算術運算式、 邏輯運算式等。例如，以下是簡單的 「 Hello World 」 查詢。

**查詢**

    SELECT "Hello World"

**結果**

    [{
      "$1": "Hello World"
    }]


以下是更複雜的範例使用純量運算式。

**查詢**

    SELECT ((2 + 11 % 7)-2)/3   

**結果**

    [{
      "$1": 1.33333
    }]


在下列範例中，純量運算式的結果會是布林值。

**查詢**

    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f 

**結果**

    [
      {
        "AreFromSameCityState": false
      }, 
      {
        "AreFromSameCityState": true
      }
    ]


### <a name="object-and-array-creation"></a>建立物件和陣列
另一個主要 DocumentDB SQL 功能是陣列/物件的建立。 在上一個範例中，請注意，我們建立一個新的 JSON 物件。 同樣地，其中一個也可以建立陣列，如下列範例所示。

**查詢**

    SELECT [f.address.city, f.address.state] AS CityState 
    FROM Families f 

**結果**  

    [
      {
        "CityState": [
          "seattle", 
          "WA"
        ]
      }, 
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]

### <a name="value-keyword"></a>值的關鍵字
**值**關鍵字提供傳回 JSON 值的方式。 例如，以下所示的查詢會傳回純量`"Hello World"`而不是`{$1: "Hello World"}`。

**查詢**

    SELECT VALUE "Hello World"

**結果**

    [
      "Hello World"
    ]


下列查詢會傳回 JSON 值，而不`"address"`在結果中的標籤。

**查詢**

    SELECT VALUE f.address
    FROM Families f 

**結果**  

    [
      {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan", 
        "city": "NY"
      }
    ]

下列範例會延伸此顯示如何傳回 JSON 基本值 （JSON 樹狀分葉層級）。 

**查詢**

    SELECT VALUE f.address.state
    FROM Families f 

**結果**

    [
      "WA",
      "NY"
    ]


###<a name="-operator"></a>* 運算子
支援的特殊的運算子 （*） 至專案文件-是。 使用時，它必須是唯一的預計的欄位。 時查詢`SELECT * FROM Families f`有效，`SELECT VALUE * FROM Families f `和`SELECT *, f.id FROM Families f `無效。

**查詢**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**結果**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

###<a name="top-operator"></a>頂端運算子
上方的關鍵字可以用來限制查詢中的值的數目。 ORDER BY 子句搭配使用上方時，結果僅限於前面的 n 個排序值;否則，則會傳回前面的 n 個結果中未定義的順序。 最佳作法是在 SELECT 陳述式，一律使用 ORDER BY 子句具有上方。 這是唯一的方式預測指出哪一列資料受影響的頂端。 


**查詢**

    SELECT TOP 1 * 
    FROM Families f 

**結果**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

為常數 （如上面範例） 或使用參數化的查詢變數值，可頂端。 如需詳細資訊，請參閱下列參數化的查詢。

## <a name="order-by-clause"></a>ORDER BY 子句
例如，您可以在包含選擇性 Order By 子句查詢時。 子句可以包含指定的必須擷取結果的順序遞增/遞減選擇性引數。 如詳細看方式，請參閱[DocumentDB 順序的逐步解說](documentdb-orderby.md)。

例如，以下是擷取系列中的內建的城市名稱順序的查詢。

**查詢**

    SELECT f.id, f.address.city
    FROM Families f 
    ORDER BY f.address.city
    
**結果**
    
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"   
      }
    ]

接下來的擷取系列中的 [建立日期儲存為代表期間的數字順序查詢時間，例如︰ 存在自 1970 年 1 月 1 日在經過的時間 （秒）。

**查詢**

    SELECT f.id, f.creationDate
    FROM Families f 
    ORDER BY f.creationDate DESC
    
**結果**
    
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472  
      }
    ]
    
## <a name="advanced-database-concepts-and-sql-queries"></a>進階的資料庫概念和 SQL 查詢
### <a name="iteration"></a>反覆運算
透過**在**關鍵字 DocumentDB SQL 提供支援逐一查看 JSON 陣列中加入新的建構。 從來源反覆運算提供支援。 現在就讓我們開始下列範例︰

**查詢**

    SELECT * 
    FROM Families.children

**結果**  

    [
      [
        {
          "firstName": "Henriette Thaulow", 
          "gender": "female", 
          "grade": 5, 
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam", 
            "givenName": "Jesse", 
            "gender": "female", 
            "grade": 1
        }, 
        {
            "familyName": "Miller", 
            "givenName": "Lisa", 
            "gender": "female", 
            "grade": 8
        }
      ]
    ]

現在讓我們來看看執行反覆運算透過子集合中的另一個查詢。 請注意輸出陣列中的差異。 本範例分割`children`並壓平結果合併成單一陣列。  

**查詢**

    SELECT * 
    FROM c IN Families.children

**結果**  

    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]

這可以進一步用來篩選陣列的每一個個別項目，如下列範例所示。

**查詢**

    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8

**結果**  

    [{
      "givenName": "Lisa"
    }]

### <a name="joins"></a>連接
在關聯式資料庫中，加入跨資料表必須是非常重要。 這是設計常態化的結構描述邏輯必然。 相反，DocumentDB 處理的結構描述免費的文件的正規化的資料模型。 這是相當於邏輯的 「 自我聯結 」。

語言支援的語法是 < from_source1 > 加入 < from_source2 > 加入...加入 < from_sourceN >。 整體來說，這會傳回一組**N**tuple (表示 tuple **N**值)。 每個 tuple 具有所產生的所有的集合別名逐一查看其個別的設定值。 換句話說，這是參與加入集的完整的交叉乘積。

下列範例會示範加入子句的運作方式。 在下列範例中，結果來源的每個文件的交叉乘積自是空的清空是空的。

**查詢**

    SELECT f.id
    FROM Families f
    JOIN f.NonExistent

**結果**  

    [{
    }]


在下列範例中，加入介於文件根和`children`子根。 就是兩個 JSON 物件之間的乘積。 子系是陣列不是有效加入因為我們在單一根字詞陣列的處理。 因此，結果會包含只有兩個結果，因為陣列的每個文件的交叉乘積產生完全只有一個文件。

**查詢**

    SELECT f.id
    FROM Families f
    JOIN f.children
 
**結果**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]


下列範例顯示傳統聯結︰

**查詢**

    SELECT f.id
    FROM Families f
    JOIN c IN f.children 

**結果**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]



首先請注意，那麼該`from_source`**加入**子句是 iterator。 因此，流程在此情況下如下︰  

-   展開陣列中的每一個子元素**c** 。
-   適用於使用的每一個子項目**c**已在均化相當第一個步驟中的文件**f**根目錄的乘積。
-   最後，專案根物件**f** name 屬性時還大。 

第一份文件 (`AndersenFamily`) 包含只有一個子元素，因此結果集包含只對應至這份文件的單一物件。 第二個文件 (`WakefieldFamily`) 包含兩個字詞。 因此，交叉乘積會產生的每一個子，藉此結果兩個物件，一個用於對應至這份文件的每一個子另一個物件。 請注意，這些文件中的根欄位會相同，就像您所預期中的交叉乘積。

實數公用程式連接的圖形，否則很難專案中的交叉產品是表單 tuple。 此外，我們將會看到下列範例中，您可以篩選可讓的使用者選擇的整體的 tuple 符合條件，表示 tuple 的組合。

**查詢**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
 
**結果**

    [
      {
        "familyName": "AndersenFamily", 
        "childFirstName": "Henriette Thaulow", 
        "petName": "Fluffy"
      }, 
      {
        "familyName": "WakefieldFamily", 
        "childGivenName": "Jesse", 
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]



此範例中的自然延伸部分上述範例中，並執行的雙的聯結。 因此，可以為下列的虛擬程式碼檢視交叉乘積。

    for-each(Family f in Families)
    {   
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName, 
                  c.givenName AS childGivenName, 
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }

`AndersenFamily`具有一個子具有一個寵物。 因此，跨產品產生一列 (1*1*1) 的這一系列。 WakefieldFamily 不過有兩個字詞，但只有一個子系 「 Jesse 」 有寵物。 Jesse 不過有 2 寵物。 因此交叉乘積產生 1*1*2 = 2 列的這一系列。

在下一個範例中，有額外的篩選上`pet`。 這會排除寵物名稱不 「 陰影 」 的所有 tuple。 請注意，我們就能建立 tuple 的陣列，其中任何一個表示 tuple 的項目上的篩選及 project 項目的任意組合。 

**查詢**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"

**結果**

    [
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]


## <a name="javascript-integration"></a>JavaScript 整合
DocumentDB 提供的程式設計模型執行 JavaScript 基礎應用程式邏輯直接預存程序和引動程序的集合。 這個選項可讓兩項︰

-   可做高效能交易的 CRUD 作業及文件一定 JavaScript 執行階段直接在資料庫引擎深度整合集合中的查詢。 
-   控制流程、 變數範圍設定與工作分派和例外處理基本項目以資料庫交易的整合自然模型。 如需 JavaScript 整合 DocumentDB 支援的詳細資訊，請參閱 JavaScript 伺服器端程式設計文件。

###<a name="user-defined-functions-udfs"></a>使用者定義函數 (Udf)
已經在本文中定義的類型，以及 DocumentDB SQL 提供支援的使用者定義函數 (UDF)。 特別是純量 Udf 支援開發人員可以傳入零個或多個引數，傳回單一引數結果回的位置。 這些引數會檢查有正在法律 JSON 值。  

DocumentDB SQL 語法延伸至支援使用這些使用者定義函數的自訂應用程式邏輯。 Udf DocumentDB 可以註冊，然後參照為 SQL 查詢的一部分。 事實上，Udf exquisitely 是由查詢設計。 此選項必然，Udf 沒有存取權的其他 JavaScript 類型 （預存程序和引動程序） 的物件。 以唯讀方式執行查詢，因為他們可以執行主要或次要複本。 因此，Udf 專為次要複本不同於其他 JavaScript 類型上執行。

以下是如何註冊 UDF，在 DocumentDB 資料庫，特別是在文件集合下的範例。

   
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) { 
                       return input.match(pattern) !== null;
                   };",
       };
       
       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
           regexMatchUdf).Result;  
                                                                             
上述範例會建立名稱的 UDF `REGEX_MATCH`。 接受兩個 JSON 字串值`input`和`pattern`，如果在第二個指定的第一個相符項目模式的檢查使用 JavaScript 的 string.match() 函數。


我們現在可以使用此 UDF 的查詢中預測。 Udf 必須限定以區分大小寫的前置詞 「 udf。 」 中呼叫時查詢。 

>[AZURE.NOTE] 3/17/2015年之前 DocumentDB 支援 UDF 來電，而 「 udf 」。 字首，例如選取 REGEX_MATCH()。 這個呼叫模式已被取代。  

**查詢**

    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families

**結果**

    [
      {
        "$1": true
      }, 
      {
        "$1": false
      }
    ]

UDF 也可以使用篩選內如下圖所示的範例所示，也合格 」 udf 」。 前置字元︰

**查詢**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")

**結果**

    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]


實際上，Udf 都是有效的純量運算式，並可在預測和篩選。 

若要展開 Udf 的電源，我們來看看另一個範例的條件邏輯︰

       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                switch (city) {
                    case 'seattle':
                        return 520;
                    case 'NY':
                        return 410;
                    case 'Chicago':
                        return 673;
                    default:
                        return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
                seaLevelUdf);
    
    
以下是 UDF 的範例。

**查詢**

    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f 

**結果**

     [
      {
        "city": "seattle", 
        "seaLevel": 520
      }, 
      {
        "city": "NY", 
        "seaLevel": 410
      }
    ]


如前述範例展現，Udf 會將 JavaScript 語言的整合提供豐富可程式化介面執行複雜的程序、 條件邏輯的內建的 JavaScript 執行階段功能協助 DocumentDB SQL 中。

DocumentDB SQL udf，每個來源中的文件在處理 UDF 的目前階段 （WHERE 子句或 SELECT 子句），提供引數。 結果是完美整合整體執行管道。 如果屬性參照 UDF 的參數中無法使用 JSON 值，參數會被視為未定義，因此 UDF 引動完全略過。 同樣的未定義 UDF 的結果時，它不會納入結果。 

在 [摘要] Udf 是很好的工具，來執行複雜的商務邏輯做為查詢的一部分。

### <a name="operator-evaluation"></a>運算子評估
DocumentDB，virtue 的 JSON 資料庫，以繪製 JavaScript 運算子與評估語意相似情形。 雖然 DocumentDB 嘗試保留 JavaScript 語意 JSON 支援而言，作業評估偏離在某些情況下。

在 DocumentDB SQL 不同於在傳統 SQL 中，類型的值是通常不知道直到實際上從資料庫擷取的值。 以有效率地執行查詢時，大部分的運算子有嚴格類型需求。 

DocumentDB SQL 不會執行與 JavaScript 不同的隱含轉換。 舉例來說，查詢等`SELECT * FROM Person p WHERE p.Age = 21`符合文件，其中包含其值是 21 Age 屬性。 任何其他屬性的年齡符合字串 「 21 」 或其他可能為無限變化的文件喜歡 」 021 」、 「 21.0 」、 「 0021 」、 「 00021 」 時，不會比對等。 這是相反 javascript 字串值會隱含地轉換為數字的位置 (根據運算子，例如: = =)。 此選項相當重要有效率的索引中 DocumentDB SQL 比對。 

## <a name="parameterized-sql-queries"></a>參數化的 SQL 查詢
DocumentDB 支援表示使用熟悉的參數查詢@標記法。 參數化的 SQL 提供強大的處理和逸出的使用者輸入，避免意外洩露透過 SQL 插入的資料。 

例如，您可以撰寫查詢做為參數，採用 [姓氏] 和 [地址狀態，，然後執行其姓氏] 和 [根據使用者輸入的地址狀態的各種值。

    SELECT * 
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState

此可以然後會傳送要求給 DocumentDB 為參數化 JSON 查詢類似以下所示。

    {      
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",     
        "parameters": [          
            {"name": "@lastName", "value": "Wakefield"},         
            {"name": "@addressState", "value": "NY"},           
        ] 
    }

頂端的引數可以使用設定參數化的查詢，例如以下所示。

    {      
        "query": "SELECT TOP @n * FROM Families",     
        "parameters": [          
            {"name": "@n", "value": 10},         
        ] 
    }

參數值可以為任何有效的 JSON (字串、 數字、 布林值，null，甚至是陣列或巢狀 JSON)。 也由於 DocumentDB 結構描述的情況，參數會根據任何類型不驗證。

##<a name="built-in-functions"></a>內建函數
DocumentDB 也支援常見作業，例如使用者定義函數 (Udf) 的查詢也可以使用內建函數的數字。

<table>
<tr>
<td>數學函數</td> 
<td>ABS CEILING、 EXP、 樓面、 記錄、 LOG10、 POWER、 ROUND、 登，SQRT、 正方形、 TRUNC、 ACOS、 ASIN、 ATAN、 ATN2、 COS，COT、 度、 PI、 弧度、 SIN 及 TAN</td>
</tr>
<tr>
<td>檢查函數的類型</td>    
<td>IS_ARRAY IS_BOOL，IS_NULL，IS_NUMBER，IS_OBJECT，IS_STRING，IS_DEFINED，與 IS_PRIMITIVE</td>
</tr>
<tr>
<td>字串函數</td>   
<td>Concat 函數、 包含、 ENDSWITH、 INDEX_OF、 左右長度、 較低、 LTRIM、 取代、 複寫、 反向、 RTRIM、 STARTSWITH、 子字串及 UPPER</td>
</tr>
<tr>
<td>陣列函數</td>    
<td>ARRAY_CONCAT、 ARRAY_CONTAINS、 ARRAY_LENGTH 及 ARRAY_SLICE</td>
</tr>
<tr>
<td>空間函數</td>  
<td>ST_DISTANCE 與 ST_WITHIN，ST_ISVALID ST_ISVALIDDETAILED</td>
</tr>
</table>  

如果您目前使用的內建函數現在已可使用的使用者定義的函數 (UDF)，您就應該使用相對應的內建函數，因為它即將成為更快速地執行且更有效率。 

### <a name="mathematical-functions"></a>數學函數
數學函數每個執行計算，通常會根據輸入的值，作為引數，並傳回數字的值。 以下是支援的內建數學函數的資料表。

<table>
<tr>
<td><strong>使用方式</strong></td>
<td><strong>描述</strong></td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_abs">ABS (num_expr)</a></td> 
<td>傳回指定數值運算式的絕對 （正值）。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ceiling">CEILING (num_expr)</a></td> 
<td>傳回最小整數值大於或等於指定的數值運算式。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_floor">FLOOR (num_expr)</a></td> 
<td>小於或等於指定的數值運算式傳回的最大整數。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_exp">EXP (num_expr)</a></td> 
<td>傳回指定數值運算式的指數。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log">記錄 （num_expr [，基底]）</a></td> 
<td>傳回指定數值運算式或使用指定的底數的對數的自然對數</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log10">LOG10 (num_expr)</a></td> 
<td>傳回指定數字的運算式之底數為 10 對數的值。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_round">圓形 (num_expr)</a></td> 
<td>傳回數字，四捨五入到最接近的整數值。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_trunc">TRUNC (num_expr)</a></td> 
<td>傳回數字，到最接近的整數值取至整數。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sqrt">SQRT (num_expr)</a></td>   
<td>傳回指定數值運算式的平方根。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_square">方形 (num_expr)</a></td>   
<td>傳回指定數值運算式。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_power">POWER （num_expr、 num_expr）</a></td>   
<td>傳回指定數值運算式以指定的值。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sign">符號 (num_expr)</a></td>   
<td>傳回指定數值運算式登值 （-1，0，1）。</td>
</tr>
<tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_acos">ACOS (num_expr)</a></td>   
<td>傳回以弧度表示，其餘弦值是指定的數值運算式; 的角度也稱為反餘弦值。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_asin">ASIN (num_expr)</a></td>   
<td>傳回以弧度表示，其正弦是指定的數值運算式。 這也稱為反正弦值。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_atan">ATAN (num_expr)</a></td>   
<td>傳回以弧度表示，其正切值是指定的數值運算式。 這也稱為反正切值。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_atn2">ATN2 (num_expr)</a></td>   
<td>傳回以弧度表示，之間正 x 軸和從原點藍光點 y （x），其中 x 和 y 兩個指定的浮動運算式的值。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_cos">COS (num_expr)</a></td> 
<td>傳回以弧度表示，指定之運算式中的三角函數指定角度的餘弦值。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_cot">COT (num_expr)</a></td> 
<td>傳回三角指定角度的餘切值，以弧度表示，指定的數值運算式。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_degrees">度 (num_expr)</a></td> 
<td>傳回對應的角度以度為單位，以弧度表示之角度。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_pi">PI （)</a></td>   
<td>傳回常數 PI 的值。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_radians">弧度表示 (num_expr)</a></td> 
<td>當您輸入的數字的運算式，以角度表示，會傳回弧度。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sin">SIN (num_expr)</a></td> 
<td>傳回指定角度三角正弦值，以弧度表示，指定之運算式中。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_tan">TAN (num_expr)</a></td> 
<td>傳回指定之運算式中的輸入運算式之後的正切值。</td>
</tr>

</table> 

例如，您現在可以執行查詢，如下所示︰

**查詢**

    SELECT VALUE ABS(-4)

**結果**

    [4]

相較於 ANSI SQL DocumentDB 的函數的主要差異為它們專適用於結構描述較及混合的結構描述資料。 例如，如果您的文件位置遺失，[大小] 屬性，或具有非數字值，例如 「 未知 」，然後文件時，會跳過上,，而不會傳回錯誤。

### <a name="type-checking-functions"></a>檢查函數的類型
輸入檢查功能可讓您檢查 SQL 查詢中的運算式的類型。 輸入檢查函數可以用於變數或未知時，決定的即時的文件中的內容類型。 以下是類型的支援內建，檢查函式的表格。

<table>
<tr>
  <td><strong>使用方式</strong></td>
  <td><strong>描述</strong></td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_array">IS_ARRAY (expr)</a></td>
  <td>傳回指出值的類型是否為陣列的 Boolean。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_bool">IS_BOOL (expr)</a></td>
  <td>傳回指出值的類型是否為布林值的 Boolean。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_null">IS_NULL (expr)</a></td>
  <td>傳回布林值，指出是否 null 值的類型。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_number">IS_NUMBER (expr)</a></td>
  <td>傳回指出值的類型是否數字的 Boolean。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_object">IS_OBJECT (expr)</a></td>
  <td>傳回指出值的類型是否 JSON 物件的 Boolean。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_string">IS_STRING (expr)</a></td>
  <td>傳回指出值的類型是否為字串的 Boolean。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_defined">IS_DEFINED (expr)</a></td>
  <td>傳回指出屬性是否已經指派值的 Boolean。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_primitive">IS_PRIMITIVE (expr)</a></td>
  <td>傳回指出值的類型是否字串、 數字、 布林值或 null 布林值。</td>
</tr>

</table>

使用這些函數，您現在可以執行查詢，如下所示︰

**查詢**

    SELECT VALUE IS_NUMBER(-4)

**結果**

    [true]

### <a name="string-functions"></a>字串函數
下列純量函數的字串輸入值來執行作業，並傳回字串、 數值或布林值。 以下是內建的字串函數的表格︰

使用方式|描述
---|---
[長度 (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_length)|傳回指定的字串運算式的字元數
[Concat 函數 （str_expr，str_expr [，str_expr]）](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_concat)|傳回字串串連兩個或多個字串值的結果。
[子字串 （str_expr、 num_expr num_expr）](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_substring)|傳回字串運算式的一部分。
[STARTSWITH （str_expr、 str_expr）](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_startswith)|傳回布林值，指出是否第一個字串運算式一句是第二個
[ENDSWITH （str_expr，str_expr）](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_endswith)|傳回布林值，指出是否第一個字串運算式一句是第二個
[包含 （str_expr，str_expr）](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_contains)|傳回布林值，指出是否第一個字串運算式包含第二個。
[INDEX_OF （str_expr，str_expr）](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_index_of)|傳回的開始位置的第一個項目，第二個字串中第一個指定的字串運算式或-1 的運算式，如果找不到的字串。
[向左 （str_expr、 num_expr）](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_left)|傳回包含指定的字元數的字串的左邊。
[權限 （str_expr、 num_expr）](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_right)|傳回包含指定的字元數的字串的正確部分。
[LTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ltrim)|傳回的字串運算式之後便會移除前置空格。
[RTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_rtrim)|傳回截斷所有行尾空白之後的字串運算式。
[LOWER (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_lower)|傳回轉換成小寫的大寫字元資料之後的字串運算式。
[UPPER (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_upper)|傳回轉換成大寫的小寫字元之後的字串運算式。
[取代 str_expr，str_expr （str_expr）](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replace)|指定的字串值的所有項目取代為另一個字串值。
[複寫 （str_expr、 num_expr）](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replicate)|依指定的次數重複字串值。
[反向 (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_reverse)|傳回字串值相反順序。

使用這些函數，您可以立即執行查詢，如下所示。 例如，您可以傳回系列名稱以大寫字母表示，如下所示︰

**查詢**

    SELECT VALUE UPPER(Families.id)
    FROM Families

**結果**

    [
        "WAKEFIELDFAMILY", 
        "ANDERSENFAMILY"
    ]

或串連字串，例如在此範例中︰

**查詢**

    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families

**結果**

    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "seattle,WA"
    }]


字串函數也可在 WHERE 子句可以篩選結果，例如，下列範例中︰

**查詢**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")

**結果**

    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]

### <a name="array-functions"></a>陣列函數
下列純量函數上執行作業陣列輸入的值傳回數值，布林值或陣列的值。 以下是內建的陣列函數的表格︰

使用方式|描述
---|---
[ARRAY_LENGTH (arr_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_length)|傳回指定的陣列運算式的項目數。
[ARRAY_CONCAT （arr_expr、 arr_expr [，arr_expr]）](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_concat)|傳回陣列結果的串連兩個或多個陣列的值。
[ARRAY_CONTAINS （arr_expr、 expr）](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_contains)|若為 true 表示陣列是否包含指定的值。
[ARRAY_SLICE arr_expr、 num_expr [(num_expr]）](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_slice)|傳回陣列運算式的一部分。

陣列函數可用於操作 JSON 內的陣列。 例如，以下是查詢傳回的所有文件的父系之一是 「 Robin Wakefield 」 的位置。 

**查詢**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })

**結果**

    [{
      "id": "WakefieldFamily"
    }]

以下是使用 ARRAY_LENGTH 取得孩子數量的每一系列的另一個範例。

**查詢**

    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 

**結果**

    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]

### <a name="spatial-functions"></a>空間函數

DocumentDB 支援下列開啟休士頓協會 (OGC) 內建函數休士頓查詢。 如需詳細資訊休士頓 DocumentDB 支援，請參閱[使用休士頓 Azure DocumentDB 中的資料](documentdb-geospatial.md)。 

<table>
<tr>
  <td><strong>使用方式</strong></td>
  <td><strong>描述</strong></td>
</tr>
<tr>
  <td>（point_expr、 point_expr） ST_DISTANCE</td>
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

空間函數可用於執行鄰近範圍 querries 針對空間資料。 例如，以下是位置的查詢會傳回所指定使用 ST_DISTANCE 內建函數 30 金鑰管理中的所有家庭文件。 

**查詢**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**結果**

    [{
      "id": "WakefieldFamily"
    }]

如果您加入空間索引編製功能在您編製索引的原則，然後 「 距離查詢 」 將提供有效率地透過索引。 如需空間編製索引作業的詳細資訊，請參閱下方的一節。 如果您沒有安裝空間索引針對指定的路徑，您仍然可以執行空間查詢指定`x-ms-documentdb-query-enable-scan`要求標題為 「 true 」 設定的值。 在.NET 中，進行選擇性**FeedOptions**引數傳遞[EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery)設定為 true 的查詢。 

若要檢查是否點所在多邊形內可 ST_WITHIN。 經常多邊形用來代表界限郵遞區號、 狀態界限或自然陣式等。 一次如果包含空間索引編製功能在您編製索引的原則，然後 「 中 」 查詢將提供有效率地透過索引。 

ST_WITHIN 多邊形引數可以包含單一撥打給，亦即多邊形不能包含在這些文章。 檢查點中多邊形允許 ST_WITHIN 查詢的數目上限[DocumentDB 限制](documentdb-limits.md)。

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
    
>[AZURE.NOTE] 類似如何不相符類型運作在 DocumentDB 查詢中，如果在 [引數不正確或不正確，然後它會評估**未定義**和評估文件的查詢結果會略過指定的 [位置] 的值。 如果您的查詢會不傳回任何結果，請執行 ST_ISVALIDDETAILED 至偵錯 spatail 類型不正確的原因。     

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
    
DocumentDB 的空間的函數，及 SQL 語法換行。 現在讓我們來看看如何運作，以及與語法互動的方式查詢 LINQ 我們看到為止。

## <a name="linq-to-documentdb-sql"></a>LINQ to DocumentDB SQL
LINQ 是.NET 程式設計模型的計算為流物件的查詢。 DocumentDB 提供用戶端側文件庫，以促進 JSON 和.NET 物件和 LINQ 子集從的對應之間的轉換 linq 介面查詢 DocumentDB 查詢。 

下圖顯示架構支援使用 DocumentDB LINQ 查詢。  開發人員可以使用 DocumentDB 用戶端，來建立**IQueryable**物件的直接查詢 DocumentDB 查詢提供者，然後將 LINQ 查詢轉換成 DocumentDB 查詢。 查詢然後傳遞至 DocumentDB 伺服器擷取一組 JSON 格式的結果。 還原成.NET 物件在用戶端資料流序列化傳回的結果。

![架構的支援 LINQ 使用查詢 DocumentDB-SQL 語法、 JSON 查詢語言、 資料庫概念及 SQL 查詢][1]
 


### <a name="net-and-json-mapping"></a>.NET 和 JSON 對應
.NET 物件和 JSON 文件之間的對應自然-每個資料成員欄位會對應到 JSON 物件，其中的欄位名稱] 已對應至物件的 「 金鑰 」 部分，而 「 值 」 部分是遞迴對應值組件的物件。 請考慮下列範例。 建立的家庭物件會對應到 JSON 文件中，如下所示。 反之亦然，JSON 文件] 已對應至.NET 物件。

**C# 類別**

    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };
    
    public struct Parent
    {
        public string familyName;
        public string givenName;
    };
    
    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };
    
    public class Pet
    {
        public string givenName;
    };
    
    public class Address
    {
        public string state;
        public string county;
        public string city;
    };
    
    // Create a Family object.
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };


**JSON**  

    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                "givenName": "Jesse", 
                "gender": "female", 
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller", 
              "givenName": "Lisa", 
              "gender": "female", 
              "grade": 8 
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };



### <a name="linq-to-sql-translation"></a>SQL 翻譯 LINQ
DocumentDB 查詢提供者從 LINQ 查詢執行最佳的項目對應到 DocumentDB SQL 查詢。 在 [下列的描述，我們假設讀者 LINQ 基本認識。

首先，請輸入系統，我們支援所有 JSON 基本型類型 – 數值類型、 布林值、 字串，以及 null。 支援僅這些 JSON 類型。 支援下列純量運算式。

-   常數值 – 這些會包含基本資料類型的常數值在評估查詢的時間。

-   屬性/陣列索引運算式 – 下列運算式參照物件或陣列元素的屬性。

        family.Id;
        family.children[0].familyName;
        family.children[0].grade;
        family.children[n].grade; //n is an int variable

-   算術運算式-包括數字和布林值的常見算術運算式。 [完成] 清單中，請參閱 SQL 規格。

        2 * family.children[0].grade;
        x + y;

-   字串比較運算式-包括比較一些常數字串值的字串值。  
 
        mother.familyName == "Smith";
        child.givenName == s; //s is a string variable

-   物件/陣列建立運算式-這些運算式傳回複合值類型或匿名類型的物件或陣列的這類物件。 可以巢狀這些值。

        new Parent { familyName = "Smith", givenName = "Joe" };
        new { first = 1, second = 2 }; //an anonymous type with 2 fields              
        new int[] { 3, child.grade, 5 };

### <a name="list-of-supported-linq-operators"></a>支援 LINQ 運算子的清單
以下是支援 LINQ 運算子隨附 DocumentDB.NET SDK LINQ 提供者中的清單。

-   **選取 [**: 計帳翻譯 SQL 選取包括物件建築
-   **位置**︰ 篩選翻譯 SQL 位置，及支援] 之間的翻譯 & &，個管線與 ！ SQL 運算子
-   **SelectMany**︰ 可讓陣列加入 SQL 子句的回溯。 可用於鏈狀陣列項目篩選運算式
-   **排序方式和 OrderByDescending**︰ 順序以遞增/遞減等同於角色︰
-   **CompareTo**︰ 將轉換為範圍比較。 常用的字串由於目前不在.NET 相容
-   **採取**︰ 翻譯 SQL 頂端限制查詢結果
-   **數學函數**︰ 支援從翻譯。網路的 Abs，Acos，Asin，Atan，Ceiling Cos，Exp，Floor，記錄，Log10，Pow，Round、 登，Sin，Sqrt，Tan，截斷相當於 SQL 內建函數。
-   **字串函數**︰ 支援從翻譯。網路的 concat 函數，包含，EndsWith IndexOf、 Count、 ToLower、 TrimStart、 取代、 反向、 TrimEnd、 StartsWith、 子字串、 ToUpper 相當於 SQL 內建函數。
-   **陣列函數**︰ 支援從翻譯。網路的 concat 函數、 包含及對等的 SQL 內建函數的計數。
-   **休士頓擴充函式**︰ 支援虛設常式方法 IsValid 和 IsValidDetailed 內的距離，相當於 SQL 內建函數的轉譯。
-   **使用者定義函數副檔名函數**︰ 從虛設常式方法 UserDefinedFunctionProvider.Invoke 的支援翻譯至對應的使用者定義函數。
-   **其他**︰ 支援翻譯聯合與條件的運算子。 可以翻譯包含字串包含、 ARRAY_CONTAINS 或 SQL 中，根據內容而定。

### <a name="sql-query-operators"></a>SQL 查詢運算子
以下是一些範例說明部分標準 LINQ 查詢運算子如何翻譯下 DocumentDB 查詢。

#### <a name="select-operator"></a>選取運算子
語法`input.Select(x => f(x))`，其中`f`是純量運算式。

**LINQ lambda 運算式**

    input.Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f



**LINQ lambda 運算式**

    input.Select(family => family.children[0].grade + c); // c is an int variable


**SQL** 

    SELECT VALUE f.children[0].grade + c
    FROM Families f 



**LINQ lambda 運算式**

    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });


**SQL** 

    SELECT VALUE {"name":f.children[0].familyName, 
                  "grade": f.children[0].grade + 3 }
    FROM Families f



#### <a name="selectmany-operator"></a>SelectMany 運算子
語法`input.SelectMany(x => f(x))`，其中`f`會傳回集合類型的純量運算式。

**LINQ lambda 運算式**

    input.SelectMany(family => family.children);

**SQL** 

    SELECT VALUE child
    FROM child IN Families.children



#### <a name="where-operator"></a>位置運算子
語法`input.Where(x => f(x))`，其中`f`是純量運算式會傳回一個 Boolean 值。

**LINQ lambda 運算式**

    input.Where(family=> family.parents[0].familyName == "Smith");

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith" 



**LINQ lambda 運算式**

    input.Where(
        family => family.parents[0].familyName == "Smith" && 
        family.children[0].grade < 3);

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
    AND f.children[0].grade < 3


### <a name="composite-sql-queries"></a>複合索引的 SQL 查詢
上述運算子可組成表單功能更強大的查詢。 由於 DocumentDB 支援巢狀的集合，撰寫可能串連或巢狀結構。

#### <a name="concatenation"></a>串連 

語法`input(.|.SelectMany())(.Select()|.Where())*`。 串連的查詢可以開始選擇性`SelectMany`查詢追蹤多個`Select`或`Where`運算子。


**LINQ lambda 運算式**

    input.Select(family=>family.parents[0])
        .Where(familyName == "Smith");

**SQL**

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"



**LINQ lambda 運算式**

    input.Where(family => family.children[0].grade > 3)
        .Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f
    WHERE f.children[0].grade > 3



**LINQ lambda 運算式**

    input.Select(family => new { grade=family.children[0].grade}).
        Where(anon=> anon.grade < 3);
            
**SQL** 

    SELECT *
    FROM Families f
    WHERE ({grade: f.children[0].grade}.grade > 3)



**LINQ lambda 運算式**

    input.SelectMany(family => family.parents)
        .Where(parent => parents.familyName == "Smith");

**SQL** 

    SELECT *
    FROM p IN Families.parents
    WHERE p.familyName = "Smith"



#### <a name="nesting"></a>巢狀結構

語法`input.SelectMany(x=>x.Q())`Q 在哪裡`Select`， `SelectMany`，或`Where`運算子。

在巢狀查詢中，內部查詢會套用至外部集合中的每個項目。 其中一個重要的是內部查詢可以參考的欄位，例如外部集合中的項目自我聯結。

**LINQ lambda 運算式**

    input.SelectMany(family=> 
        family.parents.Select(p => p.familyName));

**SQL** 

    SELECT VALUE p.familyName
    FROM Families f
    JOIN p IN f.parents


**LINQ lambda 運算式**

    input.SelectMany(family => 
        family.children.Where(child => child.familyName == "Jeff"));
            
**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = "Jeff"



**LINQ lambda 運算式**
            
    input.SelectMany(family => family.children.Where(
        child => child.familyName == family.parents[0].familyName));

**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = f.parents[0].familyName


## <a name="executing-sql-queries"></a>執行 SQL 查詢
DocumentDB 公開可以呼叫簡訊提出 HTTP/HTTPS 要求的任何語言 REST API 透過資源。 此外，DocumentDB 提供數種常見的語言，如.NET、 Node.js、 JavaScript 及 Python 程式設計文件庫。 REST API 和各種不同的文件庫所有支援透過 SQL 查詢。 .NET SDK 支援 LINQ 除了 SQL 查詢。

下列範例會顯示如何建立查詢，並送出對 DocumentDB 資料庫帳戶。

### <a name="rest-api"></a>REST API
DocumentDB 可透過 HTTP 提供開啟 RESTful 程式設計模型。 資料庫帳戶可以使用 Azure 訂閱佈建。 DocumentDB 資源模型組成的資源資料庫帳戶] 下，每一種是定址使用邏輯化和穩定 URI 集。 一組資源被指在這份文件中的摘要。 資料庫帳戶所組成的一組資料庫，每個包含多個集合] 中，每個哪些中開啟包含文件、 Udf，與其他的資源類型。

基本互動模型使用這些資源是透過 HTTP 動詞取得、 放入、 文章及刪除其標準解譯使用。 文章動詞用來建立新的資源、 執行預存程序或發行 DocumentDB 查詢。 查詢會永遠讀取只含側效果的作業。

下列範例會示範 DocumentDB 查詢包含兩個樣本文件集合到目前為止，我們已檢閱的文章。 查詢具有 JSON name 屬性簡單的篩選器。 請注意使用`x-ms-documentdb-isquery`與內容類型︰`application/query+json`代表作業是查詢的標題。


**要求**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {      
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",     
        "parameters": [          
            {"name": "@familyId", "value": "AndersenFamily"}         
        ] 
    }
    

**結果**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32
    
    <indented for readability, results highlighted>
    
    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }


第二個範例會顯示從加入傳回多個結果的較複雜的查詢。

**要求**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json
    
    {      
        "query": "SELECT 
                     f.id AS familyName, 
                     c.givenName AS childGivenName, 
                     c.firstName AS childFirstName, 
                     p.givenName AS petName 
                  FROM Families f 
                  JOIN c IN f.children 
                  JOIN p in c.pets",     
        "parameters": [] 
    }


**結果**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84
    
    <indented for readability, results highlighted>
    
    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }


如果查詢的結果不符合的結果，在單一頁面，然後 REST API 傳回透過接續權杖`x-ms-continuation-token`回應標頭。 用戶端可以對結果分頁，包括標頭中的後續結果。 每頁結果數目也可以透過控制`x-ms-max-item-count`數字的標題。

若要管理查詢的資料一致性原則，請使用`x-ms-consistency-level`標題像是所有 REST API 邀請。 工作階段的一致性，才能也回應最新版的`x-ms-session-token`Cookie 查詢邀請中的標題。 請注意查詢的集合編製索引原則也會影響查詢結果的一致性。 預設的原則設定編製索引作業，集合索引永遠是最新的文件內容與查詢結果會符合選擇資料的一致性。 如果要 Lazy 寬鬆編製索引的原則，查詢可以傳回過期的結果。 如需詳細資訊，請參閱[DocumentDB 一致性]層級 [consistency-levels]。

如果已設定的編製索引原則集合上不支援指定的查詢，DocumentDB 伺服器會傳回 400 」 錯誤的要求]。 這會傳回範圍查詢設定雜湊 （等號） 查閱，以及路徑明確排除編製索引的路徑。 `x-ms-documentdb-query-enable-scan`標頭可以指定允許執行掃描時無法使用索引的查詢。

### <a name="c-net-sdk"></a>C# (.NET) SDK
.NET SDK 支援 LINQ 和 SQL 查詢。 下列範例會示範如何執行簡單的篩選查詢推出之前在這份文件中。


    foreach (var family in client.CreateDocumentQuery(collectionLink, 
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }
    
    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(collectionLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(collectionLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }
    
    foreach (var family in client.CreateDocumentQuery(collectionLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


此範例會比較 」 等相等性每個文件中的兩個內容，並使用匿名預測。 


    foreach (var family in client.CreateDocumentQuery(collectionLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family 
        FROM Families f 
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }
    
    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(collectionLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }
    
    foreach (var family in
        client.CreateDocumentQuery<Family>(collectionLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


下一個範例顯示聯結，透過 LINQ SelectMany 來表示。


    foreach (var pet in client.CreateDocumentQuery(collectionLink,
          @"SELECT p
            FROM Families f 
                 JOIN c IN f.children 
                 JOIN p in c.pets 
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }
    
    // Equivalent in Lambda expressions
    foreach (var pet in
        client.CreateDocumentQuery<Family>(collectionLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }



.NET 用戶端自動逐一查看查詢結果，如下圖所示上方 foreach 區塊中的所有頁面。 介紹 REST API] 區段中的 [查詢] 選項，也會出現在.NET SDK 使用`FeedOptions`和`FeedResponse`CreateDocumentQuery 方法中的類別。 可以使用控制的頁數`MaxItemCount`設定。 

建立，您可以同時明確地控制分頁`IDocumentQueryable`使用`IQueryable`物件]，然後先閱讀` ResponseContinuationToken`值並將它們傳遞返回為`RequestContinuationToken`中`FeedOptions`。 `EnableScanInQuery`可設定不受支援的設定編製索引的原則查詢時，啟用掃描。 分割的集合，您可以使用`PartitionKey`執行查詢，針對單一分割 （雖然 DocumentDB 可以自動擷取此查詢文字），及`EnableCrossPartitionQuery`執行查詢，可能需要執行多個資料分割。 

如需更多範例，其中包含查詢，請參閱[DocumentDB.NET 範例](https://github.com/Azure/azure-documentdb-net)。 

### <a name="javascript-server-side-api"></a>JavaScript 伺服器端 API 
DocumentDB 提供的程式設計模型執行 JavaScript 基礎應用程式邏輯直接使用預存程序和引動程序的集合。 註冊集合層級的 JavaScript 邏輯然後可以發行指定集合上的文件的作業的資料庫作業。 這些作業環境 ACID 交易中換行。

下列範例會示範如何使用 queryDocuments server 中的 JavaScript API，讓查詢的內部預存程序及引動程序。


    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();
        var collectionLink = collectionManager.getSelfLink()
    
        // create a new document.
        collectionManager.createDocument(collectionLink,
            { name: name, author: author },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);
    
                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);
    
                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don't need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }

## <a name="aggregate-functions"></a>彙總函數

原生支援彙總函式運作正常，但是如果您同時需要計算或加總功能，您可以來達成相同的結果，使用不同的方法。  

了解路徑︰

- 您可以執行彙總函數來擷取資料，並執行本機的計數。 它具有建議將使用例如便宜的查詢投影`SELECT VALUE 1`而不是完整的文件，例如`SELECT * FROM c`。 這可協助最大化的結果，因而避免其他來回服務，如有需要的每一頁中處理的文件數。
- 您也可以使用最小化上重複往返網路延遲的預存程序。 計算指定的篩選查詢的使用次數的範例預存程序，請參閱[Count.js](https://github.com/Azure/azure-documentdb-js-server/blob/master/samples/stored-procedures/Count.js)。 預存程序可讓使用者合併豐富的商務邏輯以及中有效率地執行彙總。

寫入路徑︰

- 其他常見的模式就是以預先彙總的 「 撰寫 」 路徑中的結果。 「 讀取 」 要求量高於 「 撰寫 」 要求時，這是非常吸引人。 一次預先彙總，結果時才可使用單一點讀取回條。  預先彙總中 DocumentDB 的最佳方式就是設定 「 撰寫 」 叫用的觸發程序及更新有最新的實體化查詢的結果的中繼資料文件。 例如，請查看[UpdateaMetadata.js](https://github.com/Azure/azure-documentdb-js-server/blob/master/samples/triggers/UpdateMetadata.js)範例中，更新 minSize maxSize，與 totalSize 的中繼資料文件中的集合。 可以擴充樣本，更新 [計數器加總、 等等。

##<a name="references"></a>參照
1.  [Azure DocumentDB 簡介][introduction]
2.  [DocumentDB SQL 規格](http://go.microsoft.com/fwlink/p/?LinkID=510612)
3.  [DocumentDB.NET 範例](https://github.com/Azure/azure-documentdb-net)
4.  [DocumentDB 一致性層級][consistency-levels]
5.  ANSI SQL 2011 [http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6.  JSON [http://json.org/](http://json.org/)
7.  Javascript 規格[http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8.  LINQ [http://msdn.microsoft.com/library/bb308959.aspx](http://msdn.microsoft.com/library/bb308959.aspx) 
9.  查詢大型資料庫[http://dl.acm.org/citation.cfm?id=152611](http://dl.acm.org/citation.cfm?id=152611)的評估的技巧
10. 中的查詢處理平行關聯式資料庫系統 IEEE 電腦協會按下，1994
11. 中的查詢處理平行關聯式資料庫系統 IEEE 電腦協會按下，1994 Lu，Ooi，Tan。
12. Christopher Olston Benjamin 涅、 Utkarsh Srivastava、 Ravi Kumar，為 Andrew Tomkins︰ 豬拉丁︰ 來處理資料，SIGMOD 2008 不讓外部索引的語言。
13.     G。 Graefe。 查詢最佳化重疊顯示架構。 IEEE 資料 Eng. Bull.、 18(3): 1995年。


[1]: ./media/documentdb-sql-query/sql-query1.png
[introduction]: documentdb-introduction.md
[consistency-levels]: documentdb-consistency-levels.md
 
