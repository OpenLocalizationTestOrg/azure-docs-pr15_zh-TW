<properties
    pageTitle="帳戶以批次管理.NET 資源管理 |Microsoft Azure"
    description="建立、 刪除及修改 Azure 批次帳戶資源與批次管理.NET 文件庫。"
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="10/19/2016"
    ms.author="marsma"/>

# <a name="manage-azure-batch-accounts-and-quotas-with-batch-management-net"></a>管理 Azure 批次帳戶和以批次管理.NET 的配額

> [AZURE.SELECTOR]
- [Azure 入口網站](batch-account-create-portal.md)
- [批次管理.NET](batch-management-dotnet.md)

您可以降低維護成本 Azure 批次應用程式中使用[批次管理.NET] [api_mgmt_net]自動化批次帳戶建立、 刪除、 金鑰管理和配額探索的文件庫。

- **建立及刪除批次帳戶**任何區域中。 如果為獨立軟體廠商 (ISV)，例如您提供您每個在此指派用於帳單的另一個批次帳戶的用戶端的服務，您可以新增帳戶建立及刪除功能至您的客戶入口網站。
- **擷取和每個帳戶金鑰**以程式設計方式的任何一個批次帳戶。 這可以協助您遵守強制執行定期變換 」 或 「 到期的帳戶金鑰的安全性原則。 當您在各種 Azure 區域內有多個批次帳戶時，此變換程序的自動化會增加您的方案效率。
- **檢查帳戶配額**和記錄試用版錯誤猜測出決定哪些批次帳戶有哪些限制。 檢查您的帳戶配額開始工作之前，請建立資料庫，或新增運算節點，您可以主動調整位置或資源時這些計算會建立。 您可以決定哪些帳戶需要配額增加之前配置額外的資源，在這些帳戶。
- **將其他 Azure 服務的功能**的完整功能管理體驗，使用批次管理.NET [Azure Active Directory][aad_about]，和[Azure 資源管理員][resman_overview]分成一組相同的應用程式。 藉由使用這些功能和其 Api，您可以提供 frictionless 驗證體驗，能夠建立及刪除資源群組]，並說明上方的端對端管理解決方案的功能。

> [AZURE.NOTE] 本文著重於批次帳戶、 索引鍵和配額的程式設計管理，您可以執行許多這些活動使用[Azure 入口網站][azure_portal]。 如需詳細資訊，請參閱[建立 Azure 批次帳戶使用 Azure 入口網站](batch-account-create-portal.md)和[配額和 Azure 批次服務的限制](batch-quota-limit.md)。

## <a name="create-and-delete-batch-accounts"></a>建立及刪除批次帳戶

