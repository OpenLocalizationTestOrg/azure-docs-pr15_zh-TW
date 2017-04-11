<properties
    pageTitle="為資料庫啟用伸展資料庫 |Microsoft Azure"
    description="瞭解如何設定伸展資料庫的資料庫。"
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
    ms.date="08/05/2016"
    ms.author="douglasl"/>

# <a name="enable-stretch-database-for-a-database"></a>啟用伸展資料庫的資料庫

若要設定伸展資料庫現有的資料庫，請選取 [**工作 |伸展 |啟用**的 SQL Server Management Studio 中可開啟**啟用伸展的資料庫**] 精靈中的資料庫。 您也可以使用 Transact-sql\-SQL 資料庫啟用伸展資料庫。

如果您選取**工作 |伸展 |啟用**的個別的資料表，而且您尚未啟用資料庫伸展資料庫，精靈會設定伸展資料庫的資料庫，並可讓您選取資料表的程序的一部分。 請依照本主題，而不是[啟用伸展資料庫表格](sql-server-stretch-database-enable-database.md)中的步驟進行。

根據資料庫或表格中啟用伸展資料庫需要 db\_擁有者權限。 在資料庫上啟用伸展資料庫也需要控制資料庫的權限。

 >   [AZURE.NOTE] 之後，如果您停用伸展資料庫，請記住，如果是資料表，或為資料庫停用伸展資料庫並不會刪除遠端物件。 如果您想要刪除遠端資料表或遠端資料庫，您必須放使用 Azure 管理入口網站。 遠端物件繼續產生 Azure 成本，直到您手動將其刪除。

## <a name="before-you-get-started"></a>在開始之前

-   設定資料庫的伸展之前，建議您執行來識別資料庫及表格可供伸展伸展資料庫顧問。 伸展資料庫顧問也會識別封鎖的問題。 詳細資訊，請參閱[識別資料庫及伸展資料庫資料表](sql-server-stretch-database-identify-databases.md)。

-   檢閱[限制伸展資料庫](sql-server-stretch-database-limitations.md)。

