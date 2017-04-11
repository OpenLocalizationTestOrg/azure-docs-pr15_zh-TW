<properties
    pageTitle="最佳作法和 Azure Web 應用程式疑難排解指南節點應用程式"
    description="進一步瞭解最佳作法和 Azure Web 應用程式的節點應用程式的疑難排解步驟。"
    services="app-service\web"
    documentationCenter="nodejs"
    authors="ranjithr"
    manager="wadeh"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="06/06/2016"
    ms.author="ranjithr;wadeh"/>
    
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-web-apps"></a>最佳作法和 Azure Web 應用程式疑難排解指南節點應用程式

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

本文中，您將學習的最佳作法和[節點應用程式](app-service-web-nodejs-get-started.md)上執行 Azure Webapps （ [iisnode](https://github.com/azure/iisnode)) 的疑難排解步驟。

>[AZURE.WARNING] 請謹慎使用生產網站上的疑難排解步驟。 建議您暫存的位置，例如疑難排解您的應用程式安裝在非生產上，並修正問題，當交換您暫存的位置和您生產位置。

## <a name="iisnode-configuration"></a>IISNODE 設定

此[結構描述檔案](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml)會顯示所有 iisnode 可以設定的設定。 設定適合您的應用程式，包括︰

* nodeProcessCountPerApplication

    這項設定控制的每個 IIS 應用程式啟動的節點程序。 預設值為 1。 您也可以設定為 VM 核心統計啟動多 node.exe 0。 建議的值是 0，大部分的應用程式，因此您可以將所有核心利用您的電腦上。 Node.exe 是核心的單一階層式，一個 node.exe 所要使用的最大值的 1，並取得最大的效能不在您想要使用所有核心節點應用程式。

* nodeProcessCommandLine

    這項設定控制 node.exe 的路徑。 您可以設定此值，指向您 node.exe 版本。

* maxConcurrentRequestsPerProcess

    這項設定控制一個要求傳送給每個 node.exe iisnode 的數上限。 Azure webapps，在此預設值為無限。 您不會擔心這項設定。 外部 azure webapps，預設值為 1024年。 您可能會想要設定這視多少要求取得應用程式與您的應用程式的每個要求的處理速度而定。

* maxNamedPipeConnectionRetry

    這項設定控制 iisnode 會再試一次之後連線透過傳送邀請以 node.exe 具名管道最大次數。 結合 namedPipeConnectionRetryDelay 此設定會決定 iisnode 中每個要求的總逾時。 預設值為 200 Azure Webapps 上。 總逾時秒數 = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000年

* namedPipeConnectionRetryDelay

    這項設定控制的時間 （以毫秒） iisnode 等候之間，傳送要求給 node.exe，透過具名管道每次重試。 預設值為 250ms年。
    總逾時秒數 = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000年

    依預設在 iisnode azure webapps 上的總逾時為 200 \* 250ms = 50 秒。

* logDirectory

    這項設定控制的目錄 iisnode 記錄 stdout/stderr 的位置。 預設值是 iisnode 這是與主要指令碼目錄 （主要 server.js 哪裡簡報目錄）

* debuggerExtensionDll

    這項設定控制偵錯節點應用程式時，會使用哪個版本的節點檢查 iisnode。 目前 iisnode-檢查-0.7.3.dll 和 iisnode inspector.dll 是此設定只有 2 有效的值。 預設值為 iisnode-檢查-0.7.3.dll。 iisnode-檢查-0.7.3.dll 版本使用節點-檢查-0.7.3，並使用 websockets，所以您必須啟用上您要使用此版本的 azure webapp websockets。 <Http://www.ranjithr.com/?p=98>如需詳細資訊，請參閱如何設定以使用新的節點檢查 iisnode。

* flushResponse

    IIS 的預設行為是它緩衝回應資料設定 4 mb 之前更新，或到結束的回應，視何者先。 iisnode 提供設定的設定，以覆寫此行為︰ iisnode 會將它接收 node.exe 時，請清除 [回應實體內容片段，必須先設定iisnode/@flushResponseweb.config 為 「 true 」 中的屬性︰
    
    ```
    <configuration>    
        <system.webServer>    
            <!-- ... -->    
            <iisnode flushResponse="true" />    
        </system.webServer>    
    </configuration>
    ```

    啟用清除每個回應實體內容片段增加效能負荷減少系統的 5%（如 v0.1.13)，因此最好範圍只需要回應串流的結束點此設定 (例如使用<location>web.config 中的項目)

    此外，串流應用程式，您必須也將您的 iisnode 處理常式 responseBufferLimit 設定為 0。
    
    ```
    <handlers>    
        <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>    
    </handlers>
    ```

* watchedFiles

    這是以分號分隔之檔案的清單會監看之變更。 檔案的變更會造成回收的應用程式。 每個項目組成選項目錄名稱，再加上必要的檔案名稱會與目錄的主要應用程式進入點所在的位置。 只有檔案名稱部分中使用萬用字元。 預設值為 「\*。 js;web.config 」

* recycleSignalEnabled

    預設值為 false。 如果啟用，節點應用程式可以連線至具名管道 (環境變數 IISNODE\_控制項\_直立線符號) 並傳送 「 資源 」 訊息。 這會導致適當地回收 w3wp。

* idlePageOutTimePeriod

    預設值為 0 表示此功能已停用。 當設定一些值大於 0，iisnode 將頁面查看所有的子程序每 'idlePageOutTimePeriod' 毫秒。 若要瞭解哪些頁面查看所代表的意義，請參閱本文的[文件](https://msdn.microsoft.com/library/windows/desktop/ms682606.aspx)。 此設定會很適合用於應用程式，使用大量的記憶體，以釋出一些 RAM 偶爾想要 pageout 記憶體到磁碟。

>[AZURE.WARNING] 請謹慎啟用生產應用程式上的下列設定。 建議啟用它們的實際生產應用程式。

* debugHeaderEnabled

    預設值為 false。 如果設為 true，iisnode 會將 HTTP 回應標頭 iisnode 偵錯新增至每個 HTTP 回應會傳送 iisnode 偵錯標頭值是 URL。 診斷資訊的各個部分可以以及查看 URL 片段，但更好的視覺效果透過瀏覽器中開啟 URL。

* loggingEnabled

    這項設定控制 iisnode stdout 和 stderr 的記錄。 Iisnode 會擷取 stdout/來自啟動節點程序，並寫入 'logDirectory' 設定中指定的目錄。 一旦啟用，您的應用程式將寫入記錄檔檔案系統，並根據記錄應用程式完成的量，可能影響效能。

* devErrorsEnabled

    預設值為 false。 當設為 true，iisnode 會顯示 HTTP 狀態碼和 Win32 錯誤碼在瀏覽器上。 Win32 程式碼會很有幫助偵錯時，某些類型的問題。
    
* debuggingEnabled （不在實際生產網站上啟用）

    這項設定控制偵錯的功能。 節點檢查整合 Iisnode。 啟用此設定，您可以啟用偵錯節點應用程式。 一旦啟用這項設定，iisnode 將版面配置節點應用程式的第一個偵錯要求 'debuggerVirtualDir' 目錄中所需的節點檢查檔案。 若要載入節點檢查，您可以 http://yoursite/server.js/debug 傳送邀請。 您可以控制偵錯 URL 區段 'debuggerPathSegment 」 設定。 預設 debuggerPathSegment = '偵錯 」。 您可以設定此範例中 guid，讓其他人可以探索較為困難。

    核取此如需詳細資訊偵錯][連結](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html)。

## <a name="scenarios-and-recommendationstroubleshooting"></a>案例和疑難排解建議 /

### <a name="my-node-application-is-making-too-many-outbound-calls"></a>我的節點應用程式正在進行太多輸出呼叫。

許多應用程式可能會想進行輸出連線的正常作業的一部分。 例如，要求時，節點應用程式要連絡 REST API 的其他位置，並取得處理要求的資訊。 您想要 http 或 https 通話時所使用的保留作用代理程式。 例如，您可以使用 agentkeepalive 模組為您保留作用代理人時進行這些輸出呼叫。 這可確保您 azure webapp VM 上重複使用的通訊端並減少建立新的通訊端針對每個輸出的要求。 此外，這可確保您使用較少的通訊端的數字，讓許多輸出的要求，並因此您不超過配置每 VM maxSockets。 建議在 Azure Webapps 是 agentKeepAlive maxSockets 值設定為每個 VM 160 通訊端總計。 這表示如果您有 4 node.exe VM 上執行時，您會想要將 agentKeepAlive maxSockets 設定為每 node.exe 這是 160 總每 VM 40。

範例 agentKeepALive 設定︰

```
var keepaliveAgent = new Agent({    
    maxSockets: 40,    
    maxFreeSockets: 10,    
    timeout: 60000,    
    keepAliveTimeout: 300000    
});
```

假設您有 4 node.exe 您 VM 上執行。 如果您有 node.exe VM 上執行的數目不同，您必須修改設定會相應地 maxSockets。

### <a name="my-node-application-is-consuming-too-much-cpu"></a>我的節點應用程式正在使用太多 CPU。

您可能會收到從 Azure Webapps 的建議高 cpu 消耗有關您入口網站上。 您也可以設定某些[指標](web-sites-monitor.md)監看的螢幕。 檢查上的 CPU 使用率[Azure 入口網站儀表板](../application-insights/app-insights-web-monitor-performance.md)，請核取的最大值的 CPU，以便不錯過的最大使用量值。
在您認為您的應用程式會使用太多 CPU，而且您不出原因的情況下，您必須設定檔節點應用程式。

### 

#### <a name="profiling-your-node-application-on-azure-webapps-with-v8-profiler"></a>分析 azure webapps 與 V8 分析工具上的應用程式節點

例如，可讓假設您有您想要的設定檔，如下所示的認識全球應用程式︰

```
var http = require('http');    
function WriteConsoleLog() {    
    for(var i=0;i<99999;++i) {    
        console.log('hello world');    
    }    
}

function HandleRequest() {    
    WriteConsoleLog();    
}

http.createServer(function (req, res) {    
    res.writeHead(200, {'Content-Type': 'text/html'});    
    HandleRequest();    
    res.end('Hello world!');    
}).listen(process.env.PORT);
```

移至您 scm 網站 https://yoursite.scm.azurewebsites.net/DebugConsole

您會看到命令提示字元中，如下所示。 移至您的網站/wwwroot 目錄

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

執行 「 npm 安裝 v8 分析工具 」 的命令

此應該安裝 v8 分析工具節點下\_模組目錄及所有的相依性。
現在，編輯您的 server.js 設定檔應用程式。

```
var http = require('http');    
var profiler = require('v8-profiler');    
var fs = require('fs');

function WriteConsoleLog() {    
    for(var i=0;i<99999;++i) {    
        console.log('hello world');    
    }    
}

function HandleRequest() {    
    profiler.startProfiling('HandleRequest');    
    WriteConsoleLog();    
    fs.writeFileSync('profile.cpuprofile', JSON.stringify(profiler.stopProfiling('HandleRequest')));    
}

http.createServer(function (req, res) {    
    res.writeHead(200, {'Content-Type': 'text/html'});    
    HandleRequest();    
    res.end('Hello world!');    
}).listen(process.env.PORT);
```

上述變更將設定檔 WriteConsoleLog 函數]，然後撰寫您的網站 wwwroot 下 'profile.cpuprofile' 檔案的 [設定檔輸出。 您的應用程式傳送邀請。 您會看到您的網站 wwwroot 下建立的 「 profile.cpuprofile 」 檔案。

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

下載此檔案，您必須使用 Chrome F12 工具開啟此檔案。 在 chrome、 按 F12，然後按一下 「 設定檔] 索引標籤]。 按一下 [載入] 按鈕。 選取您剛才下載您 profile.cpuprofile 檔案。 按一下您載入的設定檔。

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

