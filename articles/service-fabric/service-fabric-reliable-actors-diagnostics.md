<properties
   pageTitle="動作項目診斷和監視 |Microsoft Azure"
   description="本文將說明診斷和效能監視服務布料的轉印圖樣可靠的動作項目執行階段，包括事件和效能計數器它所發出的功能。"
   services="service-fabric"
   documentationCenter=".net"
   authors="abhishekram"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/05/2016"
   ms.author="abhisram"/>

# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>診斷和效能監視可靠的動作項目
可靠的動作項目執行階段發出[EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx)事件和[效能計數器](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx)。 這些提供見解執行階段如何運作，協助疑難排解並監控效能。

## <a name="eventsource-events"></a>EventSource 事件
可靠的動作項目執行階段 EventSource 提供者名稱為 「 Microsoft-ServiceFabric-動作項目]。 時的動作項目應用程式[在 Visual Studio 偵錯](service-fabric-debugging-your-application.md)，此事件來源的事件會出現在 [[診斷程式事件](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio)] 視窗。

工具和技術，協助來收集及/或檢視 EventSource 事件的範例是[PerfView](http://www.microsoft.com/download/details.aspx?id=28567)、 [Azure 診斷](../cloud-services/cloud-services-dotnet-diagnostics.md)、[語意記錄](https://msdn.microsoft.com/library/dn774980.aspx)，以及[Microsoft TraceEvent 文件庫](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent)。

### <a name="keywords"></a>關鍵字
屬於可靠的動作項目 EventSource 的所有事件一或多個關鍵字都是相關聯。 這可讓篩選所收集到的事件。 關鍵字位元的定義。

|位元|描述|
|---|---|
|0x1|設定的摘要布料的轉印圖樣動作項目執行階段的作業的重要事件。|
|0x2|組的描述動作項目方法呼叫的事件。 如需詳細資訊，請參閱[簡介主題︰ 在 [動作項目](service-fabric-reliable-actors-introduction.md#actors)。|
|0x4|動作項目狀態的相關事件的集合。 如需詳細資訊，請參閱主題的[動作項目狀態管理](service-fabric-reliable-actors-state-management.md)。|
|0x8|組相關動作項目中的 [開啟型並行的事件。 如需詳細資訊，請參閱在[並行](service-fabric-reliable-actors-introduction.md#concurrency)的主題。|

## <a name="performance-counters"></a>效能計數器
可靠的動作項目執行階段定義下列效能計數器類別。

|類別|描述|
|---|---|
|服務布料的轉印圖樣動作項目|計數器 Azure 服務布料的轉印圖樣動作項目，例如時間移至儲存動作項目狀態|
|服務布料的轉印圖樣動作項目方法|計數器特定實作服務布料的轉印圖樣動作項目的方法，例如頻率動作項目叫用的方法|

每個以上的類別都有一或多個計數器。

為 Windows 作業系統中的預設[Windows 效能監視器](https://technet.microsoft.com/library/cc749249.aspx)應用程式可用來收集及檢視效能計數器資料。 [Azure 診斷](../cloud-services/cloud-services-dotnet-diagnostics.md)是用來收集效能計數器資料，並將它上傳至 Azure 資料表的另一個選項。

### <a name="performance-counter-instance-names"></a>效能計數器執行個體名稱
含有大量動作項目服務或動作項目服務分割叢集會有大量的動作項目效能計數器執行個體。 找出特定的[磁碟分割](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors)和動作項目方法 （如果適用的話） 與相關聯的效能計數器執行個體，可協助效能計數器執行個體名稱。

#### <a name="service-fabric-actor-category"></a>服務布料的轉印圖樣動作項目分類
類別`Service Fabric Actor`，計數器執行個體名稱會以下列格式︰

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID*是效能計數器執行個體相關聯的服務布料的轉印圖樣的磁碟分割識別碼的字串表示。 分割識別碼 GUID，並透過產生它的字串表示[`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx)以格式規範"D"方法。

*ActorRuntimeInternalID*是 64 位元整數其內部用於布料的轉印圖樣動作項目執行階段所產生的字串表示。 這會包含在效能計數器執行個體名稱，以確保其唯一性並避免其他效能計數器執行個體名稱衝突。 使用者不應該嘗試解譯此部分效能計數器執行個體名稱。

下列是屬於計數器計數器執行個體名稱的範例`Service Fabric Actor`類別︰

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

在上述範例`2740af29-78aa-44bc-a20b-7e60fb783264`是服務布料的轉印圖樣分割區識別碼的字串表示和`635650083799324046`，就會產生的執行階段的內部的 64 位元識別碼使用。

#### <a name="service-fabric-actor-method-category"></a>服務布料的轉印圖樣動作項目方法類別
類別`Service Fabric Actor Method`，計數器執行個體名稱會以下列格式︰

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName*是效能計數器執行個體相關聯的動作項目方法名稱。 根據一些布料的轉印圖樣動作項目執行階段的名稱與效能計數器執行個體名稱的最大長度的限制式，在 Windows 上的可讀性中的邏輯決定方法名稱的格式。

*ActorsRuntimeMethodId*是 32 位元整數其內部用於布料的轉印圖樣動作項目執行階段所產生的字串表示。 這會包含在效能計數器執行個體名稱，以確保其唯一性並避免其他效能計數器執行個體名稱衝突。 使用者不應該嘗試解譯此部分效能計數器執行個體名稱。

*ServiceFabricPartitionID*是效能計數器執行個體相關聯的服務布料的轉印圖樣的磁碟分割識別碼的字串表示。 分割識別碼 GUID，並透過產生它的字串表示[`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx)以格式規範"D"方法。

*ActorRuntimeInternalID*是 64 位元整數其內部用於布料的轉印圖樣動作項目執行階段所產生的字串表示。 這會包含在效能計數器執行個體名稱，以確保其唯一性並避免其他效能計數器執行個體名稱衝突。 使用者不應該嘗試解譯此部分效能計數器執行個體名稱。

下列是屬於計數器計數器執行個體名稱的範例`Service Fabric Actor Method`類別︰

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

在上述範例`ivoicemailboxactor.leavemessageasync`是方法名稱、`2`是 32 位元識別碼產生用於執行階段的內部，`89383d32-e57e-4a9b-a6ad-57c6792aa521`是服務布料的轉印圖樣分割區識別碼的字串表示和`635650083804480486`產生的執行階段的內部的 64 位元識別碼使用。

## <a name="list-of-events-and-performance-counters"></a>事件和效能計數器的清單

### <a name="actor-method-events-and-performance-counters"></a>動作項目方法事件和效能計數器
可靠的動作項目執行階段會發出下列事件相關[動作項目方法](service-fabric-reliable-actors-introduction.md#actors)。

|事件名稱|事件識別碼|層級|關鍵字|描述|
|---|---|---|---|---|
|ActorMethodStart|7|詳細資訊|0x2|動作項目執行階段即將叫用動作項目方法。|
|ActorMethodStop|8|詳細資訊|0x2|執行的動作項目方法已完成執行。 也就是說，傳回執行階段的非同步呼叫動作項目方法，並完成動作項目方法所傳回的工作。|
|ActorMethodThrewException|9|警告|0x3|例外狀況執行的動作項目方法，在執行階段的動作項目方法非同步通話期間，或執行工作期間所傳回的動作項目方法。 此事件表示某種需要調查的動作項目程式碼中的錯誤。|

可靠的動作項目執行階段發佈下列相關動作項目方法的執行效能計數器。

|類別名稱|[計數器名稱|描述|
|---|---|---|
|服務布料的轉印圖樣動作項目方法|引動秒|動作項目服務方法叫秒的次數|
|服務布料的轉印圖樣動作項目方法|每個引動平均毫秒為單位|若要執行的動作項目服務方法以毫秒為單位的時間|
|服務布料的轉印圖樣動作項目方法|例外狀況秒|動作項目服務方法次數發生每第二個例外狀況|

### <a name="concurrency-events-and-performance-counters"></a>並行事件和效能計數器
可靠的動作項目執行階段發出[並行](service-fabric-reliable-actors-introduction.md#concurrency)相關事件。

|事件名稱|事件識別碼|層級|關鍵字|描述|
|---|---|---|---|---|
|ActorMethodCallsWaitingForLock|12|詳細資訊|0x8|此事件寫入的動作項目中的每個新開啟開頭。 它包含等候等待取得每個動作項目鎖定的開啟型並行會強制執行的動作項目通話的數目。|

可靠的動作項目執行階段發佈下列效能計數器相關並行。

|類別名稱|[計數器名稱|描述|
|---|---|---|
|服務布料的轉印圖樣動作項目|# 動作項目呼叫等待動作項目鎖定|擱置等候取得每個動作項目鎖定開啟型並行會強制執行的動作項目呼叫數目|
|服務布料的轉印圖樣動作項目|每個鎖定平均毫秒為單位，請等候|取得每個動作項目鎖定強制關閉型並行 （以毫秒計） 所需的時間|
|服務布料的轉印圖樣動作項目|保留平均毫秒動作項目鎖定|時間 （以毫秒為單位），在每個動作項目鎖定|

### <a name="actor-state-management-events-and-performance-counters"></a>動作項目狀態管理事件和效能計數器
可靠的動作項目執行階段會發出相關[動作項目狀態管理](service-fabric-reliable-actors-state-management.md)下列事件。

|事件名稱|事件識別碼|層級|關鍵字|描述|
|---|---|---|---|---|
|ActorSaveStateStart|10|詳細資訊|0x4|動作項目執行階段即將儲存的動作項目狀態。|
|ActorSaveStateStop|11|詳細資訊|0x4|動作項目執行階段完成儲存動作項目狀態。|

可靠的動作項目執行階段發佈相關動作項目狀態管理下列效能計數器。

|類別名稱|[計數器名稱|描述|
|---|---|---|
|服務布料的轉印圖樣動作項目|每個平均毫秒儲存狀態作業|若要儲存以毫秒為單位的動作項目狀態所需的時間|
|服務布料的轉印圖樣動作項目|每個載入狀態作業的平均毫秒|載入以毫秒為單位的動作項目狀態所需的時間|

### <a name="events-related-to-actor-replicas"></a>相關動作項目複本的事件
可靠的動作項目執行階段會發出下列事件相關[動作項目複本](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-stateful-actors)。

|事件名稱|事件識別碼|層級|關鍵字|描述|
|---|---|---|---|---|
|ReplicaChangeRoleToPrimary|1|資訊|0x1|動作項目複本會變更為主要的角色。 這表示此磁碟分割的動作項目將會建立在此複本。|
|ReplicaChangeRoleFromPrimary|2|資訊|0x1|動作項目複本會變更為非主要的角色。 這表示此磁碟分割的動作項目不會再建立內這個複本。 沒有新的邀請會傳遞給已經建立這個複本中的動作項目。 進行中的任何要求完成後，會損毀的動作項目。|

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>啟動和停用事件的動作項目和效能計數器
可靠的動作項目執行階段會發出下列事件相關[動作項目啟動和停用](service-fabric-reliable-actors-lifecycle.md)。

|事件名稱|事件識別碼|層級|關鍵字|描述|
|---|---|---|---|---|
|ActorActivated|5|資訊|0x1|已啟動動作項目。|
|ActorDeactivated|6|資訊|0x1|動作項目已停用。|

可靠的動作項目執行階段發佈下列效能計數器相關動作項目啟動和停用。

|類別名稱|[計數器名稱|描述|
|---|---|---|
|服務布料的轉印圖樣動作項目|平均 OnActivateAsync 毫秒為單位|若要執行以毫秒為單位的 OnActivateAsync 方法所需的時間|

### <a name="actor-request-processing-performance-counters"></a>動作項目要求處理效能計數器
當用戶端叫用透過動作項目 proxy 物件的方法時，它會產生邀請郵件傳送到網路動作項目服務。 服務處理要求的訊息，並傳送用戶端的回應。 可靠的動作項目執行階段發佈下列效能計數器相關動作項目要求處理。

|類別名稱|[計數器名稱|描述|
|---|---|---|
|服務布料的轉印圖樣動作項目|# 未完成要求的|在 service 服務正在處理的要求的數字|
|服務布料的轉印圖樣動作項目|每個要求的平均毫秒|所需的時間 （以毫秒計） 服務要求處理程序|
|服務布料的轉印圖樣動作項目|要求還原序列化平均毫秒為單位|在服務接收時還原序列化動作項目要求訊息 （以毫秒計） 所需的時間|
|服務布料的轉印圖樣動作項目|回應序列化平均毫秒為單位|用戶端傳送回覆前序列化服務上的動作項目回覆郵件 （以毫秒計） 所需的時間|

## <a name="next-steps"></a>後續步驟
 - [如何可靠的動作項目使用的服務布料的轉印圖樣平台](service-fabric-reliable-actors-platform.md)
 - [動作項目 API 參考文件](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [程式碼範例](https://github.com/Azure/servicefabric-samples)
