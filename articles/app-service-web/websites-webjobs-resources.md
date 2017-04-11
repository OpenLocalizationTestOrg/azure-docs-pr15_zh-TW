<properties 
    pageTitle="Azure WebJobs 文件資源" 
    description="建議您學習如何使用 Azure WebJobs 和 Azure WebJobs SDK 的資源。" 
    services="app-service" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/28/2016" 
    ms.author="tdykstra"/>

# <a name="azure-webjobs-documentation-resources"></a>Azure WebJobs 文件資源

## <a name="overview"></a>概觀

本主題會連結到文件資源，瞭解如何使用 Azure WebJobs 和 Azure WebJobs SDK。 Azure WebJobs 提供簡單的方法為[應用程式服務 web 應用程式、 API 應用程式或行動應用程式](../app-service/app-service-value-prop-what-is.md)的內容中的 [背景處理程序執行指令碼或程式。 您可以上傳並 cmd，以執行可執行檔，例如棍執行檔 (.NET)，ps1，sh，php，py，js 和 jar。 下列程式的執行為 WebJobs (cron) 的排程或持續。

[WebJobs SDK](websites-webjobs-resources.md)的目的，是以簡化 WebJob 可以執行，例如圖像處理、 佇列處理撰寫的一般工作的程式碼、 RSS 彙總、 檔案維護及傳送電子郵件。 WebJobs SDK 有使用 Azure 儲存體和服務匯流排的、 排程的工作，以及處理錯誤，以及其他許多常見的案例的內建功能。 此外，它用來擴充功能，而且沒有[開啟來源存放庫的擴充](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)。 [Azure 函數](../azure-functions/functions-overview.md)（目前在預覽版本） 根據 WebJobs SDK C# 指令碼、 Node.js，和其他語言的版本。 

建立、 部署及管理 WebJobs 是使用整合式的工具，在 Visual Studio 順暢。 您可以從範本建立 WebJobs、 發佈及管理 （執行/停止/監視器/偵錯） 它們。 

Azure 入口網站中的 [WebJobs 儀表板提供的功能強大的管理功能，讓您執行的 WebJobs，包括可叫用 WebJobs 中的個別函數的完全控制權。 函數執行階段和記錄輸出，也會顯示儀表板。 

##<a name="getstarted"></a>WebJobs 與 WebJobs SDK 快速入門

* [Azure WebJobs 簡介](http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx)
* [Azure WebJobs 是優異，您應該立即使用 ！](http://www.troyhunt.com/2015/01/azure-webjobs-are-awesome-and-you.html) （由特洛伊搜尋部落格文章）。
* [Azure WebJobs 功能](/blog/2014/10/22/webjobs-goes-into-full-production/)
* [什麼是 WebJobs SDK](websites-dotnet-webjobs-sdk.md)
* [背景的工作指示 Microsoft 模式，作法](/documentation/articles/best-practices-background-jobs/)
* [宣佈使用 1.1.0 RTM Microsoft Azure WebJobs SDK 的](/blog/azure-webjobs-sdk-1-1-0-rtm/)
* [Azure WebJobs SDK 快速入門](websites-dotnet-webjobs-sdk-get-started.md)
* [如何使用 WebJobs SDK Azure 佇列中的儲存空間](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)
* [如何使用 WebJobs SDK Azure blob 儲存體](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)
* [如何使用 WebJobs SDK Azure 資料表儲存體](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)
* [如何使用 WebJobs SDK Azure 服務匯流排](websites-dotnet-webjobs-sdk-service-bus.md)
* [如何使用 WebHooks WebJobs sdk，您可以使用 GitHub、 IFTTT，及 HTTP 的範例](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/WebHooks-Walkthrough)
* [Azure WebJobs SDK 的快速參考 （PDF 下載）](http://go.microsoft.com/fwlink/?LinkID=524028&clcid=0x409)
* [GitHub WebJobs 設定文件](https://github.com/projectkudu/kudu/wiki/Web-jobs)。
* 視訊
    * [WebJobs 和 WebJobs SDK](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-153-WebJobs-with-Pranav-Rastogi?utm_source=dlvr.it&utm_medium=twitter)
    * [Azure WebJobs 一系列影片上頻道 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)
    * [介紹 WebJobs Visual studio 工具](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
    * [WebJobs 工具和遠端偵錯](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster)
    * [Azure WebJobs 更新與 Pranav Rastogi-版本 1.1 （英文） 中的擴充功能](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-183-Azure-WebJobs-Update-with-Pranav-Rastogi)

另請參閱下列各節[部署 WebJobs](#deploy)及[測試 」 及 「 偵錯 WebJobs](#debug)。

##<a name="deploy"></a>部署 WebJobs

* [如何使用 Visual Studio 中部署 Azure WebJobs](websites-dotnet-deploy-webjobs.md)
* [部署 WebJobs 使用 Azure 入口網站的方式](web-sites-create-web-jobs.md)
* [啟用 Azure WebJobs 命令列或連續傳送](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)
* [給部署 Azure 使用 WebJobs.NET 主控台應用程式](http://blog.amitapple.com/post/73574681678/git-deploy-console-app/)
* [部署 Azure F # WebJob](http://blogs.msdn.com/b/dave_crooks_dev_blog/archive/2015/02/18/deploying-f-web-job-to-azure.aspx)
* [為 Azure Webjobs 部署自訂的服務](http://withouttheloop.com/articles/2015-06-23-deploying-custom-services-as-azure-webjobs/)
* 視訊
    * [介紹 WebJobs Visual studio 工具](http://channel9.msdn.com/Shows/Web+Camps+TV/Introducing-WebJobs-Tooling-for-Visual-Studio-with-Brady-Gaster) 
    * [WebJobs 工具和遠端偵錯](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="schedule"></a>排程 WebJobs

* [新增 Azure WebJob] 對話方塊](websites-dotnet-deploy-webjobs.md#configure)
* [Azure 入口網站中建立排程的 WebJob](web-sites-create-web-jobs.md#CreateScheduled)
* [連結至 WebJob 排程器工作](http://blog.davidebbo.com/2015/05/scheduled-webjob.html)
* [排程 Azure WebJobs 與 cron 運算式](http://blog.amitapple.com/post/2015/06/scheduling-azure-webjobs/)
* [使用 WebJobs SDK TimerTrigger 排程個別 WebJob 函數](websites-dotnet-webjobs-sdk.md#schedule)

##<a name="debug"></a>測試和偵錯 WebJobs

* [新的開發人員和偵錯 Azure WebJobs Visual Studio 中的功能](http://blogs.msdn.com/b/webdev/archive/2014/11/12/new-developer-and-debugging-features-for-azure-webjobs-in-visual-studio.aspx)
* [檢視 [WebJobs 儀表板](websites-dotnet-webjobs-sdk-get-started.md#view-the-webjobs-sdk-dashboard)
* [撰寫使用 WebJobs SDK 的記錄，並在儀表板中檢視](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)
* [遠端偵錯 WebJobs](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebugwj)
* [誰寫下該 blob？](http://blogs.msdn.com/b/jmstall/archive/2014/02/19/who-wrote-that-blob.aspx) 
* [在雲端裝載互動式的程式碼](http://blogs.msdn.com/b/jmstall/archive/2014/04/26/hosting-interactive-code-in-the-cloud.aspx)
* [新增 Azure WebJobs 追蹤](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)
* [監控、 診斷，及疑難排解 Microsoft Azure 儲存體](../storage/storage-monitoring-diagnosing-troubleshooting.md)
* 視訊
    * [WebJobs 工具和遠端偵錯](http://channel9.msdn.com/Shows/Web+Camps+TV/WebJobs-GA-Series-Episode-1-WebJobs-Tooling-with-Brady-Gaster) 

##<a name="scale"></a>縮放比例 WebJobs

* [調整您的 Web 應用程式，使用 Azure 網站](http://msdn.microsoft.com/magazine/dn786914.aspx)
* [Azure 應用程式服務︰ 架構大比例準備的商務用 Online](https://channel9.msdn.com/Events/Build/2014/3-626)。 涵蓋縮放比例與 WebJobs，包括 WebJobs SDK 的 web 應用程式。
* 視訊
    * [查看 WebJobs 縮放比例](http://channel9.msdn.com/Shows/Azure-Friday/Azure-WebJobs-105-Scaling-out-Web-Jobs)

##<a name="additional"></a>其他 WebJobs 資源

* [Magnus Mårtensson azure WebJobs GA 部落格文章](http://magnusmartensson.com/azure-webjobs-ga)
* [Azure 的應用程式服務上執行 Powershell Web 工作](http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx)
* [完成您 Azure 觸發 WebJobs 時取得通知](http://blog.amitapple.com/post/2014/03/webjobs-notification/)
* [使用 WebJobs 簡單的 Web 應用程式備份保留原則](https://azure.microsoft.com/blog/2014/04/28/simple-web-site-backup-retention-policy-with-webjobs/)
* [Azure Web 應用程式與雲端服務上第一次要求變得很慢](http://wp.sjkp.dk/windows-azure-websites-and-cloud-services-slow-on-first-request/)。 顯示如何使用 WebJobs 模擬只適用於在標準的價格層 AlwaysOn 功能。
* [WebJobs 正常的關閉](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.U72Il_5OWUl)。 WebJobs SDK 的正常的關閉，請參閱[正常關機](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful)。)
* [自動化利用 Azure WebJobs 和 AzCopy 的備份](http://markjbrown.com/azure-webjobs-azcopy/)
* 視訊
    * [以 Magnus Mårtensson azure WebJobs 影片](https://www.youtube.com/playlist?list=PLqp1ZOYYUSd81yEzMYLTw8cz91wx_LU9r)
    * [Azure WebJobs 一系列影片上頻道 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="additionalsdk"></a>其他 WebJobs SDK 資源

* [WebJobs SDK 版本資訊](https://github.com/Azure/azure-webjobs-sdk/wiki/Release-Notes)
* [WebJobs SDK 原始程式碼](https://github.com/Azure/azure-webjobs-sdk)
* [WebJobs SDK 副檔名來源程式碼](https://github.com/Azure/azure-webjobs-sdk-extensions)，與[擴充模型的詳細說明](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)。  
* [WebJobs SDK 指令碼原始碼](https://github.com/Azure/azure-webjobs-sdk-script/)（用於[Azure 函數](../azure-functions/functions-overview.md)）
* [WebJob FREB 檔案以上傳到使用 WebJobs SDK Azure 儲存體](http://thenextdoorgeek.com/post/WAWS-WebJob-to-upload-FREB-files-to-Azure-Storage-using-the-WebJobs-SDK)
* [主控 Azure webjobs Azure 外的，與從 Azure 的記錄優點裝載 webjob](http://bypassion.dk/?p=510)
* [建立與 Azure WebJobs 的資料匯入工具](http://www.freshconsulting.com/building-data-import-tool-azure-webjobs/)
* [Azure 函數概觀](../azure-functions/functions-overview.md)
* 視訊
    * [Azure WebJobs 一系列影片上頻道 9](http://channel9.msdn.com/Tags/azurefridaywebjobs)

##<a name="samples"></a>範例 WebJob 應用程式

* [GitHub WebJobs 小組所提供的範例應用程式](https://github.com/azure/azure-webjobs-sdk-samples)
* [與使用 WebJobs SDK WebJobs 後端的簡單 Azure Web 應用程式](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)
* [SiteMonitR](http://code.msdn.microsoft.com/SiteMonitR-dd4fcf77)。 示範如何使用排程，然後按一下 [事件導向 WebJobs。 請參閱的部落格文章[重建使用 Azure WebJobs SDK SiteMonitR](http://www.bradygaster.com/post/rebuilding-the-sitemonitr-using-windows-azure-webjobs)。

##<a name="blogs"></a>部落格

* [Azure 部落格](/blog)
* [Amit Apple 的部落格](http://blog.amitapple.com/)。 著重於 WebJobs (不 SDK)。
* [Magnus Mårtensson 部落格](http://magnusmartensson.com/)

##<a name="gethelp"></a>取得 WebJobs 的說明

* [針對 WebJobs StackOverflow](http://stackoverflow.com/questions/tagged/azure-webjobs)
* [StackOverflow WebJobs SDK 的](http://stackoverflow.com/questions/tagged/azure-webjobssdk)
* [StackOverflow Azure 函數](http://stackoverflow.com/questions/tagged/azure-functions)
* [Azure 和 ASP.NET 論壇](http://forums.asp.net/1247.aspx)
* [Azure 應用程式服務 Web 應用程式論壇](http://social.msdn.microsoft.com/Forums/azure/home?forum=windowsazurewebsitespreview)
* [Azure Web 應用程式使用者語音網站](https://feedback.azure.com/forums/169385-websites/)
* [Twitter](http://twitter.com/)。 使用 #AzureWebJobs # 標籤。
* [報告 WebJobs 錯誤或問題](https://github.com/projectkudu/kudu/wiki/Reporting-WebJobs-issues)

