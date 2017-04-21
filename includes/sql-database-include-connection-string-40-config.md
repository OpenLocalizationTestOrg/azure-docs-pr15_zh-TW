
<!--
includes/sql-database-include-connection-string-40-config.md

Latest Freshness check:  2015-09-04 , GeneMi.

## Connection string
-->


### <a name="example-config-file-for-connection-string-security"></a>範例的連線字串安全性的設定檔


這是 unsound 放置成 C# 程式碼的常值的連線字串。 最好是在設定檔中放入連線字串。 那里您可以編輯字串任何不需要重新編譯的時間。

假設您已編譯的 C# 程式為**ConsoleApplication1.exe**，，此.exe 位於 **bin\debug\**目錄。

在此範例中，大部分的組件的連接字串會儲存在名為完全**ConsoleApplication1.exe.config**設定檔。 此設定檔也必須位於 **bin\debug\**。

在下列設定檔的 XML，您會看到名為**ConnectionString4NoUserIDNoPassword**連線字串。 C# 程式碼會尋找這個字串。

您必須編輯之版面配置區中的實際名稱︰

- {your_serverName_here}
- {your_databaseName_here}



        <?xml version="1.0" encoding="utf-8" ?>
        <configuration>
            <startup> 
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
            </startup>
        
            <connectionStrings>
                <clear />
                <add name="ConnectionString4NoUserIDNoPassword"
                providerName="System.Data.ProviderName"
        
                connectionString=
                "Server=tcp:{your_serverName_here}.database.windows.net,1433;
                Database={your_databaseName_here};
                Connection Timeout=30;
                Encrypt=True;
                TrustServerCertificate=False;" />
            </connectionStrings>
        </configuration>



此說明我們選擇省略兩個參數︰

- 使用者識別碼 = {your_userName_here};
- 密碼 = {your_password_here};


您可以包含，但有時候最好是讓您從鍵盤輸入使用者取得這些值的程式。 視而有所不同。



<!--
These three includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-connection-string-20-portalshots.md
includes/sql-database-include-connection-string-30-compare.md
includes/sql-database-include-connection-string-40-config.md
-->
