<properties
 pageTitle="排程器高可用性和可靠性"
 description="排程器高可用性和可靠性"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/16/2016"
 ms.author="deli"/>


# <a name="scheduler-high-availability-and-reliability"></a>排程器高可用性和可靠性

## <a name="azure-scheduler-high-availability"></a>Azure 排程器高可用性

為核心 Azure 平台服務，Azure 排程器高度，並同時地理多餘的服務部署並依地理區域工作複寫。

### <a name="geo-redundant-service-deployment"></a>地理多餘的服務部署

使用幾乎所有地理區域中的 Azure 今天 UI 透過 azure 排程器。 Azure 排程器中所提供的地區清單會[列於此處](https://azure.microsoft.com/regions/#services)。 如果裝載的區域資料中心呈現無法使用，容錯移轉功能 Azure 排程器是從另一個資料中心是可用服務。

### <a name="geo-regional-job-replication"></a>地理區域工作複寫

不只是 Azure 排程器前端適用於管理要求，但您自己的工作也是地理複寫。 當有一個區域中的資料時，請 Azure 排程器會失敗，並確保執行作業時從另一個資料中心，即成對的地理區域中。

例如，如果您已在 [美國中部南部建立工作，Azure 排程器自動複製該工作，在 [美國中部北美。 後在 [美國中部南部失敗，可確保執行作業時從北美美國中部 Azure 排程器。 

![][1]

如此一來，Azure 排程器可確保您的資料保持若 Azure 失敗的相同狀況提出更廣義地理區域內。 如此一來，您不需要複製您的工作，只要將可用性 – Azure 排程器會自動提供高可用性功能，您的工作。

## <a name="azure-scheduler-reliability"></a>Azure 排程器可靠性

Azure 排程器保證自己高可用性，並使用不同的方法使用者建立工作。 例如，您的工作可能呼叫 HTTP 端點無法使用。 Azure 排程器依然是嘗試執行提供替代的選項來處理失敗成功，您的工作。 Azure 排程器會這兩種方法︰

### <a name="configurable-retry-policy-via-retrypolicy"></a>可設定再試一次的原則，透過 「 retryPolicy 」

Azure 排程器可讓您設定重試原則。 根據預設，如果工作失敗，排程器會試著工作再次四個更多的時間，在 30 秒的時間間隔。 您可能會重新設定為更積極 （例如，十個時間，在 30 秒的時間間隔） 此重試原則或鬆散 （例如，兩次，在每日時間間隔。）

當這可以幫助的範例，您可能會建立執行一週一次，呼叫 HTTP 端點的工作。 如果您工作時執行的幾個小時，向下是 HTTP 端點，您可能不想等候一更多週的時間之後即使預設重試原則，將無法再執行的工作。 在這種情況下，您可能會重新重試每 3 小時左右 （例如） 的標準重試原則設定，而不是每隔 30 秒。

若要瞭解如何設定重試原則，請參閱[retryPolicy](scheduler-concepts-terms.md#retrypolicy)。

### <a name="alternate-endpoint-configurability-via-erroraction"></a>透過 「 errorAction 」 的替代端點性

如果您 Azure 排程工作的目標端點仍然無法連線，Azure 排程器退回替代的錯誤處理端點後追蹤其重試原則。 如果設定替代的錯誤處理端點，Azure 排程器叫用它。 替代的結束點，使用您自己的工作可高度面對失敗。

舉例來說，在圖表中，Azure 排程器，請遵循重試原則叫用紐約 web 服務。 重試失敗之後，它會檢查是否有替代。 然後，直接進入並開始使用相同的重試原則替代提出要求。

![][2]

請注意，相同的重試原則適用於原始的巨集指令和替代錯誤動作。 您也可有主巨集指令的巨集指令輸入不同的替代錯誤巨集指令的巨集指令類型。 例如時可能會叫用的主要動作 HTTP 端點，錯誤動作改為可能的儲存空間佇列、 服務匯流排佇列中或沒有錯誤記錄的服務匯流排主題動作。

若要瞭解如何設定替代端點，請參閱[errorAction](scheduler-concepts-terms.md#action-and-erroraction)。

## <a name="see-also"></a>另請參閱

 [什麼是排程器？](scheduler-intro.md)

 [Azure 排程器概念、 詞彙及實體階層](scheduler-concepts-terms.md)

 [開始使用 Azure 入口網站中的排程器](scheduler-get-started-portal.md)

 [方案與帳單方面的 Azure 排程器](scheduler-plans-billing.md)

 [如何建立複雜的排程和進階的循環 Azure 排程器](scheduler-advanced-complexity.md)

 [Azure 排程器 REST API 參照](https://msdn.microsoft.com/library/mt629143)

 [Azure 排程器 PowerShell cmdlet 參考](scheduler-powershell-reference.md)

 [Azure 排程器限制與預設值]，出現錯誤碼](scheduler-limits-defaults-errors.md)

 [Azure 排程器輸出驗證](scheduler-outbound-authentication.md)


[1]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png

[2]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png
