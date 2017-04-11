<properties
   pageTitle="常見的 FabricClient 例外狀況 |Microsoft Azure"
   description="將說明常見的例外狀況及擲回 FabricClient api 執行應用程式和叢集管理操作錯誤。"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>

# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>常見的例外狀況和使用 FabricClient Api 時的錯誤
[FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) Api 讓叢集和應用程式的系統管理員在服務布料的轉印圖樣應用程式、 服務或叢集執行系統管理工作。 例如，應用程式部署、 升級和移除，檢查狀況叢集，或測試服務。 應用程式開發人員及其叢集系統管理員可以使用 FabricClient Api 開發工具來管理服務布料的轉印圖樣叢集和應用程式。

有許多不同類型的可使用 FabricClient 來執行的作業。  每一種方法輸入不正確的錯誤、 執行階段錯誤，或暫時性基礎結構問題的例外狀況。  請參閱 API 參照文件，若要尋找的例外狀況的特定的方法。 有一些例外狀況，不過，這可能會擲回許多不同的[FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) Api。 下表列出所有的 FabricClient Api 的例外狀況。

|例外狀況| 狀況的時機|
|---------|:-----------|
|[System.Fabric.FabricObjectClosedException](https://msdn.microsoft.com/library/system.fabric.fabricobjectclosedexception.aspx)|[FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx)物件為關閉狀態。 您使用的，並產生新的[FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx)物件[FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx)物件的配置。 |
|[System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx)|作業逾時。 當作業所需的時間超過 MaxOperationTimeout 完成時，會傳回[OperationTimedOut](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx) 。|
|[System.UnauthorizedAccessException](https://msdn.microsoft.com/en-us/library/system.unauthorizedaccessexception.aspx)|無法存取檢查作業。 會傳回 E_ACCESSDENIED。|
|[System.Fabric.FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx)|執行階段錯誤發生時執行的作業。 任何 FabricClient 方法會擲回[FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx)、[錯誤碼](https://msdn.microsoft.com/library/system.fabric.fabricexception.errorcode.aspx)屬性會指出例外狀況的確實原因。 錯誤碼都定義在[FabricErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx)列舉。|
|[System.Fabric.FabricTransientException](https://msdn.microsoft.com/library/system.fabric.fabrictransientexception.aspx)|作業失敗，因為暫時的錯誤狀況的某種。 例如，因為複本的仲裁暫時無法連接，可能會失敗作業。 暫時性的例外狀況會對應到作業失敗，可以重試。|

可以傳回[FabricException](https://msdn.microsoft.com/library/system.fabric.fabricexception.aspx)的一些常見[FabricErrorCode](https://msdn.microsoft.com/library/system.fabric.fabricerrorcode.aspx)錯誤︰

|錯誤| 條件|
|---------|:-----------|
|CommunicationError|通訊錯誤導致作業失敗，再試。|
|InvalidCredentialType|認證類型不正確。|
|InvalidX509FindType|X509FindType 不正確。|
|InvalidX509StoreLocation|X509 存放位置不正確。|
|InvalidX509StoreName|X509 存放區名稱不正確。|
|InvalidX509Thumbprint|X509 憑證指紋字串不正確。|
|InvalidProtectionLevel|保護層級不正確。|
|InvalidX509Store|X509 憑證存放區將無法開啟。|
|InvalidSubjectName|主旨不正確的名稱。|
|InvalidAllowedCommonNameList|常見的名稱清單字串的格式不正確。 應該會以逗號分隔清單。|
