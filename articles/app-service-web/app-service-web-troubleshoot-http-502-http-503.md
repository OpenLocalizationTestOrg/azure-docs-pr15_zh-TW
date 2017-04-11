<properties
    pageTitle="修正 502 不正確的閘道，503 服務無法使用 」 錯誤 |Microsoft Azure"
    description="502 不正確的閘道和裝載於 Azure 應用程式服務 web 應用程式中的 503 服務無法使用錯誤的疑難排解。"
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""
    tags="top-support-issue"
    keywords="502 不正確的閘道 503 服務無法使用 503 錯誤、 錯誤 502"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="cephalin"/>

# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-your-azure-web-apps"></a>疑難排解 HTTP 」 502 不正確的閘道 」 和 「 503 服務無法使用 」 Azure web 應用程式中的錯誤

「 502 不正確的閘道 」 和 「 503 服務無法使用 」 是裝載於[Azure 應用程式服務](http://go.microsoft.com/fwlink/?LinkId=529714)web 應用程式中的常見錯誤。 本文可協助您解決這些錯誤。

如果您需要更多協助，這份文件中的任一點時，您也可以連絡 Azure 專家[MSDN Azure 和堆疊溢位論壇](https://azure.microsoft.com/support/forums/)上。 或者，您也可以檔案 Azure 支援事件。 移至[Azure 支援網站](https://azure.microsoft.com/support/options/)，然後按一下 [**取得**支援。

## <a name="symptom"></a>徵狀

如果您瀏覽至 web 應用程式，就會傳回 HTTP 」 502 不正確的閘道 」 錯誤或 HTTP 「 503 服務無法使用 」 錯誤。

## <a name="cause"></a>原因

此問題通常導因應用程式層級的問題，例如︰

-   要求，而且花費很長的時間
-   使用高的記憶體 CPU 應用程式
-   因為例外狀況損毀的應用程式。

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>若要解決 「 502 不正確的閘道 」 與 「 503 服務無法使用 」 錯誤的疑難排解步驟

疑難排解可以分成三個不同的工作，連續順序︰

1.  [觀察並監控應用程式的行為](#observe)
2.  [收集資料](#collect)
3.  [減輕問題](#mitigate)

[應用程式服務 Web 應用程式](/services/app-service/web/)提供各種選項每一個步驟。

<a name="observe" />
### <a name="1-observe-and-monitor-application-behavior"></a>1.觀察並監控應用程式的行為

####    <a name="track-service-health"></a>追蹤服務健康狀況

Microsoft Azure publicizes 有服務中斷或效能降低每次。 您可以在[Azure 入口網站](https://portal.azure.com/)上追蹤服務健康狀況。 如需詳細資訊，請參閱[追蹤服務健康狀況](../monitoring-and-diagnostics/insights-service-health.md)。

####    <a name="monitor-your-web-app"></a>監控您的 web 應用程式

這個選項可讓您找出您的應用程式是否有任何問題。 在您的 web 應用程式刀中按一下 [**要求與錯誤**] 磚。 **公制**刀會顯示您可以新增的所有指標。

您可能會想要監視 web 應用程式的計量部分

-   平均記憶體工作集
-   平均回應時間
-   CPU 時間
-   記憶體工作集
-   要求

![如何解決 HTTP 錯誤 502 不正確的閘道和 503 服務無法使用的監視器 web 應用程式](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

如需詳細資訊，請參閱︰

-   [監控 Azure 應用程式服務中的 Web 應用程式](web-sites-monitor.md)
-   [收到通知的通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />
### <a name="2-collect-data"></a>2.收集資料

####    <a name="use-the-azure-app-service-support-portal"></a>使用 Azure 應用程式服務支援入口網站

Web 應用程式可讓您能夠查看 HTTP 記錄、 事件記錄檔、 程序傾印，及其他功能至您的 web 應用程式相關的問題進行疑難排解。 您可以存取所有使用我們的支援入口網站，在這項資訊**http://&lt;您的應用程式名稱 >.scm.azurewebsites.net/Support**

Azure 服務應用程式支援入口網站提供三個不同的索引標籤，以支援的一般疑難排解案例的三個步驟︰

1.  觀察目前的行為
2.  分析收集的診斷資訊，以及執行內建分析器
3.  減輕

如果立即發生問題，按一下 [**分析** > **診斷** > **診斷現在**建立的診斷工作階段，這會收集 HTTP 記錄檔，事件檢視器記錄檔，傾印、 PHP 錯誤記錄檔和 PHP 處理報表的記憶體。

後會收集資料，它也會執行資料分析並以 HTML 報表提供給您。

您要下載的資料，根據預設，它會儲存在 D:\home\data\DaaS 資料夾。

如需有關 Azure 應用程式服務支援入口網站的詳細資訊，請參閱[支援網站延伸 Azure 網站的新更新](/blog/new-updates-to-support-site-extension-for-azure-websites)。

####    <a name="use-the-kudu-debug-console"></a>使用 Kudu 偵錯主控台

Web 應用程式隨附偵錯主控台，您可以使用偵錯、 瀏覽、 上傳檔案，以及 JSON 端點取得您的環境的相關資訊。 這稱為_Kudu 主控台_或_SCM 儀表板_web 應用程式。

您可以前往連結，以存取此儀表板**https://&lt;您的應用程式名稱 >.scm.azurewebsites.net/**。

Kudu 提供的項目如下︰

-   應用程式的環境設定
-   記錄資料流
-   診斷傾印
-   偵錯的主控台您可以在其中執行 Powershell cmdlet 和基本 DOS 命令。


其他實用的 Kudu 功能，以防您的應用程式，就會擲回第一個可能的例外狀況，您可以使用 Kudu 和 SysInternals 工具來建立記憶體 Procdump 傾印。 這些記憶體傾印是程序的快照，通常可協助您疑難排解更為複雜的 web 應用程式的問題。

如需有關 Kudu 中可用功能的詳細資訊，請參閱[Azure 網站您應該瞭解的線上工具](/blog/windows-azure-websites-online-tools-you-should-know-about/)。

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

 ![重新啟動應用程式，來解決 HTTP 錯誤 502 不正確的閘道和 503 服務無法使用](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

您也可以管理 web 應用程式使用 Powershell 的 Azure。 如需詳細資訊，請參閱[使用 PowerShell 的 Azure 與 Azure 資源管理員](../powershell-azure-resource-manager.md)。
