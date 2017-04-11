 <properties
    pageTitle="如何使用批次處理改善 Azure SQL 資料庫應用程式的效能"
    description="本主題提供證據的批次的資料庫作業大幅 imroves 速度能與延展性 Azure SQL 資料庫應用程式。 雖然批次處理技巧適用於任何 SQL Server 資料庫，本文的焦點會放在 Azure。"
    services="sql-database"
    documentationCenter="na"
    authors="annemill"
    manager="jhubbard"
    editor="" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="07/12/2016"
    ms.author="annemill" />

# <a name="how-to-use-batching-to-improve-sql-database-application-performance"></a>如何使用批次處理改善 SQL 資料庫應用程式的效能

大幅批次處理到 Azure SQL 資料庫的作業，可改善效能與延展性應用程式。 若要瞭解的優點，這篇文章的第一個部分涵蓋比較循序及批次要求 SQL 資料庫一些範例測試結果。 文件的其餘部分顯示的技術、 案例、 與可協助您使用批次順利 Azure 應用程式中的考量。

**作者**︰ Jason 洛斯、 Silvano Coriani，trent 的部屬再 Swanson (完整的縮放比例 180 Inc)

**檢閱者**︰ Conor Cunningham、 Michael Thomassy

## <a name="why-is-batching-important-for-sql-database"></a>為什麼會批次處理重要 SQL 資料庫？
批次的來電至遠端服務會增加效能與延展性已知的策略。 那里固定的處理成本與遠端服務，例如序列化、 網路傳輸和還原序列化的任何互動。 包裝許多不同的交易到單一批次最小化這些。

本文中，我們想要檢查批次的策略和案例的各種 SQL 資料庫。 雖然這些策略也很重要的內部部署的應用程式，使用 SQL Server，有幾個原因醒目提示的批次處理 SQL 資料庫的用途︰

- 有可能會比較高網路延遲存取 SQL 資料庫，尤其是如果您存取 SQL 資料庫從外部相同的 Microsoft Azure 資料中心。
- SQL 資料庫的 multitenant 特性表示資料存取層級與相關聯的資料庫的整體擴充的效率。 SQL 資料庫必須防止任何單一的租用戶/使用者 monopolizing 其他租用戶而妨礙資料庫資源。 使用超過配額的預先定義的回應，SQL 資料庫可以降低處理能力，或使用節流例外回覆。 效率，例如批次處理，可讓您處理更多在 SQL 資料庫之前與這些限制。 
- 批次處理也是有效的架構，將多個資料庫 (sharding)。 您與每個資料庫單位互動的效率仍是您的整體擴充的主要因素。 

使用 SQL 資料庫的優點是，您不必管理主控資料庫伺服器。 不過，此受管理的基礎結構也表示您必須以不同方式思考資料庫最佳化。 您可以不再查詢來改善資料庫硬體或網路基礎結構。 Microsoft Azure 控制這些環境。 您可以控制的主要區域是您的應用程式與 SQL 資料庫之間的互動方式。 批次是一種這些最佳化。 

第一份文件的部分會檢查使用 SQL 資料庫的.NET 應用程式的各種批次技術。 最後的兩個區段包含批次的準則及案例。

## <a name="batching-strategies"></a>批次的策略

### <a name="note-about-timing-results-in-this-topic"></a>請注意關於本主題中的 [預存時間結果
>[AZURE.NOTE] 結果不是基準，但會用來顯示**相對的效能**。 預存時間為基礎的至少 10 個測試執行平均。 作業是插入空白資料表。 這些測試測量的前 V12，而且他們不一定對應到您可能會遇到在 V12 資料庫中使用新的[服務層](sql-database-service-tiers.md)的處理量。 批次的技巧的相對優點應該相似。

### <a name="transactions"></a>交易
看來奇怪開始檢閱討論交易批次。 但使用的用戶端交易精巧的伺服器端批次效果，可改善效能。 與交易可以新增只需幾行程式碼，讓他們提供快速的方法來改善循序進行的作業的效能。

請考慮下列 C# 程式碼包含一系列的插入和更新作業的簡單的表格。

    List<string> dbOperations = new List<string>();
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
    dbOperations.Add("insert MyTable values ('new value',1)");
    dbOperations.Add("insert MyTable values ('new value',2)");
    dbOperations.Add("insert MyTable values ('new value',3)");

下列 ADO.NET 程式碼循序執行這些作業。

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        conn.Open();
    
        foreach(string commandString in dbOperations)
        {
            SqlCommand cmd = new SqlCommand(commandString, conn);
            cmd.ExecuteNonQuery();                   
        }
    }