您會看到如下所示，由 WriteConsoleLog 函數使用 95%的時間。 這也會顯示確切的行號及會造成問題的來源檔案。

### <a name="my-node-application-is-consuming-too-much-memory"></a>我的節點應用程式會使用太多記憶體。

您可能會收到從 Azure Webapps 的建議高記憶體使用量有關您入口網站上。 您也可以設定某些[指標](web-sites-monitor.md)監看的螢幕。 檢查[Azure 入口網站儀表板](../application-insights/app-insights-web-monitor-performance.md)上的記憶體使用率，請核取記憶體的最大值，以便您不錯過的最大使用量值。

#### <a name="leak-detection-and-heap-diffing-for-nodejs"></a>遺漏偵測及堆積差異的 node.js 

您可以使用[節點 memwatch](https://github.com/lloyd/node-memwatch)可協助您識別記憶體遺漏。
您可以安裝 memwatch 就像 v8 分析工具並編輯您的程式碼來擷取和差異識別記憶體堆積遺漏應用程式中的詳細資訊。

### <a name="my-nodeexes-are-getting-killed-randomly"></a>我 node.exe 是隨機快速清除 

為什麼此，就可能發生的幾個原因有︰

1.  您的應用程式會擲回無法攔截例外狀況 – 請核取 d\\家用\\記錄檔\\應用程式\\例外狀況的詳細資料記錄 errors.txt 檔案。 此檔案都有堆疊追蹤，因此您可以修正根據這個應用程式。

2.  您的應用程式正在使用會影響其他處理程序的快速入門的記憶體太大。 如果總 VM 記憶體接近 100%，您 node.exe 可能會依，讓其他處理程序，有機會執行某些工作流程管理員。 若要修正此問題，請確定您的應用程式不遺漏記憶體或是如果您的應用程式真的需要使用大量的記憶體，請使用更多的 RAM 較大 vm 縮放。

### <a name="my-node-application-does-not-start"></a>我的節點應用程式不會啟動

如果您的應用程式傳回 500 錯誤在啟動時，可能有幾個原因︰

1.  Node.exe 不正確的位置。 檢查 nodeProcessCommandLine 設定。

2.  主要指令碼沒有檔案的正確位置。 核取 web.config 並確認主要的指令碼檔案處理常式] 區段中的名稱相符的主要的指令碼檔。

