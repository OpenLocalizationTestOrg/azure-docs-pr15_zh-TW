<properties
   pageTitle="Azure 函數概觀 |Microsoft Azure"
   description="瞭解如何使用 Azure 函數，來最佳化非同步負載以分鐘為單位。"
   services="functions"
   documentationCenter="na"
   authors="mattchenderson"
   manager="erikre"
   editor=""
   tags=""
   keywords="azure 函數、 函數、 事件處理、 webhooks、 動態計算、 無架構"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/29/2016"
   ms.author="cfowler;mahender;glenga"/>
   
   
# <a name="azure-functions-overview"></a>Azure 函數概觀

Azure 函數會位於雲端中的輕鬆地執行的程式碼或 「 函數，「 少量的解決方案。 您可以撰寫只是您需要問題先後，而不需擔心整個應用程式或基礎結構，以執行的程式碼。 這可讓開發更有效率，然後您可以使用的選項，例如 C#、 F #、 Node.js、 Python 或 PHP 開發語言。 若要縮放視支付僅適用於執行程式碼的時間和信任 Azure。

本主題提供 Azure 函數的整體概觀。 如果您想要直接並開始使用 Azure 函數，開始[建立您的第一個 Azure 函數](functions-create-first-azure-function.md)。 如果您要尋找更多技術函數的相關資訊，請參閱[開發人員參考](functions-reference.md)。

## <a name="features"></a>功能

以下是 Azure 函數的一些主要功能︰
    
* **選擇的語言**-使用 C#、 F #、 Node.js、 Python、 PHP、 批次、 艦隊、 Java 或任何可執行檔寫入函數。
* **支付每次使用價格模型**-支付僅適用於執行程式碼花費的時間。 請參閱中[價格] 區段](#pricing)下方的 [動態應用程式服務方案] 選項。  
* **將您自己的相依性**-函數支援 NuGet 和 NPM，，讓您可以使用您偏好使用的文件庫。  
* **整合式的安全性**-OAuth 提供者，例如 Azure Active Directory、 Facebook、 Google、 Twitter 與 Microsoft 帳戶的保護 HTTP 觸發函數。  
* **簡體整合**-輕鬆地利用 Azure 服務和軟體-為-的-服務 (SaaS)。 請參閱[整合] 區段](#integrations)下方的範例。  
* **彈性的開發**-函數權利入口網站中的程式碼或設定持續整合及部署透過 GitHub、 Visual Studio 小組服務和[支援開發工具](../app-service-web/web-sites-deploy.md#deploy-using-an-ide)其他程式碼。  
* **開啟來源**函數執行階段已開啟來源且[可供 GitHub 上。](https://github.com/azure/azure-webjobs-sdk-script)  

## <a name="what-can-i-do-with-functions"></a>我可以使用函數做什麼？

Azure 函數將會使用網際網路-的-項目 (IoT)，並建立簡單的 Api 及 microservices 整合系統處理資料的最佳解決方案。 請考慮函數，例如圖像或順序處理檔案進行的維修作業、 您想要執行背景主題往來中的較長時間執行工作的工作或任何您想要執行的排程的任務。 

函數提供範本可供您開始使用索引鍵的情況下，包括下列︰

* **BlobTrigger** -程序 Azure 儲存體二進位大型物件時即會新增到容器。 您可能會使用此調整圖像大小。
* **EventHubTrigger**回應事件傳遞至 Azure 事件中心。 在應用程式儀器、 使用者體驗或工作流程處理和網際網路的項目 (IoT) 案例特別有用。
* **一般 webhook**程序 webhook HTTP 會從任何支援 webhooks 的服務要求。
* **GitHub webhook** -回應您 GitHub 存放庫中發生的事件。 例如，請參閱[建立 webhook 或 API 函數](functions-create-a-web-hook-or-api-function.md)。
* **HTTPTrigger** -觸發程序執行使用 HTTP 要求的程式碼。
* **QueueTrigger**回覆郵件到達時 Azure 儲存體佇列中。 例如，請參閱[建立的繫結至 Azure 服務 Azure 函數](functions-create-an-azure-connected-function.md)。
* **ServiceBusQueueTrigger** -連線至其他 Azure 服務的程式碼或者內部部署的服務以聽取訊息佇列。 
* **ServiceBusTopicTrigger** -連線至其他 Azure 服務的程式碼或者內部部署的服務所訂閱的主題。 
* **TimerTrigger** -執行清理或其他批次工作上預先定義的排程。 例如，請參閱[建立事件處理函數](functions-create-an-event-processing-function.md)。

Azure 函數支援*引動程序*，也就是開始執行程式碼的方法及*繫結*，也就是簡化編碼的輸入與輸出資料的方式。 如需引動程序和 Azure 函數所提供的繫結的詳細說明，請參閱[Azure 函數引動程序和繫結開發人員參考](functions-triggers-bindings.md)。


## <a name="integrations"></a>整合

Azure 函數整合了各種 Azure 和 3rd 廠商服務。 您可以使用這些函數觸發程序，並開始執行或做為輸入與輸出的程式碼。 Azure 函數支援下列服務整合。 

* Azure DocumentDB
* Azure 事件集線器 
* Azure Mobile 應用程式 （表格）
* Azure 通知集線器
* Azure 服務匯流排 （佇列與主題）
* Azure 儲存空間 （blob、 佇列和資料表） 
* GitHub (webhooks)
* 內部部署 （使用服務匯流排）

## <a name="pricing"></a>多少函數成本嗎？

Azure 函數有兩種資費方案，請選擇其中一個最適合您的需求︰ 

* **動態管理計劃**-您函數執行時，Azure 提供所有必要的計算資源。 您不必擔心資源管理，您只是每年支付執行程式碼的時間。 在[函數價格] 頁面](/pricing/details/functions)上可完整定價詳細資料。 

* **應用程式服務計劃**-執行您的函數，就像您網頁、 行動電話及 API 應用程式。 當您已使用應用程式服務的其他應用程式時，您可以執行您函數搭售相同的方案。 在[應用程式服務價格] 頁面](/pricing/details/app-service/)上找完整詳細資料。

如需有關如何調整您的函數的詳細資訊，請參閱[Azure 函數不按比例縮放的方式](functions-scale.md)。

##<a name="next-steps"></a>後續步驟

+ [建立您的第一個 Azure 函數](functions-create-first-azure-function.md)  
直接，並建立您的第一個函數使用 Azure 函數快速入門。 
+ [Azure 函數開發人員參考](functions-reference.md)  
提供更多技術的相關資訊 Azure 函數執行階段與參照函數撰寫程式碼，並定義引動程序及連結。
+ [測試 Azure 函數](functions-test-a-function.md)  
說明各種工具和技術來測試您的函數。
+ [如何調整 Azure 函數](functions-scale.md)  
討論 Azure 功能，包括動態服務方案，以及如何選擇正確的計劃提供的服務方案。 
+ [深入瞭解 Azure 應用程式服務](../app-service/app-service-value-prop-what-is.md)  
Azure 函數會運用核心功能，例如部署、 環境變數和診斷 Azure 應用程式服務的平台。 