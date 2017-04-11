<properties
    pageTitle="Microsoft Azure 儲存空間的用戶端加密與 Python |Microsoft Azure"
    description="Azure 儲存用戶端文件庫 python 支援用戶端加密的最高的安全性 Azure 儲存體應用程式。"
    services="storage"
    documentationCenter="python"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>


# <a name="client-side-encryption-with-python-for-microsoft-azure-storage"></a>將加密 Python 與用戶端的 Microsoft Azure 儲存體

[AZURE.INCLUDE [storage-selector-client-side-encryption-include](../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>概觀

[Azure 儲存用戶端文件庫 python](https://pypi.python.org/pypi/azure-storage)支援加密至 Azure 儲存體上, 傳和下載至用戶端時解密資料之前的用戶端應用程式中的資料。

>[AZURE.NOTE] Azure 儲存體 Python 文件庫是在預覽中。

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>加密和解密透過信封技巧
加密和解密的程序會遵循信封技巧。

### <a name="encryption-via-the-envelope-technique"></a>加密透過信封技巧
透過信封技巧加密運作方式如下︰

1.  Azure 儲存用戶端文件庫產生的內容的加密金鑰 (CEK)，也就是其中之一單次用對稱金鑰。

2.  使用此 CEK 加密使用者資料。

3.  使用加密金鑰 (KEK) （加密），然後包裝 CEK。 KEK 由索引鍵的識別碼，並可非對稱金鑰組或對稱鍵，請在本機管理。
本身的儲存空間用戶端程式庫永遠不會有權存取 KEK。 文件庫叫用的 KEK 所提供的索引鍵的文繞圖演算法。 使用者可以解除金鑰文繞圖/包裝使用自訂的提供者，視需要選擇。

4.  加密的資料然後上傳到 Azure 儲存體服務。 換行的鍵，以及某些其他加密中繼資料儲存為中繼資料 （在 blob)，或插補加密資料 （佇列中的郵件和表格項目）。

### <a name="decryption-via-the-envelope-technique"></a>透過信封技巧解密
透過信封技巧解密的運作方式如下︰

1.  用戶端程式庫假設使用者本機管理加密金鑰 (KEK)。 不需要使用者不會知道特定金鑰用於加密。 不過，索引鍵的解析程式，鍵解析重要的不同識別項，可以設定及使用。

2.  用戶端文件庫下載加密以及任何加密資料儲存在該服務的資料。

3.  包裝內容的加密金鑰 (CEK) 是 （解密） 包裝使用加密金鑰 (KEK)。 以下一次，用戶端程式庫沒有 KEK 存取。 只要叫用自訂提供者的 unwrapping 演算法。

4.  內容的加密金鑰 (CEK) 然後用於解密加密的使用者資料。

## <a name="encryption-mechanism"></a>加密機制  
儲存用戶端文件庫使用[AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard)才能加密使用者資料。 具體來說，[加密區塊鏈結 (CBC)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29)模式 AES。 每個服務運作有些不同，因此我們會將每個以下討論。

### <a name="blobs"></a>二進位大型物件
用戶端程式庫目前支援整個 blob 只的加密。 當使用者使用**建立**的明確地說，支援加密 * 方法。 下載完成兩與範圍下載受到支援，以及上傳和下載平行有。

加密，期間的用戶端文件庫會產生隨機初始化向量 (IV) 的 16 位元，搭配使用的隨機內容加密金鑰 (CEK) 32 個位元組，，並執行使用這項資訊的 blob 資料信封加密。 Blob 中繼資料，以及服務加密的項目時，就會儲存換的 CEK 和一些額外的加密中繼資料。

>[AZURE.WARNING] 如果您要編輯或上傳您自己的 blob 的中繼資料，您需要確認此中繼資料會保留。 如果您上傳新的中繼資料，而此中繼資料，換的 CEK、 IV 和其他中繼資料將會遺失，blob 內容永遠不會擷取一次。

下載加密的項目涉及擷取內容的使用**取得**整個 blob * 便利性方法。 包裝的 CEK 會解除包裝，並用於 IV （在此情況下儲存為 blob 中繼資料） 與使用者傳回解密的資料。

下載的任意範圍 (**取得*** 範圍參數方法傳入) 加密的項目包括調整提供的使用者，若要取得少量可用於成功解密要求的範圍的其他資料的範圍。

封鎖二進位大型物件和頁面 blob 僅可加密/解密使用此配置。 目前有不支援的加密附加二進位大型物件。

### <a name="queues"></a>佇列
由於佇列中的郵件可為任何格式，用戶端程式庫定義訊息文字中包含初始化向量 (IV) 和加密內容的加密金鑰 (CEK) 的自訂格式。

加密，期間的用戶端文件庫會產生隨機 IV 的 16 位元與 32 位元隨機 CEK 一併，並執行信封加密的佇列中的郵件文字使用這項資訊。 包裝的 CEK 和一些額外的加密中繼資料，然後再加入加密佇列中的郵件。 （如下所示） 此修改的訊息會儲存在該服務。

    <MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

期間解密換行的鍵是建構從佇列中的郵件，解除包裝。 IV 是也建構從佇列中的郵件，以及包裝鍵解密佇列中郵件資料。 請注意，加密中繼資料小型 （在下 500 個位元組），因此影響時，會計算佇列中郵件的 64 KB 的限制，應該可管理。

### <a name="tables"></a>表格
用戶端程式庫支援加密的實體屬性的插入和取代作業。

>[AZURE.NOTE] 目前不支援合併。 由於屬性的子集可能已加密先前使用不同的金鑰，只要合併的新屬性及更新中繼資料會造成資料遺失。 合併可能需要進行額外的服務的來電至現有的實體讀取服務，或使用新的金鑰，每個屬性，兩者都不適用的效能。

資料表資料加密如下所示︰

1.  使用者會指定要加密的屬性。

2.  用戶端程式庫產生隨機內容加密金鑰 (CEK) 的 32 位元的每個實體，以及 16 位元隨機初始化向量 (IV)，並執行信封加密衍生每個屬性的新 IV 加密個別的屬性。 加密的屬性會儲存為二進位資料。

3.  包裝的 CEK 和一些額外的加密中繼資料會儲存為兩個額外的保留內容。 第一個保留屬性 (\_ClientEncryptionMetadata1) 是字串屬性保留 IV、 版本及換行的鍵的相關資訊。 第二個保留屬性 (\_ClientEncryptionMetadata2) 二進位屬性保存加密之內容的相關資訊。 此第二個屬性中的資訊 (\_ClientEncryptionMetadata2) 本身經過加密。

4.  這些額外的保留屬性，所需的加密，因為使用者現在可能具有只 250，而不是 252 的自訂屬性。 實體的總大小必須小於 1 MB。

    請注意，可以加密只有字串屬性。 如果其他類型的內容是加密，他們必須轉換成字串。 加密的字串儲存服務為二進位內容，且之後解密轉換回字串 （未經處理的字串，不透過類型 EdmType.STRING EntityProperties）。

    如果是表格，除了加密原則，使用者必須指定加密的屬性。 這可以由 TableEntity 物件類型設定為 [EdmType.STRING 中任一儲存這些屬性和加密設定為 true 或設定 encryption_resolver_function tableservice 物件上。 加密解析程式是分割索引鍵的資料列識別碼與屬性名稱，然後傳回一個 boolean，指出該屬性是否應該加密的函數。 加密，期間的用戶端文件庫會使用這項資訊來決定屬性是否應該加密在寫入網路。 代理人也提供邏輯周圍屬性的加密的可能性。 (例如，如果 X，然後加密屬性的; 否則加密屬性 A 和 B)請注意，不提供這項資訊讀取或查詢的實體時所需。

### <a name="batch-operations"></a>批次作業
加密原則適用於批次中的所有資料列。 用戶端程式庫內部會產生新的隨機 IV 和每個資料列的隨機 CEK 批次中。 使用者也可以選擇加密解析程式中定義這種行為來加密不同的每一批次中作業的屬性。
如果以批次的 tableservice batch() 方法建立做為內容管理員，tableservice 加密原則會自動套用至批次。 如果以批次由明確呼叫，就必須做為參數傳遞，左修改批次的存留加密原則。
請注意的實體加密插入到批次使用批次的加密原則 （實體未加密的認可使用 tableservice 加密原則批次）。

### <a name="queries"></a>查詢
若要執行查詢作業，您必須指定可以解決結果集中的所有按鍵的按鍵解析程式。 如果查詢結果中所包含的實體無法解析到的提供者，用戶端文件庫會引發錯誤。 執行伺服器端計帳任何查詢用戶端文件庫會新增特殊加密中繼資料屬性 (\_ClientEncryptionMetadata1 和\_ClientEncryptionMetadata2) 預設為選取的資料行。

>[AZURE.IMPORTANT] 使用用戶端加密時，則請注意下列重要事項︰
>
>- 從讀取或寫入加密項目，請使用整個 blob 上傳指令和範圍/整個 blob 下載指令。 避免寫入加密的項目，使用通訊協定作業，例如將區塊、 將封鎖清單、 撰寫的頁面或清除網頁。否則，您可能會損毀加密的項目，並使其無法讀取。
>
>- 如果是表格，類似的限制式存在。 請小心更新加密的內容而不會更新加密中繼資料。
>
>- 如果您在 [加密的項目設定中繼資料，您可能會覆寫解密，因為設定中繼資料不是累加所需的加密相關中繼資料。 這也適用於快照集;避免指定中繼資料建立快照的加密項目時。 如果必須設定中繼資料，確定呼叫**get_blob_metadata**方法，第一次以取得目前的加密中繼資料，並避免並行寫入時設定中繼資料。
>
>- 啟用的使用者，應只使用加密資料服務物件上**require_encryption**旗標。 取得詳細資訊，請參閱下列內容。

儲存空間的用戶端程式庫預期實作以下介面提供的 KEK 及索引鍵的解析程式。 [Azure 鍵保存庫](https://azure.microsoft.com/services/key-vault/)Python KEK 管理支援已擱置，將整合完成此文件庫。


## <a name="client-api--interface"></a>用戶端 API / 介面
使用者在建立儲存服務物件 (亦即 blockblobservice) 之後，可能會指派給構成加密原則的欄位的值︰ key_encryption_key，key_resolver_function，及 require_encryption。 使用者可以提供 KEK，只解析程式，或兩者。 key_encryption_key 是基本金鑰類型的用來識別主要的識別碼，以及提供邏輯的文繞圖/形式。 key_resolver_function 用來在解密過程中解決鍵。 會傳回指定索引鍵的識別項有效 KEK。 這可以讓使用者選擇多個位置管理多個鍵。

KEK 必須實作成功加密資料以下兩種方法︰
- wrap_key(cek)︰ 換行使用使用者選擇演算法指定的 CEK （位元組）。 傳回換行的鍵。
- get_key_wrap_algorithm()︰ 傳回用來自動換行鍵的演算法。
- get_kid()︰ 此 KEK 傳回字串金鑰識別碼。
KEK 必須實作成功解密資料以下兩種方法︰
- unwrap_key （cek、 演算法）︰ 傳回指定的 CEK 使用字串指定演算法的包裝的格式。
- get_kid()︰ 此 KEK 傳回字串索引鍵的識別碼。

索引鍵的解析程式必須至少實作的方法，指定重要的識別碼，會傳回對應的 KEK 實作上述介面。 這種方式是分派給服務物件上的 [key_resolver_function] 屬性。

- 加密，一律使用金鑰，並不存在的金鑰會導致錯誤。
- 解密︰
    - 如果以取得金鑰指定叫用金鑰解析程式。 如果解析程式指定，但是並沒有主要項目對應，會發生錯誤。
    - 如果未指定解析程式，但指定按鍵，如果其識別項符合所需的金鑰識別碼使用金鑰。 如果不相符的識別碼，會發生錯誤。

      加密範例中 azure.storage.samples<fix URL>示範更詳細的端對端案例二進位大型物件、 佇列和表格。
        範例實作 KEK 和重要解析程式中所提供的範例檔案為 KeyWrapper 和 KeyResolver 分別。

### <a name="requireencryption-mode"></a>RequireEncryption 模式
使用者也可以啟用位置必須加密所有上傳與下載作業的模式。 在此模式中，嘗試資料但不含加密原則的上傳或下載未加密服務的資料將會失敗在用戶端。 **Require_encryption**標幟服務物件上的控制項的這個問題。

### <a name="blob-service-encryption"></a>Blob 服務加密
設定原則欄位加密 blockblobservice 物件上。 其他處理用戶端程式庫內部。

    # Create the KEK used for encryption.
    # KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
    kek = KeyWrapper('local:key1') # Key identifier

    # Create the key resolver used for decryption.
    # KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
    key_resolver = KeyResolver()
    key_resolver.put_key(kek)

    # Set the KEK and key resolver on the service object.
    my_block_blob_service.key_encryption_key = kek
    my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

    # Upload the encrypted contents to the blob.
    my_block_blob_service.create_blob_from_stream(container_name, blob_name, stream)

    # Download and decrypt the encrypted contents from the blob.
    blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)

### <a name="queue-service-encryption"></a>佇列中服務加密
設定原則欄位加密 queueservice 物件上。 其他處理用戶端程式庫內部。

    # Create the KEK used for encryption.
    # KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
    kek = KeyWrapper('local:key1') # Key identifier

    # Create the key resolver used for decryption.
    # KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
    key_resolver = KeyResolver()
    key_resolver.put_key(kek)

    # Set the KEK and key resolver on the service object.
    my_queue_service.key_encryption_key = kek
    my_queue_service.key_resolver_funcion = key_resolver.resolve_key

    # Add message
    my_queue_service.put_message(queue_name, content)

    # Retrieve message
    retrieved_message_list = my_queue_service.get_messages(queue_name)

### <a name="table-service-encryption"></a>表格服務加密
除了建立加密原則，並將它設定要求選項，您必須上**tableservice**中，指定**encryption_resolver_function** ，或設定 EntityProperty 加密屬性。

### <a name="using-the-resolver"></a>使用解析程式

    # Create the KEK used for encryption.
    # KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
    kek = KeyWrapper('local:key1') # Key identifier

    # Create the key resolver used for decryption.
    # KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
    key_resolver = KeyResolver()
    key_resolver.put_key(kek)

    # Define the encryption resolver_function.
    def my_encryption_resolver(pk, rk, property_name):
            if property_name == 'foo':
                    return True
            return False

    # Set the KEK and key resolver on the service object.
    my_table_service.key_encryption_key = kek
    my_table_service.key_resolver_funcion = key_resolver.resolve_key
    my_table_service.encryption_resolver_function = my_encryption_resolver

    # Insert Entity
    my_table_service.insert_entity(table_name, entity)

    # Retrieve Entity
    # Note: No need to specify an encryption resolver for retrieve, but it is harmless to leave the property set.
    my_table_service.get_entity(table_name, entity['PartitionKey'], entity['RowKey'])

### <a name="using-attributes"></a>使用屬性
如上所述，可能會加密標示屬性，藉由將其儲存至 EntityProperty 物件並設定 [加密] 欄位。

    encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)

## <a name="encryption-and-performance"></a>加密和效能
請注意，加密額外的效能負荷您儲存資料的結果。 必須產生內容索引鍵和 IV 必須加密本身的內容，與其他的中繼資料必須格式，然後上傳。 此開銷會而定的加密的資料量。 我們建議您客戶永遠測試應用程式開發時的效能。

## <a name="next-steps"></a>後續步驟

- 下載[Azure 儲存用戶端文件庫 Java PyPi 套件](https://pypi.python.org/pypi/azure-storage)
- 下載[Azure 儲存體 Python 的用戶端文件庫來源 GitHub 的程式碼](https://github.com/Azure/azure-storage-python)
