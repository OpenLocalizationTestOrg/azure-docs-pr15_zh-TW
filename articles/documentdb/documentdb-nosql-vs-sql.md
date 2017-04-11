<properties
    pageTitle="何時使用 NoSQL 與 SQL |Microsoft Azure"
    description="比較使用 NoSQL 非關聯式解決方案與 SQL 解決方案的優點。 瞭解 Microsoft Azure NoSQL 服務或 SQL Server 最佳是否符合您的狀況。"
    keywords="nosql 與 sql，使用 NoSQL，sql 與 nosql"
    services="documentdb"
    documentationCenter=""
    authors="mimig1"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article" 
    ms.date="06/24/2016"
    ms.author="mimig"/>

# <a name="nosql-vs-sql"></a>NoSQL 與 SQL

SQL Server 和關聯式資料庫 (RDBMS) 是移至資料庫 20 年來。 不過，提高的需要處理較高的區以及各種資料速率已改變的應用程式開發人員資料儲存需求。 若要啟用這種情況下，NoSQL 資料庫，可讓儲存在非結構化及異質性資料得到常用性中。 

NoSQL 是 SQL 資料庫明顯不同的資料庫的類別。 若要參照的資料管理系統 」 不 SQL 」 或包含 「 不只 SQL 」 的資料管理一種方法的通常用於 NoSQL。 有技術 NoSQL 類別，包括文件資料庫、 索引鍵值儲存、 欄家庭存放區及與遊戲，社交，常用的圖形資料庫及 IoT 應用程式中的數字。

![NoSQL 與 SQL 概觀圖表常見的案例與資料模型](./media/documentdb-nosql-vs-sql/nosql-vs-sql-overview.png)

本文的目標是可協助您瞭解 NoSQL 和 SQL，之間的差異，並提供您 NoSQL 和 SQL 售價 microsoft 的簡介。  

## <a name="when-to-use-nosql"></a>何時使用 NoSQL？

例如，假設您建立新的互動社交網站。 使用者可以建立文章，並為其新增圖片、 影片和音樂。 其他使用者可以張貼上註解，並為點 （按讚） 若要對文章。 登陸頁面會有摘要文章的使用者可以共用，並與之互動。 

您要如何儲存此資料？ 如果您熟悉 SQL，您可能會開始繪製像這樣︰

![顯示關聯式資料模型社交互動網站 NoSQL 與 SQL 圖表](./media/documentdb-nosql-vs-sql/nosql-vs-sql-social.png)

最好，但現在想單一的文章，以及如何將其顯示的結構。 如果您想要顯示在網站或應用程式上的文章和相關聯的圖像、 音訊、 視訊、 註解、 點及使用者資訊，您必須執行有八個資料表連接，只是要擷取內容的查詢。 現在，假設的文章，以動態方式載入的畫面上顯示的資料流，您可以輕鬆地預測，它會需要數以千計的查詢和許多連接完成工作。

現在，您可能會使用關聯性的解決方案，例如 SQL Server 來儲存資料-但還有其他選項，可簡化方法 NoSQL 選項。 藉由轉換到 JSON 文件中的文章等動作，將其儲存至 DocumentDB，Azure NoSQL 文件資料庫服務，您可以增加效能並擷取一個查詢與沒有連接的整個文章。 簡單，更直接，並產生更具效能。

    {
        "id":"ew12-res2-234e-544f",
        "title":"post title",
        "date":"2016-01-01",
        "body":"this is an awesome post stored on NoSQL",
        "createdBy":User,
        "images":["http://myfirstimage.png","http://mysecondimage.png"],
        "videos":[
            {"url":"http://myfirstvideo.mp4", "title":"The first video"},
            {"url":"http://mysecondvideo.mp4", "title":"The second video"}
        ],
        "audios":[
            {"url":"http://myfirstaudio.mp3", "title":"The first audio"},
            {"url":"http://mysecondaudio.mp3", "title":"The second audio"}
        ]
    }

此外，此資料可以分割文章識別碼允許自然延展，並利用 NoSQL 比例特性的資料。 也 NoSQL 系統允許放鬆一致性，並提供高度可用的應用程式開發人員]。  最後，此方案不需要開發人員定義、 管理及維護允許快速反覆運算資料層中的結構描述。

然後您就可以建立使用其他 Azure 服務此方案︰

