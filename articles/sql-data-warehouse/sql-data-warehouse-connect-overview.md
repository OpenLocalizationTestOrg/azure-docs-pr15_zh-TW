<properties
   pageTitle="連線至 SQL Azure Data Warehouse |Microsoft Azure"
   description="如何找到的伺服器名稱與連接字串您至 Azure SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/26/2016"
   ms.author="sonyama;barbkess"/>

# <a name="connect-to-azure-sql-data-warehouse"></a>連線至 SQL Azure Data Warehouse

本文可協助您連線至 SQL Data Warehouse 第一次。

## <a name="find-your-server-name"></a>尋找您的伺服器名稱

連線至 SQL Data Warehouse 的第一個步驟了解如何尋找您的伺服器名稱。  例如，下列範例中的伺服器名稱是 sample.database.windows.net。 若要尋找的完整的伺服器名稱︰

1. 移至[Azure 入口網站][]。
2. 按一下 [ **SQL 資料庫** 
3. 按一下您要連線至資料庫。
4. 找出完整的伺服器名稱。

    ![完整的伺服器名稱][1]

## <a name="supported-drivers-and-connection-strings"></a>支援的驅動程式 」 和 「 連接字串

Azure SQL Data Warehouse 支援[ADO.NET][]、 [ODBC][]、 [PHP][]及[JDBC][]。 按一下 [在其中尋找最新版本和文件的上一個驅動程式。 自動產生的驅動程式，您所使用的連接字串從 Azure 入口網站，您可以按一下**顯示資料庫連接字串**與上述範例。  以下也有一些範例連線字串的每個驅動程式的外觀。

> [AZURE.NOTE] 考慮為 300 秒若要允許不受短的無法使用的連線設定連線逾時。

### <a name="adonet-connection-string-example"></a>ADO.NET 連接字串範例

```C#
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>ODBC 連接字串範例

```C#
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>PHP 連接字串範例

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>JDBC 連接字串範例

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="connection-settings"></a>連線設定

SQL Data Warehouse 期間連線和物件建立標準化部分設定。 無法覆寫這些設定，而且包括︰

| 資料庫設定       | 值                        |
| :--------------------- | :--------------------------- |
| [ANSI_NULLS][]         | 開啟                           |
| [QUOTED_IDENTIFIERS][] | 開啟                           |
| [日期格式][]         | mdy                          |
| [DATEFIRST][]          | 7                            |

## <a name="next-steps"></a>後續步驟

若要為了連接並 Visual Studio 的查詢，請參閱[Visual Studio 查詢][]。 若要瞭解更多有關驗證選項，請參閱[Azure SQL Data Warehouse 驗證][]。

<!--Articles-->
[使用 Visual Studio 的查詢]: ./sql-data-warehouse-query-visual-studio.md
[Azure SQL Data Warehouse 驗證]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[ANSI_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[日期格式]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Azure 入口網站]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/get-server-name.png


