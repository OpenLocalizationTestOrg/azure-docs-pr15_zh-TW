<properties
   pageTitle="不支援的 SQL Azure 資料庫 T SQL 中 |Microsoft Azure"
   description="小於完全支援的 Azure SQL 資料庫交易 SQL 陳述式"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/30/2016"
   ms.author="rick.byham@microsoft.com"/>

# <a name="azure-sql-database-transact-sql-differences"></a>Azure SQL 資料庫 TRANSACT-SQL 差異


大部分的應用程式取決於 TRANSACT-SQL 功能支援的 Microsoft SQL Server 和 Azure SQL 資料庫。 應用程式的支援功能的部分清單所示︰

- 資料類型。
- 運算子。
- 字串、 算術、 邏輯和游標函數。

不過，Azure SQL 資料庫的設計隔離從**主**資料庫上的任何相依性的功能。 因此許多的伺服器層級活動不適用於 SQL 資料庫，而且不受支援。 通常 SQL 資料庫中不支援的 SQL Server 中已遭取代的功能。

> [AZURE.NOTE]
> 本主題探討功能，可使用 SQL 資料庫時升級為最新版本。SQL 資料庫 V12。 如需有關 V12 的詳細資訊，請參閱[SQL 資料庫 V12 有什麼新](sql-database-v12-whats-new.md)。

下列各節會列出受到部分支援的功能和不完全受支援的功能。


## <a name="features-partially-supported-in-sql-database-v12"></a>部分支援的 SQL 資料庫 V12 的功能

SQL 資料庫 V12 支援一些但非全部的引數的存在於中的相對應的 SQL Server 2016 SQL 陳述式。 例如，建立程序陳述式是使用，但是建立程序的所有選項都都無法使用。 請參閱支援的每個陳述式區域的相關詳細資料的連結的語法主題。

