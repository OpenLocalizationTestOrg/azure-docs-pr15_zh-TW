
<!--
includes/sql-database-include-connection-string-30-compare.md

Latest Freshness check:  2015-09-03 , GeneMi.

## Connection string
-->


### <a name="compare-the-connection-string"></a>比較連線字串


下表將比較 C# 程式所需連線到內部部署的 SQL Server 與您在雲端的 Azure SQL 資料庫連線字串。 差異為粗體。


| 連接字串<br/>Azure SQL 資料庫 | 連接字串<br/>Microsoft SQL Server |
| :-- | :-- |
| 伺服器 =**tcp:**{your_serverName_here}**。 database.windows.net,1433**;<br/>使用者識別碼 = {your_loginName_here}**@{your_serverName_here}**;<br/>密碼 = {your_password_here};<br/>**資料庫 = {your_databaseName_here};**<br/>**連線逾時 = 30**;<br/>**加密 = True**;<br/>**TrustServerCertificate = False**; | 伺服器 = {your_serverName_here};<br/>使用者識別碼 = {your_loginName_here};<br/>密碼 = {your_password_here}; |


**資料庫 =**是選擇性的 SQL Server，但需 SQL 資料庫。


[.NET ADO SqlConnectionStringBuilder 屬性](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder_properties.aspx)-討論詳細資料的所有的參數。


<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
