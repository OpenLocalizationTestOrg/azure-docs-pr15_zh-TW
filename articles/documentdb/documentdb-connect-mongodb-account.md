<properties 
    pageTitle="連線至 DocumentDB 帳戶的 MongoDB 的通訊協定支援 |Microsoft Azure" 
    description="瞭解如何連線至 MongoDB，現在可預覽的通訊協定支援 DocumentDB 帳戶。 使用 MongoDB 連接字串連線。" 
    keywords="mongodb 連線字串"
    services="documentdb" 
    authors="AndrewHoh" 
    manager="jhubbard" 
    editor="" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="anhoh"/>

# <a name="how-to-connect-to-a-documentdb-account-with-protocol-support-for-mongodb"></a>如何連線至 DocumentDB 帳戶的 MongoDB 的通訊協定支援

瞭解如何連線至 Azure DocumentDB 帳戶的 MongoDB 使用標準 MongoDB 連接字串 URI 格式的通訊協定支援。  

## <a name="get-the-accounts-connection-string-information"></a>取得帳戶的連線字串資訊

1. 在新視窗中，登入[Azure 入口網站](https://portal.azure.com)。
2. 在**左側導覽**列中的帳戶刀，按一下 [**連線字串**。 瀏覽至**帳戶刀**，Jumpbar 上按一下 [**更多服務** **DocumentDB (NoSQL)** ，，然後按一下然後選取 [通訊協定支援 MongoDB DocumentDB 帳戶。

    ![所有設定刀的螢幕擷取畫面](./media/documentdb-connect-mongodb-account/SettingsBlade.png)

3. **連接字串資訊**刀隨即開啟，並已連線至帳戶使用 MongoDB，包括預先建構的連線字串的驅動程式所需的所有資訊。

    ![連線字串刀的螢幕擷取畫面](./media/documentdb-connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>連線字串需求

請注意，DocumentDB 支援標準 MongoDB 連接字串 URI 格式，有幾個特定需求，請務必︰ DocumentDB 帳戶需要驗證]，然後透過 SSL 安全通訊。  因此，連接字串格式是︰

    mongodb://username:password@host:port/[database]?ssl=true

位置此字串的值可在連線字串刀如上所示。

- 使用者名稱 （必要）
    - DocumentDB 帳戶名稱
- 密碼 （必要）
    - DocumentDB 帳戶密碼
- Host （主機) （必要）
    - FQDN 的 DocumentDB 帳戶
- 連接埠 （必要）
    - 10250
- （選擇性） 的資料庫
    - 連線所使用的預設資料庫
- ssl = true 時 （必要）

例如，請考慮將上述的連接字串資訊中顯示的帳戶。  有效的連接字串是︰
    
    mongodb://contoso123:<password@contoso123.documents.azure.com:10250/mydatabase?ssl=true

## <a name="connecting-with-the-c-driver-for-mongodb"></a>連線與 C# 驅動程式 MongoDB
已經說明過，所有 DocumentDB 帳戶都需要驗證和透過 SSL 安全通訊。 雖然 MongoDB 連接字串 URI 格式支援 ssl = true 查詢字串參數，使用與 MongoDB C# 驅動程式需要使用 MongoClientSettings 物件，建立 MongoClient 時。  有了上述的帳戶資訊，下列程式碼片段會顯示如何連線到您的帳戶，並使用 「 工作 」 資料庫。

            MongoClientSettings settings = new MongoClientSettings();
            settings.Server = new MongoServerAddress("contoso123.documents.azure.com", 10250);
            settings.UseSsl = true;
            settings.SslSettings = new SslSettings();
            settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

            MongoIdentity identity = new MongoInternalIdentity("Tasks", "contoso123");
            MongoIdentityEvidence evidence = new PasswordEvidence("<password>");

            settings.Credentials = new List<MongoCredential>()
            {
                new MongoCredential("SCRAM-SHA-1", identity, evidence)
            };
            MongoClient client = new MongoClient(settings);
            var database = client.GetDatabase("Tasks",);
    

## <a name="next-steps"></a>後續步驟


- 瞭解如何[使用 MongoChef](documentdb-mongodb-mongochef.md)與通訊協定 DocumentDB 帳戶支援 MongoDB。
- 探索 DocumentDB MongoDB[範例](documentdb-mongodb-samples.md)的通訊協定支援。

 