3.  Web.config 的設定不正確-核取設定名稱/值。

4.  低溫開始-您的應用程式的時間太長啟動。 如果您的應用程式耗費的時間比 (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000 的秒數，iisnode 會傳回介於 500 錯誤。 增加這些設定以符合您的應用程式開始時間，以防止 iisnode 逾時與傳回 500 的錯誤的值。

### <a name="my-node-application-crashed"></a>我節點的應用程式當機

您的應用程式會擲回無法攔截例外狀況 – 請核取 d\\家用\\記錄檔\\應用程式\\例外狀況的詳細資料記錄 errors.txt 檔案。 此檔案都有堆疊追蹤，因此您可以修正根據這個應用程式。

### <a name="my-node-application-takes-too-much-time-to-startup-cold-start"></a>我的節點應用程式所需的時間太多時間來啟動 （低溫開始）

最常見的原因是應用程式有許多節點中的檔案\_模組與應用程式會在啟動時載入大部分的這些檔案。 根據預設，因為您的檔案位於網路上共用 Azure Webapps 載入，因此多個檔案需要一些時間。
更快速讓一些解決方案是︰

1.  請確定您有一般的相依性結構和不重複的相依性，請使用 npm3 安裝您的模組。

2.  嘗試麻煩載入您的節點\_模組，無法載入所有在啟動時的模組。 這表示實際函式您嘗試使用模組中需要時，應該完成 require('module') 通話。

3.  Azure Webapps 提供稱為 「 本機快取的功能。 此功能會將您的內容從網路共用複製到本機磁碟 VM 上。 由於的檔案會在本機，節點的載入時間\_模組會更快。 的[文件](../app-service/app-service-local-cache.md)本文說明如何使用本機快取中更多詳細資料。

## <a name="iisnode-http-status-and-substatus"></a>IISNODE http 狀態和子狀態

這個[來源檔案](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h)會列出所有可能的狀態/子狀態組合 iisnode 會傳回錯誤的情況下。

啟用您的應用程式，若要查看的 win32 錯誤碼 FREB （請確定您啟用僅在非生產網站的效能 FREB）。

| Http 狀態 | Http 子狀態 | 可能的原因？                                                                                                                                                                                            
|-------------|----------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------
| 500         | 1000           | 沒有分派 IISNODE 邀請某些問題-是否啟動 node.exe 核取。 在啟動時，可能是當機 Node.exe。 核取您 web.config 的設定錯誤。                                                                                                                                                                                                                                                                                     |
| 500         | 1001           | -Win32Error 0x2-應用程式沒有回應的 url。 檢查 URL 重新寫入規則或快速應用程式是否具有正確定義的路徑。 -Win32Error 0x6d – 具名管道為忙碌-Node.exe 不接受邀請，因為管道忙碌中。 核取高 cpu 使用率。 -其他錯誤-檢查 node.exe 損毀。
| 500         | 1002           | Node.exe 損毀 – 請 d\\家用\\記錄檔\\記錄-errors.txt 的堆疊追蹤。                                                                                                                                                                                                                                                                                                                                                                                        |
| 500         | 1003           | 管道組態問題 – 就不會看到這個，但如果您這麼做，具名的管道設定不正確。                                                                                                                                                                                                                                                                                                                                                          |
| 500         | 1004-1018 錯誤      | 傳送邀請，或是處理來自 node.exe 的回應時發生錯誤。 核取 node.exe 損毀。 檢查 d\\家用\\記錄檔\\記錄-errors.txt 的堆疊追蹤。                                                                                                                                                                                                                                                                                    |
| 503         | 1000           | 沒有足夠的記憶體配置更具名的管道連線。 為什麼您的應用程式正在使用太多記憶體核取。 檢查 maxConcurrentRequestsPerProcess 設定值。 如果並非無限，而且您有很多要求、 增加此值，以避免這項錯誤。                                                                                                                                                                                                                                                                                                                  |
| 503         | 1001           | 要求無法發送至 node.exe，因為應用程式回收。 具有回收應用程式後，應提供正常。                                                                                                                                                                                                                                                                                                               |
| 503         | 1002           | 無法以 node.exe 發送核取 win32 錯誤碼實際原因 – 要求。                                                                                                                                                                                                                                                                                                                                                                               |
| 503         | 1003           | 具名的管道是忙碌 – 請大量 CPU 時，是否要使用節點                                                                                                                                                                                                                                                                                                                                                                                                        
                                                                                                                                                                                                                                                                                                            
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         
有 NODE.exe 稱為節點中的設定\_擱置\_管道\_執行個體。 根據預設，外部 azure webapps 此值為 4。 這表示該 node.exe 只能在具名管道上一次接受 4 的要求。 在 Azure Webapps 此值設為 5000，這個值應該是不錯的大部分 azure webapps 上執行的節點應用程式。 您應該上未顯示 503.1003 azure webapps 因為我們節點高值\_擱置\_管道\_執行個體。  |

## <a name="more-resources"></a>更多資源

請遵循這些連結，深入瞭解 Azure 應用程式服務 node.js 應用程式。

* [Azure 應用程式服務中的 Node.js web apps 快速入門](app-service-web-nodejs-get-started.md)
* [如何偵錯 Node.js web app 中 Azure 應用程式服務](web-sites-nodejs-debug.md)
* [Azure 應用程式中使用 Node.js 模組](../nodejs-use-node-modules-azure-apps.md)
* [Azure 應用程式服務 Web 應用程式︰ Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Node.js 開發人員中心](../nodejs-use-node-modules-azure-apps.md)
* [探索把私人 Kudu 偵錯主控台](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)