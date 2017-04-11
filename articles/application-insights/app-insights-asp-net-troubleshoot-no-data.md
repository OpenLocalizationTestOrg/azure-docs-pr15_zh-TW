<properties 
    pageTitle="疑難排解沒有資料-.NET 應用程式深入資訊" 
    description="沒有看到 Visual Studio 應用程式深入資訊中的資料？ 請嘗試以下。" 
    services="application-insights" 
    documentationCenter=".net"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/24/2016" 
    ms.author="awills"/>
 
# <a name="troubleshooting-no-data---application-insights-for-net"></a>疑難排解沒有資料-.NET 應用程式深入資訊

## <a name="some-of-my-telemetry-is-missing"></a>我遙測部分不見了

*在 [應用程式的深入見解，我只會看到我的應用程式所產生的事件分數。*

* 如果您持續看到相同的分數，可能是因為調整[範例](app-insights-sampling.md)。 若要確認此，開啟 [搜尋] （從概觀刀），查看要求或其他事件的執行個體。 在 [屬性] 區段底部按一下 [...] 以取得完整的屬性的詳細資料]。 如果要求 Count > 1，然後取樣是作業。 
* 否則，可讓您正中[資料速度限制](app-insights-pricing.md#limits-summary)價格計劃。 每分鐘，就會套用這些限制。

## <a name="no-data-from-my-server"></a>從 [我的伺服器沒有資料

*在 [我的網頁伺服器上安裝我的應用程式，現在卻從任何遙測。在 [我的開發電腦作業 [確定]。*

* 可能防火牆問題。 [設定應用程式傳送資料的深入見解的防火牆例外](app-insights-ip-addresses.md)。

*我在我監控現有的應用程式的網頁伺服器上的 [[安裝狀態監視器](app-insights-monitor-performance-live-website-now.md)。看不到任何結果。*

* 請參閱[疑難排解狀態監視器](app-insights-monitor-performance-live-website-now.md#troubleshooting)。 


## <a name="q01"></a>在 Visual Studio 中未新增應用程式觀點選項

*在 Visual Studio 中，建立新的專案或我現有的專案總管] 中以滑鼠右鍵按一下時，看不到任何應用程式的深入見解的選項。*

+ 並非所有類型的.NET 專案所都支援的工具。 支援網頁和 WCF 專案。 桌面或服務應用程式等其他專案類型，您仍然可以[手動新增應用程式的深入見解 SDK 至您的專案](app-insights-windows-desktop.md)。
+ 請確定您有[Visual Studio 2013 更新 3 或更新版本](http://go.microsoft.com/fwlink/?LinkId=397827)。 其出現預先安裝的應用程式的深入見解工具。
+ 選取 [**工具**] **Extensions 和更新**，並檢查已安裝**應用程式的深入見解工具**，並啟用。 如果是這樣，請按一下 [**更新**以查看是否有可用的更新]。
+ 開啟新的專案] 對話方塊，然後選擇 ASP.NET Web 應用程式。 如果您看到應用程式的深入見解選項，然後安裝工具。 如果沒有的話，請嘗試解除安裝，然後重新安裝應用程式的深入見解工具。


## <a name="q02"></a>新增應用程式的深入見解失敗

*建立新 web 專案，或嘗試新增的應用程式的深入見解的現有專案時，我就會看到一則錯誤訊息。*

可能的原因︰

* 無法與應用程式的深入見解入口網站的通訊。或
* 有 Azure 帳戶; 的一些問題
* 您只需要[的訂閱或您已嘗試建立新的資源] 群組中的讀取權限](app-insights-resources-roles-access-control.md)。

Fix:

+ 核取您所提供的登入認證向右 Azure 帳戶。 
+ 在瀏覽器中，核取您能存取至[Azure 入口網站](https://portal.azure.com)。 開啟 [設定]，查看是否有任何限制。
+ [新增至現有專案的應用程式深入見解](app-insights-asp-net.md)︰ 在方案總管中，以滑鼠右鍵按一下您的專案，然後選擇 [新增應用程式深入資訊]。
+ 如果仍然無法運作，請遵循[手動程序](app-insights-windows-services.md)將資源新增在入口網站，然後再將 SDK 新增至您的專案。 

## <a name="emptykey"></a>我收到 「 儀器鍵不是空白 」 錯誤

當您已安裝應用程式的深入見解或也許記錄介面卡發生錯誤看起來。

在方案總管中，以滑鼠右鍵按一下`ApplicationInsights.config`，然後選擇 [**設定應用程式深入資訊**。 您會收到邀請您登入 Azure] 對話方塊，然後建立應用程式的深入見解資源，或重複使用現有的項目。


##<a name="NuGetBuild"></a>「 NuGet 封裝遺失 「 我建立的伺服器

*我正在偵錯時，我開發在電腦上，但在建立伺服器上取得 NuGet 錯誤時，所有項目建立 [確定]。*

請參閱[NuGet 套件還原](http://docs.nuget.org/Consume/Package-Restore)及[自動套件還原](http://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore)。

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>若要從 Visual Studio 中開啟應用程式的深入見解遺失的功能表命令

*當我以滑鼠右鍵按一下專案方案總管] 中時，我沒有看到任何應用程式的深入見解命令，或看不到開啟的應用程式深入資訊] 命令。*

可能的原因︰

* 如果您以手動方式建立應用程式的深入見解資源或專案是不受支援的應用程式的深入見解工具類型。
* 應用程式的深入見解工具會在您的 Visual Studio 中停用。
* 您的 Visual Studio 比 2013年更新 3。

Fix:

* 請確定您的 Visual Studio 版本 2013年更新 3 或更新版本。
* 選取 [**工具**] **Extensions 和更新**，並檢查已安裝**應用程式的深入見解工具**，並啟用。 如果是這樣，請按一下 [**更新**以查看是否有可用的更新]。
* 以滑鼠右鍵按一下 [專案總管] 中。 如果您看到 [**設定應用程式深入資訊**] 命令，則會使用連線至應用程式的深入見解服務中的資源的專案。


否則，您的專案類型直接不支援的應用程式的深入見解工具。 若要查看您遙測，登入[Azure 入口網站](https://portal.azure.com)，選擇 [應用程式的深入見解在左側的導覽列中，然後選取您的應用程式。

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>「 拒絕存取 」 在從 Visual Studio 中開啟應用程式的深入見解

*「 開啟應用程式深入資訊] 功能表命令我 Azure 入口網站，但我收到 「 拒絕存取 」 錯誤。*

![](./media/app-insights-asp-net-troubleshoot-no-data/access-denied.png)

Microsoft 登入您上次使用預設瀏覽器上沒有存取[應用程式的深入見解已新增此應用程式時所建立的資源](app-insights-asp-net.md)。 有兩個可能的原因︰ 

* 您有一個以上的 Microsoft 帳戶-是不是公司電話和個人的 Microsoft 帳戶？ 登入您上次在預設瀏覽器使用的是有權存取[新增到專案的應用程式獲得深入見解](app-insights-asp-net.md)的項目與不同的帳戶。 

 * Fix︰ 按一下您的名稱，在最右邊的在瀏覽器視窗中，及登出。 然後有權存取的帳戶以登入。 在左側的導覽列中，按一下 [應用程式的深入見解然後選取您的應用程式。

* 其他人新增應用程式的深入見解專案中，與他們忘了可讓您建立的[存取權的資源群組](app-insights-resources-roles-access-control.md)。 

 * Fix︰ 如果他們使用組織帳戶時，他們可以將您新增至小組;或者，他們可以授予個別存取資源群組。



## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>「 資產上找不到 「 從 Visual Studio 中開啟應用程式的深入見解

*「 開啟應用程式深入資訊] 功能表命令我 Azure 入口網站，但我收到 「 找不到資產 」 錯誤。*

可能的原因︰

* 您的應用程式的應用程式的深入見解資源已被刪除;或
* 儀器鍵已設定或變更 ApplicationInsights.config 中直接編輯未更新專案檔案。 

遙測會傳送位置的 ApplicationInsights.config 控制項中儀器索引鍵。 在專案檔案控制當您在 Visual Studio 中使用的命令，會開啟的資源。 

Fix:

* 在方案總管中，以滑鼠右鍵按一下 [專案，然後選擇 [應用程式的深入見解，設定應用程式深入資訊。 在對話方塊中，您可以選擇將遙測傳送至現有的資源，或建立新的範本。 或者︰
* 直接開啟資源。 登入[Azure 入口網站](https://portal.azure.com)，在左側的導覽列中，按一下 [應用程式的深入見解，然後選取您的應用程式。



## <a name="where-do-i-find-my-telemetry"></a>哪裡可以找到我遙測？

*至[Microsoft Azure 入口網站](https://portal.azure.com)登入，我想要在 Azure 常用的儀表板。因此位置找到我的應用程式的深入見解的資料？*

* 在左的導覽列上，按一下 [應用程式的深入見解，然後您應用程式的名稱。 如果您那里沒有安裝任何專案，您需要[新增](app-insights-asp-net.md)或設定 web 專案中的應用程式深入資訊。

    那里，您會看到某些摘要的圖表。 您可以按一下即可查看更多詳細資料。

* 在 Visual Studio 中，您偵錯時您的應用程式，按一下應用程式的深入見解按鈕。


## <a name="q03"></a>沒有 server 資料 （或完全沒有資料）

*我執行我的應用程式，然後再開啟 Microsoft Azure 中的 [應用程式的深入見解服務但所有圖表都顯示 「 了解如何收集...」 或 「 未設定]。* 或者，*只頁面檢視與使用者資料，但沒有伺服器的資料。*

+ 在 Visual Studio (F5) 的偵錯模式中執行應用程式。 使用以產生一些遙測應用程式。 您可以看到 Visual Studio 輸出視窗中的事件的核取。 

    ![](./media/app-insights-asp-net-troubleshoot-no-data/output-window.png)

+ 在應用程式的深入見解入口網站中，開啟 [[診斷搜尋](app-insights-diagnostic-search.md)]。 資料通常會出現以下第一次。
+ 按一下 [重新整理] 按鈕。 刀重新整理本身定期，但您也可以執行，以手動方式。 重新整理的間隔是較大的時間範圍的更長的時間。
+ 請檢查儀器按鍵相符。 在主刀應用程式在應用程式的深入見解入口網站中的**基本資訊**] 下拉式清單，查看**儀器鍵**。 然後，在您專案中 Visual Studio 中，開啟 ApplicationInsights.config，並尋找`<instrumentationkey>`。 檢查兩個機碼等於。 如果不是︰
 + 在入口網站中，按一下 [應用程式的深入見解，尋找應用程式資源以正確的金鑰;或
 + Visual Studio 方案總管] 中以滑鼠右鍵按一下專案，然後選擇 [應用程式的深入見解，設定。 重設的應用程式傳送遙測正確的資源。
 + 如果您找不到相符的索引鍵，核取您使用相同的登入認證的 Visual Studio 中入口網站。

    ![](./media/app-insights-asp-net-troubleshoot-no-data/ikey-check.png)
    
+ [Microsoft Azure 家用儀表板](https://portal.azure.com)中查看 [服務健康狀況地圖。 如果有一些通知的指示，請等候他們回到 [確定]，然後關閉並重新開啟您的應用程式的深入見解應用程式刀。
+ 檢查也[我們狀態部落格](http://blogs.msdn.com/b/applicationinsights-status/)。
+ 您的[伺服器端 SDK](app-insights-api-custom-events-metrics.md) ，可能會變更儀器鍵，在您撰寫程式碼`TelemetryClient`執行個體或在`TelemetryContext`嗎？ 或是否您寫的內容[篩選或取樣設定](app-insights-api-filtering-sampling.md)的可能篩選出太多嗎？
+ 如果您編輯 ApplicationInsights.config，請仔細檢查[TelemetryInitializers 和 TelemetryProcessors](app-insights-api-filtering-sampling.md)的設定。 不正確的方式命名類型或參數，可能會導致 SDK 傳送沒有資料。

## <a name="q04"></a>在頁面檢視]、 [瀏覽器中，使用任何資料

*我看到伺服器回應時間和伺服器要求圖表中的資料，但沒有資料在頁面檢視的載入時間，或在瀏覽器] 或 [使用狀況刀。*

在網頁中的指令碼來自資料。 

+ 如果您新增現有的 web 專案，[您必須手動新增的指令碼](app-insights-javascript.md)應用程式的深入見解。
+ 請確定 Internet Explorer 不相容性模式中顯示您的網站。
+ 使用瀏覽器的偵錯功能 （F12 在某些瀏覽器，然後選擇 [網路），請確認傳送資料至`dc.services.visualstudio.com`。

## <a name="no-dependency-or-exception-data"></a>無相依性或例外狀況的資料

請參閱[相依性遙測](app-insights-asp-net-dependencies.md)和[例外狀況遙測](app-insights-asp-net-exceptions.md)。

## <a name="no-performance-data"></a>沒有效能資料

效能資料 (CPU、 IO rate，依此類推) 可供[Java web 服務](app-insights-java-collectd.md)、 [Windows 桌面應用程式](app-insights-windows-desktop.md)、 [IIS web 應用程式與服務，如果您安裝狀態監視器](app-insights-monitor-performance-live-website-now.md)，及[Azure 雲端服務](app-insights-azure.md)。 您會發現在 [伺服器設定。

並未提供 Azure 網站。

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>沒有 （伺服器） 資料，因為伺服器發佈應用程式

+ 核取您實際複製所有 Microsoft。 到伺服器，與 Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll ApplicationInsights Dll
+ 在您的防火牆，您可能[開啟一些 TCP 連接埠](app-insights-ip-addresses.md#data-access-api)。
+ 如果您有使用 proxy 傳送不在您的公司網路，則將[defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx)集 Web.config
+ Windows Server 2008︰ 請確定您已安裝下列更新︰ [KB2468871](https://support.microsoft.com/kb/2468871)， [KB2533523](https://support.microsoft.com/kb/2533523)， [KB2600217](https://support.microsoft.com/kb/2600217)。


## <a name="i-used-to-see-data-but-it-has-stopped"></a>我使用以查看資料，但它已停止

* 核取[狀態部落格](http://blogs.msdn.com/b/applicationinsights-status/)。
* 已按下您的資料點的每月配額嗎？ 開啟 [設定/配額價格，找出。 如果是這樣，您可以升級您的方案，或支付額外的容量。 請參閱[價格配置](https://azure.microsoft.com/pricing/details/application-insights/)。


## <a name="i-dont-see-all-the-data-im-expecting"></a>我看不到我我預期的所有資料

如果您的應用程式傳送大量資料，您使用的應用程式的深入見解 SDK ASP.NET 版本 2.0.0-beta3 或更新版本，[調整取樣](app-insights-sampling.md)功能可能運作，然後傳送您遙測的百分比。 

您可以停用，但是建議您不要。 取樣的設計，因此相關的遙測正確傳送，請進行診斷。 

## <a name="wrong-geographical-data-in-user-telemetry"></a>使用者遙測錯誤地理資料

縣/市、 地區和國家/地區維度所衍生的 IP 位址，並不一定正確。

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>例外狀況 」 找不到方法 」 上執行中 Azure 雲端服務

您建立的.NET 4.6 並未嗎？ Azure 雲端服務角色不會自動支援 4.6。 執行您的應用程式之前，先[安裝 4.6，每一個角色](../cloud-services/cloud-services-dotnet-install-dotnet.md)。

## <a name="still-not-working"></a>仍然無法運作...

* [應用程式的深入見解論壇](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

