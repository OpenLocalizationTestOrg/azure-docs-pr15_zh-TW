<properties
   pageTitle="啟用上 Azure SQL Server 伸展資料庫的透明資料加密 (TDE) |Microsoft Azure"
   description="啟用上 Azure SQL Server 伸展資料庫的透明資料加密 (TDE)"
   services="sql-server-stretch-database"
   documentationCenter=""
   authors="douglaslMS"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-server-stretch-database"
   ms.workload="data-management"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="06/14/2016"
   ms.author="douglaslMS"/>

# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure"></a>啟用透明資料加密 (TDE) 上 Azure 伸展的資料庫
> [AZURE.SELECTOR]
- [Azure 入口網站](sql-server-stretch-database-encryption-tde.md)
- [TSQL](sql-server-stretch-database-tde-tsql.md)

透明資料加密 (TDE) 可協助防止惡意活動的潛在威脅執行即時加密和解密資料庫、 相關聯的備份和在其餘的交易記錄檔，而不需要的應用程式的變更。

TDE 加密整個資料庫的儲存空間，使用名為資料庫加密金鑰對稱索引鍵。 資料庫加密金鑰受到內建的伺服器憑證。 內建的伺服器憑證都是唯一的每個 Azure 伺服器。 Microsoft 會自動將這些憑證，至少每個 90 天。 TDE 的一般描述，請參閱[透明資料加密 (TDE)]。

##<a name="enabling-encryption"></a>啟用加密

若要啟用 Azure TDE 儲存資料的資料庫移轉從已啟用伸展的 SQL Server 資料庫，請執行下列動作︰

1. [Azure 入口網站](https://portal.azure.com)中開啟資料庫
2. 在資料庫刀中，按一下 [**設定**] 按鈕
3. 選取 [**透明資料加密**] 選項![][1]
4. 選取**上**設定，然後選取 [**儲存**
![][2]


##<a name="disabling-encryption"></a>停用加密

針對 Azure 停用 TDE 儲存資料的資料庫移轉從已啟用伸展的 SQL Server 資料庫，請執行下列動作︰

1. [Azure 入口網站](https://portal.azure.com)中開啟資料庫
2. 在資料庫刀中，按一下 [**設定**] 按鈕
3. 選取 [**透明資料加密**] 選項
4. 選取 [**關閉**] 設定，然後選取 [**儲存**




<!--Anchors-->
[透明資料加密 (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-server-stretch-database-encryption-tde/stretchtde1.png
[2]: ./media/sql-server-stretch-database-encryption-tde/stretchtde2.png


<!--Link references-->
