<properties
   pageTitle="開發 U SQL 指令碼的 Visual Studio 中使用資料湖工具 |Azure"
   description="瞭解如何安裝 Visual Studio、 開發及測試 U SQL 指令碼的資料湖工具。 "
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="tutorial-get-started-with-azure-data-lake-analytics-u-sql-language"></a>教學課程︰ 開始使用 Azure 資料湖分析 U SQL 語言

U SQL 是一種語言的統一優點 SQL 使用自己的程式碼處理所有的資料在任何表達能力。 U SQL 的調整分散式的查詢功能可讓您以有效率地分析資料存放區中和關聯式存放區，例如 Azure SQL 資料庫。  可讓您非結構化的資料上套用結構描述閱讀、 插入自訂邏輯和 UDF 的以及包含擴充功能，使資訊粗略的控制如何在執行的程序。 若要進一步瞭解設計的原理 U SQL 前，請參閱此[Visual Studio 部落格文章](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/)。

有一些不同之處，從 ANSI SQL 或 T SQL。 例如，其關鍵字，例如選取必須位於 [全部大寫。

它的類型內 select 子句，在 C# 哪裡述詞等系統和運算式語言。
這表示的資料類型是 C# 類型與資料類型使用 C# NULL 語意，並述詞內的比較作業遵循 C# 語法 （例如，= = 「 foo 」）。  這也表示的值是完整的.NET 物件，讓您輕鬆地使用任何方法來操作物件 (例如 「 f o o o 」。Split(' '))。

如需詳細資訊，請參閱[U SQL 參考](http://go.microsoft.com/fwlink/p/?LinkId=691348)。

###<a name="prerequisites"></a>必要條件

您必須先完成[教學課程︰ 開發 U SQL 指令碼的 Visual Studio 中使用資料湖工具](data-lake-analytics-data-lake-tools-get-started.md)。

在教學課程中，您可以執行資料湖分析工作與下列 U SQL 指令碼︰

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();

這個指令碼沒有任何轉換的步驟。 將其從來源檔名**SearchLog.tsv**讀取、 schematizes，並將資料列輸出至一個稱為**SearchLog 第一個-u sql.csv**檔案。

請注意 [工期] 欄位的資料類型] 旁的問號。 這表示 [工期] 欄位可能是空值。

一些概念和指令碼中找到的關鍵字︰

- **資料列集變數**︰ 產生一個資料列集的每個查詢運算式可以指派給變數。 U SQL 遵循 T SQL 變數命名模式，例如**@searchlog**的指令碼。
    請注意工作分派不會強制執行。 只名稱運算式，並可讓您累積更複雜的運算式。
- **擷取**可讓您能夠讀取定義的結構描述。 結構描述被指定的資料行名稱及 C# 類型名稱組每一個資料行。 它會使用所謂**抽選程式**，例如**Extractors.Tsv()**擷取 tsv 檔案。 您可以開發自訂 extractors。
- **輸出**接受一個資料列集，並將它序列化。 Outputters.Csv() 輸出逗點分隔檔案到指定的位置。 您也可以開發自訂 Outputters。
- 請注意兩個路徑相對路徑。 您也可以使用絕對的路徑。  例如

        adl://<ADLStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

    若要存取連結的儲存空間帳戶中的檔案，您必須使用絕對的路徑。  儲存在連結的 Azure 儲存體帳戶檔案的語法是︰

        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] 目前不支援 azure Blob 容器公用 blob 或公用容器存取權限。

## <a name="use-scalar-variables"></a>使用純量變數

您可以輕鬆地將指令碼進行的維修作業，以及使用純量的變數。 前一個 U SQL 指令碼也可以撰寫如下所示︰

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();

## <a name="transform-rowsets"></a>轉換資料列集

