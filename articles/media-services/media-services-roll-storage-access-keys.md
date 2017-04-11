<properties 
    pageTitle="更新媒體服務後循環儲存便捷鍵 |Microsoft Azure" 
    description="本文提供您指引如何更新媒體服務後循環儲存便捷鍵。" 
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
    ms.author="milangada;cenkdin;juliako"/>

#<a name="update-media-services-after-rolling-storage-access-keys"></a>更新媒體服務後循環儲存便捷鍵

當您建立新的 Azure 媒體服務帳戶時，也會要求選取 Azure 儲存體帳戶，可用來儲存媒體內容。 請注意，您可以[新增多個儲存帳戶](meda-services-managing-multiple-storage-accounts.md)至您的媒體服務帳戶。

建立新的儲存空間帳戶時，Azure 會產生兩個 512 位元儲存便捷鍵，這用來驗證存取您儲存的帳戶。 若要保留您的儲存空間連線更加安全，還是建議定期重新產生並旋轉您儲存便捷鍵。 兩個便捷鍵 （主要和次要） 會提供才能讓您維護您重新產生其他便捷鍵時，請使用一個便捷鍵儲存帳戶的連線。 此程序也稱為 「 循環便捷鍵 」。

媒體服務而定，對其提供的儲存空間金鑰。 具體來說，用來將串流或下載您的資產定位器指定的儲存便捷鍵而定。 建立反飛彈系統帳戶時花相依性的主要儲存便捷鍵上依預設，但以的使用者，您可以更新反飛彈系統具有的儲存空間索引鍵。 請務必讓知道哪些金鑰] 以使用遵循的步驟，本主題中描述的媒體服務。 此外，當循環儲存便捷鍵，您需要請務必更新您定位器，因此會出現在您的資料流服務 （這個步驟也說明主題中） 不會中斷。

>[AZURE.NOTE]如果您有多個儲存帳戶，您想要執行此程序與每個儲存帳戶。
>
>執行之前生產帳戶本主題中所述的步驟，請務必在生產前的帳戶進行測試。


## <a name="step-1-regenerate-secondary-storage-access-key"></a>步驟 1︰ 重新產生次要儲存便捷鍵

開始重新產生次要儲存鍵。 根據預設，次要鍵不是由媒體服務。  如何復原儲存金鑰的資訊，請參閱[如何︰ 檢視、 複製與重新產生的儲存空間的便捷鍵](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)。
  
##<a id="step2"></a>步驟 2︰ 更新媒體服務，以使用新的次要儲存金鑰

更新媒體服務使用的次要儲存便捷鍵。 您可以使用下列兩種方法的其中一個與媒體服務同步處理的儲存空間重新產生的金鑰。

- 使用 Azure 入口網站︰ 若要尋找 [名稱] 和 [索引鍵的值，請移至 Azure 入口網站，然後選取您的帳戶。 [設定] 視窗會顯示在右側。 在 [設定] 視窗中，選取 [索引鍵。 根據您要同步處理媒體服務的儲存空間鍵，選取同步處理的主索引鍵或同步處理次要鍵] 按鈕。 在此情況下，使用 [次要鍵]。

- 使用媒體服務管理 REST API。

下列範例會示範如何建構 https://endpoint/***subscriptionId/服務/mediaservices/帳戶/accountName*/StorageAccounts/*storageAccountName*/Key 邀請，才能與媒體服務同步處理的指定的儲存鍵。 在此情況下，會使用的次要儲存空間值。 如需詳細資訊，請參閱[如何︰ 使用媒體服務管理 REST API](http://msdn.microsoft.com/library/azure/dn167656.aspx)。
    
    public void UpdateMediaServicesWithStorageAccountKey(string mediaServicesAccount, string storageAccountName, string storageAccountKey)
    {
        var clientCert = GetCertificate(CertThumbprint);
        
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts/{2}/StorageAccounts/{3}/Key",
        Endpoint, SubscriptionId, mediaServicesAccount, storageAccountName));
        request.Method = "PUT";
        request.ContentType = "application/json; charset=utf-8";
        request.Headers.Add("x-ms-version", "2011-10-01");
        request.Headers.Add("Accept-Encoding: gzip, deflate");
        request.ClientCertificates.Add(clientCert);
        
        
        using (var streamWriter = new StreamWriter(request.GetRequestStream()))
        {
            streamWriter.Write("\"");
            streamWriter.Write(storageAccountKey);
            streamWriter.Write("\"");
            streamWriter.Flush();
        }
        
        using (var response = (HttpWebResponse)request.GetResponse())
        {
            string jsonResponse;
            Stream receiveStream = response.GetResponseStream();
            Encoding encode = Encoding.GetEncoding("utf-8");
            if (receiveStream != null)
            {
                var readStream = new StreamReader(receiveStream, encode);
                jsonResponse = readStream.ReadToEnd();
            }
        }
    }

