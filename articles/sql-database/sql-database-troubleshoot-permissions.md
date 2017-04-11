<properties
    pageTitle="如何執行管理員工作，例如︰ 重設管理員密碼 |Microsoft Azure"
    description="說明如何將 SQL 資料庫中執行一般的系統管理工作。 例如，重設管理員密碼，授與及移除存取。"
    services="sql-database"
    documentationCenter=""
    authors="v-shysun"
    manager="felixwu"
    editor=""
    keywords="重設管理員密碼"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="v-shysun"/>

# <a name="how-to-perform-common-administrative-tasks-such-as-resetting-admin-password-in-azure-sql-database"></a>如何執行一般的系統管理工作，例如重設管理員密碼在 Azure SQL 資料庫
使用本主題的快速步驟來授與及移除 Azure SQL 資料庫的存取權。 更完整的詳細資訊，請參閱︰

- [管理資料庫及 Azure SQL 資料庫中的登入](sql-database-manage-logins.md)
- [保護您的 SQL 資料庫](sql-database-security.md)
- [SQL Server 資料庫引擎和 Azure SQL 資料庫的資訊安全中心](https://msdn.microsoft.com/library/bb510589)


[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="to-reset-admin-password-for-a-logical-server"></a>若要重設管理員密碼邏輯伺服器

- [Azure 入口網站](https://portal.azure.com)中按一下 [ **SQL Server**，從清單中，選取伺服器，然後按一下 [**重設密碼**。

## <a name="to-help-make-sure-only-authorized-ip-addresses-are-allowed-to-access-the-server"></a>若要讓確定只授權的 IP 位址允許存取伺服器
- 請參閱[如何︰ 在 SQL 資料庫設定防火牆](sql-database-configure-firewall-settings.md)。

## <a name="to-create-contained-database-users-in-the-user-database"></a>若要使用者在資料庫中建立包含的資料庫使用者
- 使用[建立使用者](https://msdn.microsoft.com/library/ms173463.aspx)陳述式，請參閱[包含資料庫的使用者-讓您資料庫可攜式](https://msdn.microsoft.com/library/ff929188.aspx)。

## <a name="to-authenticate-contained-database-users-by-using-your-azure-active-directory"></a>若要使用您的 Azure Active Directory 驗證所包含的資料庫使用者
- 請參閱[連線至 SQL 資料庫透過 Azure Active Directory 驗證](sql-database-aad-authentication.md)。

## <a name="to-create-additional-logins-for-high-privileged-users-in-the-virtual-master-database"></a>虛擬主要資料庫中建立其他高權限的使用者登入
- 使用[建立登入](https://msdn.microsoft.com/library/ms189751.aspx)陳述式，請參閱更多詳細資料的 [管理登入] 區段的 [[管理資料庫及 Azure SQL 資料庫中的登入](sql-database-manage-logins.md)。
