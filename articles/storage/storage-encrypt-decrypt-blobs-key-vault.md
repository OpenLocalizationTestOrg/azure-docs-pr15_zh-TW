<properties
    pageTitle="教學課程︰ 加密並解密二進位大型物件中使用 Azure 金鑰保存庫 Microsoft Azure 儲存體 |Microsoft Azure"
    description="本教學課程中會引導您如何加密並解密 blob 的 Microsoft Azure 儲存體 Azure 金鑰保存庫與使用用戶端的加密。"
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="required"
    ms.date="10/18/2016"
    ms.author="lakasa;robinsh"/>

# <a name="tutorial-encrypt-and-decrypt-blobs-in-microsoft-azure-storage-using-azure-key-vault"></a>教學課程︰ 加密並解密二進位大型物件中使用 Azure 金鑰保存庫 Microsoft Azure 儲存體

## <a name="introduction"></a>簡介

本教學課程會說明如何使用的用戶端儲存加密與 Azure 金鑰保存庫。 它逐步引導您進行加密並解密在主控台應用程式中使用這些技術 blob 的方式。

**估計完成時間︰** 20 分鐘

Azure 鍵保存庫相關的概觀資訊，請參閱[什麼是 Azure 金鑰保存庫？](../key-vault/key-vault-whatis.md)。

用戶端加密 Azure 儲存空間的概觀資訊，請參閱[用戶端加密和 Azure 金鑰保存庫的 Microsoft Azure 儲存體](storage-client-side-encryption.md)。


## <a name="prerequisites"></a>必要條件

若要完成此教學課程中，您必須具備下列項目︰

- Azure 儲存體帳戶
- Visual Studio 2013 或更新版本
- Azure PowerShell


## <a name="overview-of-client-side-encryption"></a>用戶端加密概觀

如需用戶端加密 Azure 儲存空間的概觀，請參閱[用戶端加密和 Azure 金鑰保存庫的 Microsoft Azure 儲存體](storage-client-side-encryption.md)

以下是用戶端加密的運作方式的簡短描述︰

1. Azure 儲存體用戶端 SDK 產生的內容的加密金鑰 (CEK)，也就是其中之一單次用對稱金鑰。
2. 使用此 CEK 加密客戶資料。
3. 使用加密金鑰 (KEK) （加密），然後包裝 CEK。 KEK 索引鍵的識別碼由和可以非對稱金鑰組或對稱金鑰和可以管理本機或 Azure 金鑰保存庫中儲存。 本身的儲存空間用戶端不會有權存取 KEK。 只要叫用索引鍵的文繞圖演算法所提供的金鑰保存庫。 客戶可以選擇鍵文繞圖/形式想的話，使用自訂的提供者。
4. 加密的資料然後上傳到 Azure 儲存體服務。


## <a name="set-up-your-azure-key-vault"></a>設定您的 Azure 金鑰保存庫
以繼續進行本教學課程中，您需要執行下列步驟，[開始使用 Azure 金鑰保存庫](../key-vault/key-vault-get-started.md)教學課程所述︰

- 建立索引鍵保存庫。
- 新增索引鍵保存庫鍵或私人。
- Azure Active Directory 中註冊應用程式。
- 授權使用金鑰或私人的應用程式。

請記下 ClientID 和與 Azure Active Directory 中登錄應用程式時產生的 ClientSecret。

索引鍵保存庫中建立兩個機碼。 我們教學課程的其餘部分假設您使用下列名稱︰ ContosoKeyVault 和 TestRSAKey1。


## <a name="create-a-console-application-with-packages-and-appsettings"></a>建立主控台應用程式套件與和 AppSettings

在 Visual Studio 中，建立新的主控台應用程式。

新增必要的 nuget 封包封裝管理員主控台中檢視。

    Install-Package WindowsAzure.Storage

    // This is the latest stable release for ADAL.
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

    Install-Package Microsoft.Azure.KeyVault
    Install-Package Microsoft.Azure.KeyVault.Extensions


新增和 AppSettings app.config。

    <appSettings>
        <add key="accountName" value="myaccount"/>
        <add key="accountKey" value="theaccountkey"/>
        <add key="clientId" value="theclientid"/>
        <add key="clientSecret" value="theclientsecret"/>
        <add key="container" value="stuff"/>
    </appSettings>

新增下列`using`陳述式，並請務必以新增至專案 System.Configuration 的參考。

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System.Configuration;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using Microsoft.Azure.KeyVault;
    using System.Threading;     
    using System.IO;


## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>加入方法以取得主控台應用程式的權杖

需要存取您的主要保存庫驗證的金鑰保存庫課程會使用下列方法。

    private async static Task<string> GetToken(string authority, string resource, string scope)
    {
        var authContext = new AuthenticationContext(authority);
        ClientCredential clientCred = new ClientCredential(
            ConfigurationManager.AppSettings["clientId"],
            ConfigurationManager.AppSettings["clientSecret"]);
        AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

        if (result == null)
            throw new InvalidOperationException("Failed to obtain the JWT token");

        return result.AccessToken;
    }

## <a name="access-storage-and-key-vault-in-your-program"></a>存取您的程式中的儲存空間及金鑰保存庫

在主要函數，新增下列程式碼。

    // This is standard code to interact with Blob storage.
    StorageCredentials creds = new StorageCredentials(
        ConfigurationManager.AppSettings["accountName"],
        ConfigurationManager.AppSettings["accountKey"]);
    CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
    CloudBlobClient client = account.CreateCloudBlobClient();
    CloudBlobContainer contain = client.GetContainerReference(ConfigurationManager.AppSettings["container"]);
    contain.CreateIfNotExists();

    // The Resolver object is used to interact with Key Vault for Azure Storage.
    // This is where the GetToken method from above is used.
    KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);


