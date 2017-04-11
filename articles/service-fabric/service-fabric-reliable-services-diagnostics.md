<properties
   pageTitle="設定狀態可靠的服務診斷 |Microsoft Azure"
   description="診斷功能狀態可靠的服務"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/17/2016"
   ms.author="alanwar"/>

# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>診斷功能狀態可靠的服務
狀態可靠的服務 StatefulServiceBase 課程會發出可以用來偵錯服務，提供將執行階段如何運作，並協助疑難排解的深入見解的[EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx)事件。

## <a name="eventsource-events"></a>EventSource 事件
狀態可靠的服務 StatefulServiceBase 課程 EventSource 名稱為 「 Microsoft-ServiceFabric-服務 」。 服務正在[Visual Studio 中偵錯](service-fabric-debugging-your-application.md)時，此事件來源的事件會出現在 [[診斷程式事件](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio)] 視窗。

工具和技術，協助來收集及/或檢視 EventSource 事件的範例是[PerfView](http://www.microsoft.com/download/details.aspx?id=28567)、 [Microsoft Azure 診斷](../cloud-services/cloud-services-dotnet-diagnostics.md)，與[Microsoft TraceEvent 文件庫](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent)。

## <a name="events"></a>事件

|事件名稱|事件識別碼|層級|事件的描述|
|----------|--------|-----|-----------------|
|StatefulRunAsyncInvocation|1|資訊|當您啟動服務 RunAsync 工作時發出|
|StatefulRunAsyncCancellation|2|資訊|當已取消服務 RunAsync 工作時發出|
|StatefulRunAsyncCompletion|3|資訊|當服務 RunAsync 工作完成時發出|
|StatefulRunAsyncSlowCancellation|4|警告|當服務 RunAsync 工作的時間太長，完成取消時發出|
|StatefulRunAsyncFailure|5|錯誤|發出時服務 RunAsync 工作擲回例外狀況|

## <a name="interpret-events"></a>解譯事件

StatefulRunAsyncInvocation、 StatefulRunAsyncCompletion 和 StatefulRunAsyncCancellation 事件有用處服務撰寫者，若要瞭解的服務，以及服務時啟動、 取消，或已完成的時間週期。 這會有幫助何時偵錯的服務問題，或瞭解服務生命週期。

服務作者應該請注意 StatefulRunAsyncSlowCancellation 和 StatefulRunAsyncFailure 事件，因為它們指示與服務的問題。

每當服務 RunAsync() 任務會產生例外狀況時，會發出 StatefulRunAsyncFailure。 一般而言，例外狀況指出錯誤或服務中的錯誤。 此外，例外狀況會使服務失敗，讓它會移到不同的節點。 這可能是寶貴的作業，服務會移動時，可以延遲傳入的邀請。 服務作者應該例外狀況的原因，如果可能的話，將其減輕。

取消要求 RunAsync 任務所需的時間超過四秒時，會發出 StatefulRunAsyncSlowCancellation。 當服務太長而完成取消時，它會影響到快速重新啟動另一個節點服務的能力。 這可能會影響整體可用的服務。
