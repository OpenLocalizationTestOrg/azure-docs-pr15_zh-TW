<properties 
    pageTitle="瞭解如何安全存取資料 DocumentDB |Microsoft Azure" 
    description="深入瞭解存取控制概念中 DocumentDB，包括主索引鍵、 唯讀鍵、 使用者和權限。" 
    services="documentdb" 
    authors="kiratp" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016" 
    ms.author="kipandya"/>

# <a name="securing-access-to-documentdb-data"></a>保護 DocumentDB 資料的存取權

本文提供保護資料儲存在[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)的存取權的概觀。

讀取此概觀後，您便可以回答下列問題︰  

-   DocumentDB 主索引鍵是什麼？
-   什麼是 DocumentDB 唯讀金鑰？
-   什麼是 DocumentDB 資源權杖？
-   如何保護 DocumentDB 資料的存取權使用 DocumentDB 使用者與權限？

## <a name="documentdb-access-control-concepts"></a>DocumentDB 存取控制概念

DocumentDB 提供第一本課程的概念，才能控制 DocumentDB 資源的存取權。  本主題為了，DocumentDB 資源分為兩種類別︰

- 管理資源
    - 帳戶
    - 資料庫
    - 使用者
    - 權限
- 應用程式資源
    - 集合
    - 優惠
    - 文件
    - 附件
    - 預存程序
    - 觸發程序
    - 使用者定義的函數

在這兩個類別的內容，DocumentDB 支援三種類型的存取控制人物︰ 帳戶管理員、 唯讀的系統管理員，以及資料庫使用者。  為每個 access 控制項角色的權限是︰
 
- 帳戶管理員︰ 完整存取所有指定的 DocumentDB 帳戶內的資源 （系統管理員和應用程式）。
- 唯讀的系統管理員︰ 唯讀的存取權的所有資源 （系統管理員和應用程式中指定的 DocumentDB 帳戶。 
- 資料庫使用者︰ 一組特定 DocumentDB 資料庫資源 （例如集合、 文件、 指令碼） 的相關聯的 DocumentDB 使用者資源。  可以是一個，或指定資料庫相關聯的更多使用者資源和每個使用者資源可能會有一或多個與其相關聯的權限。

上述的類別和記住的資源，DocumentDB 存取控制模型定義 access 結構的三種︰

- 主索引鍵︰ 時建立 DocumentDB 帳戶，建立兩個主索引鍵 （主要和次要）。  這些按鍵啟用 DocumentDB 帳戶內的所有資源的完整管理權限。

![DocumentDB 主索引鍵的圖例](./media/documentdb-secure-access-to-data/masterkeys.png)

- 唯讀索引鍵︰ 兩個唯讀鍵 （主要和次要） 建立在 DocumentDB 帳戶的建立。  這些按鍵啟用 DocumentDB 帳戶內的所有資源的唯讀存取。

![DocumentDB 唯讀鍵圖例](./media/documentdb-secure-access-to-data/readonlykeys.png)

- 資源權杖︰ DocumentDB 權限資源與相關聯的資源權杖，並擷取關聯資料庫的使用者與權限的使用者會有特定 DocumentDB 應用程式資源 （例如集合、 文件）。

![DocumentDB 資源權杖圖例](./media/documentdb-secure-access-to-data/resourcekeys.png)

## <a name="working-with-documentdb-master-and-read-only-keys"></a>使用 DocumentDB 母片及唯讀索引鍵

如先前所述，DocumentDB 主索引鍵時，使用唯讀鍵可帳戶內的所有資源的 「 讀取 」 權限，就會提供 DocumentDB 帳戶，內的所有資源的完整管理權限。  下列程式碼片段說明如何使用 DocumentDB 帳戶端點和主索引鍵，產生 DocumentClient 和建立新的資料庫。 

    //Read the DocumentDB endpointUrl and authorization keys from config.
    //These values are available from the Azure Classic Portal on the DocumentDB Account Blade under "Keys".
    //NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.
    
    private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
    private static readonly SecureString authorizationKey = ToSecureString(ConfigurationManager.AppSettings["AuthorizationKey"]);
        
    client = new DocumentClient(new Uri(endpointUrl), authorizationKey);
    
    // Create Database
    Database database = await client.CreateDatabaseAsync(
        new Database
        {
            Id = databaseName
        });


## <a name="overview-of-documentdb-resource-tokens"></a>DocumentDB 資源權杖的概觀

您可以使用的資源權杖 （藉由建立 DocumentDB 使用者與權限） 您希望提供不可信任具有主索引鍵的用戶端存取 DocumentDB 帳戶中的資源。 DocumentDB 主索引鍵包含同時主要和次要鍵，每個授與管理存取您的帳戶和所有的資源。 公開其中一個主索引鍵，隨即會開啟您的帳戶惡意或疏忽的可能性。 

