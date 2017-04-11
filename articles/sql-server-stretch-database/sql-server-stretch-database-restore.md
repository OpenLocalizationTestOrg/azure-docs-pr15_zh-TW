<properties
    pageTitle="還原伸展啟用資料庫 |Microsoft Azure"
    description="瞭解如何還原伸展\-啟用資料庫。"
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
    ms.topic="article"
    ms.date="08/01/2016"
    ms.author="douglasl"/>

# <a name="restore-stretch-enabled-databases"></a>還原已啟用伸展的資料庫

還原備份的必要時，若要復原有許多類型的錯誤、 錯誤，以及損毀的資料庫。

備份的詳細資訊，請參閱[啟用備份伸展的資料庫](sql-server-stretch-database-backup.md)。

>   [AZURE.NOTE] 備份是只有一個完整的可用性和業務連續性解決方案。 可用性的詳細資訊，請參閱[高可用性解決方案](https://msdn.microsoft.com/library/ms190202.aspx)。

## <a name="restore-your-sql-server-data"></a>還原您的 SQL Server 資料
若要復原的硬體失敗或損毀，還原伸展\-啟用從備份檔案的 SQL Server 資料庫。 您可以繼續使用您目前使用的 SQL Server 還原方法。 詳細資訊，請參閱[還原及修復概觀](https://msdn.microsoft.com/library/ms191253.aspx)。

還原 SQL Server 資料庫後，您必須執行伸展之間的連線，重新建立預存程序**sys.sp_rda_reauthorize_db** \-啟用 SQL Server 資料庫與遠端 Azure 資料庫。 取得詳細資訊，請參閱[還原 SQL Server 資料庫與遠端 Azure 資料庫之間的連線](#restore-the-connection-between-the-sql-server-database-and-the-remote-azure-database)。

## <a name="restore-your-remote-azure-data"></a>還原遠端 Azure 資料

### <a name="recover-a-live-azure-database"></a>復原即時 Azure 資料庫
SQL Server 伸展資料庫服務 Azure 快照上所有的即時資料使用 Azure 儲存空間的快照，至少每個 8 小時。 這些快照會維持 7 天。 這個選項可讓您將還原至其中一個至少 21 點的資料時最後一個快照的時間過去 7 天內的時間。

若要還原即時 Azure 資料庫至之前時間使用 Azure 入口網站，請執行下列項目。

1. Azure 入口網站登入。
2. 在左側的畫面上選取**瀏覽]** ，然後選取**SQL 資料庫**。
3. 瀏覽至您的資料庫並加以選取。
4. 在資料庫刀頂端，按一下 [**還原**]。
5. 指定新的**資料庫名稱**，選取 [**還原點**，再按一下 [**建立**。
6. 資料庫還原程序會開始，並可以使用**通知**監視。

### <a name="recover-a-deleted-azure-database"></a>復原已刪除的 Azure 資料庫
Azure 的 SQL Server 伸展資料庫服務所需的時間資料庫快照之前卸除資料庫，並將其保留 7 天。 之後，它不會保留即時資料庫的快照集。 這個選項可讓您刪除時，點還原已刪除的資料庫。

若要刪除使用 Azure 入口網站時，請點還原已刪除的 Azure 資料庫，請執行下列項目。

1. Azure 入口網站登入。
2. 在左側的畫面上選取**瀏覽]** ，然後選取**SQL Server**。
3. 瀏覽至您的伺服器，並加以選取。
4. 向下捲動至您的伺服器刀作業，按一下 [**刪除資料庫**] 磚。
5. 選取您要還原已刪除的資料庫。
5. 指定新的**資料庫名稱**，然後按一下 [**建立**]。
6. 資料庫還原程序會開始，並可以使用**通知**監視。

## <a name="restore-the-connection-between-the-sql-server-database-and-the-remote-azure-database"></a>還原 SQL Server 資料庫與遠端 Azure 資料庫之間的連線

1.  如果您是連線到還原 Azure 資料庫，以不同的名稱，或在不同區域中，執行預存程序[sys.sp_rda_deauthorize_db](https://msdn.microsoft.com/library/mt703716.aspx)中斷從先前的 Azure 資料庫。  

2.  執行以重新連線本機伸展預存程序[sys.sp_rda_reauthorize_db](https://msdn.microsoft.com/library/mt131016.aspx) \-啟用 Azure 資料庫的資料庫。  

    -   為 role 或 varchar 提供現有的資料庫範圍認證\(128\)值。 \(不使用 varchar\(最大\)。\) 您可以查詢檢視] 中的認證名稱**sys.database\_範圍\_認證**。  

    -   指定是否要複製的遠端資料，並連線到複本 （建議使用）。  

    ```tsql  
    USE <Stretch-enabled database name>;
    GO
    EXEC sp_rda_reauthorize_db
        @credential = N'<existing_database_scoped_credential_name>',
        @with_copy = 1 ;  
    GO
    ```  

## <a name="see-also"></a>另請參閱

[管理及疑難排解伸展資料庫](sql-server-stretch-database-manage.md)

[sys.sp_rda_reauthorize_db (SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[備份及還原的 SQL Server 資料庫](https://msdn.microsoft.com/library/ms187048.aspx)
