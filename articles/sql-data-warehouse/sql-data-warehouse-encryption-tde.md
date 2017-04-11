<properties
   pageTitle="在 SQL Data Warehouse （入口網站） 中的透明資料加密 |Microsoft Azure"
   description="在 SQL Data Warehouse 透明資料加密 (TDE)"
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

# <a name="get-started-with-transparent-data-encryption-tde-in-sql-data-warehouse"></a>開始使用透明資料加密 (TDE) 中 SQL Data Warehouse

> [AZURE.SELECTOR]
- [安全性概觀](sql-data-warehouse-overview-manage-security.md)
- [驗證](sql-data-warehouse-authentication.md)
- [加密 （入口網站）](sql-data-warehouse-encryption-tde.md)
- [加密 (T SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permssions"></a>必要的使用權

若要啟用透明資料加密 (TDE)，您必須以系統管理員或 dbmanager 角色的成員。

## <a name="enabling-encryption"></a>啟用加密

若要啟用 TDE 的 SQL Data Warehouse，請遵循下列步驟︰

1. [Azure 入口網站](https://portal.azure.com)中開啟資料庫
2. 在資料庫刀中，按一下 [**設定**] 按鈕
3. 選取 [**透明資料加密**] 選項![][1]
4. 選取 [**上**] 設定![][2]
5. 選取 [**儲存**]
![][3]  

## <a name="disabling-encryption"></a>停用加密

停用 SQL Data Warehouse TDE，請遵循下列步驟︰

1. [Azure 入口網站](https://portal.azure.com)中開啟資料庫
2. 在資料庫刀中，按一下 [**設定**] 按鈕
3. 選取 [**透明資料加密**] 選項![][1]
4. 選取 [**關閉**] 設定![][4]
5. 選取 [**儲存**]
![][5]  

## <a name="encryption-dmvs"></a>加密 Dmv

可以使用下列 Dmv 確認加密︰

- [sys.databases]
- [sys.dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