> [AZURE.NOTE] 索引鍵保存庫物件模型
>
>請務必瞭解實際兩個機碼保存庫物件模型，必須注意︰ 一根據 REST API （KeyVault 命名空間），另一個是用戶端加密副檔名。

> 索引鍵保存庫用戶端互動 REST API，並瞭解 JSON 網頁按鍵與機密兩種類型的金鑰保存庫中所包含的項目。

> 金鑰保存庫副檔名是看起來似乎特別建立用戶端加密 Azure 儲存體中的類別。 包含索引鍵 (IKey) 和類別根據鍵解析程式的概念介面。 有兩個實作必須知道的 IKey: RSAKey 和 SymmetricKey。 與索引鍵保存庫中所包含的項目一致。 現在會發生，但現在它們是獨立類別 （以便索引鍵和私人擷取金鑰保存庫用戶端不會實作 IKey）。


## <a name="encrypt-blob-and-upload"></a>加密 blob 並上傳
新增下列程式碼，若要加密 blob，並將它上傳到您 Azure 儲存的帳戶。 用**ResolveKeyAsync**方法會傳回 IKey。


    // Retrieve the key that you created previously.
    // The IKey that is returned here is an RsaKey.
    // Remember that we used the names contosokeyvault and testrsakey1.
    var rsa = cloudResolver.ResolveKeyAsync("https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", CancellationToken.None).GetAwaiter().GetResult();


    // Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
    BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
    BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

    // Reference a block blob.
    CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

    // Upload using the UploadFromStream method.
    using (var stream = System.IO.File.OpenRead(@"C:\data\MyFile.txt"))
        blob.UploadFromStream(stream, stream.Length, null, options, null);


以下是從[Azure 傳統入口網站](https://manage.windowsazure.com)已加密金鑰保存庫中儲存以金鑰使用用戶端加密 blob 的螢幕擷取畫面。 **金鑰識別碼為**屬性是做為 KEK 金鑰保存庫中的索引鍵的 URI。 **EncryptedKey**屬性包含 CEK 加密的版本。

![螢幕擷取畫面顯示加密的中繼資料的 Blob 中繼資料][1]

> [AZURE.NOTE] 如果您查看 BlobEncryptionPolicy 建構函式時，您會看到它可以接受鍵及/或解析程式。 請注意，現在您無法使用解析程式加密，因為它會在目前不支援預設鍵。



## <a name="decrypt-blob-and-download"></a>解密 blob 並下載
解密實際上是時使用解析程式類別意義。 使用加密金鑰的識別碼是在其中繼資料，blob 與相關聯，因此沒有理由擷取金鑰及記住鍵與 blob 之間的關聯。 您只需確定索引鍵，仍會在金鑰保存庫。   

RSA 金鑰的私密金鑰仍會保留在金鑰保存庫，因此解密發生時，包含 CEK blob 中繼資料加密金鑰會傳送至金鑰保存庫解密。

新增下列解密剛才上傳 blob。

    // In this case, we will not pass a key and only pass the resolver because
    // this policy will only be used for downloading / decrypting.
    BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
    BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

    using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
        blob.DownloadToStream(np, null, options, null);


> [AZURE.NOTE] 有數種其他種類的解析程式來輕鬆地金鑰管理，包括︰ AggregateKeyResolver 和 CachingKeyResolver。


## <a name="use-key-vault-secrets"></a>使用金鑰保存庫密碼
若要使用用戶端加密的密碼的方式是透過 SymmetricKey 類別，因為私人基本上對稱金鑰。 不過，如上所述，於 SymmetricKey 未對應鍵保存庫中的密碼。 有了解的一些事項︰


- SymmetricKey 的按鍵必須是固定的長度︰ 128、 192、 256、 384 或 512 位元。
- SymmetricKey 的按鍵應該 Base64 編碼。
- 作為 SymmetricKey 金鑰保存庫私人需要金鑰保存庫中的 「 應用程式/8 位元資料流 「 內容類型。

在 [可做為 SymmetricKey 的金鑰保存庫中建立私人的 PowerShell 中，以下是範例。
附註︰ 硬式編碼，$key，該值只示範用途。 在您的程式碼中，您需要產生此按鍵。

    // Here we are making a 128-bit key so we have 16 characters.
    //  The characters are in the ASCII range of UTF8 so they are
    //  each 1 byte. 16 x 8 = 128.
    $key = "qwertyuiopasdfgh"
    $b = [System.Text.Encoding]::UTF8.GetBytes($key)
    $enc = [System.Convert]::ToBase64String($b)
    $secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

    // Substitute the VaultName and Name in this command.
    $secret = Set-AzureKeyVaultSecret -VaultName 'ContoseKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"

您主控台應用程式中，您可以使用與之前相同的呼叫以擷取此 SymmetricKey 為私人。

    SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
        "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
        CancellationToken.None).GetAwaiter().GetResult();

就是它。 享受 ！

## <a name="next-steps"></a>後續步驟

如需有關如何使用 Microsoft Azure 儲存體 C# 的詳細資訊，請參閱[Microsoft Azure 儲存用戶端文件庫的.NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)。

如需有關 Blob REST API 的詳細資訊，請參閱[Blob 服務 REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)。

Microsoft Azure 儲存體的最新資訊，請移至[Microsoft Azure 儲存體小組部落格](http://blogs.msdn.com/b/windowsazurestorage/)。


<!--Image references-->
[1]: ./media/storage-encrypt-decrypt-blobs-key-vault/blobmetadata.png
