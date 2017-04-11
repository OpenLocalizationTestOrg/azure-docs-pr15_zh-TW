<properties
   pageTitle="在本機監控和診斷服務撰寫 Azure 服務布料的轉印圖樣 |Microsoft Azure"
   description="瞭解如何監控和診斷您在本機開發的電腦上使用 Microsoft Azure 服務布料的轉印圖樣撰寫的服務。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/24/2016"
   ms.author="subramar"/>


# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>監控和診斷在本機電腦開發設定服務


> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
- [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

監控、 偵測、 一般來說，及疑難排解允許繼續進行盡量減少使用者體驗的服務。 監控和診斷非常重要的實際部署的生產環境中。 採用類似的模型的服務開發時，可確保診斷管線運作時您移至生產環境。 服務布料的轉印圖樣可輕鬆服務開發人員可以順暢完美地使用同時單一電腦的本機開發安裝與實際生產叢集安裝的診斷。


## <a name="debugging-service-fabric-java-applications"></a>偵錯服務布料的轉印圖樣 Java 應用程式

Java 應用程式，可供使用[多個記錄架構](http://en.wikipedia.org/wiki/Java_logging_framework)。 由於`java.util.logging`是預設的選項與 JRE，它也可用於[github 中的程式碼範例](http://github.com/Azure-Samples/service-fabric-java-getting-started)。  下列討論說明如何設定`java.util.logging`架構。 
 
使用 java.util.logging 您可以將重新導向您的應用程式記錄記憶體、 輸出資料流時，主控台檔案或通訊端。 針對每個選項，有已經提供架構中的預設處理常式。 您可以建立`app.properties`來設定您的應用程式，來重新導向到本機檔案的所有記錄的檔案處理常式的檔案。 

下列程式碼片段包含範例設定︰ 

```java 
handlers = java.util.logging.FileHandler
 
java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log             
```

資料夾指向`app.properties`必須存在於檔案。 之後`app.properties`建立檔案，您需要也修改您進入點的指令碼`entrypoint.sh`中`<applicationfolder>/<servicePkg>/Code/`設定屬性的資料夾`java.util.logging.config.file`至`app.propertes`檔案。 項目看起來應該像下列程式碼片段︰

```sh 
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```
 
 
此設定會收集旋轉的方式，在記錄中`/tmp/servicefabric/logs/`。 **%U**和**%g**允許建立多個檔案，檔名 mysfapp0.log、 mysfapp1.log，依此類推。 依預設處理常式不明確設定時，是否已登錄主控台處理常式。 其中一個可以檢視系統下 /var/log/syslog 記錄。
 
如需詳細資訊，請參閱[github 中的程式碼範例](http://github.com/Azure-Samples/service-fabric-java-getting-started)。  



## <a name="next-steps"></a>後續步驟
相同追蹤程式碼新增至您的應用程式也會搭配使用的診斷 Azure 叢集上的應用程式。 請參閱下列文章的討論的工具不同的選項，並說明您可以如何將其設定。
* [如何收集與 Azure 診斷記錄](service-fabric-diagnostics-how-to-setup-lad.md)
