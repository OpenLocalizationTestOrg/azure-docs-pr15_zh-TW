<properties
    pageTitle="複製 Azure SQL 資料庫 |Microsoft Azure"
    description="建立一份 Azure SQL 資料庫"
    services="sql-database"
    documentationCenter=""
    authors="anosov1960"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/24/2016"
    ms.author="sstein; sashan"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>



# <a name="copy-an-azure-sql-database"></a>複製 Azure SQL 資料庫

> [AZURE.SELECTOR]
- [概觀](sql-database-copy.md)
- [Azure 入口網站](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T SQL](sql-database-copy-transact-sql.md)

您可以使用 Azure [SQL 資料庫自動備份](sql-database-automated-backups.md)的建立 SQL 資料庫的複本。 資料庫複製為地理複寫功能使用相同的技術。 但不同於地理複寫終止複寫連結，以完成種子階段。 因此，複製資料庫是在來源資料庫複製要求的時間的快照。  
您可以在相同的伺服器或其他伺服器上建立資料庫複本。 服務層和效能的層級 （價格層） 的資料庫複本會依預設在來源資料庫相同。 使用時 API，您可以選取同一個服務層級 （版） 中的不同的效能層級。 複製完畢之後，複製會變成功能完整的獨立資料庫。 此時，您可以升級或降級其為任何版本。 登入使用者與權限可獨立管理。  

當您複製到相同的邏輯伺服器的資料庫時，相同的登入能在這兩個資料庫。 安全性原則使用複製資料庫會變成新資料庫的資料庫擁有者 (DBO)。 所有資料庫使用者、 其權限及他們的安全識別項 (Sid) 會複製資料庫複本。  

當您複製到不同的邏輯伺服器的資料庫時的安全性原則新的伺服器上就會變成新資料庫的資料庫擁有者。 如果您使用的 [[包含資料庫使用者](sql-database-manage-logins.md)可確保資料存取主要和次要資料庫永遠具有相同的使用者認證複製完成後，您就可以立即存取其使用相同的認證。 如果您使用[Azure Active Directory](../active-directory/active-directory-whatis.md)，您可以完全排除需要管理認證的複本中。 不過，當您將資料庫複製到新的伺服器時，登入以存取通常無法因為登入將不存在新的伺服器上。 瞭解[如何管理 Azure SQL 資料庫安全性損毀修復後的](sql-database-geo-replication-security-config.md)，若要深入瞭解管理登入，複製到不同的邏輯伺服器的資料庫時。 

若要複製 SQL 資料庫，您需要下列項目︰

- Azure 的訂閱。 如果您需要的 Azure 訂閱，只要按一下 [**免費試用版**頂端的 [此頁面中，，然後回到完成這份文件。
- 若要複製 SQL 資料庫。 如果您沒有 SQL 資料庫，建立一個遵循本文中的步驟︰[建立您的第一個 Azure SQL 資料庫](sql-database-get-started.md)。

## <a name="next-steps"></a>後續步驟

- 請參閱複製資料庫，使用 [Azure 入口網站的 [[複製使用 Azure 入口網站的 Azure SQL 資料庫](sql-database-copy-portal.md)。
- 請參閱使用 PowerShell 將資料庫複製的 [[複製 Azure SQL 資料庫使用 PowerShell](sql-database-copy-powershell.md) 。
- 請參閱[複製 Azure SQL 資料庫使用 T SQL](sql-database-copy-transact-sql.md)複製使用 SQL 資料庫。
- 瞭解[如何管理 Azure SQL 資料庫安全性損毀修復後的](sql-database-geo-replication-security-config.md)，若要深入瞭解管理使用者與登入，複製到不同的邏輯伺服器的資料庫時。



## <a name="additional-resources"></a>其他資源

- [管理登入](sql-database-manage-logins.md)
- [連線至 SQL Server Management Studio 與 SQL 資料庫，並執行範例 T SQL 查詢](sql-database-connect-query-ssms.md)
- [匯出 BACPAC 資料庫](sql-database-export.md)
- [業務連續性概觀](sql-database-business-continuity.md)
- [SQL 資料庫文件](https://azure.microsoft.com/documentation/services/sql-database/)
