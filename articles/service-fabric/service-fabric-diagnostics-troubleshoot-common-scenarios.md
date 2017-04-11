<properties
   pageTitle="疑難排解與事件追蹤 |Microsoft Azure"
   description="最常見的問題時發生部署上 Microsoft Azure 服務布料的轉印圖樣的服務。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mattrowmsft"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/31/2016"
   ms.author="mattrow"/>


# <a name="troubleshoot-common-issues-when-you-deploy-services-on-azure-service-fabric"></a>當您部署上 Azure 服務布料的轉印圖樣的服務疑難排解常見的問題

當您執行的在 [開發人員電腦上的服務時，很容易使用[Visual Studio 偵錯工具](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)。 遠端叢集的[狀況報告](service-fabric-view-entities-aggregated-health.md)一律是開始的絕佳位置。 若要存取這些報表最簡單的方法是透過 PowerShell 或[SFX](service-fabric-visualizing-your-cluster.md)。 本文假設您偵錯遠端叢集，並了解如何使用這些工具其中一項基本。

##<a name="application-crash"></a>應用程式當機
「 磁碟分割低於目標複本或執行個體計數 」 報表可清楚指出您的服務會損毀的。 若要找出您的服務會損毀的位置會稍有更多的調查。 在執行您的服務時，最好的朋友會確實擬出追蹤一組。  我們建議您在收集這些追蹤和使用例如[彈性的搜尋](service-fabric-diagnostic-how-to-use-elasticsearch.md)解決方案的檢視及搜尋追蹤試著[Azure 診斷](service-fabric-diagnostics-how-to-setup-wad.md)。

![SFX 分割狀況](./media/service-fabric-diagnostics-troubleshoot-common-scenarios/crashNewApp.png)

###<a name="during-service-or-actor-initialization"></a>服務或動作項目初始化期間
服務類型初始化之前的任何例外狀況會造成損毀的程序。 針對這些類型的當機中，應用程式的事件記錄檔便會顯示您的服務錯誤。
這些是最常見的例外狀況，若要查看服務初始化之前。

***System.IO.FileNotFoundException***

此錯誤通常是因為缺少組件相依性。 核取 [在 Visual Studio 或全域快取節點 CopyLocal 屬性]。

***System.Runtime.InteropServices.COMException***
 *在 System.Fabric.Interop.NativeRuntime+IFabricRuntime.RegisterStatefulServiceFactory （便、 IFabricStatefulServiceFactory）*
 
 指出已註冊的服務類型名稱不相符的服務資訊清單。

[Azure 診斷](service-fabric-diagnostics-how-to-setup-wad.md)可以設定成自動上傳應用程式的事件記錄檔，您所有的節點。

###<a name="runasync-or-onactivateasync"></a>RunAsync() 或 OnActivateAsync()
如果發生初始化當機或執行您已註冊的服務類型或動作項目，例外狀況攔截到 Azure 服務布料的轉印圖樣。 您可以檢視這些提供者 EventSource 」 下一個步驟 > 一節所述。

## <a name="next-steps"></a>後續步驟

深入瞭解服務布料的轉印圖樣所提供的現有診斷︰

* [可靠的動作項目診斷程式](service-fabric-reliable-actors-diagnostics.md)
* [可靠的服務診斷程式](service-fabric-reliable-services-diagnostics.md)
