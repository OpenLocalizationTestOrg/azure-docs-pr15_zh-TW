<properties 
    pageTitle="除了 1433 SQL 資料庫的連接埠 |Microsoft Azure"
    description="Azure SQL 資料庫 V12 從 ADO.NET 用戶端連線有時候會略過 proxy，並直接與資料庫互動。 1433 以外的連接埠變得很重要。"
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor="" />


<tags 
    ms.service="sql-database" 
    ms.workload="drivers"
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/17/2016"
    ms.author="annemill"/>


# <a name="ports-beyond-1433-for-adonet-45-and-sql-database-v12"></a>除了 ADO.NET 4.5 及 SQL 資料庫 V12 1433 的連接埠


本主題說明 Azure SQL 資料庫 V12 為使用 ADO.NET 4.5 或更新版本的用戶端連線行為的變更。


## <a name="v11-of-sql-database-port-1433"></a>SQL 資料庫的 V11︰ 連接埠 1433年


當您的用戶端程式會使用 ADO.NET 4.5 連線並 SQL 資料庫 V11 的查詢時，內部順序如下︰


1. ADO.NET 嘗試連線到 SQL 資料庫。

2. ADO.NET 撥打介軟體模組，請使用連接埠 1433年並介軟體連線至 SQL 資料庫。

3. SQL 資料庫傳送其回應傳回給介軟體，轉寄 ADO.NET 連接埠 1433年的回應。


**術語︰**我們說出 ADO.NET 互動 SQL 資料庫使用的*proxy 路由*說明的前面的順序。 如果沒有介軟體所涉及，我們會說使用*直接傳送*。


## <a name="v12-of-sql-database-outside-vs-inside"></a>SQL 資料庫的 V12︰ 與內外部


連線到 V12，我們必須要求要用戶端程式*外*或執行*內*Azure 雲端邊界。 小節討論兩個常見的案例。


#### <a name="outside-client-runs-on-your-desktop-computer"></a>*外部︰*在桌上型電腦上執行的用戶端


連接埠 1433年是唯一的連接埠必須裝載 SQL 資料庫的用戶端應用程式的桌面電腦上開啟。


#### <a name="inside-client-runs-on-azure"></a>*內︰*Azure 上執行，用戶端


當您的用戶端執行 Azure 雲端邊界內時，它會使用我們可以所謂*直接路由*互動的 SQL 資料庫伺服器。 在建立的連線之後，進一步用戶端和資料庫間的互動涉及沒有介軟體 proxy。


順序如下所示︰


1. ADO.NET 4.5 （或更新版本） 啟動 Azure 雲端，簡短的互動，並會接收的動態識別連接埠號碼。
 - 動態識別連接埠號碼位於 11000 11999 或 14000 14999 的範圍。

2. ADO.NET 連線到 SQL 資料庫伺服器直接使用沒有介軟體之間。

3. 查詢會直接傳送至資料庫，並直接到用戶端傳回結果。


請確定範圍 11000 11999 和 14000-14999 Azure 用戶端電腦上會保持適用於 SQL 資料庫 V12 ADO.NET 4.5 用戶端互動的連接埠。

- 特別是您必須是免費的任何其他輸出封鎖範圍中的連接埠。

- 在您的 Azure VM，**具有進階安全性的 Windows 防火牆**控制的連接埠設定。
 - 若要新增的您指定的語法，例如**11000 11999** **TCP**通訊協定以及連接埠範圍規則，您可以使用[防火牆的使用者介面](http://msdn.microsoft.com/library/cc646023.aspx)。


## <a name="version-clarifications"></a>版本說明


本節會釐清 moniker 參照產品版本。 同時也會列出產品版本一些的配對。


#### <a name="adonet"></a>ADO.NET


- ADO.NET 4.0 支援 TDS 7.3 通訊協定，但不是 7.4。
- ADO.NET 4.5 及更新版本支援 TDS 7.4 通訊協定。


#### <a name="sql-database-v11-and-v12"></a>SQL 資料庫 V11 和 V12


本主題中，用戶端連線和之間的差異 SQL 資料庫 V11 V12 會醒目提示。


*附註︰*SQL 陳述式`SELECT @@version;`傳回的值開頭的數字，例如 「 11。 」 或者 「 12。 」，並這些符合我們版本和的名稱 V11 V12 SQL 資料庫。


## <a name="related-links"></a>相關的連結


- ADO.NET 4.6 發行在 2015 年 7 月 20 日。 從.NET 小組部落格公告有[以下](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx)。


- ADO.NET 4.5 發行 2012 年 8 月 15 日。 從.NET 小組部落格公告有[以下](http://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx)。
 - 部落格文章 ado.net 4.5.1 有[以下](http://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx)。


- [TDS 通訊協定版本] 清單](http://www.freetds.org/userguide/tdshistory.htm)


- [SQL 資料庫開發概觀](sql-database-develop-overview.md)


- [Azure SQL 資料庫防火牆](sql-database-firewall-configure.md)


- [如何︰ 設定防火牆設定 SQL 資料庫](sql-database-configure-firewall-settings.md)

