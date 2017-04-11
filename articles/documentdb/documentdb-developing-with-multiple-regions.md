<properties
   pageTitle="使用多個區域中 DocumentDB 開發 |Microsoft Azure"
   description="瞭解如何從 Azure DocumentDB，完全受管理的 NoSQL 資料庫服務存取多個區域中的資料。"
   services="documentdb"
   documentationCenter=""
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="kipandya"/>
   
# <a name="developing-with-multi-region-documentdb-accounts"></a>開發多區域 DocumentDB 帳戶

> [AZURE.NOTE] 全域通訊群組的 DocumentDB 資料庫是推出並自動啟用的任何新建立的 DocumentDB 帳戶。 我們正在啟用全域散佈所有現有帳戶，但過渡，如果您想要在您的帳戶啟用的全域通訊請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，我們會將它啟用您現在。

好充分利用[全域通訊群組](documentdb-distribute-data-globally.md)，用戶端應用程式可以指定要用來執行文件作業的地區排序的喜好設定清單。 這可以經由設定連線原則。 根據 Azure DocumentDB 帳戶設定目前地區的顯示狀態與指定的 [喜好設定] 清單，會執行寫入和讀取作業 SDK 所選擇的最佳的端點。 

使用 DocumentDB 用戶端 Sdk 初始化連線時，會指定此喜好設定] 清單。 Sdk 接受選用的參數 」 PreferredLocations 」 就是 Azure 區域的排序的清單。

SDK 會自動傳送至目前的所有寫入都撰寫區域。 

所有讀取便會都傳送至 PreferredLocations 清單中的第一個可用的區域。 如果邀請失敗，請用戶端會失敗到下一個區域，清單中向下等等。 

區域中的指定 PreferredLocations 讀取 Sdk 只會用戶端。 因此，，例如，如果在三個區域中，就能使用資料庫帳戶，但在用戶端只指定兩個非寫入區域的 PreferredLocations，然後讀取將提供範圍外的寫入，即使容錯移轉。

應用程式可以驗證目前寫入結束點，並閱讀 SDK 根據檢查兩個屬性，WriteEndpoint 和 ReadEndpoint，適用於 SDK 版本 1.8，以及上方的結束點。 

如果未設定 PreferredLocations 屬性，則將會從目前的寫入範圍服務所有要求。 


## <a name="net-sdk"></a>.NET SDK
SDK 可供不進行任何的程式碼變更。 在此情況下，SDK 自動指示同時讀取和寫入目前寫入範圍。 

1.8 及更新版本的.NET SDK 的版本，請在 ConnectionPolicy 參數 DocumentClient 建構函式會有稱為 Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations 的屬性。 此屬性是類型集合`<string>`應該包含地區名稱的清單。 字串值的格式每[Azure 區域]中的 [地區名稱] 欄 [regions]頁面，第一個前後不加空格，然後分別最後一個字元。

目前 [書寫與讀取的端點適用於 DocumentClient.WriteEndpoint 和 DocumentClient.ReadEndpoint 分別是。

> [AZURE.NOTE] 端點 Url 不應視為久常數。 服務可能會更新下列任一點。 SDK 自動處理這項變更。

    // Getting endpoints from application settings or other configuration location
    Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
    string accountKey = Properties.Settings.Default.GlobalDatabaseKey;

    //Setting read region selection preference 
    connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
    connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
    connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

    // initialize connection
    DocumentClient docClient = new DocumentClient(
        accountEndPoint,
        accountKey,
        connectionPolicy);

    // connect to DocDB 
    await docClient.OpenAsync().ConfigureAwait(false);


## <a name="nodejs-javascript-and-python-sdks"></a>NodeJS JavaScript] 與 Python Sdk
SDK 可供不進行任何的程式碼變更。 在此情況下，SDK 會自動直接同時讀取與寫入目前撰寫區域中。 

在 [1.8 和之後的每個 SDK 的版本，ConnectionPolicy 參數 DocumentClient 建構函式新屬性稱為 DocumentClient.ConnectionPolicy.PreferredLocations。 這是參數是字串陣列的地區名稱的清單。 每個[Azure 區域]中的 [地區名稱] 欄格式化名稱 [regions]頁面。 您也可以使用預先定義的常數便利性物件 AzureDocuments.Regions 中

目前 [書寫與讀取的端點適用於 DocumentClient.getWriteEndpoint 和 DocumentClient.getReadEndpoint 分別是。

> [AZURE.NOTE] 端點 Url 不應視為久常數。 服務可能會更新下列任一點。 SDK 會自動處理這項變更。

以下為 NodeJS/Javascript 程式碼範例。 Python 和 Java 會遵循相同的模式。

    // Creating a ConnectionPolicy object
    var connectionPolicy = new DocumentBase.ConnectionPolicy();
    
    // Setting read region selection preference, in the following order -
    // 1 - West US
    // 2 - East US
    // 3 - North Europe
    connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe'];
    
    // initialize the connection
    var client = new DocumentDBClient(host, { masterKey: masterKey }, connectionPolicy);


## <a name="rest"></a>其餘 
資料庫帳戶之後使用多個區域中，用戶端可用性來查詢執行下列 URI GET 要求。

    https://{databaseaccount}.documents.azure.com/

服務會傳回之地區和其對應 DocumentDB 的端點 Uri 複本的清單。 在回應中會指出目前寫入範圍。 用戶端可以按一下適當的結束點，為未來所有 REST API 邀請，如下所示。

範例回應

    {
        "_dbs": "//dbs/",
        "media": "//media/",
        "writableLocations": [
            {
                "Name": "West US",
                "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
            }
        ],
        "readableLocations": [
            {
                "Name": "East US",
                "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
            }
        ],
        "MaxMediaStorageUsageInMB": 2048,
        "MediaStorageUsageInMB": 0,
        "ConsistencyPolicy": {
            "defaultConsistencyLevel": "Session",
            "maxStalenessPrefix": 100,
            "maxIntervalInSeconds": 5
        },
        "addresses": "//addresses/",
        "id": "globaldbexample",
        "_rid": "globaldbexample.documents.azure.com",
        "_self": "",
        "_ts": 0,
        "_etag": null
    }


-   所有放入、 文章和刪除要求必須移至指定的寫入 URI
-   所有取得和其他唯讀的要求 （例如查詢） 可能會移至 [用戶端所選擇的任何端點

撰寫要求唯讀狀態區域會失敗，HTTP 錯誤碼 403 （「 禁止 」）。

如果用戶端的初始探索階段之後，變更 [寫入] 區域，後續寫入前一個寫入區塊將會失敗，HTTP 錯誤碼 403 （「 禁止 」）。 用戶端然後應收到區域]，以取得更新的寫入地區的清單。

## <a name="next-steps"></a>後續步驟

深入瞭解散發資料全域 DocumentDB 下列文章中︰

- [發佈 DocumentDB 全域的資料](documentdb-distribute-data-globally.md)
- [一致性層級](documentdb-consistency-levels.md)
- [如何處理量搭配多個區域](documentdb-manage.md#how-throughput-works-with-multiple-regions)
- [新增使用 Azure 入口網站的地區](documentdb-portal-global-replication.md)

[regions]: https://azure.microsoft.com/regions/ 
