<properties
   pageTitle="Microsoft Power BI 內嵌預覽疑難排解"
   description="Microsoft Power BI 內嵌預覽疑難排解"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="microsoft-power-bi-embedded-preview-troubleshooting"></a>Microsoft Power BI 內嵌預覽疑難排解
本文提供如何疑難排解**Power BI 內嵌**的答案。

<a name="connection-string"/>
## <a name="setting-sql-server-connection-strings"></a>設定 SQL Server 連線字串
若要設定連線字串的 SQL Server，您需要追蹤特定格式。 以下是範例連接字串的 SQL Server。

```
"Persist Security Info=False;Integrated Security=true;Initial Catalog=Northwind;server=(local)"
```

若要進一步瞭解 SQL Server 的連接字串，請參閱下列文章︰

-   [SQL Server 連線字串](https://msdn.microsoft.com/library/jj653752.aspx)
-   [SqlConnection.ConnectionString](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.connectionstring.aspx)

<a name="credentials"/>
## <a name="setting-credentials"></a>設定認證
在您有開發或暫存的環境，例如使用者名稱和密碼，認證的情況下您可能需要更新符合生產解決方案的認證。

## <a name="see-also"></a>另請參閱
- [開始使用範例](power-bi-embedded-get-started-sample.md)
- [什麼是 Power BI 內嵌](power-bi-embedded-what-is-power-bi-embedded.md)
