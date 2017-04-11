<properties
    pageTitle="降低應用程式服務中的 web 應用程式效能 |Microsoft Azure"
    description="本文可協助您疑難排解 Azure 應用程式服務變得很慢的 web 應用程式效能問題。"
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""
    tags="top-support-issue"
    keywords="web 應用程式的效能，變得很慢的應用程式，變得很慢的應用程式"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="cephalin"/>

# <a name="troubleshoot-slow-web-app-performance-issues-in-azure-app-service"></a>疑難排解 Azure 應用程式服務變得很慢的 web 應用程式效能問題

本文可協助您疑難排解[Azure 應用程式服務](http://go.microsoft.com/fwlink/?LinkId=529714)變得很慢的 web 應用程式效能問題。

如果您需要更多協助，這份文件中的任一點時，您也可以連絡 Azure 專家[MSDN Azure 和堆疊溢位論壇](https://azure.microsoft.com/support/forums/)上。 或者，您也可以檔案 Azure 支援事件。 移至[Azure 支援網站](https://azure.microsoft.com/support/options/)，然後按一下 [**取得**支援。

## <a name="symptom"></a>徵狀

當您瀏覽 web 應用程式時，頁面載入速度變慢，有時候會出現逾時。

## <a name="cause"></a>原因

此問題通常導因應用程式層級的問題，例如︰

-   要求，而且花費很長的時間
-   使用高的記憶體 CPU 應用程式
-   因為例外狀況損毀的應用程式。

## <a name="troubleshooting-steps"></a>疑難排解步驟

疑難排解可以分成三個不同的工作，連續順序︰

1.  [觀察並監控應用程式的行為](#observe)
2.  [收集資料](#collect)
3.  [減輕問題](#mitigate)

[應用程式服務 Web 應用程式](/services/app-service/web/)提供各種選項每一個步驟。

<a name="observe" />
### <a name="1-observe-and-monitor-application-behavior"></a>1.觀察並監控應用程式的行為

#### <a name="track-service-health"></a>追蹤服務健康狀況

Microsoft Azure publicizes 有服務中斷或效能降低每次。 您可以在[Azure 入口網站](https://portal.azure.com/)上追蹤服務健康狀況。 如需詳細資訊，請參閱[追蹤服務健康狀況](../monitoring-and-diagnostics/insights-service-health.md)。

#### <a name="monitor-your-web-app"></a>監控您的 web 應用程式

這個選項可讓您找出您的應用程式是否有任何問題。 在您的 web 應用程式刀中按一下 [**要求與錯誤**] 磚。 **公制**刀會顯示您可以新增的所有指標。

您可能會想要監視 web 應用程式的計量部分

-   平均記憶體工作集
-   平均回應時間
-   CPU 時間
-   記憶體工作集
-   要求

![監控 web 應用程式的效能](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

如需詳細資訊，請參閱︰

-   [監控 Azure 應用程式服務中的 Web 應用程式](web-sites-monitor.md)
-   [收到通知的通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>監控網站端點狀態

如果您執行的 web 應用程式中**標準**價格層，Web 應用程式可讓您監視 2 從 3 的地理位置的結束點。

結束點監控設定從測試回應時間] 和 [存留時間的 web Url 分佈的地理位置的 web 測試。 測試 HTTP GET 上執行運算來決定 [回應時間] 和 [存留時間從每個位置的 web URL。 每個設定的位置執行，每隔五分鐘的測試。

執行時間監視使用 HTTP 回應代碼，並回應時間以毫秒為單位。 監控測試失敗的 HTTP 回應碼是否大於或等於 400，或回覆所需的時間超過 30 秒。 端點會被視為監控測試成功從所有指定的位置，才能使用。

若要將其設定，請參閱[如何︰ 監控 web 端點狀態](web-sites-monitor.md#webendpointstatus)。

請參閱[保留 Azure 網站上再加上結束點監控-使用宗 Schackow](/documentation/videos/azure-web-sites-endpoint-monitoring-and-staying-up/)端點監控的影片。

#### <a name="application-performance-monitoring-using-extensions"></a>應用程式效能監視使用副檔名

您也可以利用_網站副檔名_監視應用程式效能。

每一個應用程式服務 web 應用程式提供可讓您運用一組功能強大的工具部署網站副檔名為 「 可延伸管理結束點。 這些工具範圍從來源程式碼編輯器，例如[Visual Studio 小組服務](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx)管理工具] 的 [連線的資源，例如 MySQL 資料庫連線到 web 應用程式。

[Azure 應用程式的深入見解](/services/application-insights/)和[新聖器](/marketplace/partners/newrelic/newrelic/)是效能的兩個監控網站的副檔名所提供。 若要使用新的聖器，您可以安裝在執行階段代理程式。 若要使用 Azure 應用程式的深入見解，重建 sdk，您可以使用程式碼，您也可以安裝提供其他資料的存取權的副檔名。 SDK 可讓您撰寫程式碼來監視使用情況和您的應用程式的更多詳細資料的效能。

若要使用的應用程式深入資訊，請參閱[在 web 應用程式的監控效能](../application-insights/app-insights-web-monitor-performance.md)。

若要使用新的聖器，請參閱[Azure 上新的聖器應用程式效能管理](../store-new-relic-cloud-services-dotnet-application-performance-management.md)。

<a name="collect" />
### <a name="2-collect-data"></a>2.收集資料

####    <a name="enable-diagnostics-logging-for-your-web-app"></a>啟用診斷記錄 web 應用程式

Web Apps 環境提供診斷記錄資訊，從網頁伺服器和 web 應用程式功能。 這些邏輯分成網頁伺服器診斷和應用程式診斷。

##### <a name="web-server-diagnostics"></a>網頁伺服器診斷程式

您可以啟用或停用下列類型的記錄︰

-   **詳細的錯誤記錄**-詳細的錯誤資訊的 HTTP 狀態碼表示失敗 （狀態碼 400 或更大）。 這可能包含可以幫助您判斷為什麼伺服器會傳回錯誤的程式碼的資訊。
-   **無法要求的追蹤**-失敗的要求，包括 IIS 元件，用來處理要求並在每個元件所花費的時間追蹤的詳細資訊。 這可能是如果您嘗試改善 web 應用程式的效能或隔離導致特定 HTTP 錯誤。
-   **網頁伺服器記錄**-使用 W3C 擴充的記錄的檔案格式的 HTTP 交易的相關資訊。 決定整體的 web 應用程式指標，例如數處理要求或多少邀請是來自特定 IP 位址時，這是很有用。

##### <a name="application-diagnostics"></a>應用程式診斷

應用程式診斷可讓您擷取所產生的 web 應用程式的資訊。 ASP.NET 應用程式可以使用`System.Diagnostics.Trace`記錄的資訊在應用程式診斷記錄的類別。

如需如何設定記錄的應用程式的詳細指示，請參閱[啟用診斷記錄 Azure 應用程式服務中的 web 應用程式](web-sites-enable-diagnostic-log.md)。

#### <a name="use-remote-profiling"></a>使用遠端分析

Azure 應用程式服務中，Web 應用程式、 API 應用程式]，然後 WebJobs 遠端剖析。 如果您的程序正在執行速度愈慢比預期，或 HTTP 要求的延遲是高於一般且程序的 CPU 使用率也高，您就可以從遠端設定檔程序，並取得，分析程序活動和程式碼快速鍵路徑的 CPU 取樣呼叫堆疊。

如需詳細資訊，請參閱[Azure 應用程式服務中的 [遠端分析支援](/blog/remote-profiling-support-in-azure-app-service)。


#### <a name="use-the-azure-app-service-support-portal"></a>使用 Azure 應用程式服務支援入口網站

Web 應用程式可讓您能夠查看 HTTP 記錄、 事件記錄檔、 程序傾印，及其他功能至您的 web 應用程式相關的問題進行疑難排解。 您可以存取所有使用我們的支援入口網站，在這項資訊**http://&lt;您的應用程式名稱 >.scm.azurewebsites.net/Support**

Azure 服務應用程式支援入口網站提供三個不同的索引標籤，以支援的一般疑難排解案例的三個步驟︰

1.  觀察目前的行為
2.  分析收集的診斷資訊，以及執行內建分析器
3.  減輕

如果立即發生問題，按一下 [**分析** > **診斷** > **診斷現在**建立的診斷工作階段，這會收集 HTTP 記錄檔，事件檢視器記錄檔，傾印、 PHP 錯誤記錄檔和 PHP 處理報表的記憶體。

後會收集資料，它也會執行資料分析並以 HTML 報表提供給您。

您要下載的資料，根據預設，它會儲存在 D:\home\data\DaaS 資料夾。

如需有關 Azure 應用程式服務支援入口網站的詳細資訊，請參閱[支援網站延伸 Azure 網站的新更新](/blog/new-updates-to-support-site-extension-for-azure-websites)。

#### <a name="use-the-kudu-debug-console"></a>使用 Kudu 偵錯主控台

Web 應用程式隨附偵錯主控台，您可以使用偵錯、 瀏覽、 上傳檔案，以及 JSON 端點取得您的環境的相關資訊。 這稱為_Kudu 主控台_或_SCM 儀表板_web 應用程式。

您可以前往連結，以存取此儀表板**https://&lt;您的應用程式名稱 >.scm.azurewebsites.net/**。

Kudu 提供的項目如下︰

-   應用程式的環境設定
-   記錄資料流
-   診斷傾印
-   偵錯的主控台您可以在其中執行 Powershell cmdlet 和基本 DOS 命令。


其他實用的 Kudu 功能，以防您的應用程式，就會擲回第一個可能的例外狀況，您可以使用 Kudu 和 SysInternals 工具來建立記憶體 Procdump 傾印。 這些記憶體傾印是程序的快照，通常可協助您疑難排解更為複雜的 web 應用程式的問題。

如需有關 Kudu 中可用功能的詳細資訊，請參閱[Azure 網站小組服務您應該瞭解的工具](/blog/windows-azure-websites-online-tools-you-should-know-about/)。

<a name="mitigate" />
### <a name="3-mitigate-the-issue"></a>3.減輕問題

####    <a name="scale-the-web-app"></a>不按比例縮放的 web 應用程式

Azure 應用程式服務中，以增加的效能和處理量，您可以調整小數位數，您執行的您的應用程式。 擴充 web 應用程式需要兩個相關的動作︰ 將您的應用程式服務方案變更為較高的價格層及設定某些設定之後您切換到較高的價格層。

按比例縮放的詳細資訊，請參閱[比例 Azure 應用程式服務中的 web 應用程式](web-sites-scale.md)。

此外，您可以選擇多個執行個體上執行應用程式。 此不只處理功能，為您提供，但也可以提供一些數量容錯能力。 如果程序當機上執行個體之一，其他執行個體仍會繼續處理要求。

您可以設定為手動或自動縮放比例。

####    <a name="use-autoheal"></a>使用 AutoHeal

AutoHeal 回收根據您選擇 （例如設定變更要求、 記憶體為基礎的限制或執行要求所需的時間） 設定您的應用程式的背景工作程序。 大部分，資源程序是時間的最快的方法來解決問題。 雖然您可以隨時重新啟動 web 應用程式從直接在 Azure 入口網站，AutoHeal 將它會自動為您進行。 您需要執行的只是根 web.config web app 中新增一些引動程序。 請注意，即使您的應用程式不是一個.Net，會以相同的方式使用這些設定。

如需詳細資訊，請參閱[自動修復 Azure 網站](/blog/auto-healing-windows-azure-web-sites/)。

####    <a name="restart-the-web-app"></a>重新啟動 web 應用程式

這通常是從一次性問題復原最簡單的方法。 在[Azure 入口網站](https://portal.azure.com/)中，在您的 web 應用程式刀必須停止或重新啟動您的應用程式的選項。

 ![重新啟動解決效能問題的 web 應用程式](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

您也可以管理 web 應用程式使用 Powershell 的 Azure。 如需詳細資訊，請參閱[使用 PowerShell 的 Azure 與 Azure 資源管理員](../powershell-azure-resource-manager.md)。
