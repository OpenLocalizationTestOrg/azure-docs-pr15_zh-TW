<properties
   pageTitle="SQL 資料庫的 SQL Server 資料庫移轉 |Microsoft Azure"
   description="瞭解如何在內部部署 SQL Server 資料庫移轉到雲端中的 Azure SQL 資料庫。 使用資料庫移轉工具來測試之前資料庫移轉的相容性。"
   keywords="資料庫移轉、 sql server 資料庫移轉資料庫移轉工具移轉資料庫、 移轉 sql 資料庫"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# <a name="sql-server-database-migration-to-sql-database-in-the-cloud"></a>SQL Server 資料庫移轉到雲端中的 SQL 資料庫

本文中，您瞭解如何將內部部署的 SQL Server 2005 或更新版本的資料庫移轉到 Azure SQL 資料庫。 在 [此資料庫移轉程序中，您移轉您的結構描述和您的資料從 SQL Server 資料庫中您目前的環境，將 SQL 資料庫。 順利完成，現有的資料庫必須先通過的相容性測試。 使用[SQL 資料庫 V12](sql-database-v12-whats-new.md)，有幾個剩餘相容性問題，以外伺服器層級和跨資料庫的作業相關的問題。 資料庫及依賴應用程式[部分或不支援的函數](sql-database-transact-sql-information.md)需要 SQL Server 資料庫可以移轉修正這些不相容部分重新設計。

若要移轉，下列是步驟您需要︰

- **相容性測試**︰ 驗證與[SQL 資料庫 V12](sql-database-v12-whats-new.md)資料庫相容性]。 
- **修正相容性問題，如果有任何**︰ 如果驗證失敗，您必須修正驗證錯誤。  
- **執行移轉**一旦您的資料庫相容，您可以使用一或多個方法來執行移轉。 

