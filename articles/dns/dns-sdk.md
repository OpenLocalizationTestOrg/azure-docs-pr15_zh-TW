<properties 
   pageTitle="建立 DNS 區域，並在使用.NET SDK Azure DNS 記錄集 |Microsoft Azure" 
   description="如何建立 DNS 區域和記錄會使用.NET SDK 設定 Azure DNS。" 
   services="dns" 
   documentationCenter="na" 
   authors="jtuliani" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/19/2016"
   ms.author="jtuliani"/>


# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>建立 DNS 區域和使用.NET SDK 的記錄集

您可以自動化作業來建立、 刪除，或與.NET DNS 管理文件庫中使用 DNS SDK 更新 DNS 區域記錄集]，與記錄。 完整的 Visual Studio 專案有[以下。](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>主要帳戶給服務

一般而言，以程式設計方式存取 Azure 的資源授與透過專用的帳戶，而不是您自己的使用者認證。 這些專用的帳戶被稱為 [服務主體] 帳戶。 若要使用的 Azure DNS SDK 範例專案，您需要建立主要指定帳戶給服務，並將其指派正確的權限。

1. 請遵循[這些指示](../resource-group-authenticate-service-principal.md)帳戶給服務主要 （Azure DNS SDK 範例專案假設密碼驗證）。

2. 建立資源群組 ([以下說明如何](../azure-portal/resource-group-portal.md))。

3. 資源群組的 [DNS Zone 參與者 」 權限授與服務的本金帳戶使用 Azure RBAC ([以下說明如何](../active-directory/role-based-access-control-configure.md)。)

4. 如果使用 Azure DNS SDK 範例專案，編輯 「 program.cs 」 檔案，如下所示︰
    * 插入 tenantId、 clientId （也稱為帳戶識別碼）、 秘密 （服務主體帳戶密碼） 和 subscriptionId 步驟 1 中使用正確的值。
    * 輸入選擇在步驟 2 中的資源群組名稱。
    * 輸入您所選擇的 DNS 區域名稱。

## <a name="nuget-packages-and-namespace-declarations"></a>NuGet 套件和命名空間宣告

若要使用 Azure DNS.NET SDK，您必須安裝**Azure DNS 管理文件庫**NuGet 套件及其他必要的 Azure 套件。
 
1. 在**Visual Studio**中，開啟 [專案] 或 [新的專案。 

2. 移至 [**工具** **>** **NuGet 封裝管理員** **>** **管理解決方案...NuGet 套件**。 

3. 按一下 [**瀏覽**、 啟用**包含搶鮮版**] 核取方塊，並在 [搜尋] 方塊中輸入**Microsoft.Azure.Management.Dns** 。

4. 選取的套件，然後按一下 [**安裝**] 以將其新增至您的 Visual Studio 專案。
 
5. 重複上述也會安裝下列封裝的程序︰ **Microsoft.Rest.ClientRuntime.Azure.Authentication**和**Microsoft.Azure.Management.ResourceManager**。

## <a name="add-namespace-declarations"></a>新增命名空間宣告

新增下列命名空間宣告

    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.Dns;
    using Microsoft.Azure.Management.Dns.Models;

## <a name="initialize-the-dns-management-client"></a>初始化 DNS 管理用戶端

*DnsManagementClient*包含的方法及屬性所需的管理 DNS 區域和記錄集。  下列程式碼服務主體帳戶登入，並建立 DnsManagementClient 物件。

    // Build the service credentials and DNS management client
    var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
    var dnsClient = new DnsManagementClient(serviceCreds);
    dnsClient.SubscriptionId = subscriptionId;

## <a name="create-or-update-a-dns-zone"></a>建立或更新 DNS 區域

若要建立 DNS 區域，第一次 [區域] 會建立一個物件以包含 DNS 區域的參數。 因為 DNS 區域不連結至特定的區域，位置設定為 「 通用 」。 在此範例中，也會加入[Azure 資源管理員 [標籤]](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/)區域。

實際建立或更新 Azure dns 區域，區域物件中包含區域的參數傳遞*DnsManagementClient.Zones.CreateOrUpdateAsyc*方法。

>[AZURE.NOTE] DnsManagementClient 支援作業的三種模式︰ 同步 (「 CreateOrUpdate 」)、 非同步 (「 CreateOrUpdateAsync 」)，或非同步有權存取 HTTP 回應 (「 CreateOrUpdateWithHttpMessagesAsync 」)。  您可以選擇任何這些模式中，根據您的應用程式的需求。

Azure DNS 支援樂觀，稱為[Etag](dns-getstarted-create-dnszone.md)。 在此範例中，指定 「 * 」 的 「 If 無-比對 「 頁首會告訴 Azure DNS 建立 DNS 區域，如果不存在。  如果有指定名稱區域已經存在指定的資源] 群組中，將會失敗通話。

    // Create zone parameters
    var dnsZoneParams = new Zone("global"); // All DNS zones must have location = "global"
    
    // Create a Azure Resource Manager 'tag'.  This is optional.  You can add multiple tags
    dnsZoneParams.Tags = new Dictionary<string, string>();
    dnsZoneParams.Tags.Add("dept", "finance");
    
    // Create the actual zone.
    // Note: Uses 'If-None-Match *' ETAG check, so will fail if the zone exists already.
    // Note: For non-async usage, call dnsClient.Zones.CreateOrUpdate(resourceGroupName, zoneName, dnsZoneParams, null, "*")
    // Note: For getting the http response, call dnsClient.Zones.CreateOrUpdateWithHttpMessagesAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*")
    var dnsZone = await dnsClient.Zones.CreateOrUpdateAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*");

## <a name="create-dns-record-sets-and-records"></a>建立 DNS 記錄設定及記錄

管理 DNS 記錄的記錄的設定。 記錄集是一組具有相同名稱和區域內的記錄類型的記錄。  相對於區域名稱，而不是完整的 DNS 名稱會記錄集名稱。

若要建立或更新記錄設定，「 記錄集 」 建立並傳送至*DnsManagementClient.RecordSets.CreateOrUpdateAsync*參數物件。 為使用 DNS 區域中，有三種模式的作業︰ 同步 (「 CreateOrUpdate 」)、 非同步 (「 CreateOrUpdateAsync 」)，或非同步有權存取 HTTP 回應 (「 CreateOrUpdateWithHttpMessagesAsync 」)。

就跟 DNS 區域記錄集作業包含樂觀支援。  在此範例中，因為 'If 符合' 或 ' If 無-比對 「 都不指定，記錄集一律建立。  此通話會覆寫任何現有的記錄，以相同的名稱和記錄類型設定這個 DNS 區域中。

    // Create record set parameters
    var recordSetParams = new RecordSet();
    recordSetParams.TTL = 3600;

    // Add records to the record set parameter object.  In this case, we'll add a record of type 'A'
    recordSetParams.ARecords = new List<ARecord>();
    recordSetParams.ARecords.Add(new ARecord("1.2.3.4"));

    // Add metadata to the record set.  Similar to Azure Resource Manager tags, this is optional and you can add multiple metadata name/value pairs
    recordSetParams.Metadata = new Dictionary<string, string>();
    recordSetParams.Metadata.Add("user", "Mary");

    // Create the actual record set in Azure DNS
    // Note: no ETAG checks specified, will overwrite existing record set if one exists
    var recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSetParams);

## <a name="get-zones-and-record-sets"></a>取得區域和記錄集

*DnsManagementClient.Zones.Get*和*DnsManagementClient.RecordSets.Get*方法分別擷取個別的區域，記錄集]。 其類型、 名稱，以及它們存在於的時區和資源群組來識別記錄集。 他們的名稱和位於資源群組來識別區域。

    var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
    
## <a name="update-an-existing-record-set"></a>更新現有的記錄設定

若要更新現有的 DNS 記錄的設定，先擷取記錄集，然後更新記錄設定內容，然後提交變更。  在此範例中，我們會指定擷取的記錄集 ' If 比對 「 參數中的 'Etag 」。 如果是同時作業修改同時設定的記錄，則會失敗通話。

    var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

    // Add a new record to the local object.  Note that records in a record set must be unique/distinct
    recordSet.ARecords.Add(new ARecord("5.6.7.8"));

    // Update the record set in Azure DNS
    // Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
    recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);

