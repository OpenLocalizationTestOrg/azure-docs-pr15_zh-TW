<properties
 pageTitle="什麼是 Azure 排程器？ |Microsoft Azure"
 description="Azure 排程器可讓您可以用宣告方式描述在雲端中執行的動作。 它然後排程，並會自動執行的動作。"
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
 ms.topic="hero-article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="what-is-azure-scheduler"></a>什麼是 Azure 排程器？

Azure 排程器可讓您可以用宣告方式描述在雲端中執行的動作。 它然後排程，並會自動執行的動作。  排程器會使用[Azure 入口網站](scheduler-get-started-portal.md)、 程式碼， [REST API](https://msdn.microsoft.com/library/mt629143.aspx)或 PowerShell 的 Azure 負責這項工作。

排程器建立、 維護和叫用排定的工時。  排程器不會裝載任何負載或執行任何程式碼。 只有_叫用_的程式碼裝載於別處的 it — Azure 於內部部署，或另一個提供者。 它會透過 HTTP、 HTTPS、 儲存空間佇列、 服務匯流排佇列中或服務匯流排主題叫用。

排程器排程[的工作](scheduler-concepts-terms.md)，會保留工作的執行結果的記錄，其中一個可以檢閱及決定性且確實排程要執行的工作量。 Azure WebJobs （Azure 應用程式服務的 Web 應用程式功能的一部分） 和其他排程功能的 Azure 背景中使用排程器。 [排程器 REST API](https://msdn.microsoft.com/library/mt629143.aspx)協助您管理這些動作的通訊。 因此，排程器支援[複雜的排程和進階的循環](scheduler-advanced-complexity.md)輕鬆。

有數個案例的擔任排程器的使用方式。 例如︰

+ _週期性應用程式動作︰_定期收集 Twitter 到摘要資料。
+ _每日進行的維修作業︰_需每日的記錄檔，請執行備份和其他維護工作的詳細剪除。 例如，系統管理員可以選擇 1:00 a.m.備份資料庫 下一步 9 個月每一天。

排程器可讓您建立、 更新、 刪除、 檢視與管理工作與[工作集合以程式設計方式，使用指令碼，並在入口網站](scheduler-concepts-terms.md)。

## <a name="see-also"></a>另請參閱

 [Azure 排程器概念、 詞彙及實體階層](scheduler-concepts-terms.md)

 [開始使用 Azure 入口網站中的排程器](scheduler-get-started-portal.md)

 [方案與帳單方面的 Azure 排程器](scheduler-plans-billing.md)

 [如何建立複雜的排程和進階的循環 Azure 排程器](scheduler-advanced-complexity.md)

 [Azure 排程器 REST API 參照](https://msdn.microsoft.com/library/mt629143)

 [Azure 排程器 PowerShell cmdlet 參考](scheduler-powershell-reference.md)

 [Azure 排程器高可用性和可靠性](scheduler-high-availability-reliability.md)

 [Azure 排程器限制與預設值]，出現錯誤碼](scheduler-limits-defaults-errors.md)

 [Azure 排程器輸出驗證](scheduler-outbound-authentication.md)
