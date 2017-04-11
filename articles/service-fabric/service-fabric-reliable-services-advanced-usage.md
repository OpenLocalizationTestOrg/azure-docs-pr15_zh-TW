<properties
   pageTitle="進階可靠的服務使用情況的 |Microsoft Azure"
   description="深入了解進階服務使用情況的服務結構可靠在您的服務彈性。"
   services="Service-Fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="advanced-usage-of-the-reliable-services-programming-model"></a>進階的程式設計模型可靠服務的使用方式
Azure 服務布料的轉印圖樣簡化撰寫及管理可靠的狀態和狀態服務。 本指南會討論可靠的服務，以取得更多的彈性並控制與服務的進階用法。 之前閱讀本指南，請先熟悉[可靠的服務程式設計模型](service-fabric-reliable-services-introduction.md)。

設定狀態和狀態服務有兩個主要的項目點，讓使用者程式碼︰

 - `RunAsync`是您的服務代碼用途進入點。
 - `CreateServiceReplicaListeners`與`CreateServiceInstanceListeners`適用於開啟通訊接聽用戶端的要求。
 
如需大部分的服務，這些兩個進入點已足夠。 在某些情況時服務的生命週期更多的控制，其他生命週期的事件會出現。

## <a name="stateless-service-instance-lifecycle"></a>無服務執行個體生命週期

無服務的生命週期是非常簡單。 無服務可以只會開啟、 關閉或中止。 `RunAsync`無服務中執行服務執行個體時已開啟，並取消服務執行個體是關閉或中止。 

雖然`RunAsync`應該要足夠中幾乎所有的情況下，開啟]，關閉，且無狀態服務中的中止事件，也會出現︰

- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)`
  使用此狀態服務執行個體時，稱為 OnOpenAsync。 延伸的服務初始化工作開始這一次。

- `Task OnCloseAsync(CancellationToken)`
  OnCloseAsync 稱為時沒有狀態服務執行個體前往適當會關閉。 正在升級服務的程式碼，因為負載平衡，移動服務執行個體，或暫時性的錯誤偵測到，這可能會發生。 OnCloseAsync 可安全地關閉任何資源、 停止任何背景處理、 完成儲存外部狀態，或關閉 [現有連線。

- `void OnAbort()`
  無服務執行個體正在強制關閉時，會呼叫 OnAbort。 這通常稱為當永久性錯誤偵測到的節點上，或服務布料的轉印圖樣無法確實管理服務執行個體的生命週期，因為內部失敗。

## <a name="stateful-service-replica-lifecycle"></a>設定狀態服務複本生命週期

設定狀態服務複本的生命週期是更複雜比無服務執行個體。 此外開啟、 關閉，並放棄事件，狀態服務複本期經歷角色變更其存留期間。 當狀態服務複本變更角色，`OnChangeRoleAsync`觸發事件︰

- `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)`
  OnChangeRoleAsync 稱為狀態服務複本會變更時的角色，例如主要或次要。 主要的複本會取得寫入狀態 （允許建立和寫入可靠的集合）。 次要複本可以讀取的狀態 （只可以讀取來自可靠的現有集合）。 大部分的工時狀態服務中執行在主要的複本。 唯讀驗證、 報表產生器、 資料採礦或其他唯讀的工作，可以執行第二個的複本。

狀態服務中主要的複本狀態擁有寫入權限，因此通常是服務時執行實際工時。 `RunAsync`只有主要狀態服務複本時，才執行狀態服務中的方法。 `RunAsync`離主要，以及在關閉及中止事件時，變更主要複本的角色時取消方法。 

使用`OnChangeRoleAsync`事件可讓您可執行根據複本角色以及與回應至角色變更的工作。

設定狀態服務也會提供相同的四個週期事件為無狀態的服務，相同的語意與使用情況下︰

- `Task OnOpenAsync(IStatefulServicePartition, CancellationToken)`
- `Task OnCloseAsync(CancellationToken)`
- `void OnAbort()`



## <a name="next-steps"></a>後續步驟
更多進階主題相關服務布料的轉印圖樣，請參閱下列文章︰

- [設定狀態可靠的服務](service-fabric-reliable-services-configuration.md)

- [服務布料的轉印圖樣狀況簡介](service-fabric-health-introduction.md)

- [使用系統的狀況報告的疑難排解](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

- [設定服務與服務布料的轉印圖樣叢集資源管理員](service-fabric-cluster-resource-manager-configure-services.md)
