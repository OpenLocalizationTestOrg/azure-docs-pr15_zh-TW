<properties
   pageTitle="可靠的服務通知 |Microsoft Azure"
   description="服務布料的轉印圖樣可靠的服務通知的概念性文件"
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
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="mcoskun"/>

# <a name="reliable-services-notifications"></a>可靠的服務通知

通知允許用戶端來追蹤他們感興趣的物件所進行的變更。 兩種類型的物件支援通知︰*可靠的狀態管理員*與*可靠的字典*。

使用通知的常見原因包括︰

- 建置實體化檢視，例如次要索引或彙總篩選的檢視複本的狀態。 範例是排序可靠的字典中的所有索引鍵的索引。
- 傳送監控資料，例如新增最後一小時內的使用者數目。

通知會引發套用作業的一部分。 因此，應該及同步事件不應該包括任何昂貴的作業方式快速處理通知。

## <a name="reliable-state-manager-notifications"></a>可靠狀態管理員通知

可靠狀態管理員提供下列事件的通知︰

- 交易
    - 確認
- 狀態管理員
    - 重建
    - 額外的可靠的狀態
    - 移除的可靠的狀態

可靠狀態管理員追蹤目前 inflight 交易。 只會引發通知的交易狀態變更為認可交易。

可靠狀態管理員保留可靠的狀態，例如可靠的字典和可靠的佇列中的集合。 此集合變更時，可靠狀態管理員會引發通知︰ 可靠的狀態是新增或移除或重建整個集合。
在三種情況下重建可靠的狀態管理員集合︰

- 復原︰ 複本啟動時，其復原先前的狀態從磁碟。 復原的結尾，它會使用**NotifyStateManagerChangedEventArgs**啟動包含的復原可靠的狀態集的事件。
- 完整複本︰ 複本，就可以加入組態設定之前，必須建置。 有時候，這需要可靠狀態管理員的狀態，從主要的複本，可套用至閒置次要複本的完整複本。 可靠狀態管理員次要複本上的使用**NotifyStateManagerChangedEventArgs**啟動事件包含可靠的狀態，取得從主要的複本的設定。
- 還原︰ 中損毀修復狀況，複本的狀態可以從備份還原透過**RestoreAsync**。 在這種情況下，可靠的狀態管理員主要複本上使用**NotifyStateManagerChangedEventArgs**啟動包含可靠的狀態，從備份還原一組的事件。

註冊交易通知，及/或狀態管理員通知，您需要登錄可靠的狀態管理員**TransactionChanged**或**StateManagerChanged**事件。 註冊使用這些事件處理常式的常見位置是您的狀態服務建構函式。 當您註冊建構函式時，您就不會錯過任何所造成的**IReliableStateManager**存留期間變更的通知。

```C#
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

**TransactionChanged**事件處理常式使用**NotifyTransactionChangedEventArgs**提供事件的相關詳細資料。 包含指定的變更類型的動作屬性 (例如， **NotifyTransactionChangedAction.Commit**)。 同時也包含交易屬性所提供的交易變更的參照。

>[AZURE.NOTE] 現在，才會認可交易，會引發**TransactionChanged**事件。 然後，等於**NotifyTransactionChangedAction.Commit**動作。 但以後可能引發事件的其他類型的交易狀態變更。 我們建議您檢查動作並處理事件，只有當您預期的。

以下是範例**TransactionChanged**事件處理常式。

```C#
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

**StateManagerChanged**事件處理常式使用**NotifyStateManagerChangedEventArgs**提供事件的相關詳細資料。
**NotifyStateManagerChangedEventArgs**具有兩個子類別︰ **NotifyStateManagerRebuildEventArgs**和**NotifyStateManagerSingleEntityChangedEventArgs**。
您使用 [動作] 屬性中**NotifyStateManagerChangedEventArgs**將**NotifyStateManagerChangedEventArgs**轉換為正確的子類別︰

- **NotifyStateManagerChangedAction.Rebuild**: **NotifyStateManagerRebuildEventArgs**
- **NotifyStateManagerChangedAction.Add**和**NotifyStateManagerChangedAction.Remove**: **NotifyStateManagerSingleEntityChangedEventArgs**

以下是範例**StateManagerChanged**通知處理常式。

