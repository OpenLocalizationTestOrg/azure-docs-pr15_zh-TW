<properties 
    pageTitle="使用.NET 建立 ContentKeys" 
    description="瞭解如何建立資產提供安全存取的內容索引鍵。" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="juliako"/>


#<a name="create-contentkeys-with-net"></a>使用.NET 建立 ContentKeys

> [AZURE.SELECTOR]
- [其餘](media-services-rest-create-contentkey.md)
- [.NET](media-services-dotnet-create-contentkey.md)

媒體服務可讓您建立並傳送加密的資產。 **ContentKey**提供安全存取您的**資產**s。 

當您建立新的資產 （例如之前您[上傳檔案](media-services-dotnet-upload-files.md)） 時，您可以指定下列的加密選項︰ **StorageEncrypted**、 **CommonEncryptionProtected**或**EnvelopeEncryptionProtected**。 

當您傳送至您的用戶端的資產時，您可以[設定的動態加密資產](media-services-dotnet-configure-asset-delivery-policy.md)的其中一個下列兩個加密︰ **DynamicEnvelopeEncryption**或**DynamicCommonEncryption**。

加密的資產有**ContentKey**s 與相關聯。 本文將說明如何建立內容索引鍵。

>[AZURE.NOTE] 在建立新的**StorageEncrypted**資產，使用媒體服務.NET SDK， **ContentKey**自動建立並連結的資產。

##<a name="contentkeytype"></a>ContentKeyType

其中一個值，您必須將何時建立內容索引鍵是重要的內容類型。 選擇其中一個下列的值。 

    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for configuration encryption.
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }

##<a id="envelope_contentkey"></a>建立信封類型 ContentKey

下列程式碼片段建立信封加密類型的內容的金鑰。 然後建立索引鍵關聯與指定的資產。

    static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
    {
        // Create envelope encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.EnvelopeEncryption);

        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int size)
    {
        byte[] randomBytes = new byte[size];
        using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
        {
            rng.GetBytes(randomBytes);
        }

        return randomBytes;
    }

通話

    IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);



##<a id="common_contentkey"></a>建立一般類型 ContentKey    

下列程式碼片段建立一般加密類型的內容的金鑰。 然後建立索引鍵關聯與指定的資產。

    static public IContentKey CreateCommonTypeContentKey(IAsset asset)
    {
        // Create common encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.CommonEncryption);

        // Associate the key with the asset.
        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int length)
    {
        var returnValue = new byte[length];

        using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
        {
            rng.GetBytes(returnValue);
        }

        return returnValue;
    }
通話

    IContentKey key = CreateCommonTypeContentKey(encryptedsset); 


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