使用 [**選取**要轉換的資料列集︰

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

WHERE 子句中使用[C# 布林運算式](https://msdn.microsoft.com/library/6a71f45d.aspx)。 您可以使用 C# 運算式語言執行您自己的運算式及函數。 您甚至可以執行更複雜的篩選，結合邏輯連接 (ANDs) 與 disjunctions (ORs)。

DateTime.Parse() 方法，並搭配使用，則會使用下列指令碼。

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datatime.csv"
        USING Outputters.Csv();

請注意，第二個查詢運作的第一個資料列的結果，因此結果是兩個篩選的組合。 您也可以重複使用的變數名稱和詞彙性範圍名稱。

## <a name="aggregate-rowsets"></a>彙總的資料列集

U SQL 可讓您熟悉**ORDER BY**、 與**群組依據**的彙總。

下列查詢每個地區，尋找總工期，然後輸出頂端順序 5 工期。

U SQL 資料列集 」 不會保留您的下一個查詢的順序。 因此，若要排序的輸出，您需要新增順序輸出陳述式，如下所示︰

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;

    @res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();
    OUTPUT @res
        TO @out2
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

U SQL ORDER BY 子句必須配合擷取子句中選取的運算式。

有 U SQL 子句可以用於將輸出限制符合 HAVING 條件的群組︰

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

## <a name="join-data"></a>加入資料

U SQL 提供一般的連接運算子，例如內部聯結全向左/聯結、 半聯結加入表格不僅任何資料列集 （即使產生的檔案）。

下列指令碼加入廣告印象記錄檔 searchlog，而且會提供我們給定日期的查詢字串的廣告。

    @adlog =
        EXTRACT UserId int,
                Ad string,
                Clicked int
        FROM "/Samples/Data/AdsLog.tsv"
        USING Extractors.Tsv();

    @join =
        SELECT a.Ad, s.Query, s.Start AS Date
        FROM @adlog AS a JOIN <insert your DB name>.dbo.SearchLog1 AS s
                        ON a.UserId == s.UserId
        WHERE a.Clicked == 1;

    OUTPUT @join   
        TO "/output/Searchlog-join.csv"
        USING Outputters.Csv();


U SQL 僅支援 ANSI 標準聯結語法︰ Rowset1 加入 Rowset2 開啟述詞。 不支援從 Rowset1，Rowset2 哪裡述詞的舊的語法。
聯結述都必須相等聯結與任何運算式。 如果您想要使用運算式，請將其新增至前一個資料列集的 select 子句。 如果您想要執行不同的比較，您可以將其移到 WHERE 子句。


## <a name="create-databases-table-valued-functions-views-and-tables"></a>建立資料庫、 資料表值函式、 檢視和資料表

U SQL 可讓您使用的資料庫和結構描述的內容中的資料。 因此您不必隨時從讀取或寫入檔案。

每個 U SQL 指令碼會以其預設內容執行預設資料庫 （主要） 與預設的結構描述 (DBO)。 您可以建立自己的資料庫及/或結構描述。 若要變更的內容，使用**使用**陳述式變更的內容。


### <a name="create-a-table-valued-function-tvf"></a>建立資料表值函式 (TVF)

在 [上一個 U SQL 指令碼您重複使用解壓縮讀取相同的來源檔案。 U SQL 資料表值函式可讓您封裝日後重複使用的資料。   

下列指令碼會建立預設資料庫和結構描述中名為*Searchlog()* TVF:

    DROP FUNCTION IF EXISTS Searchlog;

    CREATE FUNCTION Searchlog()
    RETURNS @searchlog TABLE
    (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
    )
    AS BEGIN
    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
    RETURN;
    END;

下列指令碼會顯示如何使用 TVF 定義中的上一個指令碼︰

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM Searchlog() AS S
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/SerachLog-use-tvf.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-views"></a>建立檢視

如果您只有一個查詢運算式所要擷取不想將參數化它，您可以建立的檢視，而不是資料表值函式。

下列指令碼會建立一個名*SearchlogView*預設資料庫和結構描述中的檢視︰

    DROP VIEW IF EXISTS SearchlogView;

    CREATE VIEW SearchlogView AS  
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

下列指令碼示範如何使用已定義的檢視︰

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchlogView
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-use-view.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-tables"></a>建立資料表

類似關聯式資料庫的資料表，U SQL 可讓您使用的預先定義的結構描述建立資料表或建立表格並推斷的結構描述中填入表格 （也就是建立資料表另存新檔選取或 CTAS） 的查詢。

下列指令碼會建立資料庫及兩個資料表︰

    DROP DATABASE IF EXISTS SearchLogDb;
    CREATE DATABASE SeachLogDb
    USE DATABASE SearchLogDb;

    DROP TABLE IF EXISTS SearchLog1;
    DROP TABLE IF EXISTS SearchLog2;

    CREATE TABLE SearchLog1 (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string,

                INDEX sl_idx CLUSTERED (UserId ASC)
                    PARTITIONED BY HASH (UserId)
    );

    INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

    CREATE TABLE SearchLog2(
        INDEX sl_idx CLUSTERED (UserId ASC)
                PARTITIONED BY HASH (UserId)
    ) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here


### <a name="query-tables"></a>查詢表格

對資料檔案，您可以為您的查詢相同的方式查詢 （在先前的指令碼中建立） 的資料表。 而非建立使用擷取資料列集，您現在可以只參照資料表名稱。

從資料表讀取修改您在先前所用的轉換指令碼︰

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchLogDb.dbo.SearchLog2
    GROUP BY Region;

    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;

    OUTPUT @res
        TO "/output/Searchlog-query-table.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

請注意，您目前無法執行選取的表格中的指令碼為相同的指令碼讓您建立的表格。


##<a name="conclusion"></a>結束時

在教學課程涵蓋的項目是 U SQL 一小部分。 由於此教學課程的範圍，其無法涵蓋，例如︰

- 使用交叉套用到解壓縮的字串，部分陣列，而且對應到資料列。
- 操作分割的資料集的 （檔案集和分割的資料表）。
- 開發使用者定義的運算子，例如 extractors、 outputters、 處理器、 C# 使用者定義的彙總。
- 使用 U SQL 視窗化函數。
- 管理檢視、 資料表值函式和預存程序的 U SQL 程式碼]。
- 在 [您處理節點上執行任何自訂程式碼。
- 在他們和您 U SQL 和 Azure 資料湖資料，以連線至 Azure SQL 資料庫及 federate 查詢。

## <a name="see-also"></a>另請參閱

- [Microsoft Azure 資料湖分析的概觀](data-lake-analytics-overview.md)
- [開發 U SQL 指令碼的 Visual Studio 中使用資料湖工具](data-lake-analytics-data-lake-tools-get-started.md)
- [Azure 資料湖分析工作使用 U SQL 視窗函數](data-lake-analytics-use-window-functions.md)
- [監控和疑難排解 Azure 資料湖分析工作使用 Azure 入口網站](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

## <a name="let-us-know-what-you-think"></a>讓我們知道您的想法

- [提交功能要求](http://aka.ms/adlafeedback)
- [在論壇中取得說明](http://aka.ms/adlaforums)
- [提供意見反應 U SQL](http://aka.ms/usqldiscuss)