-   延伸資料庫會移轉至 Azure 的資料。 因此，您必須有 Azure 帳戶與帳單的訂閱。 若要取得 Azure 帳戶，請[按一下這裡](http://azure.microsoft.com/pricing/free-trial/)。

-   有您需要建立新的 Azure 伺服器，或是選取現有的 Azure 伺服器的連線和登入資訊。

## <a name="EnableTSQLServer"></a>必要︰ 啟用伸展資料庫伺服器上
您可以根據資料庫或表格中啟用伸展資料庫之前，必須在本機的伺服器上啟用。 這項作業需要系統管理員或 serveradmin 權限。

-   如果您有必要的系統管理員權限時，**啟用資料庫伸展**精靈就會設定伸展的伺服器。

-   如果您沒有必要的權限，管理員可以執行手動啟用選項**預存程序\_設定**執行精靈，或是系統管理員必須執行精靈。

若要手動啟用伸展資料庫伺服器上，執行**預存程序\_設定**，開啟 [**遠端資料封存**選項。 下列範例會啟用**遠端資料封存**] 選項，以將其值設為 1。

```
EXEC sp_configure 'remote data archive' , '1';
GO

RECONFIGURE;
GO
```
取得詳細資訊，請參閱[設定遠端資料封存伺服器設定] 選項](https://msdn.microsoft.com/library/mt143175.aspx)和[sp_configure (SQL)](https://msdn.microsoft.com/library/ms188787.aspx)。

## <a name="Wizard"></a>使用精靈來啟用伸展資料庫在資料庫
針對啟用資料庫中的 [伸展] 精靈的相關資訊，包括您必須輸入的資訊和選項，您必須進行，請參閱[開始執行啟用資料庫中的 [伸展] 精靈](sql-server-stretch-database-wizard.md)。

## <a name="EnableTSQLDatabase"></a>使用 Transact-sql\-SQL 資料庫上啟用伸展資料庫
您可以在個別的資料表中啟用伸展資料庫之前，必須啟用該資料庫。

根據資料庫或表格中啟用伸展資料庫需要 db\_擁有者權限。 在資料庫上啟用伸展資料庫也需要控制資料庫的權限。

1.  在開始之前，選擇現有的 Azure 伺服器之資料的移轉伸展資料庫，或建立新的 Azure 伺服器。

2.  Azure 伺服器上建立的 SQL Server，讓 SQL Server 進行通訊與遠端伺服器的 IP 位址範圍防火牆規則。

    您可以輕鬆地找到您需要以嘗試連線到 Azure 伺服器物件總管在 SQL Server 管理 Studio (SSMS) 中建立的防火牆規則的值。 SSMS 可協助您建立的規則開啟下列對話方塊中已包含必要的 IP 位址值。

    ![在 SSMS 建立防火牆規則][FirewallRule]

3.  若要設定伸展資料庫的 SQL Server 資料庫，就必須資料庫主索引鍵的資料庫。 資料庫主要金鑰保護伸展資料庫連線到遠端資料庫所使用的認證。 以下是範例會建立新的資料庫主索引鍵。

    ```tsql
    USE <database>;
    GO

    CREATE MASTER KEY ENCRYPTION BY PASSWORD ='<password>';
    GO
    ```

    資料庫的主索引鍵的詳細資訊，請參閱[建立主索引鍵 (SQL)](https://msdn.microsoft.com/library/ms174382.aspx)和[建立資料庫的主索引鍵](https://msdn.microsoft.com/library/aa337551.aspx)。

4.  當您設定資料庫伸展資料庫時，您必須提供的伸展的資料庫，才能使用 SQL Server 上的部署和遠端 Azure 伺服器通訊的認證。 您有兩個選項。

    -   您可以提供系統管理員認證。

        -   如果您啟用伸展資料庫執行精靈時，您可以在該時間來建立認證。

        -   如果您打算要啟用伸展資料庫執行**修改的資料庫**，您必須建立手動之前執行**ALTER DATABASE**啟用伸展資料庫認證。

        以下是範例會建立新的憑證。

        ```tsql
        CREATE DATABASE SCOPED CREDENTIAL <db_scoped_credential_name>
            WITH IDENTITY = '<identity>' , SECRET = '<secret>';
        GO
        ```

        如需更多相關認證的詳細資訊，請參閱[建立資料庫範圍認證 (SQL)](https://msdn.microsoft.com/library/mt270260.aspx)。 建立認證需要更改任何認證權限。

    -   您可以使用 SQL Server 的同盟的服務帳戶下列條件為真時，與遠端 Azure 伺服器通訊。

        -   執行 SQL Server 執行個體的服務帳戶是網域帳戶。

        -   Azure Active Directory 與同盟其 Active Directory 網域所屬的網域帳戶。

        -   遠端 Azure 伺服器設定為支援 Azure Active Directory 驗證。

        -   執行 SQL Server 執行個體的服務帳戶必須設定為 dbmanager 或系統管理員帳戶遠端 Azure 伺服器上。

5.  若要設定伸展資料庫的資料庫，請執行 ALTER DATABASE 命令。

    1.  [伺服器] 引數，提供現有 Azure 伺服器名稱，包括`.database.windows.net`部分名稱\-，例如`MyStretchDatabaseServer.database.windows.net`。

    2.  現有的管理員認證提供的認證引數，或指定聯盟\_服務\_帳戶 = [開啟]。 下列範例會提供現有的認證。

    ```tsql
    ALTER DATABASE <database name>
        SET REMOTE_DATA_ARCHIVE = ON
            (
                SERVER = '<server_name>',
                CREDENTIAL = <db_scoped_credential_name>
            ) ;
    GO
    ```

## <a name="next-steps"></a>後續步驟
-   若要啟用其他資料表[啟用伸展資料庫的資料表](sql-server-stretch-database-enable-table.md)。

-   若要查看的資料移轉狀態[監視器伸展資料庫](sql-server-stretch-database-monitor.md)。

-   [將游標暫停並繼續伸展資料庫](sql-server-stretch-database-pause.md)

-   [管理及疑難排解伸展資料庫](sql-server-stretch-database-manage.md)

-   [備份伸展啟用資料庫](sql-server-stretch-database-backup.md)

## <a name="see-also"></a>另請參閱

[識別資料庫及表格伸展資料庫](sql-server-stretch-database-identify-databases.md)

[ALTER 資料庫設定選項 (TRANSACT-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[FirewallRule]: ./media/sql-server-stretch-database-enable-database/firewall.png