SQL Server 提供完成這些工作有數種方法。 本文提供可用的方法，每項工作的概觀。 下圖說明的步驟，並方法。

  ![VSSSDT 移轉圖表](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)
  
 > [AZURE.NOTE] 若要移轉非 SQL Server 資料庫，包括 Microsoft Access、 Sybase、 MySQL Oracle 及 DB2 到 Azure SQL 資料庫，請參閱[SQL Server 移轉小幫手](http://blogs.msdn.com/b/ssma/)。

## <a name="database-migration-tools-test-sql-server-database-compatibility-with-sql-database"></a>資料庫移轉工具測試與 SQL 資料庫的 SQL Server 資料庫相容性

若要開始資料庫移轉程序之前，請測試 SQL 資料庫相容性問題，請使用下列方法之一︰

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [升級指導](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

- [SQL Server Data Tools for Visual Studio (「 SSDT 」)](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): SSDT 使用最新的相容性規則來偵測 SQL 資料庫 V12 不相容。 未偵測到不相容，您可以直接在這項工具來修正偵測到的問題。 這個方法是建議的方法來測試並修正 SQL 資料庫 V12 相容性問題。 
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md): SqlPackage 是命令列公用程式的相容性問題，並產生報告包含偵測到的相容性問題測試。 如果您是使用這項工具，請確定您要使用的最新的相容性規則使用最新版本。 如果未偵測到錯誤，您必須使用另一種工具來修正任何偵測到的相容性問題-SSDT 建議。  
- [在 SQL Server Management Studio 中的匯出資料層應用程式精靈](sql-database-cloud-migrate-determine-compatibility-ssms.md)︰ 此精靈會偵測並在螢幕報告錯誤。 如果未偵測到錯誤，您可以繼續並完成移轉到 SQL 資料庫。 如果未偵測到錯誤，您必須使用另一種工具來修正任何偵測到的相容性問題-SSDT 建議。
- [Microsoft SQL Server 2016 升級顧問 Preview](http://www.microsoft.com/download/details.aspx?id=48119)︰ 這個獨立的工具，目前的預覽，偵測到，並產生的 SQL 資料庫 V12 不相容性報告。 這項工具還沒有最新的相容性規則。 如果未偵測不到任何錯誤，您可以繼續，並完成移轉到 SQL 資料庫。 如果未偵測到錯誤，您必須使用另一種工具來修正任何偵測到的相容性問題-SSDT 建議。 
- [SQL Azure 移轉精靈 (「 SAMW 」)](sql-database-cloud-migrate-fix-compatibility-issues.md): SAMW 是使用 Azure SQL 資料庫 V11 相容性規則來偵測 Azure SQL 資料庫 V12 不相容的 codeplex 工具。 如果未偵測到不相容，可以直接在這項工具中修正的一些問題。 這項工具可能會發現不需要修正的不相容性。 它所提供的第一個的 Azure SQL 資料庫移轉的協助工具，並積極支援的 SQL Server 社群。 此外，這項工具可完成的移轉工具本身內。 

## <a name="fix-database-migration-compatibility-issues"></a>修正資料庫移轉的相容性問題

如果未偵測到的相容性問題，您必須進行 SQL Server 資料庫移轉之前，先修正問題。 有各式各樣的相容性問題，您可能會發生，視兩者的 SQL Server 資料庫，以及您要移轉的資料庫複雜度中的版本。 較舊版本的 SQL Server 有多個相容性問題。 使用下列資源，除了使用您的選擇的搜尋引擎目標網際網路搜尋︰

- [Azure SQL 資料庫中不支援的 SQL Server 資料庫功能](sql-database-transact-sql-information.md)
- [在 SQL Server 2016 中已停用的資料庫引擎功能](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
- [在 SQL Server 2014 中已停用的資料庫引擎功能](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
- [在 SQL Server 2012 中已停用的資料庫引擎功能](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
- [在 SQL Server 2008 R2 中已停用的資料庫引擎功能](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
- [在 SQL Server 2005 中已停用的資料庫引擎功能](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

除了在網際網路上搜尋和使用這些資源，請使用[MSDN SQL Server 社群論壇](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver)或[StackOverflow](http://stackoverflow.com/)。

您可以使用下列資料庫移轉工具來修正偵測到的問題︰

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

- 使用[SQL Server Data Tools for Visual Studio (「 SSDT 」)](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): 若要使用 SSDT，匯入您的資料庫結構描述至 SQL Server Data Tools for Visual Studio 「 SSDT 」)，建立 SQL 資料庫 V12 部署的專案。 然後，您會修正所有偵測到的相容性問題 SSDT 中。 完成時，您同步處理的變更回原始檔資料庫 （或在來源資料庫的複本。 SSDT 目前是建議的方法來測試並修正 SQL 資料庫 V12 相容性問題。 請遵循[逐步解說使用 SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)的連結。
- 使用[SQL Server Management Studio (「 SSMS 」)](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md): 若要使用 SSMS，您可以執行 SQL 命令來修正錯誤偵測到使用另一個工具。 這個方法是主要目的是為了要修改的資料庫結構描述，直接在來源資料庫中的進階使用者。 
- 使用[SQL Azure 移轉精靈 (「 SAMW 」)](sql-database-cloud-migrate-fix-compatibility-issues.md): 若要使用 SAMW，產生 TRANSACT-SQL 指令碼，從來源資料庫。 精靈會轉換指令碼，可能的話，若要讓結構描述和 SQL 資料庫 V12。 完成時，SAMW 可以連線至 SQL 資料庫 V12 執行指令碼。 這項工具也會追蹤檔案來決定相容性問題。 指令碼可以產生只結構描述，或可以包含 BCP 格式的資料。

## <a name="migrate-a-compatible-sql-server-database-to-sql-database"></a>相容的 SQL Server 資料庫移轉到 SQL 資料庫

若要移轉的相容的 SQL Server 資料庫，Microsoft 提供各種情況有數種移轉方法。 您選擇的方法而定的容錯停機時間、 大小和複雜的 SQL Server 資料庫，並連線到 Microsoft Azure 雲端。  

> [AZURE.SELECTOR]
- [SSMS 移轉精靈](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [匯出至 BACPAC 檔案](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [從 BACPAC 檔案匯入](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [交易複寫](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

若要選擇您的移轉方法，要求的第一個問題是如果您能負擔的不在移轉過程生產資料庫。 移轉資料庫時所發生的作用中的交易可以產生資料庫不一致，資料庫可能損毀。 有許多方法靜止的資料庫，停用戶端連線至建立[資料庫的快照](https://msdn.microsoft.com/library/ms175876.aspx)。

若要移轉的最小停機時間，請使用[SQL Server 交易複寫](sql-database-cloud-migrate-compatible-using-transactional-replication.md)，如果您的資料庫中符合交易複寫的需求。 如果可作一些停機時間或更新版本的移轉執行生產資料庫的測試移轉，請考慮下列三種方法之一︰

- [SSMS 移轉精靈](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)︰ 小型中型的資料庫，移轉相容的 SQL Server 2005 或更新版本的資料庫的簡單，只要在 SQL Server Management Studio 中執行[部署至 Microsoft Azure 資料庫] 精靈的資料庫](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)。
- [匯出 BACPAC 檔案](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)，然後[從 BACPAC 檔案匯入](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)︰ 如果您已有連線挑戰 （沒有連線、 低頻寬或逾時問題），並對於大型資料庫中，使用 [ [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)檔案。 使用此方法，您將 SQL Server 結構描述和匯出資料 BACPAC 檔案。 您再匯入 BACPAC 檔案使用中的 [匯出資料層應用程式精靈 SQL Server Management Studio 或[SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx)命令提示字元公用程式的 SQL 資料庫。
- 搭配使用 BACPAC BCP︰ 帶有更複雜達成大於平行增加效能，請使用[BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)檔案和[BCP](https://msdn.microsoft.com/library/ms162802.aspx)較大的資料庫。 使用此方法，將移轉結構描述和資料分開。
 - [匯出至 BACPAC 檔案僅結構描述](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)。
 - 將 SQL 資料庫[匯入僅從 BACPAC 檔案的結構描述](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)。
 - 您可以使用[BCP](https://msdn.microsoft.com/library/ms162802.aspx)來擷取資料到一般檔案，然後再[平行載入](https://technet.microsoft.com/library/dd425070.aspx)到 Azure SQL 資料庫這些檔案。

     ![SQL Server 資料庫移轉-將 SQL 資料庫移轉至雲端。](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

## <a name="next-steps"></a>後續步驟

- [Microsoft SQL Server 2016 升級顧問預覽](http://www.microsoft.com/download/details.aspx?id=48119)
- [最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [最新版本的 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

##<a name="additional-resources"></a>其他資源

- [SQL 資料庫 V12](sql-database-v12-whats-new.md)
[TRANSACT-SQL 部分或不支援的函數](sql-database-transact-sql-information.md)
- [移轉非 SQL Server 資料庫時使用 SQL Server 移轉小幫手](http://blogs.msdn.com/b/ssma/)
