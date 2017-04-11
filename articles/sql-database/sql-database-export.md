<properties
    pageTitle="封存 BACPAC 檔案 Azure 入口網站 Azure SQL 資料庫"
    description="封存 BACPAC 檔案 Azure 入口網站 Azure SQL 資料庫"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="08/15/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="archive-an-azure-sql-database-to-a-bacpac-file-using-the-azure-portal"></a>封存 BACPAC 檔案 Azure 入口網站 Azure SQL 資料庫

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)

本文所提供的指示，封存 Azure SQL 資料庫 BACPAC 檔案 （儲存在 Azure blob 儲存體） 使用[Azure 入口網站](https://portal.azure.com)。

當您需要建立 Azure SQL 資料庫的封存時，您可以將資料庫結構描述和資料匯出至 BACPAC 檔案。 只要 ZIP 檔案副檔名為 BACPAC BACPAC 檔案。 Azure blob 儲存體中或本機存放在內部部署的位置，可以稍後再儲存 BACPAC 檔案，並稍後 Azure SQL 資料庫或 SQL Server 匯入的後內部部署安裝。 

***考量***

- 如要交易一致封存，您必須確定任一沒有寫入活動發生期間匯出]，或您要匯出從 Azure SQL 資料庫的[交易一致的複本](sql-database-copy.md)。
- 封存至 Azure Blob 儲存體 BACPAC 檔案的大小上限為 200 GB。 若要封存較大的 BACPAC 檔案本機存放區，請使用[SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx)命令提示字元公用程式。 此公用程式隨附 Visual Studio 和 SQL Server。 您也可以[下載](https://msdn.microsoft.com/library/mt204009.aspx)最新版本的 SQL Server Data Tools，以取得此公用程式。
- 不支援使用 BACPAC 檔案封存到進階版 Azure 儲存體。
- 如果匯出作業超過 20 小時，則可能會取消。 若要增加匯出期間的效能，您可以︰
 - 暫時增加您的服務等級。
 - 停止所有讀取和寫入匯出期間的活動。
 - 使用所有大型資料表上的非 null 值[叢集的索引](https://msdn.microsoft.com/library/ms190457.aspx)。 沒有叢集索引，如果花更長的時間超過 6-12 小時，仍然無法匯出。 這是因為需要完成，嘗試將整個表格匯出資料表掃描匯出服務。 決定是否表格適用於匯出的好方法是執行**DBCC SHOW_STATISTICS** ，並確認*RANGE_HI_KEY*不是空值，且其值具有良好的通訊群組。 如需詳細資訊，請參閱[DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx)。


> [AZURE.NOTE] BACPACs 不打算使用備份與還原作業。 Azure SQL 資料庫會自動建立的每個使用者資料庫的備份。 如需詳細資訊，請參閱[業務連續性概觀](sql-database-business-continuity.md)。

若要完成這份文件您需要下列項目︰

- Azure 的訂閱。
- Azure SQL 資料庫。 
- [Azure 標準儲存體帳戶](../storage/storage-create-storage-account.md)blob 容器儲存 BACPAC 標準的儲存空間。

## <a name="export-your-database"></a>匯出您的資料庫

開啟您想要匯出的資料庫的 SQL 資料庫刀。

> [AZURE.IMPORTANT] 若要確保交易一致的 BACPAC 檔案應該[建立資料庫的複本](sql-database-copy.md)的第一個，然後匯出資料庫複本。 

1.  移至[Azure 入口網站](https://portal.azure.com)。
2.  按一下 [ **SQL 資料庫]**。
3.  按一下要封存的資料庫。
4.  在 [SQL 資料庫刀中，按一下 [**匯出**]，開啟**匯出資料庫**刀︰

    ![[匯出] 按鈕][1]

5.  按一下 [**儲存空間**，然後選取您儲存的帳戶和 blob 的容器 BACPAC 儲存的位置︰

    ![匯出資料庫][2]

6. 選取您的驗證類型。 
7.  輸入包含您要匯出的資料庫的 SQL Azure 伺服器適當的驗證認證。
8.  按一下**[確定**] 以備份資料庫]。 按一下**[確定]**建立匯出資料庫要求，並提交服務。 匯出帶的時間長度，取決於大小和您的資料庫，與您的服務層級的複雜性。 您會收到通知。

    ![匯出通知][3]

## <a name="monitor-the-progress-of-the-export-operation"></a>監控匯出作業的進度

1.  按一下 [ **SQL server**]。
2.  按一下包含您封存 （來源） 資料庫伺服器。
3.  向下作業捲動。
4.  在 SQL server 刀中按一下 [**匯入/匯出歷程記錄**︰

    ![匯入匯出歷程記錄][4]

## <a name="verify-the-bacpac-is-in-your-storage-container"></a>請確認 BACPAC 是在您儲存容器

1.  按一下 [**儲存帳戶**]。
2.  按一下 [儲存 BACPAC 封存的儲存空間帳戶]。
3.  按一下 [**容器**，然後選取 [匯出到資料庫，如需詳細資訊 （您可以下載及儲存從這裡開始 BACPAC） 的容器。

    ![.bacpac 檔案的詳細資料][5]  

## <a name="next-steps"></a>後續步驟

- 若要瞭解如何匯入至 Azure SQL 資料庫的 BACPAC，請參閱[匯入至 Azure SQL 資料庫 BACPCAC](sql-database-import.md)
- 若要進一步瞭解 SQL Server 資料庫匯入 BACPAC，請參閱[匯入到 SQL Server 資料庫 BACPCAC](https://msdn.microsoft.com/library/hh710052.aspx)



<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-notification.png
[4]: ./media/sql-database-export/export-history.png
[5]: ./media/sql-database-export/bacpac-archive.png

