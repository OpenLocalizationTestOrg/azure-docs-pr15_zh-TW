<properties
    pageTitle="建立及管理不按比例縮放出 Azure SQL 資料庫時使用彈性工作 |Micosoft Azure"
    description="逐步執行建立和管理的彈性的資料庫作業。"
    services="sql-database"
    documentationCenter=""
    manager="jhubbard"
    authors="ddove"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/27/2016"
    ms.author="ddove"/>

# <a name="create-and-manage-scaled-out-azure-sql-databases-using-elastic-jobs-preview"></a>建立及管理不按比例縮放出 Azure SQL 資料庫時使用彈性的工作 （預覽版本）

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-elastic-jobs-create-and-manage.md)
- [PowerShell](sql-database-elastic-jobs-powershell.md)


**彈性的資料庫作業**簡化管理群組的資料庫執行系統管理作業，例如結構描述變更、 認證管理、 參考資料更新、 收集效能資料或租用戶 （客戶） 遙測集合。 彈性的資料庫作業是目前透過 Azure 入口網站和 PowerShell cmdlet。 不過，Azure 入口網站表面精簡限於執行[彈性的資料庫資料庫 （預覽版本）](sql-database-elastic-pool.md)中的所有資料庫的功能。 若要存取群組包括自訂集合或晶怪集合 （使用[彈性的資料庫用戶端文件庫](sql-database-elastic-scale-introduction.md)建立） 的資料庫中的其他功能並執行指令碼，請參閱[建立和管理工作使用 PowerShell](sql-database-elastic-jobs-powershell.md)。 如需關於工作的詳細資訊，請參閱[彈性的資料庫工作概觀](sql-database-elastic-jobs-overview.md)。 

## <a name="prerequisites"></a>必要條件

* Azure 的訂閱。 免費的試用版，請參閱[一個月免費的試用版](https://azure.microsoft.com/pricing/free-trial/)。
* 彈性的資料庫集區。 請參閱[關於彈性的資料庫集區](sql-database-elastic-pool.md)
* 彈性的資料庫作業服務元件的安裝。 請參閱[安裝彈性的資料庫作業服務](sql-database-elastic-jobs-service-installation.md)。

## <a name="creating-jobs"></a>建立工作

1. 使用[Azure 入口網站](https://portal.azure.com)，如果從現有的彈性的資料庫作業集區，按一下 [**建立工作**。
2. 輸入中的使用者名稱和密碼 （建立在安裝的工作） 的資料庫管理員工作控制資料庫 （為工作的中繼資料儲存區）。

    ![命名的工作，輸入或貼上程式碼，然後按一下 [執行]][1]
2. 在**建立工作**刀中，輸入工作的名稱。
3. 輸入使用者名稱和密碼連線至的目標資料庫有足夠的權限的成功的指令碼執行。
4. 貼上或輸入 T SQL 指令碼。
5. 按一下 [**儲存**]，然後按一下 [**執行**。

    ![建立工作，並執行][5]

## <a name="run-idempotent-jobs"></a>執行冪工作

當您針對一組資料庫執行指令碼時，您必須是確認指令碼冪。 也就是說，指令碼必須能夠執行多次，即使其狀態不完全失敗之前。 例如，指令碼失敗時，工作會自動重試直到成功 （限制，內為重試邏輯會最後停止重試）。 若要執行此動作，方法是使用 「 IF 存在 」 子句之前，刪除任何找到的執行個體建立新的物件。 範例如下所示︰

    IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
    GO
    CREATE INDEX IX_ProductVendor_VendorID
    ON Purchasing.ProductVendor (VendorID);

或者，建立新的執行個體之前，先使用 「 IF 不存在 」 子句︰

    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
     CREATE TABLE TestTable(
      TestTableId INT PRIMARY KEY IDENTITY,
      InsertionTime DATETIME2
     );
    END
    GO

    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO

這個指令碼，然後更新先前建立的表格。

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN

    ALTER TABLE TestTable

    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO


## <a name="checking-job-status"></a>檢查工作狀態

工作已開始之後，您可以檢查其進度。

1. 從 [彈性的資料庫資料庫] 頁面中，按一下 [**管理工作**]。

    ![按一下 [管理工作 」][2]

2. 按一下工作的名稱 (a)。 **狀態**可以 「 完成 」 或 「 失敗 」。 工作的詳細資料出現在其日期與時間的建立和執行 (b)。 下面清單 (c) 顯示針對每個資料庫的指令碼的進度在資料庫中，讓它的日期和時間的詳細資料。

    ![檢查完成的工作][3]


## <a name="checking-failed-jobs"></a>檢查失敗的工作

如果工作失敗，您可以找到它執行的記錄。 按一下 [查看詳細資料失敗的工作名稱]。

![檢查失敗的工作][4]


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/screen-2.png

 
