<properties
    pageTitle="延伸資料庫概觀 |Microsoft Azure"
    description="瞭解如何伸展資料庫移轉低溫資料無障礙和安全地至 Microsoft Azure 雲端。"
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/27/2016"
    ms.author="douglasl"/>

# <a name="stretch-database-overview"></a>延伸資料庫概觀

延伸資料庫移轉低溫資料無障礙和安全地至 Microsoft Azure 雲端。

如果您只想伸展資料庫立刻開始，請參閱[開始執行啟用資料庫中的 [伸展] 精靈](sql-server-stretch-database-wizard.md)。

## <a name="what-are-the-benefits-of-stretch-database"></a>伸展資料庫的優點是什麼？
延伸資料庫有下列好處︰

### <a name="provides-cost-effective-availability-for-cold-data"></a>提供成本\-有效低溫資料可用性
延伸暖色調陰影和低溫交易資料動態從 SQL Server 到 Microsoft Azure SQL Server 伸展資料庫。 與一般低溫資料的儲存空間，您的資料很線上且有空查詢。 您可以提供更長的時間資料保留時間表，而不會中斷大的表格，例如客戶採購單歷程記錄的銀行。 受益低成本的 Azure 而不是按比例縮放昂貴上\-部署儲存空間。 您選擇價格層，然後在維護成本控制 Azure 入口網站中進行設定。 如有需要請調整向上或向下。 請造訪[SQL Server 伸展資料庫定價](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/)詳細資料] 頁面。

### <a name="doesnt-require-changes-to-queries-or-applications"></a>不需要的變更來查詢或應用程式
存取您的 SQL Server 資料完美無論它是否\-部署或雲端延伸。  您設定原則，以判斷資料儲存，及 SQL Server 控點的背景中的資料移動位置。 整個表格永遠器在線上並且可查詢。 伸展資料庫不需要變更現有的查詢或應用程式 – 資料的位置是完全透明應用程式。

### <a name="streamlines-on-premises-data-maintenance"></a>簡化在\-部署資料進行的維修作業
降低\-部署進行的維修作業，為您的資料的儲存空間。 備份您在\-部署資料執行速度愈進行的維修作業] 視窗內完成。 備份您的資料的雲端部分自動執行。 您在\-大幅降低部署儲存需求。 Azure 儲存空間可新增至 [開啟成本較低的 80%\-部署 SSD。

### <a name="keeps-your-data-secure-even-during-migration"></a>即使在移轉，保留您的資料安全
當您延伸至雲端安全地您最重要的應用程式將很享受和談的想法。 SQL Server 永遠加密提供加密的影片中的資料。 列層級安全性 (RLS) 和其他進階的 SQL Server 安全性功能也使用伸展資料庫來保護您的資料。

## <a name="what-does-stretch-database-do"></a>伸展資料庫做什麼？
啟用伸展資料庫的 SQL Server 執行個體、 資料庫及至少有一個表格之後，伸展資料庫會自動開始，將低溫資料移轉到 Azure。

-   如果您在不同的資料表儲存低溫資料，您可以移轉整個表格。

-   如果您的資料表包含快速鍵和低溫資料，您可以指定以選取要移轉的資料列篩選函數。

**您不需要變更現有的查詢和用戶端應用程式。** 您繼續順暢存取本機與遠端資料，甚至移轉中的資料。 少量的遠端查詢延遲，但您只時，發生此延遲查詢低溫的資料。

如果在移轉過程中發生失敗，請**伸展資料庫可確保不會遺失任何資料**。 也有重試邏輯處理可能會在移轉過程中發生的連線問題。 動態管理檢視提供移轉的狀態。

**您可以將游標暫停於資料移轉**到本機伺服器上的問題進行疑難排解，或最大化可用的網路頻寬。

![延伸資料庫概觀][StretchOverviewImage1]

## <a name="is-stretch-database-for-you"></a>您是伸展資料庫？
如果您可以進行下列陳述式，伸展資料庫協助符合您的需求，解決您的問題。

|如果您是決策人員|如果您是資料庫管理員|
|------------------------------|-------------------|
|我有保留交易資料的較長的時間。|我資料表的大小會收到登出控制項。|
|有時候，我必須查詢低溫的資料。|我的使用者說他們想要存取低溫資料，但它們很少使用。|
|有應用程式，包括較舊的應用程式，不想要更新。|我有保留購買並新增更多儲存空間。|
|我要尋找省錢儲存空間的方式。|我無法備份或還原 SLA 內這類大型的資料表。|

## <a name="what-kind-of-databases-and-tables-are-candidates-for-stretch-database"></a>哪一類的資料庫及表格是伸展資料庫的對象？
將資料庫目標交易資料庫大量低溫資料，通常會儲存在小的數字的資料表。 下列表格可能包含多個億資料列。

如果您使用的 SQL Server 2016 暫時表格功能，請使用縮放資料庫來移轉全部或部分的相關聯的歷程記錄表格成本\-有效 Azure 中的儲存空間。 取得詳細資訊，請參閱[管理保留的歷史表格中的資料系統版本暫時](https://msdn.microsoft.com/library/mt637341.aspx)。

使用縮放資料庫顧問，SQL Server 2016 升級顧問，來識別資料庫及表格伸展資料庫的功能。 詳細資訊，請參閱[識別資料庫及伸展資料庫資料表](sql-server-stretch-database-identify-databases.md)。 若要瞭解關於潛在封鎖問題的詳細資訊，請參閱[伸展資料庫的限制](sql-server-stretch-database-limitations.md)。

## <a name="test-drive-stretch-database"></a>測試磁碟機伸展資料庫
**測試磁碟機伸展資料庫 AdventureWorks 範例資料庫。** 若要取得 AdventureWorks 範例資料庫，至少資料庫檔案和範例和指令碼檔從下載[以下](https://www.microsoft.com/download/details.aspx?id=49502)。 還原範例資料庫的 SQL Server 2016 執行個體之後，將範例檔案解壓縮並開啟伸展 DB 範例檔案從 [伸展 DB] 資料夾。 若要檢查您的資料之前所使用的空間此檔案中執行指令碼，並啟用伸展資料庫，可追蹤資料移轉的進度，並確認已選取之後，您可以繼續查詢現有的資料，並在過程中及資料移轉後，插入新資料。

## <a name="next-step"></a>下一步
**識別資料庫及伸展資料庫的對象的資料表。** 下載 SQL Server 2016 升級顧問並執行伸展資料庫顧問來識別資料庫及伸展資料庫的對象的資料表。 延伸資料庫顧問也會識別封鎖的問題。 詳細資訊，請參閱[識別資料庫及伸展資料庫資料表](sql-server-stretch-database-identify-databases.md)。

<!--Image references-->
[StretchOverviewImage1]: ./media/sql-server-stretch-database-overview/StretchDBOverview.png
[StretchOverviewImage2]: ./media/sql-server-stretch-database-overview/StretchDBOverview1.png
[StretchOverviewImage3]: ./media/sql-server-stretch-database-overview/StretchDBOverview2.png
