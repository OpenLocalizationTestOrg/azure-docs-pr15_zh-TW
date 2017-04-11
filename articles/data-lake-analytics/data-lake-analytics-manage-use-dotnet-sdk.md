<properties 
   pageTitle="管理 Azure 資料湖分析使用 Azure.NET SDK |Azure" 
   description="瞭解如何管理資料湖分析工作、 資料來源的使用者。 " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/23/2016"
   ms.author="jgao"/>

# <a name="manage-azure-data-lake-analytics-using-azure-net-sdk"></a>管理使用 Azure.NET SDK Azure 資料湖分析

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

瞭解如何管理 Azure 資料湖分析帳戶、 資料來源、 使用者，以及使用 Azure.NET SDK 的工作。 若要查看管理主題使用其他工具，按一下 [] 索引標籤選取上方。

**必要條件**

本教學課程之前，您必須具備下列項目︰

- **Azure 訂閱**。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。


<!-- ################################ -->
<!-- ################################ -->


## <a name="connect-to-azure-data-lake-analytics"></a>連線至 Azure 資料湖狀況分析

您將需要下列 Nuget 套件︰

    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Common 
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre


下列程式碼範例會示範如何連線至 Azure 和清單底下 Azure 訂閱的現有資料湖分析帳戶。

    using System;
    using System.Collections.Generic;
    using System.Threading;

    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;

    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Analytics;
    using Microsoft.Azure.Management.DataLake.Analytics.Models;

    namespace ConsoleAcplication1
    {
        class Program
        {

            private const string SUBSCRIPTIONID = "<Enter Your Azure Subscription ID>";
            private const string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
            private const string DOMAINNAME = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.

            private static DataLakeAnalyticsAccountManagementClient _adlaClient;

            private static void Main(string[] args)
            {

                var creds = AuthenticateAzure(DOMAINNAME, CLIENTID);

                _adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
                _adlaClient.SubscriptionId = SUBSCRIPTIONID;

                var adlaAccounts = ListADLAAccounts();

                Console.WriteLine("You have %i Data Lake Analytics account(s).", adlaAccounts.Count);
                for (int i = 0; i < adlaAccounts.Count; i ++)
                {
                    Console.WriteLine(adlaAccounts[i].Name);
                }

                System.Console.WriteLine("Press ENTER to continue");
                System.Console.ReadLine();
            }

            public static ServiceClientCredentials AuthenticateAzure(
            string domainName,
            string nativeClientAppCLIENTID)
            {
                // User login via interactive popup
                SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
                // Use the client ID of an existing AAD "Native Client" application.
                var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
                return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
            }

            public static List<DataLakeAnalyticsAccount> ListADLAAccounts()
            {
                var response = _adlaClient.Account.List();
                var accounts = new List<DataLakeAnalyticsAccount>(response);

                while (response.NextPageLink != null)
                {
                    response = _adlaClient.Account.ListNext(response.NextPageLink);
                    accounts.AddRange(response);
                }

                return accounts;
            }
        }
    }


## <a name="manage-accounts"></a>管理帳戶

之前執行任何資料湖分析工作，您必須有資料湖分析帳戶。 不同於 Azure HDInsight 您不是每年支付分析帳戶時不執行工作。  您只是付款時執行工作的時間。  如需詳細資訊，請參閱[Azure 資料湖分析概觀](data-lake-analytics-overview.md)。  

###<a name="create-accounts"></a>建立帳戶

您可以執行下列範例之前，您必須具有 Azure 資源管理] 群組中和資料湖存放帳戶。

下列程式碼會說明如何建立資源群組︰

    public static async Task<ResourceGroup> CreateResourceGroupAsync(
        ServiceClientCredentials credential,
        string groupName,
        string subscriptionId,
        string location)
    {

        Console.WriteLine("Creating the resource group...");
        var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };
        var resourceGroup = new ResourceGroup { Location = location };
        return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
    }

下列程式碼會說明如何建立資料湖存放區帳戶︰

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

