<properties
   pageTitle="可測試性︰ 服務通訊 |Microsoft Azure"
   description="服務-服務通訊是服務布料的轉印圖樣應用程式的要徑整合點。 本文討論設計考量和測試技巧。"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/06/2016"
   ms.author="vturecek"/>

# <a name="service-fabric-testability-scenarios-service-communication"></a>服務布料的轉印圖樣可測試性案例︰ 服務通訊

Microservices 和服務導向架構樣式曲面圖自然中 Azure 服務布料的轉印圖樣。 在這種分散式架構，通常被組成需要彼此的多個服務的元件化的 microservice 應用程式。 在偶數最簡單的情況下，您通常需要至少無 web 服務及必須進行溝通狀態的資料儲存服務。

服務-服務通訊是應用程式的要徑整合點，因為每個服務公開遠端 API 其他服務。 使用一組 API 界限，通常涉及 I/O 小心，以良好少的測試和驗證。

有許多這些服務界限分散式系統連接在一起時的考量︰

 - *傳輸通訊協定*。 您要使用 HTTP 提高互通性或自訂的二進位通訊協定的最大處理量？
 - *錯誤處理*。 如何處理永久或暫時錯誤？ 移至不同的節點的服務時，會發生什麼事？
 - *逾時與延遲*。 多層應用程式中，如何將每個服務層處理延遲透過堆疊和使用者？

無論您使用的服務布料的轉印圖樣所提供的內建的服務通訊元件，或您建立您自己，測試您的服務之間的互動是確保恢復應用程式中的關鍵。

## <a name="prepare-for-services-to-move"></a>準備要移動的服務

服務執行個體可能會在一段時間內移動。 這些設定的自訂量身打造的最佳資源平衡的載入指標使用時，這是如此。 服務布料的轉印圖樣移動您的服務執行個體，即使在升級與容錯移轉、 向外，其他分散式系統的存留期間發生的情況下最大的可用性。

當在叢集移動服務，用戶端與其他服務還需要準備時，與服務處理兩種案例︰

- 最後一次您談到它移動服務執行個體或磁碟分割複本。 這是標準的組件的服務生命週期與應該會在您的應用程式的生命週期發生。
- 服務執行個體或磁碟分割複本正在移動。 雖然發生容錯移轉到另一個節點服務的快速服務布料的轉印圖樣中可能有延遲可用性如果您的服務通訊元件速度很慢開始。

適當地處理這些案例務必平滑執行系統。 若要這麼做，請記住:

- 可連線至每個服務有其接聽 （例如，HTTP 或 WebSockets） 的*地址*。 時的服務執行個體或磁碟分割移動時，其位址的結束點就會變更。 （會移至不同的 IP 位址的另一個節點）。如果您使用內建的通訊元件，他們會為您處理重新解決服務的地址。
- 可能會增加為其接聽程式服務會執行個體啟動的服務延遲的暫時一次。 移動服務執行個體後服務快速開啟接聽程式而定。
- 任何現有的連線，就必須關閉並重新開啟後服務會開啟新的節點。 正常節點關機] 或 [重新啟動可以讓現有的連線，正常關閉時間。

### <a name="test-it-move-service-instances"></a>測試︰ 移動服務執行個體

藉由使用服務布料的轉印圖樣的可測試性工具，您可以撰寫測試案例進行測試這些情況下，以不同方式︰

1. 移動狀態服務的主要複本。

    設定狀態服務磁碟分割的主要複本可以移動的任何數字的原因。 使用此選項目標若要查看如何服務回應移動中控制特定的磁碟分割的主要複本。

    ```powershell

    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService

    ```

2. 停止節點。

    停止節點時，服務布料的轉印圖樣移的所有服務執行個體或磁碟分割區已在其中一個叢集其他可用的節點的節點。 使用此選項測試位置節點遺失叢集及所有服務執行個體，而該節點的複本有若要移動的情形。

    您可以使用 PowerShell**停駐點 ServiceFabricNode** cmdlet，以避免節點︰

    ```powershell

    PS > Restart-ServiceFabricNode -NodeName Node_1

    ```

## <a name="maintain-service-availability"></a>維護服務可用性

做為平台，服務布料的轉印圖樣是設計用來提供服務的可用性。 但非常的情況下，在基礎基礎結構的問題仍會導致無法使用。 請務必也測試這些情況下。

設定狀態服務使用仲裁型系統複寫高可用性狀態。 這表示複本的仲裁需求可用於執行寫入操作。 在某些情況中廣泛的硬體失敗，例如複本的仲裁可能無法使用。 在下列情況下，您將無法執行寫入操作，但您仍然可以執行讀取的作業。

### <a name="test-it-write-operation-unavailability"></a>測試︰ 撰寫作業無法使用

使用服務布料的轉印圖樣中可測試性工具，您可以插入引發做為測試仲裁遺失的錯誤。 雖然這種狀況很少，但請務必準備用戶端和服務的狀態的服務而定，處理，它們無法成為要求寫入的情況。 請務必也有狀態服務本身留意這個可能性，並可以順利地傳達給呼叫者。

您可以使用 PowerShell**叫用 ServiceFabricPartitionQuorumLoss**指令程式來產生仲裁影響︰

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

此範例中，設定`QuorumLossMode`至`QuorumReplicas`表示我們要產生仲裁遺失不需關閉所有的複本。 如此一來，還是有可能是讀取的作業。 若要測試整個的磁碟分割無法使用案例，您可以將此參數為`AllReplicas`。

## <a name="next-steps"></a>後續步驟

[進一步瞭解可測試性動作](service-fabric-testability-actions.md)

[進一步瞭解可測試性案例](service-fabric-testability-scenarios.md)