最佳化將此程式碼的最佳方式就是實作的用戶端批次處理這些呼叫。 但有一個簡單的方法，若要增加只要文繞圖的通話的順序交易中將此程式碼的效能。 以下是使用交易的相同程式碼。

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        conn.Open();
        SqlTransaction transaction = conn.BeginTransaction();
    
        foreach (string commandString in dbOperations)
        {
            SqlCommand cmd = new SqlCommand(commandString, conn, transaction);
            cmd.ExecuteNonQuery();
        }
    
        transaction.Commit();
    }

交易實際中兩個範例正在使用。 在第一個範例中，每個個別的通話會隱含交易。 在第二個範例中，明確交易換行的呼叫。 每個文件[預先寫入的交易記錄檔](https://msdn.microsoft.com/library/ms186259.aspx)，記錄清除磁碟時交易認可。 因此交易中包含更多來電，直到交易的認可可以延遲交易記錄檔寫入。 實際上，您要啟用的伺服器的交易記錄檔寫入批次。

下表顯示一些臨機操作測試結果。 執行相同連續的插入和交易的測試。 更多的觀點來看的第一組測試執行遠端從膝上型電腦至 Microsoft Azure 中的資料庫。 第二輪測試執行從雲端服務，兩者都位於相同的 Microsoft Azure 資料中心 （西適用於美國） 內的資料庫。 下表顯示以毫秒為單位的連續的插入和交易的持續時間。

**內部部署至 Azure**:

| 作業 | 沒有交易 （毫秒） | 交易 （毫秒） |
|---|---|---|
| 1 | 130 | 402 |
| 10 | 1208 | 1226 |
| 100 | 12662 | 10395 |
| 1000 | 128852 | 102917 |


**若要 Azure azure （相同資料中心）**︰

| 作業 | 沒有交易 （毫秒） | 交易 （毫秒） |
|---|---|---|
| 1 | 21 | 26 |
| 10 | 220 | 56 |
| 100 | 2145 | 341 |
| 1000 | 21479 | 2756 |

>[AZURE.NOTE] 結果不是基準測試。 請參閱[本主題中的 [預存時間結果的相關附註](#note-about-timing-results-in-this-topic)。

根據先前的測試結果，一次文繞圖的交易中實際將會減少效能。 但您增加單一交易中作業的數目，效能提升變成其他標記。 Microsoft Azure 資料中心內的所有作業都發生時，也更醒目的通知效能的差異。 使用 SQL 資料庫從 Microsoft Azure 資料中心外的增加的延遲時間遮住使用交易的效能提升。

雖然使用的交易可以增加效能，請繼續[交易和連線的最佳作法可以一同觀看](https://msdn.microsoft.com/library/ms187484.aspx)。 保留為簡短越好，交易，在工作完成後，請關閉資料庫連線]。 在先前範例使用陳述式可確保後續程式碼區塊完成時，會關閉連接。

上一個範例示範，您可以新增本機交易任何 ADO.NET 程式碼，有兩個欄。 交易提供快速的方法，以改善效能的程式碼，可讓您可以插入、 更新及刪除作業。 不過，最快的效能，請考慮利用批次處理用戶端，例如資料表值參數進一步的程式碼的變更。

如需 ADO.NET 交易的詳細資訊，請參閱[ADO.NET 中的本機交易](https://msdn.microsoft.com/library/vstudio/2k2hy99x.aspx)。

### <a name="table-valued-parameters"></a>資料表值參數
資料表值參數 SQL 陳述式、 預存程序和函數中的參數為支援使用者定義的表格類型。 此用戶端批次技巧，可讓您傳送多個資料列中的資料表值參數的資料。 若要使用資料表值參數，請先定義表格類型。 下列 SQL 陳述式中建立名為**MyTableType**表格類型。

    CREATE TYPE MyTableType AS TABLE 
    ( mytext TEXT,
      num INT );
 

程式碼中，您可以建立**資料表**完全相同的名稱與類型的資料表類型。 在 [文字在查詢中的參數傳遞這個**資料表**或預存程序。 下列範例顯示這項技巧︰

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();
    
        DataTable table = new DataTable();
        // Add columns and rows. The following is a simple example.
        table.Columns.Add("mytext", typeof(string));
        table.Columns.Add("num", typeof(int));    
        for (var i = 0; i < 10; i++)
        {
            table.Rows.Add(DateTime.Now.ToString(), DateTime.Now.Millisecond);
        }
    
        SqlCommand cmd = new SqlCommand(
            "INSERT INTO MyTable(mytext, num) SELECT mytext, num FROM @TestTvp",
            connection);
                    
        cmd.Parameters.Add(
            new SqlParameter()
            {
                ParameterName = "@TestTvp",
                SqlDbType = SqlDbType.Structured,
                TypeName = "MyTableType",
                Value = table,
            });
    
        cmd.ExecuteNonQuery();
    }

**Sql 命令**物件在上一個範例中，從資料表值參數，將資料列**@TestTvp**。 先前建立的**資料表**物件**SqlCommand.Parameters.Add**方法指派給此參數。 大幅批次處理一通電話插入一段連續插入增加效能。

若要改善進一步的上一個範例，使用預存程序，而非以文字為基礎的命令。 下列 SQL 命令會建立**SimpleTestTableType**資料表值參數的預存程序。

    CREATE PROCEDURE [dbo].[sp_InsertRows] 
    @TestTvp as MyTableType READONLY
    AS
    BEGIN
    INSERT INTO MyTable(mytext, num) 
    SELECT mytext, num FROM @TestTvp
    END
    GO

變更下列先前的程式碼範例中的**sql 命令**物件宣告。

    SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
    cmd.CommandType = CommandType.StoredProcedure;

在大部分情況下，資料表值參數會有其他批次處理技術比相同或更高的效能。 資料表值參數通常是最好，因為它們是更有彈性比其他選項。 例如，其他技術，例如 SQL 大量複製，只允許插入新列。 但資料表值參數，您可以使用邏輯預存程序來決定哪些資料列會更新，亦即會插入。 您也可以包含 「 作業] 欄，指出是否指定的資料列應該插入、 更新，或刪除修改表格類型。

下表顯示以毫秒為單位的臨機操作的資料表值參數使用的測試結果。

| 作業 | 內部部署至 Azure （毫秒）  | Azure 的相同資料中心 （毫秒） |
|---|---|---|
| 1 | 124 | 32 |
| 10 | 131 | 25 |
| 100 | 338 | 51 |
| 1000 | 2615 | 382 |
| 10000 | 23830 | 3586 |

>[AZURE.NOTE] 結果不是基準測試。 請參閱[本主題中的 [預存時間結果的相關附註](#note-about-timing-results-in-this-topic)。

從批次處理效能提升是明顯。 在先前的循序進行測試，1000年作業所花費的外部資料中心，從資料中心內的 21 秒 129 的秒數。 但資料表值參數，1000年作業秒後才只 2.6 外部資料中心，0.4 秒內的資料中心。

如需資料表值參數的詳細資訊，請參閱[屬性參數](https://msdn.microsoft.com/library/bb510489.aspx)。

### <a name="sql-bulk-copy"></a>SQL 大量複製
SQL 大量複製是插入目標資料庫中的大量資料的另一種方法。 .NET 應用程式可以使用**SqlBulkCopy**類別來執行大量插入作業。 **SqlBulkCopy**就像在函數中的命令列工具、 **Bcp.exe**或 SQL 陳述式，**大量插入**。 下列範例顯示如何大量複製的資料列來源**資料表**，資料表，以在 SQL Server，我的表格中的目的資料表中。

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();
    
        using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connection))
        {
            bulkCopy.DestinationTableName = "MyTable";
            bulkCopy.ColumnMappings.Add("mytext", "mytext");
            bulkCopy.ColumnMappings.Add("num", "num");
            bulkCopy.WriteToServer(table);
        }
    }

有大量複製哪裡慣用資料表值參數在某些情況。 請參閱比較表的資料表值參數，與大量插入主題[屬性參數](https://msdn.microsoft.com/library/bb510489.aspx)的作業。

下列臨機操作測試結果會顯示與**SqlBulkCopy**批次處理以毫秒為單位的效能。

| 作業 | 內部部署至 Azure （毫秒）  | Azure 的相同資料中心 （毫秒） |
|---|---|---|
| 1 | 433 | 57 |
| 10 | 441 | 32 |
| 100 | 636 | 53 |
| 1000 | 2535 | 341 |
| 10000 | 21605 | 2737 |

>[AZURE.NOTE] 結果不是基準測試。 請參閱[本主題中的 [預存時間結果的相關附註](#note-about-timing-results-in-this-topic)。

在較小批次中使用資料表值參數 outperformed **SqlBulkCopy**類別。 不過， **SqlBulkCopy**執行的測試 1000 到 10000 個資料列的資料表值參數比快 12-31%。 資料表值參數，例如**SqlBulkCopy** ，就適合用來批次的插入、 特別是比較非批次作業的效能。

如需有關在 ADO.NET 大量複製的詳細資訊，請參閱[在 SQL Server 中的大量複製作業](https://msdn.microsoft.com/library/7ek5da1a.aspx)。

### <a name="multiple-row-parameterized-insert-statements"></a>多個資料列參數化插入陳述式
一個小批次的替代方案是建構大型參數化的插入陳述式中插入多個資料列。 下列範例會示範這項技巧。

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();
    
        string insertCommand = "INSERT INTO [MyTable] ( mytext, num ) " +
            "VALUES (@p1, @p2), (@p3, @p4), (@p5, @p6), (@p7, @p8), (@p9, @p10)";
    
        SqlCommand cmd = new SqlCommand(insertCommand, connection);
    
        for (int i = 1; i <= 10; i += 2)
        {
            cmd.Parameters.Add(new SqlParameter("@p" + i.ToString(), "test"));
            cmd.Parameters.Add(new SqlParameter("@p" + (i+1).ToString(), i));
        }
    
        cmd.ExecuteNonQuery();
    }
 

此範例中是要顯示的基本概念。 更多實際案例想迴圈必要的項目，以同時建構查詢字串的命令參數。 您是限於 2100年查詢參數的總計，因此此限制可處理這種方式的資料列的總數。

下列臨機操作測試結果會顯示此類型的插入陳述式的效能，以毫秒為單位。

| 作業 | 資料表值參數 （毫秒） | 單一陳述式插入 （毫秒） |
|---|---|---|
| 1 | 32 | 20 |
| 10 | 30 | 25 |
| 100 | 33 | 51 |

>[AZURE.NOTE] 結果不是基準測試。 請參閱[本主題中的 [預存時間結果的相關附註](#note-about-timing-results-in-this-topic)。

這種方法可更快一點批次的小於 100 個資料列。 雖然改進的很小，這個方法是在特定的應用程式案例可能運作的另一個選項。

### <a name="dataadapter"></a>DataAdapter
**DataAdapter**類別可讓您修改**資料集**物件，然後再提交變更為插入、 更新及刪除作業。 如果您使用**DataAdapter**以這種方式，很重要的另一個電話所做的每個不同的作業。 若要改善效能，使用**UpdateBatchSize**屬性一次批次的作業的數目。 如需詳細資訊，請參閱[使用配接執行批次作業](https://msdn.microsoft.com/library/aadf8fk2.aspx)。

### <a name="entity-framework"></a>實體架構
實體架構目前不支援批次。 在社群的開發人員嘗試示範解決方法，例如覆寫**SaveChanges**方法。 但解決方案通常是複雜和自訂應用程式和資料模型。 實體架構 codeplex 專案目前具有此功能要求討論頁面。 若要檢視此討論區，請參閱[設計會議記錄 – 2012 年 8 月 2 日](http://entityframework.codeplex.com/wikipage?title=Design%20Meeting%20Notes%20-%20August%202%2c%202012)。

### <a name="xml"></a>XML
完整性，我們對，是很重要談 XML 為批次的策略。 不過，使用 XML 有任何其他方法的優點與多個缺點。 方法很類似於資料表值參數，但 XML 檔案或字串傳遞給預存程序，而不是使用者定義的資料表。 預存程序會剖析預存程序中的命令。

有多個缺點這種方法︰

- 使用 XML 可麻煩與容易發生錯誤。
- 剖析資料庫 XML，可能會需要大量 CPU。
- 在大部分情況下，這個方法是低於資料表值參數。

基於下列原因，不建議使用 XML 批次查詢。

## <a name="batching-considerations"></a>批次處理考量
下列各節提供更多指示的批次處理 SQL 資料庫應用程式中使用。

### <a name="tradeoffs"></a>必須做的取捨
根據您的結構，批次可能需要權衡效能和恢復。 例如，假設您的角色意外前進向下案例。 如果您遺失了一列的資料，影響小於損失大量批次的未提交的資料列的影響。 當您傳送至指定的時間範圍中的資料庫之前，需要緩衝列時，有較大的風險。

由於此折衷，評估作業的類型，您批次。 批次更積極 （較大的批次和較長的時間 windows） 使用較不重要的資料。

### <a name="batch-size"></a>批次大小
在我們的測試時，通常是沒有好處分成較小的區塊中的大型批次。 事實上，此細分通常會導致比送出單一大型批次的緩慢效能。 例如，請考慮的情況，您要插入 1000 個資料列的位置。 下表顯示使用資料表值參數，若要插入 1000年列分成小批次時所需的時間長度。

| 批次大小 | 高次數 | 資料表值參數 （毫秒） |
| -------- | --- | --- |
| 1000 | 1 | 347 |
| 500 | 2 | 355 |
| 100 | 10 | 465 |
| 50 | 20 | 630 |

>[AZURE.NOTE] 結果不是基準測試。 請參閱[本主題中的 [預存時間結果的相關附註](#note-about-timing-results-in-this-topic)。

您可以看到 1000 個資料列的最佳效能是送出，一次。 在其他測試 （在此未顯示） 中時，將兩個批次的 5000 10000 列批次小型的效能提升。 但這些測試的資料表結構描述是相當簡單，，因此您應該執行測試您的特定資料和批次來確認這些結果。

另一個要考慮的因素是總批次變得過大，如果 SQL 資料庫可能節流，並拒絕認可批次。 為了獲得最佳的結果，測試您的特定案例，判斷是否適合批次大小。 請在執行階段啟用根據效能或錯誤的快速調整哪個批次的大小。

最後，平衡批次的大小與批次處理相關聯的風險。 如果有暫時性錯誤，或角色失敗，請考慮重試作業或遺失批次中的資料的結果。

### <a name="parallel-processing"></a>平行處理
如果您原本減少批次大小的方法，但用於多執行緒執行工作嗎？ 同樣地，我們測試顯示的數個更小的多執行緒批次通常會執行比單一較大批次。 下列測試嘗試插入一或多個平行批次的 1000 個資料列。 這項測試會顯示更多如何同時批次實際減少效能。

| 批次大小 [高次數] | 兩個執行緒 （毫秒） | 四個執行緒 （毫秒） | 六個執行緒 （毫秒） |
| -------- | --- | --- | --- |
| 1000 [1] | 277 | 315 | 266 |
| 500 [2] | 548 | 278 | 256 |
| 250 [4] | 405 | 329 | 265 |
| 100 [10] | 488 | 439 | 391 |

>[AZURE.NOTE] 結果不是基準測試。 請參閱[本主題中的 [預存時間結果的相關附註](#note-about-timing-results-in-this-topic)。

由於平行效能降低 for 幾個可能的原因有︰

- 有多個同時網路來電，而不是一個。
- 針對資料表的多個作業可能會導致競爭並封鎖。
- 有與相關聯的負荷多執行緒處理。
- 開啟多個連線的費用超過平行處理的優點。

如果您要的不同表格或資料庫，則可能會取得這個策略一些效能。 資料庫 sharding 」 或 「 聯盟 」 是這種方法的案例。 Sharding 使用多個資料庫，而且每個資料庫路由不同的資料。 如果每個小批次移至不同的資料庫，然後同時執行作業可能會更有效率。 不過，效能提升不大，作為您的方案中使用資料庫 sharding 決定的基礎。

設計而定，平行小批次會導致改良處理量的要求負載系統。 在此情況下，即使是更快速地處理單一較大批次，處理平行的多個批次可能會更有效率。

如果您使用平行執行，請考慮控制的執行緒數上限。 較小的數字可能會產生更少競爭，更快速的執行時間。 此外，請考慮這會在連線和交易的目標資料庫的其他負載。

### <a name="related-performance-factors"></a>相關的效能的因素
在資料庫效能的一般指導方針也會影響批次。 例如，插入具有大型的主索引鍵或多個非叢集索引的資料表，減少效能。

如果資料表值參數使用預存程序，您可以使用命令**SET NOCOUNT ON**程序的開頭。 此陳述式隱藏的程序中受影響的資料列的計數。 不過，在我們的測試**設定 NOCOUNT ON**使用影響或是效能降低。 測試預存程序很簡單的**插入**單一指令從資料表值參數。 有可能較為複雜的預存程序會因這個陳述式。 但不假設自動新增至您的預存程序的**設定 NOCOUNT ON**可改善效能。 若要瞭解效果，測試預存程序使用，而且沒有**設定 NOCOUNT ON**陳述式。

## <a name="batching-scenarios"></a>批次處理案例
下列各節說明如何使用三個應用程式案例中的資料表值參數。 第一種情況會顯示如何緩衝和批次處理搭配運作。 第二個案例可改善效能，藉由執行 [單一預的存程序中的 [母片詳細資料作業。 最後的案例示範如何使用 「 了 「 作業中的資料表值參數。

### <a name="buffering"></a>緩衝
雖然是明顯候選批次處理某些情況下，有許多情況下，可以利用延遲處理以批次。 不過，延遲的處理也會沿用更大的風險，資料會遺失未預期的錯誤。 請務必瞭解這類風險，考慮的結果。

例如，請考慮追蹤瀏覽歷程記錄的每位使用者的 web 應用程式。 在每個網頁要求時，應用程式無法進行通話錄製的使用者] 頁面檢視資料庫。 但較高的效能與延展性可以達到緩衝使用者的導覽活動，然後將此資料傳送到批次中的資料庫。 您可以依據經過的時間及/或緩衝大小資料庫更新觸發程序。 例如，規則可以指定應該處理 20 的秒數，或當緩衝到達 1000年的項目後的批次。

下列範例會使用[被動式副檔名-接收](https://msdn.microsoft.com/data/gg577609)來處理緩衝引發的監控類別的事件。 當緩衝填滿或逾、 批次使用者資料會傳送至資料表值參數使用的資料庫。

下列 NavHistoryData 類別模型使用者導覽詳細資料。 它包含基本資訊，例如使用者識別碼、 URL 存取，以及存取時間。

    public class NavHistoryData
    {
        public NavHistoryData(int userId, string url, DateTime accessTime)
        { UserId = userId; URL = url; AccessTime = accessTime; }
        public int UserId { get; set; }
        public string URL { get; set; }
        public DateTime AccessTime { get; set; }
    }

NavHistoryDataMonitor 類別負責緩衝使用者瀏覽資料至資料庫。 其中一個方法，RecordUserNavigationEntry，會引發**OnAdded**事件來回應。 下列程式碼會顯示使用接收建立顯著集合根據事件建構函式邏輯。 然後訂閱此顯著集合緩衝方法。 超載指定每 20 秒或 1000年項目，會傳送緩衝。

    public NavHistoryDataMonitor()
    {
        var observableData =
            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");
    
        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);           
    }

處理常式將所有緩衝的項目轉換成表格值的類型，然後將此類型傳遞給處理批次的預存程序。 下列程式碼會顯示 NavHistoryDataEventArgs 和 NavHistoryDataMonitor 課程的完整定義。

    public class NavHistoryDataEventArgs : System.EventArgs
    {
        public NavHistoryDataEventArgs(NavHistoryData data) { Data = data; }
        public NavHistoryData Data { get; set; }
    }
    
    public class NavHistoryDataMonitor
    {
        public event EventHandler<NavHistoryDataEventArgs> OnAdded;
    
        public NavHistoryDataMonitor()
        {
            var observableData =
                Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");
    
            observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);           
        }
    
        public void RecordUserNavigationEntry(NavHistoryData data)
        {    
            if (OnAdded != null)
                OnAdded(this, new NavHistoryDataEventArgs(data));
        }
    
        protected void Handler(IList<EventPattern<NavHistoryDataEventArgs>> items)
        {
            DataTable navHistoryBatch = new DataTable("NavigationHistoryBatch");
            navHistoryBatch.Columns.Add("UserId", typeof(int));
            navHistoryBatch.Columns.Add("URL", typeof(string));
            navHistoryBatch.Columns.Add("AccessTime", typeof(DateTime));
            foreach (EventPattern<NavHistoryDataEventArgs> item in items)
            {
                NavHistoryData data = item.EventArgs.Data;
                navHistoryBatch.Rows.Add(data.UserId, data.URL, data.AccessTime);
            }
    
            using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
            {
                connection.Open();
    
                SqlCommand cmd = new SqlCommand("sp_RecordUserNavigation", connection);
                cmd.CommandType = CommandType.StoredProcedure;
    
                cmd.Parameters.Add(
                    new SqlParameter()
                    {
                        ParameterName = "@NavHistoryBatch",
                        SqlDbType = SqlDbType.Structured,
                        TypeName = "NavigationHistoryTableType",
                        Value = navHistoryBatch,
                    });
    
                cmd.ExecuteNonQuery();
            }
        }
    }

若要使用這個緩衝類別，應用程式建立的靜態 NavHistoryDataMonitor 物件。 使用者存取] 頁面上，每次應用程式呼叫 NavHistoryDataMonitor.RecordUserNavigationEntry 的方法。 緩衝邏輯進行到可將這些項目以批次資料庫。

### <a name="master-detail"></a>母片的詳細資料
資料表值參數的簡單的插入情況。 不過，可能更複雜批次插入包含一個以上的表格。 「 主要 」 案例是很好的範例。 主資料表識別主要的實體。 一或多個詳細資料資料表儲存實體的詳細的資料。 在此案例中，外部索引鍵關聯強制執行詳細資料] 以唯一的主版實體的關係。 請考慮找到資料表和其相關聯的 OrderDetail 資料表的簡化的版。 下列 TRANSACT-SQL 四個資料行建立找到資料表: [訂單編號]、 [訂單日期]、 客戶編號、 和狀態。

    CREATE TABLE [dbo].[PurchaseOrder](
    [OrderID] [int] IDENTITY(1,1) NOT NULL,
    [OrderDate] [datetime] NOT NULL,
    [CustomerID] [int] NOT NULL,
    [Status] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrder] 
    PRIMARY KEY CLUSTERED ( [OrderID] ASC ))

每個訂單包含一或多個產品購買。 這項資訊會擷取 PurchaseOrderDetail 資料表中。 下列 TRANSACT-SQL 五個資料行建立 PurchaseOrderDetail 資料表︰ 訂單編號、 OrderDetailID、 產品識別碼]、 [單價] 和 OrderQty。

    CREATE TABLE [dbo].[PurchaseOrderDetail](
    [OrderID] [int] NOT NULL,
    [OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
    [ProductID] [int] NOT NULL,
    [UnitPrice] [money] NULL,
    [OrderQty] [smallint] NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED 
    ( [OrderID] ASC, [OrderDetailID] ASC ))

PurchaseOrderDetail 資料表中的 [訂單編號] 欄必須參考找到資料表中的順序。 外部索引鍵的下列定義會強制執行這項限制式。

    ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD 
    CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
    REFERENCES [dbo].[PurchaseOrder] ([OrderID])

使用資料表值參數，您必須為每個目標資料表的一個使用者定義的表格類型。

    CREATE TYPE PurchaseOrderTableType AS TABLE 
    ( OrderID INT,
      OrderDate DATETIME,
      CustomerID INT,
      Status NVARCHAR(50) );
    GO
    
    CREATE TYPE PurchaseOrderDetailTableType AS TABLE 
    ( OrderID INT,
      ProductID INT,
      UnitPrice MONEY,
      OrderQty SMALLINT );
    GO

然後定義接受下列類型的資料表的預存程序。 此程序可讓應用程式本機批次的訂單] 和 [訂單詳細資料，在單一通話中的一組。 下列 TRANSACT-SQL 提供這項購買順序範例的完整的預存程序宣告。

    CREATE PROCEDURE sp_InsertOrdersBatch (
    @orders as PurchaseOrderTableType READONLY,
    @details as PurchaseOrderDetailTableType READONLY )
    AS
    SET NOCOUNT ON;
    
    -- Table that connects the order identifiers in the @orders
    -- table with the actual order identifiers in the PurchaseOrder table
    DECLARE @IdentityLink AS TABLE ( 
    SubmittedKey int, 
    ActualKey int, 
    RowNumber int identity(1,1)
    );
     
          -- Add new orders to the PurchaseOrder table, storing the actual
    -- order identifiers in the @IdentityLink table   
    INSERT INTO PurchaseOrder ([OrderDate], [CustomerID], [Status])
    OUTPUT inserted.OrderID INTO @IdentityLink (ActualKey)
    SELECT [OrderDate], [CustomerID], [Status] FROM @orders ORDER BY OrderID;
    
    -- Match the passed-in order identifiers with the actual identifiers
    -- and complete the @IdentityLink table for use with inserting the details
    WITH OrderedRows As (
    SELECT OrderID, ROW_NUMBER () OVER (ORDER BY OrderID) As RowNumber 
    FROM @orders
    )
    UPDATE @IdentityLink SET SubmittedKey = M.OrderID
    FROM @IdentityLink L JOIN OrderedRows M ON L.RowNumber = M.RowNumber;
    
    -- Insert the order details into the PurchaseOrderDetail table, 
          -- using the actual order identifiers of the master table, PurchaseOrder
    INSERT INTO PurchaseOrderDetail (
    [OrderID],
    [ProductID],
    [UnitPrice],
    [OrderQty] )
    SELECT L.ActualKey, D.ProductID, D.UnitPrice, D.OrderQty
    FROM @details D
    JOIN @IdentityLink L ON L.SubmittedKey = D.OrderID;
    GO

在此範例中，在本機上已定義之@IdentityLink表格會儲存到新插入列的實際 [訂單編號] 值。 訂單識別碼有不同的暫時訂單識別碼值@orders和@details資料表值參數。 因此，@IdentityLink表格連線的訂單識別碼值@orders參數找到資料表中的新資料列的實際 [訂單編號] 值。 在此步驟中之後,@IdentityLink表格可以幫助您插入訂單詳細資料，與實際 OrderID 符合外部索引鍵的限制。

從程式碼或其他 TRANSACT-SQL 來電，可以使用此預存程序。 請參閱本文的程式碼範例資料表值參數區段。 下列 TRANSACT-SQL 示範如何呼叫 sp_InsertOrdersBatch。

    declare @orders as PurchaseOrderTableType
    declare @details as PurchaseOrderDetailTableType
    
    INSERT @orders 
    ([OrderID], [OrderDate], [CustomerID], [Status])
    VALUES(1, '1/1/2013', 1125, 'Complete'),
    (2, '1/13/2013', 348, 'Processing'),
    (3, '1/12/2013', 2504, 'Shipped')
    
    INSERT @details
    ([OrderID], [ProductID], [UnitPrice], [OrderQty])
    VALUES(1, 10, $11.50, 1),
    (1, 12, $1.58, 1),
    (2, 23, $2.57, 2),
    (3, 4, $10.00, 1)
    
    exec sp_InsertOrdersBatch @orders, @details

此方案可讓您使用的一組訂單識別碼值從 1 開始的每個批次。 這些暫存的訂單識別碼值說明批次中的關聯，但實際的 [訂單編號] 值決定插入作業的時間。 您可以在先前範例中重複執行相同的陳述式，並產生唯一的訂單資料庫中。 因此，請考慮新增更多程式碼或資料庫的邏輯，使得重複訂單時使用此批次處理技巧。

此範例會示範，可以使用資料表值參數批次處理更複雜的資料庫作業，例如母片詳細資料作業。

### <a name="upsert"></a>了
另一個批次的分析藍本涉及同時更新現有的資料列和插入的新資料列。 這項作業有時也稱為 「 了 」 （更新 + 插入） 作業。 而不進行來插入和更新的另一個電話，請合併陳述式是最適合這項工作。 合併陳述式可以執行兩個插入和更新的單一呼叫的作業。

資料表值參數可用於合併陳述式與執行更新及插入。 例如，請考慮簡化的員工資料表，其中包含下列資料行︰ 員工、 名字、 姓氏、 SocialSecurityNumber:

    CREATE TABLE [dbo].[Employee](
    [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
    [FirstName] [nvarchar](50) NOT NULL,
    [LastName] [nvarchar](50) NOT NULL,
    [SocialSecurityNumber] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED 
    ([EmployeeID] ASC ))
 
在此範例中，您可以使用 SocialSecurityNumber 是唯一執行多位員工的合併列印。 首先，建立使用者定義的表格類型︰

    CREATE TYPE EmployeeTableType AS TABLE 
    ( Employee_ID INT,
      FirstName NVARCHAR(50),
      LastName NVARCHAR(50),
      SocialSecurityNumber NVARCHAR(50) );
    GO

接下來，建立預存程序或撰寫程式碼，若要執行更新並插入使用合併列印陳述式。 下列範例會使用合併列印陳述式資料表值參數，@employees,類型 EmployeeTableType。 內容@employees表格不如下所示。

    MERGE Employee AS target
    USING (SELECT [FirstName], [LastName], [SocialSecurityNumber] FROM @employees) 
    AS source ([FirstName], [LastName], [SocialSecurityNumber])
    ON (target.[SocialSecurityNumber] = source.[SocialSecurityNumber])
    WHEN MATCHED THEN 
    UPDATE SET
    target.FirstName = source.FirstName, 
    target.LastName = source.LastName
    WHEN NOT MATCHED THEN
       INSERT ([FirstName], [LastName], [SocialSecurityNumber])
       VALUES (source.[FirstName], source.[LastName], source.[SocialSecurityNumber]);

如需詳細資訊，請參閱文件與合併陳述式的範例。 雖然中儲存多個步驟來執行相同的工作與程序呼叫分隔插入和更新作業，合併陳述式會更有效率。 資料庫程式碼，也可以建立使用合併列印陳述式直接不需要兩個資料庫呼叫的插入和更新的 TRANSACT-SQL 來電。

## <a name="recommendation-summary"></a>建議摘要

下列清單提供本主題中所討論的批次處理建議摘要︰

- 若要增加的效能與延展性 SQL 資料庫應用程式中使用緩衝和批次處理。
- 了解取捨批次處理/緩衝和恢復。 在角色失敗，遺失的重要資料處理批次的風險所帶來的效能優勢批次。
- 嘗試將以減少延遲的資料庫單一資料中心內的所有呼叫。
- 如果您選擇單一批次技巧，資料表值參數會提供最佳效能和彈性。
- 最快的插入效能，請遵循這些一般指導方針，但測試您的狀況︰
    - < 100 列，使用單一的參數化 [插入] 命令。
    - 針對 < 1000 個資料列，使用資料表值參數。
    - 為 > = 1000年列，使用 SqlBulkCopy。
- 更新和刪除作業，請使用預存程序邏輯決定表格參數中每個資料列的正確操作的資料表值參數。
- 批次大小指導方針︰
    - 使用您的應用程式和業務需求有意義的最大批次大小。
    - [餘額大型批次的風險，暫時或嚴重錯誤的效能的提升。 重試的結果或批次中資料遺失的功能？ 
    - 測試驗證 SQL 資料庫不會拒絕其最大批次大小。
    - 建立該控制項批次，例如批次的大小或緩衝時間範圍設定的設定。 這些設定會提供彈性。 您可以變更批次的行為生產環境中沒有重新部署雲端服務。
- 避免平行執行的上一個資料庫中的單一資料表操作的批次。 如果您選擇要跨多個工作執行緒除一個批次，執行測試，判斷適合的執行緒數目。 未指定的閥值之後, 更多執行緒將會減少效能而增加。
- 請考慮緩衝大小和時間實作的詳細案例批次的方式。

## <a name="next-steps"></a>後續步驟

本文著重於如何資料庫設計和編碼相關批次的技巧可以改善您的應用程式的效能和延展性。 但是，這是您的整體策略只要因素。 改善效能與延展性的其他方法，請參閱[Azure SQL 資料庫效能指導單一資料庫](sql-database-performance-guidance.md)和[彈性的資料庫資料庫的價格和效能考量](sql-database-elastic-pool-guidance.md)。
