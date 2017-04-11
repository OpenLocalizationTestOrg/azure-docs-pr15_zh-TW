<properties
   pageTitle="可靠的集合 |Microsoft Azure"
   description="服務布料的轉印圖樣狀態服務可提供可靠的集合，可讓您撰寫高度的可用，可調整和低延遲雲端應用程式。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="masnider,vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="10/18/2016"
   ms.author="mcoskun"/>

# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>在 [Azure 服務布料的轉印圖樣狀態服務可靠的集合簡介

可靠的集合，讓您撰寫高度的可用，可調整和低延遲雲端應用程式，即使您在撰寫單一電腦應用程式。 **Microsoft.ServiceFabric.Data.Collections**命名空間中的類別提供的方塊出集合，其中會自動提供您的狀態高度的一組。 開發人員需要只可靠的集合 Api 的程式，並讓管理複寫與本機狀態可靠的集合。

可靠的集合及其他高可用性技術 （例如意指、 Azure 資料表服務和 Azure 佇列服務） 之間的主要差異為的狀態會保留在本機上服務執行個體中時也進行高度可用。 這表示︰

- 所有讀取本機，而導致低延遲而高處理量讀取。
- 所有寫入都帶來的最小數網路 IOs 導致低延遲和寫入高處理量。

![集合發展的圖像。](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

可靠的集合可以視為**System.Collections**類別的自然發展︰ 一組新的集合，可用於不提高複雜度開發人員的雲端和多電腦應用程式。 因此，是可靠的集合︰

- 複製︰ 高可用性複寫狀態的變更。
- 保留︰ 保留資料會持續性針對大型中斷 （例如，資料中心電源中斷） 的磁碟。
- 非同步︰ Api 是以確保支付 IO 時，並不會被執行緒非同步。
- 讓您輕鬆地管理服務中的多個可靠集合交易︰ Api 利用交易的抽象。

可靠的集合提供強式的一致性保證預設工作，才能更輕鬆地原因的相關應用程式狀態。
加強的一致性，來確保只有在整個交易已登入的複本，包括主要大部分仲裁後，認可完成的交易達成。
若要達成弱一致性，可以在回到用戶端/要求者確認應用程式非同步認可傳回之前。

可靠的集合 Api 是一個集合 （ **System.Collections.Concurrent**命名空間中找到） 的 Api 演變︰

- 非同步︰ 任務，因此傳回，與同時集合] 中，不同的作業複寫，而且保存。
- 不出參數︰ 使用`ConditionalValue<T>`傳回 bool 和而不是出參數的值。 `ConditionalValue<T>`就像`Nullable<T>`但不需要 T 結構。
- 交易︰ 使用交易物件，讓使用者在交易中的多個可靠集合群組採取動作。

現在， **Microsoft.ServiceFabric.Data.Collections**包含兩個集合︰

