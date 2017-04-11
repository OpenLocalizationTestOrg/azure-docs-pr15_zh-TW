<properties
    pageTitle="選擇流程、 邏輯應用程式、 功能和 WebJobs |Microsoft Azure"
    description="比較和比較雲端的整合 microsoft 服務，決定您應該使用哪一個服務。"
    services="functions,app-service\logic"
    documentationCenter="na"
    authors="cephalin"
    manager="wpickett"
    tags=""
    keywords="microsoft 流程、 流程、 邏輯應用程式、 azure 函數、 函數、 azure webjobs，webjobs，處理，動態計算，無伺服器架構的事件"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="09/08/2016"
    ms.author="chrande; glenga"/>

# <a name="choose-between-flow-logic-apps-functions-and-webjobs"></a>選擇流程、 邏輯應用程式、 功能和 WebJobs

本文比較，與對照 Microsoft 雲端，這種方法可以所有解決整合問題和自動化商務程序中的下列服務︰

- [Microsoft 流程](https://flow.microsoft.com/)
- [Azure 邏輯應用程式](https://azure.microsoft.com/services/logic-apps/)
- [Azure 函數](https://azure.microsoft.com/services/functions/)
- [Azure 應用程式服務 WebJobs](../app-service-web/web-sites-create-web-jobs.md)

當 「 結合 」 在一起不同系統，所有這些服務很有用。 輸入、 動作、 條件和輸出，所有可以定義它們。 您可以執行每個排程或觸發程序。 不過，每個服務新增一組唯一的值，並比較這些不是 「 哪些服務是最佳？ 」 的問題 但其中 「 哪一種服務最適合的這種情況？ 」 通常，這些服務的組合是快速建立可調整、 完整的主要的整合解決方案的最佳方式。

<a name="flow"></a>
## <a name="flow-vs-logic-apps"></a>流程與邏輯應用程式

我們可以討論 Microsoft 流程和 Azure 邏輯應用程式一起因為它們是兩種*設定第一個*整合服務，就可輕鬆建立程序與工作流程，以及整合與各種 SaaS 版和企業版應用程式。 

- 流程內建邏輯應用程式的頂端
- 他們擁有相同的工作流程設計工具
- 工作中的也可以使用其他的[連接器](../connectors/apis-list.md)

流程能讓執行簡單的整合任何 office 背景工作 （例如取得 SMS 重要的電子郵件） 而非透過開發人員或 IT。 另一方面，邏輯應用程式可啟用進階或關鍵的整合功能 （例如 B2B 程序） 企業級 DevOps 及安全性作法所需的位置。 商務工作流程增長複雜度加班通常會。 因此，您可以開始的第一個，流程，然後將它轉換成邏輯應用程式中，視需要。

下表可協助您判斷流程或邏輯應用程式是否適合使用指定的整合。

|               | 流程                                                                             | 邏輯應用程式                                                                                          |
|---------------|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| 對象      | office 工作者，商務使用者                                                   | IT 專業人員，開發人員                                                                                 |
| 案例     | 自助                                                                     | 關鍵任務                                                                                    |
| 設計工具   | 在瀏覽器，只 UI                                                              | 在瀏覽器和[Visual Studio](../app-service/logic/app-service-logic-deploy-from-vs.md)，[程式碼檢視](../app-service-logic/app-service-logic-author-definitions.md)可用 |
| DevOps        | 臨機操作開發生產環境中                                                    | 來源控制、 測試、 支援和自動化和[Azure 資源管理](../app-service-logic/app-service-logic-arm-provision.md)中的管理能力|
| 管理員體驗| [https://flow.microsoft.com](https://flow.microsoft.com)                       | [https://portal.azure.com](https://portal.azure.com)                                                |
| 安全性      | 標準的作法︰[資料主權](https://wikipedia.org/wiki/Technological_Sovereignty)、[加密其餘](https://wikipedia.org/wiki/Data_at_rest#Encryption)的機密資料等。 | Azure 安全性保證︰ [Azure 安全性](https://www.microsoft.com/trustcenter/Security/AzureSecurity)、[資訊安全中心](https://azure.microsoft.com/services/security-center/)、[稽核記錄](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/)及其他功能。 |

<a name="function"></a>
## <a name="functions-vs-webjobs"></a>與 WebJobs 函數

我們可以討論 Azure 函數和 Azure 應用程式服務 WebJobs 一起因為它們是兩種*程式碼第一個*整合服務，並設計適用於開發人員。 讓您能以不同的事件，例如[新增儲存空間二進位大型物件](functions-bindings-storage.md)或[要求 WebHook](functions-bindings-http-webhook.md)回應執行指令碼或一段程式碼。 以下是其相似︰ 

- 同時[Azure 應用程式服務](../app-service/app-service-value-prop-what-is.md)上建立並享受[來源控制](../app-service-web/app-service-continuous-deployment.md)、[驗證](../app-service/app-service-authentication-overview.md)]，並[監控](../app-service-web/web-sites-monitor.md)等功能。
- 都開發人員導向服務。
- 同時支援標準的指令碼和程式設計語言。
- 兩者都有 NuGet 和 NPM 支援。

函數的自然的 WebJobs 發展之 WebJobs 最佳功能並改善這些工具。 改良項目包括︰ 

- 精簡的開發，測試，然後執行程式碼，直接在瀏覽器中。
- 內建的整合更 Azure 服務與 3rd 廠商服務，例如[GitHub WebHooks](https://developer.github.com/webhooks/creating/)。
- 支付-每次使用，不需要支付[計劃的應用程式服務](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)。
- 自動，[動態縮放比例](functions-scale.md)。
- 針對現有的應用程式服務方案 （以善用下利用的資源） 仍然可以執行的應用程式服務的客戶。
- 使用邏輯應用程式的整合。

下表摘要列出函數和 WebJobs 之間的差異︰

|                        | 函數                                                                                                                                                                | WebJobs                            |
|------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------|
| 縮放比例                | Configurationless 縮放比例                                                                                                                                                | 使用應用程式服務方案不按比例縮放        |
| 價格                | 支付每次使用或部分的應用程式服務計劃                                                                                                                                  | 應用程式服務計劃的一部分           |
| 執行類型               | 觸發、 （依計時器觸發程序） 的排程                                                                                                                                  | 觸發、 連續排程   |
| 觸發事件         | [計時器](functions-bindings-timer.md)、 [ [Azure DocumentDB](functions-bindings-documentdb.md)、 Azure 事件集線器](functions-bindings-event-hubs)、 [HTTP/WebHook （GitHub、 寬限時間）](functions-bindings-http-webhook.md)、 [Azure 應用程式服務行動應用程式](functions-bindings-mobile-apps.md)、 [Azure 通知集線器](functions-bindings-notification-hubs.md)、 [Azure 服務匯流排](functions-bindings-service-bus.md)、 [Azure 儲存體](articles/functions-bindings-storage.md) | [Azure 儲存空間](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)， [Azure 服務匯流排](websites-dotnet-webjobs-sdk-service-bus.md)         |
| 在瀏覽器開發 | x                                                                                                                                                                        |                                    |
| 指令碼] 視窗       | 實驗                                                                                                                                                             | x                                  |
| PowerShell             | 實驗                                                                                                                                                             | x                                  |
| C#                     | x                                                                                                                                                                        | x                                  |
| F#                     | x                                                                                                                                                                        |                                    |
| 被                   | 實驗                                                                                                                                                             | x                                  |
| PHP                    | 實驗                                                                                                                                                             | x                                  |
| Python                 | 實驗                                                                                                                                                             | x                                  |
| JavaScript             | x                                                                                                                                                                        | x                                  |
| Java                   | 實驗                                                                                                                                                             | x                                  |

使用函數或 WebJobs 最後取決於您已經的動作與應用程式服務。 如果您有應用程式服務應用程式，您想要執行程式碼片段，且您想要在相同的 DevOps 環境中共同管理，您應該使用 WebJobs。 如果您想要執行程式碼片段的其他 Azure 服務或甚至 3rd 廠商應用程式，或如果您想要從您的應用程式服務應用程式，分別管理您的整合程式碼片段，或您要撥打您的程式碼片段從邏輯應用程式，您應該利用所有的改良功能在函數中。  

<a name="together"></a>
## <a name="flow-logic-apps-and-functions-together"></a>邏輯應用程式，與函數一起流動

如先前所述，哪一種服務最適合您取決於您的情況。 

- 對於簡單商務最佳化，然後使用流程。
- 如果您整合案例太進階流程，或者必須 DevOps 功能與安全性 compliances，然後使用邏輯應用程式。
- 如果您的整合案例中的步驟需要高度自訂轉換或特定的程式碼，然後撰寫函數應用程式，然後為邏輯應用程式中的動作觸發函數。

您可以呼叫邏輯應用程式的流程。 您也可以在函數呼叫邏輯應用程式，以及邏輯應用程式中的函數。 流程、 邏輯應用程式，以及函數之間的整合持續改進加班。 您可以建立一個服務中的項目，並將其用於其他服務。 因此，在這三種技術中所做的任何投資人們很重要。

## <a name="next-steps"></a>後續步驟

開始使用每個服務來建立第一個流程、 邏輯應用程式、 函數的應用程式或 WebJob。 按一下下列連結︰

- [開始使用 Microsoft 流程](https://flow.microsoft.com/en-us/documentation/getting-started/)
- [建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)
- [建立您的第一個 Azure 函數](../azure-functions/functions-create-first-azure-function.md)
- [部署 WebJobs 使用 Visual Studio](../app-service-web/websites-dotnet-deploy-webjobs.md)

或者，您也可以取得關於使用下列連結這些整合服務的詳細資訊︰

- [運用 Azure 函數與整合案例的 Christopher Anderson Azure 應用程式服務](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
- [查理 Lamanna 進行簡單的整合功能](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
- [邏輯應用程式的即時網路廣播](http://aka.ms/logicappslive)
- [Microsoft 流程常見問題集](https://flow.microsoft.com/documentation/frequently-asked-questions/)
- [Azure WebJobs 文件資源](../app-service-web/websites-webjobs-resources.md)
