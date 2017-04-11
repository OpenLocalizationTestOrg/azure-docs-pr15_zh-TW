<properties
    pageTitle="追蹤與 Azure 診斷在雲端服務應用程式中的流程 |Microsoft Azure"
    description="新增追蹤訊息 Azure 的應用程式，以協助偵錯時測量效能、 監控、 流量分析及其他功能。"
    services="cloud-services"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="02/20/2016"
    ms.author="robb"/>



# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>追蹤與 Azure 診斷在雲端服務應用程式的流程

追蹤可供您在執行中時，監視應用程式的執行方式。 您可以使用[System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx) [System.Diagnostics.Debug](https://msdn.microsoft.com/library/system.diagnostics.debug.aspx)，與[System.Diagnostics.TraceSource](https://msdn.microsoft.com/library/system.diagnostics.tracesource.aspx)類別來記錄、 文字檔案或更新版本的分析的其他裝置中記錄錯誤及執行應用程式的相關資訊。 如需有關追蹤的詳細資訊，請參閱[追蹤及操作的應用程式](https://msdn.microsoft.com/library/zs6s4h68.aspx)。


## <a name="use-trace-statements-and-trace-switches"></a>使用追蹤陳述式與追蹤選項

藉由新增到應用程式設定的[DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx)打電話 System.Diagnostics.Trace 或 System.Diagnostics.Debug 應用程式碼中，在雲端服務應用程式中實作追蹤。 使用設定檔*app.config*工作者角色和*web.config*的 web 角色。 當您建立的新的代管的服務，使用 Visual Studio 範本時，Azure 診斷會自動新增至專案，並 DiagnosticMonitorTraceListener 會新增至您所新增的角色的適當的設定檔。

放置追蹤陳述式的詳細資訊，請參閱[如何︰ 新增應用程式碼的追蹤陳述式](https://msdn.microsoft.com/library/zd83saa2.aspx)。

[追蹤參數](https://msdn.microsoft.com/library/3at424ac.aspx)放在您的程式碼中，您可以控制是否發生追蹤及龐大。 這個選項可讓您監視生產環境中的應用程式的狀態。 這是很重要，特別使用多個元件執行多部電腦上的商務應用程式。 如需詳細資訊，請參閱[如何︰ 設定追蹤參數](https://msdn.microsoft.com/library/t06xyy08.aspx)。

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Azure 應用程式中設定追蹤接聽

追蹤偵錯] 和 [TraceSource，要求您設定 「 接聽 」 來收集和記錄傳送的郵件。 接聽收集、 儲存及傳送追蹤訊息。 他們追蹤輸出導向至適當的目標，例如記錄、] 視窗中或文字檔案。 Azure 診斷使用[DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx)類別。

在您完成下列程序之前，您必須啟動 Azure 診斷監視器。 若要這麼做，請參閱[Microsoft Azure 中的 [啟用診斷](cloud-services-dotnet-diagnostics.md)。

請注意，是否您使用的範本所提供的 Visual Studio，接聽程式的設定會自動新增您。


### <a name="add-a-trace-listener"></a>新增追蹤接聽

1. 開啟您的角色 web.config 或 app.config 檔案。
2. 將下列程式碼新增至的檔案。 變更版本屬性，以使用您參考的組件的版本號碼。 更新，除非組件版本不一定變更每個 Azure SDK 發行。

    ```
    <system.diagnostics>
        <trace>
            <listeners>
                <add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener,
                  Microsoft.WindowsAzure.Diagnostics,
                  Version=2.8.0.0,
                  Culture=neutral,
                  PublicKeyToken=31bf3856ad364e35"
                  name="AzureDiagnostics">
                  <filter type="" />
                </add>
            </listeners>
        </trace>
    </system.diagnostics>
    ```
    >[AZURE.IMPORTANT] 請確定您有 Microsoft.WindowsAzure.Diagnostics 組件的專案參考。 更新的版本號碼在上述 xml 符合參考 Microsoft.WindowsAzure.Diagnostics 組件的版本。

3. 儲存設定檔。

如需有關接聽程式的詳細資訊，請參閱[追蹤接聽](https://msdn.microsoft.com/library/4y5y10s7.aspx)。

完成步驟以新增接聽程式之後，您可以新增您的程式碼的追蹤陳述式。


### <a name="to-add-trace-statement-to-your-code"></a>若要新增您的程式碼的追蹤陳述式

1. 開啟應用程式的來源檔案。 例如，<RoleName>工作者角色或網頁角色.cs 檔案。
2. 新增下列使用陳述式，如果它不已新增︰
    ```
        using System.Diagnostics;
    ```
3. 新增您要擷取的應用程式的狀態相關資訊的追蹤陳述式。 若要設定格式之追蹤陳述式的成果，您可以使用各種不同的方法。 如需詳細資訊，請參閱[如何︰ 新增應用程式碼的追蹤陳述式](https://msdn.microsoft.com/library/zd83saa2.aspx)。
4. 儲存來源檔案。