下列程式碼會說明如何建立資料湖分析帳戶︰

    var defaultAdlsAccount = new List<DataLakeStoreAccountInfo> { new DataLakeStoreAccountInfo(adlsAccountName, new DataLakeStoreAccountInfoProperties()) };
    var adlaProperties = new DataLakeAnalyticsAccountProperties(defaultDataLakeStoreAccount: adlsAccountName, dataLakeStoreAccounts: defaultAdlsAccount);
    var adlaParameters = new DataLakeAnalyticsAccount(properties: adlaProperties, location: location);
    var adlaAccount = _adlaClient.Account.Create(resourceGroupName, adlaAccountName, adlaParameters);

###<a name="list-accounts"></a>帳戶清單

請參閱[連線至 Azure 資料湖分析](#connect_to_azure_data_lake_analytics)。

###<a name="find-an-account"></a>找出帳戶

一旦您在取得資料湖分析帳戶清單中的物件，您可以使用下列，若要尋找帳戶︰

    Predicate<DataLakeAnalyticsAccount> accountFinder = (DataLakeAnalyticsAccount a) => { return a.Name == adlaAccountName; };
    var myAdlaAccount = adlaAccounts.Find(accountFinder);

###<a name="delete-data-lake-analytics-accounts"></a>刪除資料湖分析帳戶

下列程式碼片段刪除資料湖分析帳戶︰

    _adlaClient.Account.Delete(resourceGroupName, adlaAccountName);

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>管理帳戶資料來源

資料湖分析目前支援下列資料來源︰

- [Azure 資料湖儲存空間](../data-lake-store/data-lake-store-overview.md)
- [Azure 儲存體](../storage/storage-introduction.md)

當您建立的分析帳戶時，您必須指定為預設儲存帳戶 Azure 資料湖儲存體帳戶。 預設資料湖存放帳戶用來儲存工作中繼資料] 及 [工作稽核記錄。 建立分析帳戶後，您可以新增其他資料湖儲存帳戶及/或 Azure 儲存體帳戶。 

### <a name="find-the-default-data-lake-store-account"></a>尋找預設資料湖存放區帳戶

請參閱尋找本文中的帳戶以尋找資料湖分析帳戶。 然後使用下列步驟︰

    string adlaDefaultDataLakeStoreAccountName = myAccount.Properties.DefaultDataLakeStoreAccount;


## <a name="use-azure-resource-manager-groups"></a>使用 Azure 資源管理員群組

應用程式通常是許多元件，例如在 web 應用程式、 資料庫、 資料庫伺服器、 儲存及第 3 廠商服務。 Azure 資源管理員可讓您使用應用程式] 群組中，稱為 Azure 資源] 群組中的資源。 您可以部署、 更新、 監控或刪除的所有資源在單一、 協同作業應用程式。 使用範本，以供部署，該範本能夠為不同的環境，例如執行與生產測試。 您可以檢視上顯型成本給整個群組，計費釐清為您的組織。 如需詳細資訊，請參閱[Azure 資源管理員的概觀](../azure-resource-manager/resource-group-overview.md)。 

資料湖分析服務可以包含下列元件︰

- Azure 資料湖分析帳戶
- 必要的預設 Azure 資料湖儲存體帳戶
- 其他 Azure 資料湖儲存帳戶
- 其他 Azure 儲存體帳戶

您可以建立一個資源管理群組，若要使其更易於管理] 底下的所有這些元件。

![Azure 資料湖分析帳戶和儲存空間](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

資料湖分析帳戶從屬參照儲存帳戶必須放在同一個 Azure 資料中心。
資源管理群組不過可以位於不同的資料中心。  

##<a name="see-also"></a>另請參閱 

- [Microsoft Azure 資料湖分析的概觀](data-lake-analytics-overview.md)
- [開始使用資料湖分析使用 Azure 入口網站](data-lake-analytics-get-started-portal.md)
- [管理 Azure 資料湖分析使用 Azure 入口網站](data-lake-analytics-manage-use-portal.md)
- [監控和疑難排解 Azure 資料湖分析工作使用 Azure 入口網站](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

