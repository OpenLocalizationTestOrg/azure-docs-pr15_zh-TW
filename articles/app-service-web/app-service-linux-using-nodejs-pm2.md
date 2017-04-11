<properties 
    pageTitle="NodeJS linux 的 Web 應用程式中使用 PM2 設定 |Microsoft Azure" 
    description="NodeJS linux 的 Web 應用程式中使用 PM2 設定" 
    keywords="azure 應用程式服務、 web 應用程式、 nodejs、 pm2、 linux、 os"
    services="app-service" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="naziml"/>

# <a name="using-pm2-configuration-for-nodejs-in-web-apps-on-linux"></a>Node.js linux 的 Web 應用程式中使用 PM2 設定

如果您網頁上的應用程式 Linux 設定 Node.js 的應用程式堆疊，就會設定 Node.js 啟動檔案下圖所示的選項。

![][1]

您可以使用以下任一

-   指定啟動指令碼 Node.js 應用程式 (例如︰ /bin/server.js)
-   指定要用於 Node.js 應用程式的 PM2 設定檔案 (例如︰ /foo/process.json)

 >[AZURE.NOTE] 如果您想要修改的特定檔案時自動重新啟動您節點的程序，您必須使用 PM2 設定。 否則您的應用程式不會重新啟動在收到時變更通知的項目，例如 [接續本頁] 分部署應用程式碼變更時。

您可以檢查 Node.js[程序檔案的文件](http://pm2.keymetrics.io/docs/usage/application-declaration/)的所有選項，但以下是您想要使用作為 process.json 檔案的範例

        {
          "name"        : "worker",
          "script"      : "/bin/server.js",
          "instances"   : 1,
          "merge_logs"  : true,
          "log_date_format" : "YYYY-MM-DD HH:mm Z",
          "watch": ["/bin/server.js", "foo.txt"],
          "watch_options": {
            "followSymlinks": true,
            "usePolling"   : true,
            "interval"    : 5
          }
        }

重要事項請注意，在此設定會 

-   「 指令碼 」 屬性會指定您的應用程式開始指令碼。
-   「 執行個體] 屬性會指定節點程序，以啟動執行的個體數目。 如果您執行的在較大的 VM 大小有多核心的應用程式，您會想要充分利用您的資源設定較高的值。
-   [監看式] 陣列指定您要為其變更重新啟動您的節點程序的所有檔案。
-   針對 「 watch_options 」，您目前需要 「 usePolling 」 指定為 true，因為裝載您的應用程式的內容的方式。


## <a name="next-steps"></a>後續步驟 ##

* [什麼是 Linux 上的應用程式服務？](./app-service-linux-intro.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-nodejs-pm2/nodejs-startup-file.png