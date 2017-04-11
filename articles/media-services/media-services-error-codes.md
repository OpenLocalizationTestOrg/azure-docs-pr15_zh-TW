<properties
    pageTitle="Azure 媒體服務錯誤碼 |Microsoft Azure"
    description="主題提供 Azure 媒體服務錯誤碼的概觀。"
    authors="Juliako"
    manager="erikre"
    editor=""
    services="media-services"
    documentationCenter=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016" 
    ms.author="juliako"/>

# <a name="azure-media-services-error-codes"></a>Azure 媒體服務錯誤碼

使用 Microsoft Azure 媒體服務時，您可能會收到 HTTP 錯誤碼根據問題，例如驗證的權杖動作不支援的媒體服務即將過期服務。 以下是**HTTP 錯誤碼**可能會傳回的媒體服務和可能的原因，他們的清單。  
  
## <a name="400-bad-request"></a>400 錯誤的要求

要求包含無效的資訊，並拒絕其中一個原因如下︰

- 指定不受支援的 API 版本。 最新版本，請參閱[媒體服務 REST API 開發的設定](media-services-rest-how-to-use.md)。
- 未指定的媒體服務 API 版本。 如何指定 API 版本的資訊，請參閱[媒體服務 REST API 與媒體服務的連線](media-services-rest-connect-programmatically.md)。 
   
    >[AZURE.NOTE] 如果您連線到媒體服務使用的.NET 或 Java Sdk，API 版本會指定給您，當您嘗試執行某些動作媒體服務。
- 已指定定義的屬性。 屬性名稱是錯誤訊息中。 您可以指定所指定的實體成員這些屬性。 實體和其屬性的清單，請參閱[Azure 媒體服務 REST API 參考](http://msdn.microsoft.com/library/azure/hh973617.aspx)。
- 已指定無效的屬性值。 屬性名稱是錯誤訊息中。 請參閱有效的內容類型和數值的上一個連結。
- 屬性值遺失，而是必要。
- 部分指定的 URL 會包含錯誤值。
- 您嘗試更新 WriteOnce 屬性。
- 您嘗試建立具有與 [未指定或無法決定主要 AssetFile 輸入的資產的工作。
- 您嘗試更新 SA 定位器。 SA 定位器僅能建立或刪除。 串流定位器都可以更新。 如需詳細資訊，請參閱[定位器](http://msdn.microsoft.com/library/azure/hh974308.aspx)。
- 已提交不受支援的作業或查詢。 

## <a name="401-unauthorized"></a>401 未經授權

要求無法驗證 （之前可以授權） 其中一個原因如下︰

- 遺漏的驗證標頭。
- 不正確的驗證標頭值。
    - 權杖已過期。 如果直接使用 REST Api，請參閱[連線至媒體服務與媒體服務 REST API](media-services-rest-connect_programmatically.md) ，瞭解如何產生新的驗證憑證。 如果您使用.NET 或 Java Sdk，建立 CloudMediaContext 或 MediaContract 物件產生的權杖。 如需有關如何進行此動作的詳細資訊，請參閱[連線至與媒體服務 SDK.net 媒體服務。](media-services-dotnet-connect-programmatically.md)
    - 權杖包含無效簽章。</li></ul></li></ul>

## <a name="403-forbidden"></a>403

要求不允許其中一個原因如下︰

- 媒體服務帳戶找不到或已被刪除。
- 媒體服務帳戶已停用，且要求類型不 HTTP GET。 服務作業會傳回以及 403 回應。
- 驗證權杖不含使用者認證資訊︰ AccountName 及/或 SubscriptionId。 您可以在管理入口網站 Azure 媒體服務帳戶的媒體服務使用者介面擴充尋找這項資訊。
- 無法存取資源。
    - 您嘗試使用不適用於您的媒體服務帳戶 MediaProcessor。
    - 您嘗試更新工作媒體服務所定義的範本。
    - 您嘗試覆寫一些其他媒體服務帳戶的定位器。
    - 您嘗試覆寫一些其他媒體服務帳戶的 ContentKey。

- 由於已達媒體服務帳戶的服務配額，因此無法建立資源。 如需有關服務配額的詳細資訊，請參閱[配額和限制](media-services-quotas-and-limitations.md)。

## <a name="404-not-found"></a>404 找不到

要求不允許的資源，因為下列原因之一︰

- 您嘗試更新不存在的實體。
- 您嘗試刪除不存在的實體。
- 您嘗試建立實體不存在的實體的連結。
- 嘗試取得不存在的實體。
- 您嘗試指定儲存帳戶的不是與媒體服務帳戶相關聯。  

## <a name="409-conflict"></a>409 衝突

要求不允許其中一個原因如下︰

- 多個 AssetFile 有資產在指定的名稱。
- 您嘗試建立第二個主要 AssetFile 內的資產。
- 您嘗試建立 ContentKey 已使用的指定識別碼。
- 您嘗試建立定位器已使用的指定識別碼。
- 多個 IngestManifestFile 有 IngestManifest 中指定的名稱。
- 您嘗試連結第二個儲存加密 ContentKey 儲存加密資產。
- 您嘗試連結相同 ContentKey 資產。
- 您嘗試建立資產其存放容器遺失或已不再與資產相關聯的定位器。
- 您嘗試建立資產已經在使用具有 5 定位器定位器。 （azure 儲存體會強制執行限制的上一個存放容器的五個共用的存取原則）。
- 連結 IngestManifestAsset 資產的儲存空間帳戶不是使用上層 IngestManifest 的儲存空間帳戶相同。  

## <a name="500-internal-server-error"></a>500 內部伺服器錯誤

要求的處理，期間媒體服務會發生錯誤，而導致無法繼續處理。 這可能是因為下列原因之一︰

- 建立資產或工作失敗，因為媒體服務帳戶的服務配額資訊暫時無法使用。
- 建立資產或 IngestManifest blob 儲存體容器失敗，因為客戶儲存帳戶資訊會暫時無法使用。
- 其他意外的錯誤。 

## <a name="503-service-unavailable"></a>503 服務無法使用

目前無法收到要求伺服器。 此錯誤可能會因過多的服務要求。 節流機制媒體服務限制應用程式服務進行過多的要求，資源使用的狀況。

>[AZURE.NOTE]檢查錯誤訊息及錯誤代碼字串，以取得您適合 503 錯誤原因的相關詳細的資訊。 此錯誤並不一定表示節流設定。

可能的狀態說明如下︰

- 「 伺服器是忙碌。 此類型的要求的上一個執行超過 {0} 秒。 」
- 「 伺服器是忙碌。 超過一 {0} 要求秒可以會降低。 」
- 「 伺服器是忙碌。 超過 {0} 要求 {1} 數秒內可以經流速控制。 」

若要處理此錯誤，建議您使用指數返回關閉重試邏輯。 這表示使用變得更長的時間等待之間重試連續錯誤回應。  如需詳細資訊，請參閱[暫時性錯誤處理應用程式區塊](https://msdn.microsoft.com/library/hh680905.aspx)。 

>[AZURE.NOTE]如果您使用[.Net Azure 媒體服務 SDK](https://github.com/Azure/azure-sdk-for-media-services/tree/master)，具有已實作 SDK 重試邏輯 503 錯誤。  
  
## <a name="see-also"></a>另請參閱  

[媒體服務管理錯誤碼](http://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>後續步驟

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
