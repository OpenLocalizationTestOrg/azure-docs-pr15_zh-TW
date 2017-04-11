<properties
    pageTitle="Microsoft Azure 儲存空間的用戶端加密 java |Microsoft Azure"
    description="Java Azure 儲存用戶端程式庫最高的安全性 Azure 儲存體應用程式支援用戶端加密和 Azure 金鑰保存庫整合。"
    services="storage"
    documentationCenter="java"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>


# <a name="client-side-encryption-with-java-for-microsoft-azure-storage"></a>用戶端的加密 java Microsoft Azure 儲存體   

[AZURE.INCLUDE [storage-selector-client-side-encryption-include](../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>概觀  

[Azure 儲存用戶端文件庫 java](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)支援加密至 Azure 儲存體上, 傳和下載至用戶端時解密資料之前的用戶端應用程式中的資料。 文件庫儲存帳戶金鑰管理也支援與[Azure 金鑰保存庫](https://azure.microsoft.com/services/key-vault/)整合。

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>加密和解密透過信封技巧    
加密和解密的程序會遵循信封技巧。  

### <a name="encryption-via-the-envelope-technique"></a>加密透過信封技巧  
透過信封技巧加密運作方式如下︰  

1.  Azure 儲存用戶端文件庫產生的內容的加密金鑰 (CEK)，也就是其中之一單次用對稱金鑰。  

2.  使用此 CEK 加密使用者資料。   

3.  使用加密金鑰 (KEK) （加密），然後包裝 CEK。 KEK 金鑰的識別碼由和可以非對稱金鑰組或對稱金鑰和可以管理本機或 Azure 金鑰保存庫中儲存。  
本身的儲存空間用戶端程式庫永遠不會有權存取 KEK。 文件庫叫用索引鍵的文繞圖演算法所提供的金鑰保存庫。 使用者可以解除金鑰文繞圖/包裝使用自訂的提供者，視需要選擇。  

4.  加密的資料然後上傳到 Azure 儲存體服務。 換行的鍵，以及某些其他加密中繼資料儲存為中繼資料 （在 blob)，或插補加密資料 （佇列中的郵件和表格項目）。

### <a name="decryption-via-the-envelope-technique"></a>透過信封技巧解密  
透過信封技巧解密的運作方式如下︰  

1.  用戶端程式庫假設本機或 Azure 金鑰保存庫中的使用者就管理加密金鑰 (KEK)。 不需要使用者不會知道特定金鑰用於加密。 相反地，可以設定及使用解決索引鍵的索引鍵的不同識別項關鍵解析程式。  

2.  用戶端文件庫下載加密以及任何加密資料儲存在該服務的資料。  

3.  包裝內容的加密金鑰 (CEK) 是 （解密） 包裝使用加密金鑰 (KEK)。 以下一次，用戶端程式庫沒有 KEK 存取。 只要叫用自訂或金鑰保存庫提供者的 unwrapping 演算法。  

4.  內容的加密金鑰 (CEK) 然後用於解密加密的使用者資料。

## <a name="encryption-mechanism"></a>加密機制  
儲存用戶端文件庫使用[AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard)才能加密使用者資料。 具體來說，[加密區塊鏈結 (CBC)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29)模式 AES。 每個服務運作有些不同，因此我們會將每個以下討論。

### <a name="blobs"></a>二進位大型物件  
用戶端程式庫目前支援整個 blob 只的加密。 當使用者使用**上傳**時，支援加密*方法或* *openOutputStream** 方法。 支援下載，同時完成和範圍下載。  

加密，期間的用戶端文件庫會產生隨機初始化向量 (IV) 的 16 位元，搭配使用的隨機內容加密金鑰 (CEK) 32 個位元組，，並執行使用這項資訊的 blob 資料信封加密。 Blob 中繼資料，以及服務加密的項目時，就會儲存換的 CEK 和一些額外的加密中繼資料。

>[AZURE.WARNING] 如果您要編輯或上傳您自己的 blob 的中繼資料，您需要確認此中繼資料會保留。 如果您上傳新的中繼資料，而此中繼資料，換的 CEK、 IV 和其他中繼資料將會遺失，blob 內容永遠不會擷取一次。

下載加密的項目涉及擷取內容的使用**下載*/openInputStream*整個 blob * 便利性方法。 包裝的 CEK 會解除包裝，並用於 IV （在此情況下儲存為 blob 中繼資料） 與使用者傳回解密的資料。

下載的任意範圍 (**downloadRange*** 方法) 加密的項目包括調整提供的使用者，若要取得少量可用於成功解密要求的範圍的其他資料的範圍。  

所有 blob 類型 （封鎖二進位大型物件、 頁面二進位大型物件，以及附加 blob） 來加密/解密使用此配置。

### <a name="queues"></a>佇列  
由於佇列中的郵件可為任何格式，用戶端程式庫定義訊息文字中包含初始化向量 (IV) 和加密內容的加密金鑰 (CEK) 的自訂格式。  

加密，期間的用戶端文件庫會產生隨機 IV 的 16 位元與 32 位元隨機 CEK 一併，並執行信封加密的佇列中的郵件文字使用這項資訊。 包裝的 CEK 和一些額外的加密中繼資料，然後再加入加密佇列中的郵件。 （如下所示） 此修改的訊息會儲存在該服務。

    <MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

解密，期間換行的鍵是建構從佇列中的郵件，然後解除包裝。 IV 是也建構從佇列中的郵件，以及包裝鍵解密佇列中郵件資料。 請注意，加密中繼資料 （在下 500 個位元組），小型，影響時，會計算佇列中郵件的 64 KB 的限制，應該可管理。

### <a name="tables"></a>表格  
用戶端程式庫插入的支援的實體內容的加密和取代作業。

>[AZURE.NOTE] 合併列印目前不支援。 由於屬性的子集可能已加密先前使用不同的金鑰，只要合併的新屬性及更新中繼資料會造成資料遺失。 合併可能需要進行額外的服務的來電至現有的實體讀取服務，或使用新的金鑰，每個屬性，兩者都不適用的效能。

資料表資料加密如下所示︰  

1.  使用者會指定要加密的屬性。  

2.  用戶端程式庫產生隨機內容加密金鑰 (CEK) 的 32 位元的每個實體，以及 16 位元隨機初始化向量 (IV)，並執行信封加密衍生每個屬性的新 IV 加密個別的屬性。 加密的屬性會儲存為二進位資料。  

3.  包裝的 CEK 和一些額外的加密中繼資料會儲存為兩個額外的保留內容。 第一個保留的屬性 (_ClientEncryptionMetadata1) 是字串屬性保留 IV、 版本及換行的鍵的相關資訊。 第二個保留的屬性 (_ClientEncryptionMetadata2) 是二進位屬性保存加密之內容的相關資訊。 此第二個屬性 (_ClientEncryptionMetadata2) 中的資訊是加密本身。  

4.  這些額外的保留屬性，所需的加密，因為使用者現在可能具有只 250，而不是 252 的自訂屬性。 實體的總大小必須小於 1 MB。  

    請注意，可以加密只字串的屬性。 如果其他類型的內容是加密，他們必須轉換成字串。 加密的字串儲存服務為二進位內容，且之後解密轉換回字串。

    如果是表格，除了加密原則，使用者必須指定加密的屬性。 這可以經由任一指定 [加密] 屬性 （適用於衍生自 TableEntity POCO 實體） 或加密解析程式要求選項。 加密解析為代理人的分割索引鍵的資料列識別碼與屬性名稱，然後傳回一個 boolean，指出該屬性是否應該加密。 加密，期間的用戶端文件庫會決定屬性是否應該加密寫入到網路時使用這項資訊。 代理人也提供邏輯周圍屬性的加密的可能性。 (例如，如果 X，然後將加密的屬性; 否則加密 A 和 b 的內容)請注意，不提供這項資訊讀取或查詢的實體時所需。

### <a name="batch-operations"></a>批次作業  
批次作業相同 KEK 將使用過的批次運算中的所有資料列因為用戶端程式庫只允許一選項物件 （和因此一個的原則 KEK） 每個批次作業。 不過，用戶端文件庫會在內部產生新的隨機 IV 和每個資料列的隨機 CEK 批次中。 使用者也可以選擇加密解析程式中定義這種行為來加密不同的每一批次中作業的屬性。

### <a name="queries"></a>查詢  
若要執行查詢作業，您必須指定可以解決結果集中的所有按鍵的按鍵解析程式。 如果查詢結果中所包含的實體無法解析到的提供者，用戶端程式庫會引發錯誤。 執行伺服器端計帳任何查詢用戶端文件庫會將 （_ClientEncryptionMetadata1 和 _ClientEncryptionMetadata2） 的特殊加密中繼資料屬性預設加入所選資料行。

## <a name="azure-key-vault"></a>Azure 鍵保存庫  
Azure 鍵保存庫可協助保護加密金鑰和雲端應用程式與服務所使用的密碼。 藉由使用 Azure 金鑰保存庫，使用者可以加密金鑰和機密資料 （例如驗證金鑰儲存帳戶金鑰、 資料加密金鑰，。PFX 檔案及密碼) 使用受到硬體安全性模組 (Hsm)。 如需詳細資訊，請參閱[什麼是 Azure 金鑰保存庫？](../key-vault/key-vault-whatis.md)。

儲存空間的用戶端程式庫才能透過 Azure 提供的一般的架構管理金鑰使用金鑰保存庫核心文件庫。 使用者也可以取得使用金鑰保存庫副檔名文件庫的其他優點。 延伸文件庫提供簡單且順暢 Symmetric/RSA 本機和雲端金鑰提供者，以及使用彙總和快取的實用功能。

### <a name="interface-and-dependencies"></a>介面和相依性  
有三個機碼保存庫套件︰  

- azure keyvault 核心包含 IKey 和 IKeyResolver。 它是小型套件與無相依性。 Java 的儲存空間用戶端文件庫會定義它做為相依性。
- azure keyvault 包含金鑰保存庫其他用戶端。  
- 擴充 keyvault azure 功能包含包含的加密演算法 RSAKey 和 SymmetricKey 實作擴充程式碼。 它核心和 KeyVault 命名空間而定，並提供定義彙總的解析程式 （當使用者想要使用多個主要的提供者） 和快取的金鑰解決的功能。 雖然儲存用戶端文件庫不會直接根據這個套件中，如果使用者想要使用 Azure 金鑰保存庫，儲存其索引鍵，或使用金鑰保存庫副檔名取用本機及雲端密碼編譯提供者，必須將此套件。  

  金鑰保存庫專為最高值主索引鍵，然後節流每鍵保存庫的限制專為此設計。 在執行時使用金鑰保存庫的用戶端加密，慣用的模型是本機使用對稱主索引鍵儲存為金鑰保存庫中的機密並快取。 使用者必須執行下列動作︰  

1.  建立離線的密碼，並將它上傳到金鑰保存庫。  

2.  使用密碼的基底識別碼做為參數，解決目前版本的加密的密碼及快取本機這項資訊。 用於 CachingKeyResolver 快取。使用者不希望實作自己的快取邏輯。  

3.  建立加密原則時，做為輸入使用快取解決。
可以加密程式碼範例中找到更多關於金鑰保存庫使用方式。 <fix URL>  

## <a name="best-practices"></a>最佳作法  
加密支援都提供只 Java 的儲存空間用戶端文件庫。

>[AZURE.IMPORTANT] 使用用戶端加密時，則請注意下列重要事項︰
>  
>- 從讀取或寫入加密項目，請使用整個 blob 上傳指令和範圍/整個 blob 下載指令。 避免使用通訊協定作業，例如將區塊、 放置封鎖清單、 撰寫頁面、 清除頁面或附加區塊; 加密項目寫入否則，您可能會損毀加密的項目，並使其無法讀取。
>
>- 如果是表格，類似的限制式存在。 請小心更新加密的內容而不會更新加密中繼資料。  
>
>- 如果您在 [加密的項目設定中繼資料，您可能會覆寫解密，因為設定中繼資料不是累加所需的加密相關中繼資料。 這也適用於快照集;避免指定中繼資料建立快照的加密項目時。 如果必須設定中繼資料，確定呼叫**downloadAttributes**方法，第一次以取得目前的加密中繼資料，並避免並行寫入時設定中繼資料。  
>
>- 啟用**requireEncryption**中的旗標應該只使用加密資料的使用者的預設要求選項。 取得詳細資訊，請參閱下列內容。  

## <a name="client-api--interface"></a>用戶端 API / 介面  
建立時 EncryptionPolicy 物件，使用者可以提供只鍵 （實作 IKey），請只器 （實作 IKeyResolver），或兩者。 IKey 是基本金鑰類型的用來識別主要的識別碼，以及提供邏輯的文繞圖/形式。 IKeyResolver 用來在解密過程中解決鍵。 它會定義會傳回指定索引鍵的識別項 IKey ResolveKey 方法。 這可以讓使用者選擇多個位置管理多個鍵。

- 加密，一律使用金鑰，並不存在的金鑰會導致錯誤。  
- 解密︰  
    - 如果以取得金鑰指定叫用金鑰解析程式。 如果解析程式指定，但是並沒有主要項目對應，會發生錯誤。  
    - 如果未指定解析程式，但指定按鍵，如果其識別項符合所需的金鑰識別碼使用金鑰。 如果不相符的識別碼，會發生錯誤。  

      [加密範例](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples)<fix URL>金鑰保存庫整合以及示範更詳細的端對端案例二進位大型物件、 佇列和表格。

### <a name="requireencryption-mode"></a>RequireEncryption 模式  
使用者也可以啟用位置必須加密所有上傳與下載作業的模式。 在此模式中，嘗試資料但不含加密原則的上傳或下載未加密服務的資料將會失敗在用戶端。 要求選項物件的**requireEncryption**旗標控制此行為。 如果您的應用程式會加密儲存在 Azure 儲存體中的所有物件，您可以在該服務的用戶端物件的預設要求選項設定**requireEncryption**屬性。   

例如，使用**CloudBlobClient.getDefaultRequestOptions().setRequireEncryption(true)**執行透過的用戶端物件的所有 blob 作業都需要加密。

### <a name="blob-service-encryption"></a>Blob 服務加密  
建立**BlobEncryptionPolicy**物件並將其在 [邀請] 選項 （每 API 或使用**DefaultRequestOptions**用戶端層級）。 其他處理用戶端程式庫內部。

    // Create the IKey used for encryption.
    RsaKey key = new RsaKey("private:key1" /* key identifier */);

    // Create the encryption policy to be used for upload and download.
    BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

    // Set the encryption policy on the request options.
    BlobRequestOptions options = new BlobRequestOptions();
    options.setEncryptionPolicy(policy);

    // Upload the encrypted contents to the blob.
    blob.upload(stream, size, null, options, null);

    // Download and decrypt the encrypted contents from the blob.
    ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
    blob.download(outputStream, null, options, null);

### <a name="queue-service-encryption"></a>佇列中服務加密  
建立**QueueEncryptionPolicy**物件並將其在 [邀請] 選項 （每 API 或使用**DefaultRequestOptions**用戶端層級）。 其他處理用戶端程式庫內部。

    // Create the IKey used for encryption.
    RsaKey key = new RsaKey("private:key1" /* key identifier */);

    // Create the encryption policy to be used for upload and download.
    QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

    // Add message
    QueueRequestOptions options = new QueueRequestOptions();
    options.setEncryptionPolicy(policy);

    queue.addMessage(message, 0, 0, options, null);

    // Retrieve message
    CloudQueueMessage retrMessage = queue.retrieveMessage(30, options, null);

### <a name="table-service-encryption"></a>表格服務加密  
除了建立加密原則，並將它設定要求選項，您必須中**TableRequestOptions**，指定**EncryptionResolver** ，或設定的實體 getter 及設定器的 [加密] 屬性。

### <a name="using-the-resolver"></a>使用解析程式  

    // Create the IKey used for encryption.
    RsaKey key = new RsaKey("private:key1" /* key identifier */);

    // Create the encryption policy to be used for upload and download.
    TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

    TableRequestOptions options = new TableRequestOptions()
    options.setEncryptionPolicy(policy);
    options.setEncryptionResolver(new EncryptionResolver() {
        public boolean encryptionResolver(String pk, String rk, String key) {
            if (key == "foo")
            {
                return true;
            }
            return false;
        }
    });

    // Insert Entity
    currentTable.execute(TableOperation.insert(ent), options, null);

    // Retrieve Entity
    // No need to specify an encryption resolver for retrieve
    TableRequestOptions retrieveOptions = new TableRequestOptions()
    retrieveOptions.setEncryptionPolicy(policy);

    TableOperation operation = TableOperation.retrieve(ent.PartitionKey, ent.RowKey, DynamicTableEntity.class);
    TableResult result = currentTable.execute(operation, retrieveOptions, null);

### <a name="using-attributes"></a>使用屬性  
如上所述，如果實體實作 TableEntity，屬性 getter 和設定器可以會使用 [加密] 屬性，而不是指定**EncryptionResolver**裝飾。

    private string encryptedProperty1;

    @Encrypt
    public String getEncryptedProperty1 () {
        return this.encryptedProperty1;
    }

    @Encrypt
    public void setEncryptedProperty1(final String encryptedProperty1) {
        this.encryptedProperty1 = encryptedProperty1;
    }

## <a name="encryption-and-performance"></a>加密和效能  
請注意，加密額外的效能負荷您儲存資料的結果。 必須產生內容索引鍵和 IV 必須加密本身的內容，與其他的中繼資料必須格式化和上傳。 這項負荷會而定的加密的資料量。 我們建議您客戶永遠測試應用程式開發時的效能。

## <a name="next-steps"></a>後續步驟  

- 下載[Azure 儲存用戶端文件庫 Java Maven 套件](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)  
- 下載[Azure 儲存從 GitHub Java 來源程式碼的用戶端文件庫](https://github.com/Azure/azure-storage-java)   
- 下載 Azure 金鑰保存庫 Maven 文件庫的 Java Maven 套件︰
    - [核心](http://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault-core)套件
    - [用戶端](http://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault)套件
- 請造訪[Azure 鍵地窖文件](../key-vault/key-vault-whatis.md)  