- [Azure 搜尋](https://azure.microsoft.com/services/search/)可透過 web 應用程式，讓使用者可以搜尋文章。
- [Azure 應用程式服務](https://azure.microsoft.com/services/app-service/)可以用於主控應用程式和背景處理程序。
- [Azure Blob 儲存體](https://azure.microsoft.com/services/storage/)可以用來儲存包含圖像的完整的使用者設定檔中。
- [Azure SQL 資料庫](https://azure.microsoft.com/services/sql-database/)可以用於儲存大量資料登入資訊，例如和使用狀況分析資料。
- [Azure 電腦學習](https://azure.microsoft.com/services/machine-learning/)可用於建立知識和智慧的可以提供意見反應程序，並協助正確的使用者提供正確的內容。

此社交互動網站只是其中一個 NoSQL 資料庫是正確的資料模型工作的其中一種情況。 如果您想要進一步瞭解這種情況下，以及如何社交媒體應用程式中的資料模型的 DocumentDB 讀取，請參閱[將 DocumentDB 與社交](documentdb-social-media-apps.md)。 

## <a name="nosql-vs-sql-comparison"></a>NoSQL 與 SQL 比較

下表將比較 NoSQL 與 SQL 的主要差異。 

![顯示 NoSQL 的使用時機與使用 SQL NoSQL 與 SQL 圖表。 SQL 與 NoSQL 比較](./media/documentdb-nosql-vs-sql/nosql-vs-sql-comparison.png)

如果 NoSQL 資料庫最適合您的需求，請繼續下一節，若要進一步瞭解可從 Azure NoSQL 服務。 如果 SQL 資料庫最適合您的需求，否則，請跳至[什麼是 Microsoft SQL 售價？](#what-are-the-microsoft-sql-offerings)

## <a name="what-are-the-microsoft-azure-nosql-offerings"></a>什麼是 Microsoft Azure NoSQL 售價？

Azure 有四個完全管理 NoSQL 服務︰ 

- [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)
- [Azure 資料表儲存體](https://azure.microsoft.com/services/storage/)
- [Azure HBase HDInsight 的一部分](https://azure.microsoft.com/services/hdinsight/)
- [Azure 意指快取](https://azure.microsoft.com/services/cache/)

下列比較表地圖查看每個服務的重要的區別因素。 哪一種最能正確說明應用程式的需求？ 

![NoSQL 與 SQL 圖表顯示何時使用從 Microsoft Azure HDInsight，屬於包括 DocumentDB、 資料表儲存體 HBase NoSQL 方案，並 Redis 快取](./media/documentdb-nosql-vs-sql/nosql-vs-sql-documentdb-storage-hbase-hdinsight-redis-cache.png)

如果一或多個這些服務可能符合您的應用程式的需求，請深入瞭解使用下列資源︰ 

- [DocumentDB 學習路徑](https://azure.microsoft.com/documentation/learning-paths/documentdb/)和[DocumentDB 使用案例](documentdb-use-cases.md)
- [開始使用 Azure 資料表儲存體](../storage/storage-dotnet-how-to-use-tables.md)
- [什麼是 HBase HDInsight 中](../hdinsight/hdinsight-hbase-overview.md)
- [Redis 快取學習路徑](https://azure.microsoft.com/documentation/learning-paths/redis-cache/)

然後移至[下一個步驟](#next-steps)的免費試用版的資訊。

## <a name="what-are-the-microsoft-sql-offerings"></a>什麼是 Microsoft SQL 售價？

Microsoft 有五個 SQL 方案︰ 

- [Azure SQL 資料庫](https://azure.microsoft.com/services/sql-database/)
- [SQL Server Azure 虛擬機器上](https://azure.microsoft.com/services/virtual-machines/sql-server/)
- [SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-2016/)
- [Azure SQL Data Warehouse （預覽版本）](https://azure.microsoft.com/services/sql-data-warehouse/)
- [分析平台系統 （內部部署應用裝置）](https://www.microsoft.com/en-us/server-cloud/products/analytics-platform-system/)

如果您有興趣的虛擬機器或 SQL 資料庫的 SQL Server，然後閱讀[選擇雲端 SQL Server] 選項: (PaaS) 的 Azure SQL 資料庫或 SQL Server Azure Vm (IaaS) 上](../sql-database/sql-database-paas-vs-sql-server-iaas.md)若要進一步瞭解這兩個之間的差異。

如果 SQL 聽起來最佳的選項，然後移至要進一步瞭解 Microsoft SQL 產品和服務必須什麼提供的[SQL Server](https://www.microsoft.com/server-cloud/products/) 。

然後移至[下一個步驟](#next-steps)免費試用版] 和 [評估] 連結。

## <a name="next-steps"></a>後續步驟

若要進一步瞭解 SQL 和 NoSQL 產品來試用這些免費您邀請。 

- 如需所有 Azure 服務，您可以註冊[免費試用一個月](https://azure.microsoft.com/pricing/free-trial/)的，和接收 $200 在任何 Azure 服務。
    - [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)
    - [Azure HBase HDInsight 的一部分](https://azure.microsoft.com/services/hdinsight/)
    - [Azure 意指快取](https://azure.microsoft.com/services/cache/)
    - [Azure SQL Data Warehouse （預覽版本）](https://azure.microsoft.com/services/sql-data-warehouse/)
    - [Azure SQL 資料庫](https://azure.microsoft.com/services/sql-database/)
    - [Azure 資料表儲存體](https://azure.microsoft.com/services/storage/)

- 您可以[評估版本的 SQL Server 2016 虛擬機器](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2016ctp33evaluationwindowsserver2012r2/)微調或下載的[評估版本的 SQL Server](https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2016)。
    - [SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-2016/)
    - [SQL Server Azure 虛擬機器上](https://azure.microsoft.com/services/virtual-machines/sql-server/)

