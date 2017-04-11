<properties
   pageTitle="在 [動作項目上可靠的動作項目筆記輸入序列化 |Microsoft Azure"
   description="說明用於定義序列化的類別，可以用來定義服務布料的轉印圖樣可靠的動作項目狀態和介面的基本需求"
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
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>服務布料的轉印圖樣可靠的動作項目上的筆記輸入序列化


所有方法，所傳回的動作項目介面，在每一種方法的工作和動作項目狀態管理員儲存物件的結果類型的引數必須是[資料合約序列化](https://msdn.microsoft.com/library/ms731923.aspx). 這也適用於引數的[動作項目事件介面](service-fabric-reliable-actors-events.md#actor-events)中定義的方法。 （動作項目事件介面方法一定會傳回 void。）

## <a name="custom-data-types"></a>自訂資料類型

在此範例中，以下的動作項目介面定義會傳回稱為自訂資料類型的方法`VoicemailBox`。

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

介面是依演員，用來儲存狀態管理員 impelemented`VoicemailBox`物件︰

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

在此範例中，`VoicemailBox`物件序列化時︰
 - 動作項目執行個體與來電者之間傳送物件。
 - 物件會儲存在 [狀態管理員它是保存在磁碟及複寫到其他節點的位置。
 
可靠的動作項目架構使用 DataContract 序列化。 因此，自訂資料物件和其成員必須使用加註**DataContract**和**DataMember**屬性分別

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```

```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```

## <a name="next-steps"></a>後續步驟
 - [動作項目生命週期與回收集合](service-fabric-reliable-actors-lifecycle.md)
 - [動作項目計時器及提醒](service-fabric-reliable-actors-timers-reminders.md)
 - [動作項目事件](service-fabric-reliable-actors-events.md)
 - [動作項目重新進入](service-fabric-reliable-actors-reentrancy.md)
 - [動作項目多型和物件導向設計模式](service-fabric-reliable-actors-polymorphism.md)
 - [動作項目診斷和效能監視](service-fabric-reliable-actors-diagnostics.md)