```C#
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStataManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## <a name="reliable-dictionary-notifications"></a>可靠的字典通知

可靠的字典提供下列事件的通知︰

- 重建︰ 稱為時**ReliableDictionary**已復原的狀態，從復原或複製的本機狀態或備份。
- 清除︰ 時，呼叫的**ReliableDictionary**狀態已清除透過**ClearAsync**方法。
- 當項目已新增至**ReliableDictionary**新增︰ 呼叫。
- 更新︰ 稱為時已更新**IReliableDictionary**中的項目。
- 移除︰ 稱為時**IReliableDictionary**中的項目已被刪除。

若要取得可靠的字典通知，您需要登錄**DictionaryChanged**事件處理常式**IReliableDictionary**上。 一般的位置，使用這些事件處理常式登錄位於**ReliableStateManager.StateManagerChanged**新增通知。
註冊**IReliableDictionary**加入**IReliableStateManager**時，可確保您不會錯過電話通知。

```C#
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
            }
        }
    }
}
```

>[AZURE.NOTE] **ProcessStateManagerSingleEntityNotification**是範例方法上述範例中**OnStateManagerChangedHandler**呼叫。

上述的程式碼設定**IReliableNotificationAsyncCallback**介面，以及**DictionaryChanged**。 因為**NotifyDictionaryRebuildEventArgs**包含**IAsyncEnumerable**介面-的需求，以非同步-列舉重建通知，而不是**OnDictionaryChangedHandler** **RebuildNotificationAsyncCallback**透過會引發。

```C#
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

>[AZURE.NOTE] 在上述的程式碼，屬於處理重建通知，第一次維護彙總的狀態，會清除。 因為正在使用新的狀態重建可靠的集合，所有的上一個通知都不相關的。

**DictionaryChanged**事件處理常式使用**NotifyDictionaryChangedEventArgs**提供事件的相關詳細資料。
**NotifyDictionaryChangedEventArgs**有五個子類別。 使用**NotifyDictionaryChangedEventArgs**中的 [動作] 屬性，將**NotifyDictionaryChangedEventArgs**轉換為正確的子類別︰

- **NotifyDictionaryChangedAction.Rebuild**: **NotifyDictionaryRebuildEventArgs**
- **NotifyDictionaryChangedAction.Clear**: **NotifyDictionaryClearEventArgs**
- **NotifyDictionaryChangedAction.Add**和**NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemAddedEventArgs**
- **NotifyDictionaryChangedAction.Update**: **NotifyDictionaryItemUpdatedEventArgs**
- **NotifyDictionaryChangedAction.Remove**: **NotifyDictionaryItemRemovedEventArgs**

```C#
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## <a name="recommendations"></a>建議

- **盡快完成通知事件。
- *請不要*執行任何昂貴的作業 （例如，I/O 作業） 做為同步事件的一部分。
- **核取動作類型，再處理事件。 可能會在未來新增新動作類型。

以下是要記住的一些事項︰

- 通知會引發執行作業的一部分。 例如，還原通知不會啟動還原作業的最後一個步驟。 還原直到處理通知事件時，將無法完成。
- 因為通知會引發套用作業的一部分，用戶端就會看到只在本機已確認作業的通知。 因為作業並保證只是本機已確認 （也就是說，登入），它們可能或可能無法復原未來。
- 取消復原路徑，每個套用作業引發單一通知。 這表示的交易 T1 包含 Create(X) Delete(X)，與 Create(X)，就會出現一個通知的 X，一個用於刪除動作，，一個用於建立一次，建立的順序。
- 包含多個作業的交易，操作會套用在使用者的主要複本上已收到的順序。
- 屬於處理 false 的進度，某些作業可能復原。 通知會引發的這類復原作業，複本的狀態回復穩定的點。 復原通知的重要的差異之一，是有重複的索引鍵的事件會彙總。 例如，如果交易 T1 會被復原，您會看到 Delete(X) 單一通知。

## <a name="next-steps"></a>後續步驟

- [可靠的集合](service-fabric-work-with-reliable-collections.md)
- [可靠的服務快速入門](service-fabric-reliable-services-quick-start.md)
- [可靠的服務備份與還原 （損毀修復）](service-fabric-reliable-services-backup-restore.md)
- [開發人員參考可靠的集合](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
