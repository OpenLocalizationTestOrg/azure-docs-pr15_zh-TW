<properties
    pageTitle="重試邏輯媒體服務 sdk.NET |Microsoft Azure"
    description="主題提供.NET 媒體服務 sdk 重試邏輯的概觀。"
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


# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>.Net 重試媒體服務 SDK 中的邏輯

使用 Microsoft Azure 服務工作時，就會發生暫時性錯誤。 如果暫時性會發生錯誤，在大部分情況下，作業成功的幾個重試後。 媒體服務 SDK.net 實作重試邏輯處理例外狀況和網頁要求，會造成錯誤相關聯的暫時性錯誤執行查詢，儲存變更，並儲存作業。  根據預設，.NET 媒體服務 SDK 執行重新擲回您的應用程式的例外狀況之前的四個重試。 您的應用程式中的程式碼然後必須正確地處理此例外狀況。  
  
 以下是 Web 要求、 儲存、 查詢和 SaveChanges 原則的簡短指導方針︰  
  
-   儲存原則用於 blob 儲存體作業 （上傳或下載的資產檔案）。  
  
-   Web 要求會使用原則 （例如快速驗證權杖，以及解決使用者叢集端點） 的通用網頁要求。  
  
-   查詢原則來查詢實體其他 (例如，mediaContext.Assets.Where(...))。  
  
-   SaveChanges 原則可用於執行任何變更資料 （例如，建立實體更新實體，作業呼叫服務函數） 服務中的項目。  
  
 本主題列出的例外狀況類型，並由.net 媒體服務 SDK 的錯誤碼重試邏輯。  
  
## <a name="exception-types"></a>例外狀況類型  

下表說明媒體服務 SDK.net 控點，或不處理可能會造成的暫時性錯誤某些作業的例外狀況。  
  
例外狀況|Web 要求|儲存空間|查詢|SaveChanges
----|------|----|---|---
WebException<br/>如需詳細資訊，請參閱[WebException 狀態碼](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus)] 區段。|[是]|[是]|[是]|[是]  
DataServiceClientException<br/> 如需詳細資訊，請參閱[HTTP 錯誤狀態碼](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)。|無|[是]|[是]|[是]
DataServiceQueryException<br/> 如需詳細資訊，請參閱[HTTP 錯誤狀態碼](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)。|無|[是]|[是]|[是]  
DataServiceRequestException<br/> 如需詳細資訊，請參閱[HTTP 錯誤狀態碼](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)。|無|[是]|[是]|[是]  
DataServiceTransportException|無|無|[是]|[是]
「 逾時|[是]|[是]|[是]|無
SocketException|[是]|[是]|[是]|[是]  
StorageException|無|[是]|無|無 
IOException|無|[是]|無|無
  
###  <a name="WebExceptionStatus"></a>WebException 狀態碼  

下表顯示哪些 WebException 修正錯誤碼實作重試邏輯。 [WebExceptionStatus](http://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx)列舉定義的狀態碼。  
  
狀態|Web 要求|儲存空間|查詢|SaveChanges  
-----|-----------------|-------------|-----------|----------  
ConnectFailure|[是]|[是]|[是]|[是]
NameResolutionFailure|[是]|[是]|[是]|[是]  
ProxyNameResolutionFailure|[是]|[是]|[是]|[是]  
SendFailure|[是]|[是]|[是]|[是]
PipelineFailure|[是]|[是]|[是]|無  
ConnectionClosed|[是]|[是]|[是]|無  
KeepAliveFailure|[是]|[是]|[是]|無  
UnknownError|[是]|[是]|[是]|無 
ReceiveFailure|[是]|[是]|[是]|無  
RequestCanceled|[是]|[是]|[是]|無  
逾時|[是]|[是]|[是]|無
ProtocolError <br/>HTTP 狀態碼處理控制 ProtocolError 重試。 如需詳細資訊，請參閱[HTTP 錯誤狀態碼](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)。|[是]|[是]|[是]|[是]|  
  
###  <a name="HTTPStatusCode"></a>HTTP 錯誤狀態碼  

查詢和 SaveChanges 作業擲回 DataServiceClientException、 DataServiceQueryException 或 DataServiceQueryException，HTTP 錯誤狀態碼會傳回 StatusCode] 屬性中。  下表顯示哪些修正錯誤碼實作重試邏輯。  
  
 
狀態|Web 要求|儲存空間|查詢|SaveChanges 
---|----|----|----|----
401|無|[是]|無|無
403|無|[是]<br/>使用更長的時間等待處理重試。|無|無  
408|[是]|[是]|[是]|[是]
429|[是]|[是]|[是]|[是]  
500|[是]|[是]|[是]|無  
502|[是]|[是]|[是]|無  
503|[是]|[是]|[是]|[是]  
504|[是]|[是]|[是]|無  
  
如果您想要採取的.NET 媒體服務 SDK 的實際實作看重試邏輯，請參閱[azure-sdk-為-媒體-服務](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling)。

## <a name="next-steps"></a>後續步驟

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