- 資料庫︰[建立](https://msdn.microsoft.com/library/dn268335.aspx )/[變更](https://msdn.microsoft.com/library/ms174269.aspx)
- Dmv 通常可提供的功能。
- 函式︰[建立](https://msdn.microsoft.com/library/ms186755.aspx)/[改變函數](https://msdn.microsoft.com/library/ms186967.aspx)
- [刪除](https://msdn.microsoft.com/library/ms173730.aspx) 
- 登入︰[建立](https://msdn.microsoft.com/library/ms189751.aspx)/[改變登入](https://msdn.microsoft.com/library/ms189828.aspx)
- 預存程序︰[建立](https://msdn.microsoft.com/library/ms187926.aspx)/[改變程序](https://msdn.microsoft.com/library/ms189762.aspx)
- 表格︰[建立](https://msdn.microsoft.com/library/dn305849.aspx)/[變更](https://msdn.microsoft.com/library/ms190273.aspx)
- 類型 （自訂）︰[建立類型](https://msdn.microsoft.com/library/ms175007.aspx)
- 使用者︰[建立](https://msdn.microsoft.com/library/ms173463.aspx)/[變更使用者](https://msdn.microsoft.com/library/ms176060.aspx)
- 檢視︰[建立](https://msdn.microsoft.com/library/ms187956.aspx)/[改變檢視](https://msdn.microsoft.com/library/ms173846.aspx)

## <a name="features-not-supported-in-sql-database"></a>在 SQL 資料庫中不支援的功能

- 系統物件的定序
- 相關的連線︰ 端點陳述式，ORIGINAL_DB_NAME。 SQL 資料庫不支援 Windows 驗證，但支援類似的 Azure Active Directory 驗證。 某些驗證類型需要 SSMS 的最新版本。 如需詳細資訊，請參閱[連線至 SQL 資料庫或 SQL 資料倉庫來使用 Azure Active Directory 驗證](sql-database-aad-authentication.md)。
- 跨資料庫查詢，使用三或四部分的名稱。 （唯讀跨資料庫查詢支援使用[彈性的資料庫查詢](sql-database-elastic-query-overview.md)）。
- 跨資料庫擁有權鏈結、 值得信任設定
- 資料收集
- 資料庫圖表
- 資料庫的郵件
- DATABASEPROPERTY （改用 DATABASEPROPERTYEX）
- 執行 AS 登入
- 加密︰ 可延伸金鑰管理
- 事件︰ 事件、 事件通知、 查詢通知
- 與資料庫檔案位置、 大小和自動管理的 Microsoft Azure 資料庫檔案相關的功能。
- 高可用性，透過您的 Microsoft Azure 帳戶管理相關的功能︰ 備份、 還原，AlwaysOn 資料庫鏡像、 記錄傳送，修復模式。 如詳細資訊，請參閱 Azure SQL 資料庫備份和還原。
- 執行 SQL 資料庫的記錄讀取所依賴的功能︰ 推播複寫、 變更資料擷取。
- SQL Server 代理程式或 MSDB 資料庫所依賴的功能︰ 工作、 通知、 運算子，原則式管理資料庫郵件、 中央管理伺服器。
- FILESTREAM
- 函式︰ fn_get_sql fn_virtualfilestats、 fn_virtualservernodes
- 全域暫存資料表
- 硬體相關的伺服器設定︰ 記憶體、 執行緒、 CPU 相關性、 追蹤旗標等。請改用服務等級。
- HAS_DBACCESS
- 刪除 STATS 工作
- 連結的伺服器、 openquery 巨集、 OPENROWSET、 OPENDATASOURCE、 大量插入與四部分名稱
- 母片/目標伺服器
- .NET framework[與 SQL Server 的 CLR 整合](http://msdn.microsoft.com/library/ms254963.aspx)
- 資源管理員
- 語意搜尋
- 伺服器認證。 使用資料庫改為範圍的認證。
- 伺服器層級的項目︰ 伺服器角色，IS_SRVROLEMEMBER，sys.login_token。 雖然部分會被取代資料庫層級權限，則無法使用伺服器層級權限。 雖然部分會被取代資料庫層級 Dmv，都無法使用某些伺服器層級 Dmv。
- 無 express: localdb，使用者執行個體
- 服務代理人
- 設定 REMOTE_PROC_TRANSACTIONS
- 關閉
- sp_addmessage
- sp_configure 選項並重新設定。 某些選項，可使用[變更資料庫範圍設定](https://msdn.microsoft.com/library/mt629158.aspx)。
- sp_helpuser
- sp_migrate_user_to_contained
- SQL Server 稽核。 使用 SQL 資料庫改為稽核。
- SQL Server 分析工具
- SQL Server 追蹤
- 追蹤標幟。 某些追蹤標幟的項目已移至 [相容性模式。
- TRANSACT-SQL 偵錯
- 觸發程序︰ 伺服器範圍或登入引動程序
- 使用陳述式︰ 若要變更的資料庫內容到不同的資料庫，您必須進行新連線到新的資料庫。


## <a name="full-transact-sql-reference"></a>完整的 SQL 參考

如需有關 TRANSACT-SQL 文法、 使用方式，與範例的詳細資訊，請參閱 SQL Server 線上活頁簿中的[SQL 參考 （資料庫引擎）](https://msdn.microsoft.com/library/bb510741.aspx) 。 

### <a name="about-the-applies-to-tags"></a>如需 「 適用於 」 的標籤

SQL 參考包含與 SQL Server 2008 至簡報的主題。 主題標題下方圖示列，列出的四個的 SQL Server 平台，而指出是否適。 例如，可用性群組所採用 SQL Server 2012。 [建立 AVAILABILTY 群組](https://msdn.microsoft.com/library/ff878399.aspx)主題表示陳述式套用到 * * SQL Server （從 2012年開始）。 陳述式不適用於 SQL Server 2008、 SQL Server 2008 R2、 Azure SQL 資料庫、 Azure SQL Data Warehouse 或 Parallel Data Warehouse。

在某些情況下，一般主題的主旨使用產品，但有稍微產品之間的差異。 在主題中的適當的中間點表示差異。

