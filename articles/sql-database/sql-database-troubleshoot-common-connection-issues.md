<properties
    pageTitle="Azure SQL 資料庫連線常見問題的疑難排解"
    description="找出並解決常見的連線錯誤 Azure SQL 資料庫的步驟。"
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="daleche"/>

# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>疑難排解連線問題到 Azure SQL 資料庫

Azure SQL 資料庫連線失敗時，您會收到[錯誤訊息](sql-database-develop-error-messages.md)。 本文是集中的主題可協助您疑難排解 Azure SQL 資料庫連線發生問題。 它會介紹的連線問題的[常見原因](#cause)，建議[的疑難排解工具](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues)，可協助您識別該問題，並提供解決[暫時性的錯誤](#troubleshoot-transient-errors)和[持續或非暫時性錯誤](#troubleshoot-the-persistent-errors)的疑難排解步驟。 最後，它會列出[所有 Azure SQL 資料庫連線問題的相關文章](#all-topics-for-azure-sql-database-connection-problems)。

如果發生連線問題，請嘗試本文所述的疑難排解步驟。
[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>原因

連線問題可能會因下列任何一項︰

- 若要套用的最佳作法和設計的指導方針應用程式設計程序期間失敗。  請參閱[SQL 資料庫開發概觀](sql-database-develop-overview.md)，即可開始使用。
- Azure SQL 資料庫重新設定
- 防火牆設定
- 連線逾時
- 不正確的登入資訊
- 達 Azure SQL 資料庫資源的最大限制

一般而言，Azure SQL 資料庫連線問題加以分類，如下所示︰

- [暫時性錯誤 （短期或間歇性）](#troubleshoot-transient-errors)
- [持續性或非暫時性錯誤 （定期重複發生的錯誤）](#troubleshoot-the-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>請嘗試 Azure SQL 資料庫連線問題的疑難排解

如果您遇到特定連接錯誤時，嘗試[這項工具](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database)，可協助您快速身分識別和解決問題。

## <a name="troubleshoot-transient-errors"></a>疑難排解暫時性錯誤
如果您的應用程式會發生暫時性錯誤，請檢閱如何疑難排解並降低這些錯誤的相關秘訣下列主題︰

- [疑難排解資料庫&lt;x&gt;在伺服器上&lt;y&gt;無法使用 (錯誤︰ 40613)](sql-database-troubleshoot-connection.md)
- [疑難排解、 診斷，並避免 SQL 連線錯誤和暫時性錯誤 SQL 資料庫](sql-database-connectivity-issues.md)

<a id="troubleshoot-the-persistent-errors" name="troubleshoot-the-persistent-errors"></a>

## <a name="troubleshoot-persistent-errors-non-transient-errors"></a>疑難排解常設錯誤 （非暫時性錯誤）

如果持續無法連線至 Azure SQL 資料庫應用程式，通常表示有問題的下列其中一項︰

- 防火牆設定。 Azure SQL 資料庫或用戶端防火牆封鎖了 Azure SQL 資料庫連線。
- 網路上的用戶端重新設定︰ 例如，新的 IP 位址或 proxy 伺服器。
- 使用者錯誤︰ 例如，輸入錯誤連線參數，例如連線字串中的伺服器名稱。

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>若要解決常設連線問題的步驟

1.  設定[防火牆規則](sql-database-configure-firewall-settings.md)，允許用戶端 IP 位址。
2.  在用戶端與網際網路之間的所有防火牆，請確定連接埠 1433年已開啟的傳出連線。 檢閱其他指標[設定為允許 SQL Server 存取 [Windows 防火牆](https://msdn.microsoft.com/library/cc646023.aspx)。
3.  確認您的連線字串] 和 [其他連線設定。 請參閱[連線問題主題](sql-database-connectivity-issues.md#connections-to-azure-sql-database)中的 [連線字串] 區段。
4.  核取 [服務健康狀況儀表板中。 如果您認為有地區的資料，請參閱[從中斷復原](sql-database-disaster-recovery.md)的步驟來復原至新的區域。

## <a name="all-topics-for-azure-sql-database-connection-problems"></a>所有主題 Azure SQL 資料庫連線問題

下表列出會直接套用至 Azure SQL 資料庫服務每個連線問題主題。


| &nbsp; | 標題 | 描述 |
| --: | :-- | :-- |
| 1 | [疑難排解連線問題到 Azure SQL 資料庫](sql-database-troubleshoot-common-connection-issues.md) | 這是 Azure SQL 資料庫中的連線問題的疑難排解的登陸頁面。 說明如何找出並解決暫時性的錯誤和持續或非暫時性 Azure SQL 資料庫中的錯誤。 |
| 2 | [疑難排解、 診斷，並避免 SQL 連線錯誤和暫時性錯誤 SQL 資料庫](sql-database-connectivity-issues.md) | 瞭解如何疑難排解、 診斷，並避免 SQL 連接錯誤或暫時 Azure SQL 資料庫中的錯誤。 |
| 3 | [一般的暫時性錯誤處理指引](best-practices-retry-general.md) | 連線至 Azure SQL 資料庫時，請提供暫時性錯誤處理的一般指導的方針。 |
| 4 | [疑難排解 Microsoft Azure SQL 資料庫的連線問題](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database) | 這項工具可協助身分識別您的問題解決連線錯誤。 |
| 5 | [疑難排解 「 資料庫&lt;x&gt;在伺服器上&lt;y&gt;目前無法。請再試一次連線稍後 」 錯誤](sql-database-troubleshoot-connection.md) | 說明如何找出並解決 40613 的錯誤: 「 資料庫&lt;x&gt;在伺服器上&lt;y&gt;目前無法。 請重新連線稍後。 」 |
| 6 | [SQL 資料庫用戶端應用程式的 SQL 錯誤碼︰ 資料庫連線錯誤及其他問題](sql-database-develop-error-messages.md) | 提供 SQL 資料庫用戶端應用程式，例如常見的資料庫連線錯誤、 資料庫複製問題及一般錯誤 SQL 錯誤碼的相關資訊。 |
| 7 | [Azure SQL 資料庫效能指導單一資料庫](sql-database-performance-guidance.md) | 提供的指導方針可協助您判斷哪一個服務層級適合您的應用程式。 也提供建議微調] 應用程式，以善用 Azure SQL 資料庫。 |
| 8 | [SQL 資料庫開發概觀](sql-database-develop-overview.md) | 提供各種不同的技術，您可以用來連線到並與其互動 Azure SQL 資料庫的程式碼範例的連結。 |
| 9 | 升級至 Azure SQL 資料庫 v12 頁面 （[Azure 入口網站](sql-database-upgrade-server-portal.md)， [PowerShell](sql-database-upgrade-server-powershell.md)） | 提供指示升級現有的 Azure SQL 資料庫 V11 伺服器和資料庫至 Azure SQL 資料庫 V12 使用 Azure 入口網站或 PowerShell。 |


## <a name="next-steps"></a>後續步驟

- [Azure SQL 資料庫效能問題的疑難排解](sql-database-troubleshoot-performance.md)
- [Azure SQL 資料庫的權限問題進行疑難排解](sql-database-troubleshoot-permissions.md)
- [請參閱 Azure SQL 資料庫服務的所有主題](sql-database-index-all-articles.md)
- [搜尋 Microsoft Azure 上的文件](http://azure.microsoft.com/search/documentation/)
- [檢視最新的更新 Azure SQL 資料庫服務](http://azure.microsoft.com/updates/?service=sql-database)


## <a name="additional-resources"></a>其他資源

- [SQL 資料庫開發概觀](sql-database-develop-overview.md)
- [一般的暫時性錯誤處理指引](../best-practices-retry-general.md)
- [SQL 資料庫與 SQL Server 的連線庫](sql-database-libraries.md)
- [使用 Azure SQL 資料庫的學習路徑](https://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database)
- [使用彈性的資料庫功能及工具學習路徑](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale) 
