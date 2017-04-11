<properties
   pageTitle="啟用 Azure TSQL 在 SQL Server 伸展資料庫的透明資料加密 (TDE) |Microsoft Azure"
   description="啟用 Azure TSQL 在 SQL Server 伸展資料庫的透明資料加密 (TDE)"
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
   ms.date="06/14/2016"
   ms.author="douglaslMS"/>

# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Azure (TRANSACT-SQL) 上的縮放資料庫啟用透明資料加密 (TDE)
> [AZURE.SELECTOR]
- [Azure 入口網站](sql-server-stretch-database-encryption-tde.md)
- [TSQL](sql-server-stretch-database-tde-tsql.md)

透明資料加密 (TDE) 可協助防止惡意活動的潛在威脅執行即時加密和解密資料庫、 相關聯的備份和在其餘的交易記錄檔，而不需要的應用程式的變更。

TDE 加密整個資料庫的儲存空間，使用名為資料庫加密金鑰對稱索引鍵。 資料庫加密金鑰受到內建的伺服器憑證。 內建的伺服器憑證都是唯一的每個 Azure 伺服器。 Microsoft 會自動將這些憑證，至少每個 90 天。 TDE 的一般描述，請參閱[透明資料加密 (TDE)]。

##<a name="enabling-encryption"></a>啟用加密

若要啟用 Azure TDE 儲存資料的資料庫移轉從已啟用伸展的 SQL Server 資料庫，請執行下列動作︰

1. 連線至*母片*上使用的是系統管理員或主要資料庫中**dbmanager**角色的成員登入資料庫 Azure 伺服器資料庫
2. 執行下列陳述式，將資料庫加密。

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

##<a name="disabling-encryption"></a>停用加密

針對 Azure 停用 TDE 儲存資料的資料庫移轉從已啟用伸展的 SQL Server 資料庫，請執行下列動作︰

1. 連線至*主版*資料庫使用的是系統管理員或主要資料庫中**dbmanager**角色的成員登入
2. 執行下列陳述式，將資料庫加密。

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

##<a name="verifying-encryption"></a>驗證加密

若要確認加密的狀態，會將資料儲存為 Azure 資料庫移轉從已啟用伸展的 SQL Server 資料庫，執行下列動作︰

1. 連線至*主圖形*，或執行個體資料庫使用的是系統管理員或主要資料庫中**dbmanager**角色的成員登入
2. 執行下列陳述式，將資料庫加密。

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

結果是```1```會指出加密的資料庫，```0```指出非加密的資料庫。


<!--Anchors-->
[透明資料加密 (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
