<properties
    pageTitle="嘗試 SQL 資料庫︰ 使用 C# 建立 SQL 資料庫 |Microsoft Azure"
    description="試試看 SQL 資料庫開發 SQL 及 C# 應用程式，並使用 C#.net 使用 SQL 資料庫文件庫建立 Azure SQL 資料庫。"
    keywords="請嘗試 sql，sql c#"   
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="csharp"
   ms.workload="data-management"
   ms.date="10/04/2016"
   ms.author="sstein"/>

# <a name="try-sql-database-use-c-to-create-a-sql-database-with-the-sql-database-library-for-net"></a>嘗試 SQL 資料庫︰ 使用 C#.NET 與 SQL 資料庫文件庫建立 SQL 資料庫


> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

瞭解如何使用 C# 與[Microsoft Azure SQL 管理文件庫的.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)建立 Azure SQL 資料庫。 本文將說明如何使用 SQL 及 C# 建立單一資料庫。 若要建立彈性的資料庫集區，請參閱[建立彈性的資料庫資料庫](sql-database-elastic-pool-create-csharp.md)。

Azure SQL 資料庫管理文件庫的.NET 提供[Azure 資源管理員](../azure-resource-manager/resource-group-overview.md)-基礎換行[資源管理員型 SQL 資料庫 REST API](https://msdn.microsoft.com/library/azure/mt163571.aspx)的 API。

>[AZURE.NOTE] 您使用的[Azure 資源管理員部署模型](../azure-resource-manager/resource-group-overview.md)，因此您應該一律使用最新版時，才支援的 SQL 資料庫的許多新功能**Azure SQL 資料庫管理文件庫的.NET ([文件](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [NuGet 套件](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**。 [傳統的部署模型基礎的文件庫](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql)的較舊版本支援的回溯相容性，因此我們建議您使用較新的資源管理員根據文件庫。

若要完成此文件中的步驟進行，您需要下列項目︰

- Azure 的訂閱。 如果您需要的 Azure 訂閱，只要按一下 [**免費的帳戶**] 頁面的頂端，然後回到完成這份文件。
- Visual Studio 中。 Visual Studio 免費複本，請參閱[Visual Studio 下載](https://www.visualstudio.com/downloads/download-visual-studio-vs)頁面。

>[AZURE.NOTE] 本文會建立新的空白 SQL 資料庫。 修改*CreateOrUpdateDatabase(...)*方法在下列範例中複製的資料庫、 不按比例縮放資料庫、 資料庫等建立資料庫。如需詳細資訊，請參閱[DatabaseCreateMode](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databasecreatemode.aspx)和[DatabaseProperties](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databaseproperties.aspx)類別。



## <a name="create-a-console-app-and-install-the-required-libraries"></a>建立主控台應用程式，並安裝所需的文件庫

1. 啟動 [Visual Studio。
2. 按一下 [**檔案** > **新** > **專案**。
3. 建立 C#**主控台應用程式**，並將其命名︰ *SqlDbConsoleApp*


若要建立 SQL 資料庫與 C#，載入所需的管理文件庫 （使用[套件管理員主控台](http://docs.nuget.org/Consume/Package-Manager-Console)）︰

1. 按一下 [**工具]** > **NuGet 封裝管理員** > **封裝管理員主控台**。
2. 輸入`Install-Package Microsoft.Azure.Management.Sql –Pre`安裝最新的[Microsoft Azure SQL 管理文件庫](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)。
3. 輸入`Install-Package Microsoft.Azure.Management.ResourceManager –Pre`安裝[Microsoft Azure 資源管理員文件庫](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)。
4. 輸入`Install-Package Microsoft.Azure.Common.Authentication –Pre`安裝[Microsoft Azure 一般驗證文件庫](https://www.nuget.org/packages/Microsoft.Azure.Common.Authentication)。 



> [AZURE.NOTE] 本文中的範例使用的每個 API 邀請同步表單，並封鎖直到完成其餘的通話基礎的服務。 有可用的非同步方法。


## <a name="create-a-sql-database-server-firewall-rule-and-sql-database---c-example"></a>建立 SQL 資料庫伺服器的防火牆規則與 SQL 資料庫-C# 範例

下列範例會建立資源群組、 伺服器、 防火牆規則，以及 SQL 資料庫。 請參閱[建立主要存取資源的服務](#create-a-service-principal-to-access-resources)]，以取得`_subscriptionId, _tenantId, _applicationId, and _applicationSecret`變數。

使用下列步驟，取代**Program.cs**的內容，並更新`{variables}`與您的應用程式值 (不包括`{}`)。


    using Microsoft.Azure;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    namespace SqlDbConsoleApp
    {
    class Program
       {
        // For details about these four (4) values, see
        // https://azure.microsoft.com/documentation/articles/resource-group-authenticate-service-principal/
        static string _subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _tenantId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationSecret = "{your-password}";

        // Create management clients for the Azure resources your app needs to work with.
        // This app works with Resource Groups, and Azure SQL Database.
        static ResourceManagementClient _resourceMgmtClient;
        static SqlManagementClient _sqlMgmtClient;

        // Authentication token
        static AuthenticationResult _token;

        // Azure resource variables
        static string _resourceGroupName = "{resource-group-name}";
        static string _resourceGrouplocation = "{Azure-region}";

        static string _serverlocation = _resourceGrouplocation;
        static string _serverName = "{server-name}";
        static string _serverAdmin = "{server-admin-login}";
        static string _serverAdminPassword = "{server-admin-password}";

        static string _firewallRuleName = "{firewall-rule-name}";
        static string _startIpAddress = "{0.0.0.0}";
        static string _endIpAddress = "{255.255.255.255}";

        static string _databaseName = "{dbfromcsarticle}";
        static string _databaseEdition = DatabaseEditions.Basic;
        static string _databasePerfLevel = ""; // "S0", "S1", and so on here for other tiers


        static void Main(string[] args)
        {
            // Authenticate:
            _token = GetToken(_tenantId, _applicationId, _applicationSecret);
            Console.WriteLine("Token acquired. Expires on:" + _token.ExpiresOn);

            // Instantiate management clients:
            _resourceMgmtClient = new ResourceManagementClient(new Microsoft.Rest.TokenCredentials(_token.AccessToken));
            _sqlMgmtClient = new SqlManagementClient(new TokenCloudCredentials(_subscriptionId, _token.AccessToken));


            Console.WriteLine("Resource group...");
            ResourceGroup rg = CreateOrUpdateResourceGroup(_resourceMgmtClient, _subscriptionId, _resourceGroupName, _resourceGrouplocation);
            Console.WriteLine("Resource group: " + rg.Id);


            Console.WriteLine("Server...");
            ServerGetResponse sgr = CreateOrUpdateServer(_sqlMgmtClient, _resourceGroupName, _serverlocation, _serverName, _serverAdmin, _serverAdminPassword);
            Console.WriteLine("Server: " + sgr.Server.Id);

            Console.WriteLine("Server firewall...");
            FirewallRuleGetResponse fwr = CreateOrUpdateFirewallRule(_sqlMgmtClient, _resourceGroupName, _serverName, _firewallRuleName, _startIpAddress, _endIpAddress);
            Console.WriteLine("Server firewall: " + fwr.FirewallRule.Id);

            Console.WriteLine("Database...");
            DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase(_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _databaseEdition, _databasePerfLevel);
            Console.WriteLine("Database: " + dbr.Database.Id);


            Console.WriteLine("Press any key to continue...");
            Console.ReadKey();
        }

        static ResourceGroup CreateOrUpdateResourceGroup(ResourceManagementClient resourceMgmtClient, string subscriptionId, string resourceGroupName, string resourceGroupLocation)
        {
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = resourceGroupLocation,
            };
            resourceMgmtClient.SubscriptionId = subscriptionId;
            ResourceGroup resourceGroupResult = resourceMgmtClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
            return resourceGroupResult;
        }

        static ServerGetResponse CreateOrUpdateServer(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverLocation, string serverName, string serverAdmin, string serverAdminPassword)
        {
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = serverLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = serverAdmin,
                    AdministratorLoginPassword = serverAdminPassword,
                    Version = "12.0"
                }
            };
            ServerGetResponse serverResult = sqlMgmtClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
            return serverResult;
        }


        static FirewallRuleGetResponse CreateOrUpdateFirewallRule(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string firewallRuleName, string startIpAddress, string endIpAddress)
        {
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = startIpAddress,
                    EndIpAddress = endIpAddress
                }
            };
            FirewallRuleGetResponse firewallResult = sqlMgmtClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
            return firewallResult;
        }



        static DatabaseCreateOrUpdateResponse CreateOrUpdateDatabase(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string databaseName, string databaseEdition, string databasePerfLevel)
        {
            // Retrieve the server that will host this database
            Server currentServer = sqlMgmtClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    CreateMode = DatabaseCreateMode.Default,
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel
                }
            };
            DatabaseCreateOrUpdateResponse dbResponse = sqlMgmtClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
            return dbResponse;
        }



        private static AuthenticationResult GetToken(string tenantId, string applicationId, string applicationSecret)
        {
            AuthenticationContext authContext = new AuthenticationContext("https://login.windows.net/" + tenantId);
            _token = authContext.AcquireToken("https://management.core.windows.net/", new ClientCredential(applicationId, applicationSecret));
            return _token;
        }
      }
    }





## <a name="create-a-service-principal-to-access-resources"></a>建立服務本金存取資源

Active Directory (AD) 應用程式，且我們需要驗證我們 C# 應用程式服務原則，就會建立下列 PowerShell 指令碼。 指令碼輸出我們需要上述 C# 範例值。 如詳細資訊，請參閱[使用 Azure PowerShell 來建立主要存取資源的服務](../resource-group-authenticate-service-principal.md)。

   
    # Sign in to Azure.
    Add-AzureRmAccount
    
    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId
    
    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.
    
    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"
    
    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret
    
    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    
    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15
    
    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    
    
    # Output the values we need for our C# application to successfully authenticate
    
    Write-Output "Copy these values into the C# sample app"
    
    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret



## <a name="next-steps"></a>後續步驟
既然您已嘗試 SQL 資料庫，並設定 C# 的資料庫，您就可以進行下列文章︰

- [連線至 SQL Server Management Studio 與 SQL 資料庫，並執行範例 T SQL 查詢](sql-database-connect-query-ssms.md)

## <a name="additional-resources"></a>其他資源

- [SQL 資料庫](https://azure.microsoft.com/documentation/services/sql-database/)
- [資料庫類別](https://msdn.microsoft.com/library/azure/microsoft.azure.management.sql.models.database.aspx)




<!--Image references-->
[1]: ./media/sql-database-get-started-csharp/aad.png
[2]: ./media/sql-database-get-started-csharp/permissions.png
[3]: ./media/sql-database-get-started-csharp/getdomain.png
[4]: ./media/sql-database-get-started-csharp/aad2.png
[5]: ./media/sql-database-get-started-csharp/aad-applications.png
[6]: ./media/sql-database-get-started-csharp/add.png
[7]: ./media/sql-database-get-started-csharp/add-application.png
[8]: ./media/sql-database-get-started-csharp/add-application2.png
[9]: ./media/sql-database-get-started-csharp/clientid.png
