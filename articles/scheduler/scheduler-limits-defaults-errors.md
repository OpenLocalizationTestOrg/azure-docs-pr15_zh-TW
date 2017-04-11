<properties
 pageTitle="排程器限制及預設值"
 description="排程器限制及預設值"
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

# <a name="scheduler-limits-and-defaults"></a>排程器限制及預設值

## <a name="scheduler-quotas-limits-defaults-and-throttles"></a>排程器配額、 限制、 預設值]，以及節流

[AZURE.INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="the-x-ms-request-id-header"></a>X ms-要求識別碼標頭

每個對排程器服務的要求傳回回應標頭名稱**x ms-要求 id**。 這個標題包含不透明的值，可唯一識別要求。

如果要求一致失敗，而您已驗證要求正確建構而成，您可能會使用這個值來至 Microsoft 報告的錯誤。 在您的報表，包括 x-ms-要求-識別碼，進行要求的大約時間的值、 訂閱、 工作集合，及/或工作，並輸入要求嘗試執行的作業的識別碼。

## <a name="see-also"></a>另請參閱


 [什麼是排程器？](scheduler-intro.md)

 [Azure 排程器概念、 詞彙及實體階層](scheduler-concepts-terms.md)

 [開始使用 Azure 入口網站中的排程器](scheduler-get-started-portal.md)

 [方案與帳單方面的 Azure 排程器](scheduler-plans-billing.md)

 [Azure 排程器 REST API 參照](https://msdn.microsoft.com/library/mt629143)

 [Azure 排程器 PowerShell cmdlet 參考](scheduler-powershell-reference.md)

 [Azure 排程器高可用性和可靠性](scheduler-high-availability-reliability.md)

 [Azure 排程器輸出驗證](scheduler-outbound-authentication.md)