- [可靠的字典](https://msdn.microsoft.com/library/azure/dn971511.aspx)︰ 表示複寫與交易式非同步金鑰/值組的集合。 類似於**ConcurrentDictionary**，同時索引鍵和值可以是任何類型。
- [可靠的佇列中](https://msdn.microsoft.com/library/azure/dn971527.aspx)︰ 表示複寫與交易式非同步嚴格以先進 (FIFO) 佇列。 類似於**ConcurrentQueue**，值可以是任何類型。

## <a name="isolation-levels"></a>隔離層級
隔離層級定義的交易必須與其他交易所做修改隔離的程度。
有兩種可靠的集合中支援的隔離層級︰

- **可重複讀取**︰ 指定陳述式無法讀取修改但尚未認可其他交易的資料，以及其他任何交易可以修改目前的交易完成之前已讀取，目前的交易的資料。 如需詳細資訊，請參閱[https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx)。
- **快照**︰ 指定閱讀交易中的任何陳述式的資料將會在於交易開始之資料的交易一致的版本。
交易都可以辨識的交易開始之前所認可資料修改。
目前的交易開始後所做的其他交易修改資料不會在目前的交易中執行的陳述式。
結果是存在於交易開始交易的陳述式為收到認可資料的快照。
快照是一致跨可靠的集合。
如需詳細資訊，請參閱[https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx)。

可靠的集合自動選擇要用於讀取一次的交易的建立作業指定根據複本的角色及操作隔離層級。
下列是描繪可靠的字典和佇列中作業隔離層級的預設值的資料表。

| 作業 \ 角色      | 主要          | 次要        |
| --------------------- | :--------------- | :--------------- |
| 單一項目已讀取    | 可重複讀取  | 快照         |
| 列舉 \ 計數   | 快照         | 快照         |

>[AZURE.NOTE] 為單一實體作業的常見範例是`IReliableDictionary.TryGetValueAsync`， `IReliableQueue.TryPeekAsync`。

同時可靠的字典和可靠佇列中支援閱讀您所撰寫。
換句話說，會看到下列讀取屬於相同的交易的交易內任何寫入。

## <a name="locking"></a>鎖定
在 [可靠的集合，所有交易實行兩個階段︰ 交易並不會釋放交易終止中止或認可之前，它所取得鎖定。

可靠的字典使用鎖定所有的單一項目作業的資料列層級。
嚴格的交易 FIFO 屬性並行交換可靠的佇列。
可靠的佇列中使用作業層級的鎖定，允許與一次交易`TryPeekAsync`及/或`TryDequeueAsync`和與一次交易`EnqueueAsync`一次。
請注意，若要保留 FIFO，如果`TryPeekAsync`或`TryDequeueAsync`曾會觀察可靠佇列中是空值，他們也會鎖定`EnqueueAsync`。

撰寫作業都需要專用鎖定。
讀取作業，鎖定取決於幾個因素。
使用快照集隔離任何讀取的作業完成是免費的鎖定。
所有可重複讀取作業，依預設會共用鎖定。
不過，支援可重複讀取任何讀取作業，使用者可以要求更新鎖定，而不是共用鎖定。
更新鎖定是機制的用來防止一般形式的多個交易鎖定稍後可能更新的資源時，會發生非對稱式鎖定。

您可以找到鎖定相容性矩陣下方︰

| 要求 \ 授與 | 無         | 共用       | 更新      | 不含    |
| ----------------- | :----------- | :----------- | :---------- | :----------- |
| 共用            | 沒有衝突  | 沒有衝突  | 衝突    | 衝突     |
| 更新            | 沒有衝突  | 沒有衝突  | 衝突    | 衝突     |
| 不含         | 沒有衝突  | 衝突     | 衝突    | 衝突     |

請注意，在可靠的集合 Api 逾時引數用於死結偵測。
例如，兩個交易 （T1 和 T2） 嘗試讀取和更新版 K1。
可能是形成死結，因為這兩個得到的共用鎖定。
在此情況下，一項或兩項作業會逾時。

請注意，上述死結案例的更新鎖定如何防止死結最佳範例。

## <a name="persistence-model"></a>持續性模型
可靠的狀態管理員和可靠的集合，請遵循稱為記錄並檢查點持續模型。
這是的模型磁碟上登入及套用只在記憶體中的每個狀態變更的位置。
完成狀態本身保存偶 （又名 檢查點）。
優點是差異已將連續僅附加寫入磁碟上以改善效能。

若要進一步瞭解記錄並檢查點模型，現在就讓我們先查看無限磁碟案例。
複製之前，可靠的狀態管理員記錄每一項作業。
這個選項可讓可靠的集合，來套用在記憶體中的作業。
記錄會持續，即使的複本會失敗，必須重新啟動，因為可靠的狀態管理員將其重新執行複本已遺失的所有作業的記錄中有足夠的資訊。
磁碟無限現狀，若要移除不需要的記錄，且可靠的集合需要管理記憶體內狀態。

現在讓我們來看看有限的磁碟案例。
為記錄累積，可靠的狀態管理員會執行磁碟空間。
這種情況發生之前，可靠的狀態管理員必須截斷其記錄的新記錄的空間。
它會要求可靠的集合，來檢查點到磁碟其記憶體中的狀態。
負責可靠的集合保存的狀態為止。
一旦可靠的集合完成其檢查點，可靠的狀態管理員可以捨去的記錄檔以釋放磁碟空間。
如此一來，複本必須重新啟動時，可靠的集合會復原其檢查狀態，並可靠的狀態管理員會復原播放檢查點之後的所有狀態變更。

>[AZURE.NOTE] 檢查點新增另一個值是它可以改善常見的情況下復原效能。
這是因為檢查點包含最新的版本。

## <a name="recommendations"></a>建議

- 請勿修改讀取作業所傳回的自訂類型的物件 (例如`TryPeekAsync`或`TryGetValueAsync`)。 可靠的集合，就像同時集合] 中，傳回物件並不是複本的參考。
- 修改之前執行深層複本自訂類型傳回的物件。 結構和內建類型是傳遞的值，因為您不需要執行這些深層複本。
- 不要使用`TimeSpan.MaxValue`的逾時。 若要偵測死結應逾時。
- 已確認、 中止，或處置之後，請務必使用交易。
- 建立交易範圍之外，請勿使用列舉。
- 無法建立另一個交易`using`陳述式因為它可能會造成死結。
- 執行，請確定您`IComparable<TKey>`實作正確無誤。 系統所需的時間相依性在此合併檢查點。
- 使用更新鎖定閱讀意圖的項目時，更新，以防止特定類別的死結。
- 請考慮使用備份與還原到已損毀修復這項功能。
- 避免混合單一項目作業和多實體作業 (例如`GetCountAsync`， `CreateEnumerableAsync`) 中相同的交易，因為不同的隔離層級。

以下是要記住的一些事項︰

- 預設逾時是 4 秒鐘的可靠的集合 api。 大部分的使用者不應該覆寫此。
- 預設取消權杖`CancellationToken.None`中所有可靠的集合 Api。
- 索引鍵類型參數 (*TKey*) 可靠的字典必須正確實作`GetHashCode()`和`Equals()`。 索引鍵必須是不變。
- 若要達到可用性可靠的集合，每個服務應該要有至少目標和設定大小 3 的最小的複本。
- 讀取操作在次要可能讀取不是已確認仲裁的版本。
這表示可能 false 前進資料讀取從單一的次要版本。
當然，總是穩定從主要的讀取︰ 可以永遠不會 false 進行。

## <a name="next-steps"></a>後續步驟

- [可靠的服務快速入門](service-fabric-reliable-services-quick-start.md)
- [使用可靠的集合](service-fabric-work-with-reliable-collections.md)
- [可靠的服務通知](service-fabric-reliable-services-notifications.md)
- [可靠的服務備份與還原 （損毀修復）](service-fabric-reliable-services-backup-restore.md)
- [可靠狀態管理員設定](service-fabric-reliable-services-configuration.md)
- [服務布料的轉印圖樣 Web API services 快速入門](service-fabric-reliable-services-communication-webapi.md)
- [進階的程式設計模型可靠服務的使用方式](service-fabric-reliable-services-advanced-usage.md)
- [開發人員參考可靠的集合](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