同樣地，DocumentDB 唯讀鍵提供當然-DocumentDB 帳戶內的所有資源的權限的資源，除了 「 讀取 」 權限，無法用於提供更細緻存取特定的 DocumentDB 資源。

DocumentDB 資源權杖提供可以讓閱讀、 撰寫及刪除不需要使用母片 」 或 「 只索引鍵的讀取及權限授與您，根據您 DocumentDB 帳戶中的資源的用戶端安全替代方案。

以下是將一般的設計模式，也就是資源權杖可能會要求產生與傳遞給用戶端︰

1. 多層服務已設定要做的行動應用程式共用使用者的相片。
2. 多層服務建立 DocumentDB 帳戶主索引鍵。
3. 使用者在行動裝置上安裝相片應用程式。 
4. 在登入，[相片] 應用程式建立多層服務的使用者身分的識別。 這項機制身分識別建立的是完全至應用程式。
5. 身分識別建立之後，多層服務要求識別為基礎的權限。
6. 多層服務會將資源權杖傳回給手機應用程式。
7. 手機應用程式可以繼續使用的資源 token 直接存取 DocumentDB 資源，有定義的資源 token 及資源權杖所允許的時間間隔的權限。 
8. 當資源權杖到期時，後續要求會收到 401 未經授權的例外狀況。  此時，[電話] 應用程式重新建立 [身分識別，並要求新的資源權杖。

![DocumentDB 資源權杖工作流程](./media/documentdb-secure-access-to-data/resourcekeyworkflow.png)

## <a name="working-with-documentdb-users-and-permissions"></a>使用 DocumentDB 使用者與權限
DocumentDB 使用者資源是 DocumentDB 資料庫與相關聯。  每個資料庫可能包含零個或多個 DocumentDB 使用者。  下列程式碼片段會顯示如何建立 DocumentDB 使用者資源。

    //Create a user.
    User docUser = new User
    {
        Id = "mobileuser"
    };

    docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);

> [AZURE.NOTE] 每個 DocumentDB 使用者有 PermissionsLink 屬性這可以用來擷取使用者相關聯的權限的清單。

DocumentDB 權限資源是 DocumentDB 使用者與相關聯。  每位使用者可能包含零個或多個 DocumentDB 權限。  權限資源提供安全性權杖嘗試存取特定應用程式資源時，需要使用者存取。
有兩種可能會提供的權限資源可用的存取層級︰

- 所有︰ 使用者擁有完整權限的資源
- 讀取︰ 使用者只能讀取資源的內容，但無法執行寫入、 更新或刪除作業的資源。


> [AZURE.NOTE] 若要執行 DocumentDB 預存程序使用者必須具備的所有的權限集合預存程序執行。


下列程式碼片段示範如何建立權限的資源，讀取權限資源的資源權杖並與先前所建立的使用者權限。

    // Create a permission.
    Permission docPermission = new Permission
    {
        PermissionMode = PermissionMode.Read,
        ResourceLink = documentCollection.SelfLink,
        Id = "readperm"
    };
            
  docPermission = 著您用戶端。CreatePermissionAsync （UriFactory.CreateUserUri （「 db 」、 「 使用者 」）、 docPermission）;Console.WriteLine (docPermission.Id + 」 具有權杖: 「 + docPermission.Token);
  
如果您已指定資料分割索引鍵的集合，然後集合的權限，則文件和附件的資源也必須包含除了 ResourceLink ResourcePartitionKey。

輕鬆地取得所有與特定使用者相關聯的權限資源，才能 DocumentDB 會讓權限的摘要每個使用者物件。  下列程式碼片段示範如何擷取先前所建立的使用者與相關聯的權限、 建構權限清單，並產生新的 DocumentClient 代表使用者。

    //Read a permission feed.
    FeedResponse<Permission> permFeed = await client.ReadPermissionFeedAsync(
      UriFactory.CreateUserUri("db", "myUser"));

    List<Permission> permList = new List<Permission>();
      
    foreach (Permission perm in permFeed)
    {
        permList.Add(perm);
    }
            
    DocumentClient userClient = new DocumentClient(new Uri(endpointUrl), permList);

> [AZURE.TIP] 資源權杖有預設的有效時段 1 小時。  權杖存留時間，不過，明確地指定，上限為 5 小時。

## <a name="next-steps"></a>後續步驟

- 若要進一步瞭解 DocumentDB，請按一下[這裡](http://azure.com/docdb)。
- 若要深入瞭解管理母片及唯讀金鑰，請按一下[這裡](documentdb-manage-account.md)。
- 若要瞭解如何建構 DocumentDB 授權權杖，請按一下[這裡](https://msdn.microsoft.com/library/azure/dn783368.aspx)
 
