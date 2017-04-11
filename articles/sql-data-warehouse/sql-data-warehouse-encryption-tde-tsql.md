<properties
   pageTitle="在 SQL Data Warehouse (T SQL) 中的透明資料加密 |Microsoft Azure"
   description="在 SQL Data Warehouse (T SQL) 中的透明資料加密 (TDE)"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/24/2016"
   ms.author="rortloff;barbkess;sonyama"/>

# <a name="get-started-with-transparent-data-encryption-tde"></a>開始使用透明資料加密 (TDE)


> [AZURE.SELECTOR]
- [安全性概觀](sql-data-warehouse-overview-manage-security.md)
- [驗證](sql-data-warehouse-authentication.md)
- [加密 （入口網站）](sql-data-warehouse-encryption-tde.md)
- [加密 (T SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permssions"></a>必要的使用權

若要啟用透明資料加密 (TDE)，您必須以系統管理員或 dbmanager 角色的成員。

## <a name="enabling-encryption"></a>啟用加密

請遵循這些步驟來啟用 SQL Data Warehouse TDE:

1. 連線至*主版*資料庫上使用的是系統管理員或主要資料庫中**dbmanager**角色的成員登入資料庫伺服器
2. 執行下列陳述式，將資料庫加密。

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>停用加密

請遵循下列步驟，停用 SQL Data Warehouse TDE:

1. 連線至*主版*資料庫使用的是系統管理員或母片的資料庫中**dbmanager**角色的成員登入
2. 執行下列陳述式，將資料庫加密。

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [AZURE.NOTE] 變更 TDE 設定之前，必須繼續暫停的 SQL Data Warehouse。

## <a name="verifying-encryption"></a>驗證加密

若要驗證 SQL Data Warehouse 加密的狀態，請遵循下列步驟︰

1. 連線至*母片*或執行個體資料庫使用的是系統管理員或母片的資料庫中**dbmanager**角色的成員登入
2. 執行下列陳述式，將資料庫加密。

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

結果是```1```會指出加密的資料庫，```0```指出非加密的資料庫。

## <a name="encryption-dmvs"></a>加密 Dmv  

- [sys.databases][] 
- [sys.dm_pdw_nodes_database_encryption_keys][]


<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