## <a name="list-zones-and-record-sets"></a>清單區域和記錄集

清單區域中，使用*DnsManagementClient.Zones.List...*方法，可支援列出 [指定的資源群組中的所有區域或指定 Azure 訂閱中的所有區域 （透過資源群組）。列出記錄的集合，請使用*DnsManagementClient.RecordSets.List...*方法，支援清單指定區域中的所有記錄集或特定類型的記錄集。

列出區域時，請注意，結果的記錄集可能分頁。  下列範例會示範如何逐一查看結果的頁面。 （手動小型頁面大小為 [2] 用來強制分頁; 在練習中應該省略此參數和使用預設頁面大小）。

    // Note: in this demo, we'll use a very small page size (2 record sets) to demonstrate paging
    // In practice, to improve performance you would use a large page size or just use the system default
    int recordSets = 0;
    var page = await dnsClient.RecordSets.ListAllInResourceGroupAsync(resourceGroupName, zoneName, "2");
    recordSets += page.Count();

    while (page.NextPageLink != null)
    {
        page = await dnsClient.RecordSets.ListAllInResourceGroupNextAsync(page.NextPageLink);
        recordSets += page.Count();
    }

## <a name="next-steps"></a>後續步驟

下載[Azure DNS.NET SDK 範例專案](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)，包括如何使用 Azure DNS.NET SDK，包括其他 DNS 記錄類型的範例進一步的範例。