如上所述，其中一個批次管理 API 的主要功能可建立及刪除批次 Azure 區域中的帳戶。 若要這麼做，請使用 [ [BatchManagementClient.Account.CreateAsync] [net_create]和[DeleteAsync][net_delete]，或其同步有何差異。

下列程式碼片段建立帳戶、 從批次服務，取得新建立的帳戶，然後刪除它。 在此程式碼片段和本文中，其他`batchManagementClient`完全初始化[BatchManagementClient]的執行個體[net_mgmt_client]。

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [AZURE.NOTE] 使用批次管理.NET 文件庫和其 BatchManagementClient 類別的應用程式需要擁有批次帳戶，來管理訂閱**服務系統管理員**或**coadministrator**存取。 如需詳細資訊，請參閱[Azure Active Directory](#azure-active-directory)區段和[AccountManagement] [acct_mgmt_sample]程式碼範例。

## <a name="retrieve-and-regenerate-account-keys"></a>擷取及重新產生帳戶金鑰

從您的訂閱中的任何批次帳戶取得主要和次要帳戶金鑰，使用[ListKeysAsync][net_list_keys]。 您可以使用[RegenerateKeyAsync]重新產生金鑰[net_regenerate_keys]。

```csharp
// Get and print the primary and secondary keys
BatchAccountListKeyResult accountKeys =
    await batchManagementClient.Account.ListKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [AZURE.TIP] 您可以建立精簡的連線工作流程管理應用程式。 首先，取得您想要使用[ListKeysAsync]管理批次帳戶的帳戶金鑰[net_list_keys]。 然後，初始化批次.NET 文件庫的[BatchSharedKeyCredentials]時，使用此按鍵[net_sharedkeycred]類別，當初始化[BatchClient][net_batch_client]。

## <a name="check-azure-subscription-and-batch-account-quotas"></a>檢查 Azure 訂閱及批次帳戶配額

Azure 訂閱及個別 Azure 服務等批次所有有預設的配額限制內的特定項目數目。 預設的配額 Azure 訂閱，請參閱[Azure 訂閱及服務限制，配額和限制式](./../azure-subscription-service-limits.md)。 批次服務的預設配額，請參閱[配額和 Azure 批次服務的限制](batch-quota-limit.md)。 藉由使用批次管理.NET 文件庫，您可以在應用程式中檢查這些配額。 這可讓您決定配置新增帳戶或計算集區之類的資源，並計算節點之前。

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>檢查批次帳戶配額 Azure 訂閱

建立之前批次帳戶區域中，您可以檢查您 Azure 的訂閱，以查看您是否可以在該區域新增帳戶。

下列程式碼片段，我們在第一次使用[BatchManagementClient.Account.ListAsync] [net_mgmt_listaccounts]以取得在訂閱中的所有批次帳戶的集合。 一旦我們已取得此集合，我們會決定幾個帳戶是在 [目標區域中。 然後，我們使用[BatchManagementClient.Subscriptions] [net_mgmt_subscriptions]取得批次帳戶配額並判斷幾個帳戶 （如果有的話） 可以建立在該區域。

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.Account.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

在上述片段`creds` [TokenCloudCredentials]的執行個體[azure_tokencreds]。 若要建立此物件的範例，請參閱[AccountManagement] [ acct_mgmt_sample] GitHub 的程式碼範例。

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>檢查計算資源配額批次帳戶

前增加批次方案中的計算資源，您可以檢查以確保您想要配置的資源不超過配額的帳戶。 在下列程式碼片段，我們列印批次帳戶名稱的配額資訊`mybatchaccount`。 在您的應用程式，您可以使用這類資訊決定帳戶是否可以處理建立其他的資源。

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [AZURE.IMPORTANT] Azure 訂閱及服務的預設配額時，這些限制許多可能引發發出[Azure 入口網站]中的要求[azure_portal]。 例如，請參閱[配額和 Azure 批次服務限制](batch-quota-limit.md)指示增加您批次帳戶的配額。

## <a name="batch-management-net-azure-ad-and-resource-manager"></a>批次管理.NET Azure AD，以及資源管理員

當您使用的批次管理.NET 文件庫時，您通常也使用[Azure Active Directory] [ aad_about] (Azure AD) 和[Azure 資源管理員][resman_overview]。 它會示範批次管理.NET API 如下所示的範例專案使用 Azure Active Directory 和資源管理員。

### <a name="azure-active-directory"></a>Azure Active Directory

Azure 本身使用 Azure AD 來驗證其客戶、 服務管理員和組織的使用者。 在批次管理.NET 的內容，您可以使用 Azure AD 驗證訂閱系統管理員或共同管理員。 這個選項可讓管理文件庫查詢批次服務，並執行本文所述的作業。

在範例專案討論下方 Azure [Active Directory 驗證庫][ aad_adal] (ADAL) 用來提示使用者輸入其 Microsoft 認證。 當提供服務的系統管理員或 coadministrator 認證時，應用程式可以 Azure 查詢清單的訂閱，可以建立及刪除資源群組與批次帳戶。

### <a name="resource-manager"></a>資源管理員

當您以批次管理.NET 文件庫中建立批次帳戶時，您通常要建立這些[資源群組]內[resman_overview]。 您可以使用[ResourceManagementClient] ，以程式設計方式建立資源群組[resman_client]類別中的 [[資源管理員.NET] [resman_api]文件庫。 您可以新增至現有的資源群組先前使用[Azure 入口網站]所建立的帳戶或者[azure_portal]。

## <a name="sample-project-on-github"></a>在 GitHub 範例專案

若要觀看批次管理.NET 的實際操作，請查看[AccountManagment] [acct_mgmt_sample]上 GitHub 範例專案。 此主控台應用程式顯示的建立及使用方式[BatchManagementClient] [net_mgmt_client]和[ResourceManagementClient][resman_client]。 也會示範 Azure [Active Directory 驗證庫]的使用狀況[aad_adal](ADAL)，其所需的兩個用戶端。

若要順利執行範例應用程式，您必須先將其 Azure AD 使用登錄 Azure 入口網站。 遵循 [[拷貝應用程式與 Azure Active Directory]中的 [[新增應用程式](../active-directory/active-directory-integrating-applications.md#adding-an-application)] 區段中的步驟[aad_integrate]註冊您自己的帳戶中的範例應用程式的預設目錄。 請務必選取**原始的用戶端應用程式**類型的應用程式，而且您可以指定任何有效的 URI (例如`http://myaccountmanagementsample`) 為**重新導向 URI**，它不需要是真正的端點。

新增您的應用程式後, 委派**存取 Azure 服務管理組織**的權限在入口網站應用程式的設定] 中的*Windows Azure 服務管理 API*應用程式︰

![Azure 入口網站中的應用程式權限][2]

> [AZURE.TIP] 如果**Windows Azure 服務管理 API**沒有*其他應用程式的權限*] 下，按一下 [**新增應用程式**，選取**Windows Azure 服務管理 API**，然後按一下 [核取符號] 按鈕。 然後，代理人如上所述的權限。

一旦您新增的應用程式上所述，更新`Program.cs`中[AccountManagment] [acct_mgmt_sample]範例專案與您的應用程式重新導向 URI 用戶端識別碼 在您的應用程式中的 [**設定**] 索引標籤中，您可以找到這些值︰

![Azure 入口網站中的應用程式設定][3]

[AccountManagment] [acct_mgmt_sample]範例應用程式會示範下列作業︰

1. 取得從 Azure AD 安全性權杖使用[ADAL][aad_adal]。 如果使用者沒有已登入，系統會提示其 Azure 認證。
2. 使用 [從 Azure AD 取得的安全性 token，建立[SubscriptionClient] [ resman_subclient] query Azure 帳戶相關聯的訂閱的清單。 這個選項可讓找到的 [如果有多個訂閱的選取範圍。
3. 建立與所選的訂閱相關聯的認證物件。
4. 建立[ResourceManagementClient] [resman_client]所使用的憑證。
5. 使用[ResourceManagementClient] [resman_client]建立資源群組。
6. 使用[BatchManagementClient] [net_mgmt_client]執行多個批次帳戶作業︰
  - 建立新的 [資源] 群組中的批次的帳戶。
  - 批次服務取得新建立的帳戶。
  - 列印新帳戶的帳戶金鑰。
  - 重新產生新的主索引鍵的帳戶。
  - 列印配額資訊的帳戶。
  - 列印訂閱的配額資訊。
  - 列印訂閱中的所有帳戶。
  - 刪除新建立的帳戶。
7. 刪除資源群組。

在刪除之前新建立的批次帳戶和資源群組，您可以檢查在[Azure 入口網站][azure_portal]:

![顯示資源群組和批次帳戶 azure 入口網站][1]
<br />
*顯示新的資源群組與批次帳戶 azure 入口網站*

[aad_about]: ../active-directory/active-directory-whatis.md "Azure Active Directory 是什麼？"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Azure ad 驗證案例"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "整合應用程式與 Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: http://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspxs
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
