<properties
    pageTitle="Microsoft Azure 中的通知概觀 |Microsoft Azure"
    description="通知可讓您監控 Azure 資源指標、 事件或記錄，以及您指定的條件符合時收到通知。"
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/24/2016"
    ms.author="robb"/>

# <a name="overview-of-alerts-in-microsoft-azure"></a>Microsoft Azure 中的通知的概觀


本文將說明何種通知，他們的福利，以及如何開始使用它們。  

## <a name="what-are-alerts"></a>什麼是通知？
提醒是一種方法的監控 Azure 資源指標、 事件，或記錄，以及您指定為符合的條件時，然後通知。

您可以收到通知，根據︰

- **度量值**︰ 此提醒觸發的度量單位，指定值交叉您指派以任意方向閥值。 就是它觸發兩時條件符合時第一次，然後之後時的條件不符合。
- **活動記錄事件**︰ 每一個事件或特定數目的事件發生時，才可以觸發程序此通知。

## <a name="alerts-in-different-azure-services"></a>在不同的 Azure 服務中的通知

提醒可在不同的服務，包括︰

- **應用程式的深入見解**︰ 可讓網頁測試與公制提醒。 請參閱[設定應用程式的深入見解的警示](../application-insights/app-insights-alerts.md)，並[監控可用性及任何網站的回應](../application-insights/app-insights-monitor-web-app-availability.md)。
- **記錄檔分析 （作業管理套件）**︰ 啟用診斷記錄至記錄分析的路由。 作業管理套件可讓公制、 記錄和其他提醒類型。 如需詳細資訊，請參閱[記錄分析的警示](../log-analytics/log-analytics-alerts.md)。  
- **Azure 監視器**︰ 可讓根據度量值和活動記錄事件的提醒。 Azure 監視器包含[Azure 監視器 REST API](https://msdn.microsoft.com/library/dn931943.aspx)。  如需詳細資訊，請參閱[使用 Azure 入口網站、 PowerShell 或命令列介面建立提醒](insights-alerts-portal.md)。

## <a name="alert-actions"></a>通知的動作
您可以設定提醒，執行下列動作︰

- 服務管理員、 共同管理員，或您指定的其他電子郵件地址，請傳送電子郵件通知。
- [撥號給 webhook，這可讓您啟動其他自動化的動作。

 ![說明的通知](./media/monitoring-overview-alerts/AlertsOverviewResource3.png)


## <a name="next-steps"></a>後續步驟

取得警示規則，並使用這些設定的相關資訊︰

- [Azure 入口網站](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [命令列介面 (CLI)](insights-alerts-command-line-interface.md)
- [Azure 監視器 REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)