這個步驟之後，請更新現有的定位器 （相依性上的舊的儲存空間鍵） 下, 一個步驟所示。

>[AZURE.NOTE]等待 30 分鐘左右，再執行任何作業與媒體服務 （例如，建立新定位器） 若要避免在上的任何影響擱置的工作。

##<a name="step-3-update-locators"></a>步驟 3︰ 更新定位器

>[AZURE.NOTE]循環儲存便捷鍵，當您需要請務必更新您現有的定位器，因此就不會中斷您串流服務中。

請至少等待 30 分鐘後與反飛彈系統同步處理新的儲存空間金鑰。 然後，您可以重新建立您視需要定位器，讓他們採取相依性指定的儲存鍵與維護現有的 URL。

請注意，當您更新 （或重新建立） SA 定位器，URL 會一律會變更。

>[AZURE.NOTE] 若要確保您要保留現有的您視需要定位器 Url，您需要刪除現有定位器，建立一個新相同的識別碼。

下面這個.NET 範例示範如何重新建立定位器相同的識別碼。

私人靜態 ILocator RecreateLocator(CloudMediaContext context, ILocator locator) {/ / 儲存的現有定位器屬性。
var 資產 = 定位器。資產。var accessPolicy = 定位器。AccessPolicy;var locatorId = 定位器。識別碼。var startDate = 定位器。開始時間。var locatorType = 定位器。輸入;var locatorName = 定位器。欄位名稱。

刪除舊定位器。
定位器。Delete();

如果 （定位器。ExpirationDateTime < = DateTime.UtcNow) {新例外狀況 (a0 (「 無法重新建立定位器 Id = {0} 因為定位器到期時間已經過了 」，定位器。識別碼））。}
    
        // Create new locator using saved properties.
        var newLocator = context.Locators.CreateLocator(
            locatorId,
            locatorType,
            asset,
            accessPolicy,
            startDate,
            locatorName);
    
    
    
        return newLocator;
    }


##<a name="step-5-regenerate--primary-storage-access-key"></a>步驟 5︰ 重新產生主要儲存便捷鍵

重新產生的主要儲存便捷鍵。 如何復原儲存金鑰的資訊，請參閱[如何︰ 檢視、 複製與重新產生的儲存空間的便捷鍵](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)。

##<a name="step-6-update-media-services-to-use-the-new-primary-storage-key"></a>若要使用新的主要儲存金鑰步驟 6︰ 更新媒體服務
    
使用相同的程序中所述[步驟 2](media-services-roll-storage-access-keys.md#step2)僅限這次同步處理新的主要儲存便捷鍵與媒體服務帳戶。

>[AZURE.NOTE]等待 30 分鐘左右，再執行任何作業與媒體服務 （例如，建立新定位器） 若要避免在上的任何影響擱置的工作。

##<a name="step-7-update-locators"></a>步驟 7︰ 更新定位器  

30 分鐘後就可以重新建立您視需要定位器，讓他們採用新的主要儲存金鑰相依性與維護現有的 URL。

[步驟 3](media-services-roll-storage-access-keys.md#step-3-update-locators)中所述，請使用相同的程序。


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



###<a name="acknowledgments"></a>通知 

我們想要確認下列提供建立這份文件的人員︰ Cenk Dingiloglu，米蘭 Gada Seva Titov。
