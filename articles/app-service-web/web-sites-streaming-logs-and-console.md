<properties 
    pageTitle="串流記錄檔和主控台" 
    description="串流記錄檔和主控台概觀" 
    authors="btardif" 
    manager="wpickett" 
    editor="" 
    services="app-service\web" 
    documentationCenter=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="10/12/2016" 
    ms.author="byvinyal"/>

# <a name="streaming-logs-and-the-console"></a>串流記錄檔和 [主控台

## <a name="streaming-logs"></a>串流記錄

**Azure 入口網站**提供整合式串流記錄檔檢視器可讓您即時檢視追蹤**應用程式服務**應用程式的事件。  

設定此功能需要幾個簡單步驟︰

- 撰寫程式碼中的追蹤
- 啟用您的應用程式的應用程式**診斷記錄**
- **Azure 入口網站**中檢視的內建的**串流記錄**UI 的資料流。

### <a name="how-to-write-traces-in-your-code"></a>如何撰寫程式碼中的追蹤 ###

撰寫程式碼中的追蹤很容易。  在 C# 是，只要撰寫下列程式碼︰

`````````````````````````
Trace.TraceInformation("My trace statement");
`````````````````````````

`````````````````````````
Trace.TraceWarning("My warning statement");
`````````````````````````

`````````````````````````
Trace.TraceError("My error statement");
`````````````````````````

追蹤類別放在 System.Diagnostics 命名空間。

在 node.js 應用程式中，您可以撰寫此程式碼來達成相同的結果︰

`````````````````````````
console.log("My trace statement").
`````````````````````````

### <a name="how-to-enable-and-view-the-streaming-logs"></a>如何啟用並檢視串流記錄
![][BrowseSitesScreenshot]啟用診斷每個應用程式為基礎。 首先瀏覽至您想要在啟用此功能的網站。  
  
![][DiagnosticsLogs]從 [設定] 功能表中，向下捲動的 「**監控**」 區段並按一下 [ **(1) 的診斷記錄**。 然後**(2) 啟用****的應用程式記錄 （系統）** ] 或 [**應用程式記錄 (blob)** **層級**] 選項可讓您變更追蹤要擷取的重要性層級。 如果您只想熟悉功能，則會將等級設定以確保所有您追蹤陳述式收集的**詳細資訊**。

按一下 [刀頂端的 [**儲存**並您準備好要檢視記錄檔。

>[AZURE.NOTE] 使用更多資源記錄和的更多的追蹤的**重要性層級**產生的高。 請確定**重要性層級**設定為正確的詳細等級生產或高流量網站。 

![][StreamingLogsScreenshot]若要檢視**資料流記錄**從 Azure 入口網站中，按一下**(1) 的記錄資料流**也**監控**的區段中 [設定] 功能表。 如果您的應用程式正在撰寫追蹤陳述式，然後您應該看到中**(2) 串流記錄 UI**近期即時。

## <a name="console"></a>主控台
**Azure 入口網站**提供主控台存取您的應用程式。 您可以瀏覽您的應用程式檔案系統，並執行 powershell/cmd 指令碼。 您是繫結執行主控台命令時，為您執行的應用程式碼設定相同的權限。 受保護的目錄或執行指令碼需要提高權限的權限會被封鎖的存取權。  

![][ConsoleScreenshot]從 [設定] 功能表中，向下**開發工具**] 區段，按一下**（1） 主控台**捲動和**（2） 主控台**UI 會開啟右邊。

若要熟悉**主控台**，請嘗試基本的命令，例如︰

`````````````````````````
dir
`````````````````````````

`````````````````````````
cd
`````````````````````````

<!-- Images. -->
[DiagnosticsLogs]: ./media/web-sites-streaming-logs-and-console/diagnostic-logs.png
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png
