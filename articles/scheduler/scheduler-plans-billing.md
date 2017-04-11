<properties
 pageTitle="方案與帳單方面的 Azure 排程器"
 description="方案與帳單方面的 Azure 排程器"
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
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="plans-and-billing-in-azure-scheduler"></a>方案與帳單方面的 Azure 排程器

## <a name="job-collection-plans"></a>工作集合計劃

工作集合都是 Azure 排程器中的計費實體。 工作集合包含數字的工作，如下所述的三個方案 – 免費、 標準和進階版 – 了。

|**工作集合計劃**|**最大的 # 個每個集合工作的工作**|**最大值循環**|**每個訂閱的最大工作集合**|**限制**|
|:---|:---|:---|:---|:---|
|**免費**|每個工作集合 5 工作|一次每小時。 無法比小時經常執行的作業|訂閱允許 1 進位至免費的工作集合|無法使用[HTTP 輸出授權物件](scheduler-outbound-authentication.md)
|**標準**|每個工作集合 50 工作|一次每分鐘。 不能超過一次分鐘經常執行的作業|允許最多 100 個標準工作集合的訂閱|存取完整的功能集，排程器|
|**P10 進階版**|每個工作集合 50 工作|一次每分鐘。 不能超過一次分鐘經常執行的作業|訂閱允許最多 10000 個 P10 進階工作集合。 如需詳細資訊，請<a href="mailto:wapteams@microsoft.com">與我們連絡</a>。|存取完整的功能集，排程器|
|**P20 進階版**|每個工作集合 1000年工作|一次每分鐘。 不能超過一次分鐘經常執行的作業|訂閱允許最多 10000 個 P20 進階工作集合。 如需詳細資訊，請<a href="mailto:wapteams@microsoft.com">與我們連絡</a>。|存取完整的功能集，排程器|

## <a name="upgrades-and-downgrades-of-job-collection-plans"></a>升級和 Downgrades 工作集合計劃

您可能會升級或降級為工作集合方案隨時免費、 標準和進階版方案之間。 不過，當降級至免費的工作集合，降級可能會失敗的原因如下︰

- 免費的工作集合已存在於訂閱
- 在工作集合中的工作有較高的週期超過允許的免費工作集合中的工作。 免費的工作集合允許的最大週期會每小時一次
- 在工作集合中有 5 個以上的工作
- 工作集合中的工作有使用[HTTP 輸出授權物件](scheduler-outbound-authentication.md)HTTP 或 HTTPS 巨集指令

## <a name="billing-and-azure-plans"></a>帳單與 Azure 方案

訂閱會不會免費工作集合。 如果您有多包含 100 個標準工作集合 （10 標準帳單的單位），則有進階版方案中的所有工作集合了更大。

如果您有一個標準工作集合及一個 premium 工作集合，您就是計費一標準帳單單位_和_一個進階版付款單位。 根據的使用中工作的集合，其中的設定為 [標準] 或 [進階版; 數排程器服務帳單這詳述下兩節。

## <a name="standard-billable-units"></a>標準的計費單位

標準的計費單位可以包含最多 10 個標準工作集合。 由於標準工作集合可以有超過 50 個工作，每個工作集合，一個標準帳單單位可讓有最多 500 個工作 – 最幾乎 22 百萬作業執行每月的訂閱。

如果您有 1 和 10 標準工作集合之間，則您會付費的 1 的標準帳單單位。 如果您有 11 和 20 標準工作集合之間，則您會付費的 2 的標準帳單單位。 如果您有 21 和 30 標準工作集合之間，您會計費的 3 個標準帳單單位，依此類推。

## <a name="p10-premium-billable-units"></a>P10 進階版的計費單位

P10 進階版的計費單位可以包含最多 10000 個 P10 進階工作集合。 由於 P10 進階工作集合可以有超過 50 個工作，每個工作集合，一個進階版的計費單位可讓有最 500000 工作 – 最幾乎 22 億作業執行每月的訂閱。

如果您有 1 到 10000 個 premium 工作集合之間，則您會付費的 1 P10 premium 帳單單位。 如果您有 10,001 和 20000 進階工作集合之間，您會計費 2 P10 進階版付款單位，依此類推。

因此，P10 premium 工作集合有標準工作集合相同的功能，但提供價格符號，以防您的應用程式需要許多工作集合。

## <a name="p20-premium-billable-units"></a>P20 進階版的計費單位

P20 進階版的計費單位可以包含多達 5000 個 P20 進階工作集合。 由於 P20 進階工作集合可以有 1000 個工作，每個工作集合，一個進階版的計費單位可讓有最 5000000 工作 – 最幾乎 220 億作業執行每月的訂閱。

P20 進階工作集合提供 P10 進階工作集合為相同的功能，但也支援更大的數字工作，每個集合工作，以及更大的工作總數整體比可讓您有多個延展性 P10 進階版。

## <a name="billing-and-active-status"></a>帳單與作用中狀態

工作集合一律是作用中的除非您整個的訂閱已經將某些暫時停用狀態，因為帳單問題。 確保工作集合不會向收費的唯一方法是先將其以_釋放_計劃或刪除工作集合。

雖然您可能會停用一次在工作集合內所有工作，不會變更的工作集合帳單狀態-工作集合會_仍然_都要付費。 同樣地，空白的工作集合視為作用中，且將付款。

## <a name="pricing"></a>價格

定價詳細資料，請參閱[排程器價格](https://azure.microsoft.com/pricing/details/scheduler/)。

## <a name="see-also"></a>另請參閱


 [什麼是排程器？](scheduler-intro.md)

 [Azure 排程器概念、 詞彙及實體階層](scheduler-concepts-terms.md)

 [開始使用 Azure 入口網站中的排程器](scheduler-get-started-portal.md)

 [Azure 排程器 REST API 參照](https://msdn.microsoft.com/library/mt629143)

 [Azure 排程器 PowerShell cmdlet 參考](scheduler-powershell-reference.md)

 [Azure 排程器高可用性和可靠性](scheduler-high-availability-reliability.md)

 [Azure 排程器限制與預設值]，出現錯誤碼](scheduler-limits-defaults-errors.md)

 [Azure 排程器輸出驗證](scheduler-outbound-authentication.md)
