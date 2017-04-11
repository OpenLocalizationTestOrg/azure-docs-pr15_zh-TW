<properties
   pageTitle="可靠的動作項目事件 |Microsoft Azure"
   description="事件的簡介服務布料的轉印圖樣可靠的動作項目。"
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
   ms.date="08/30/2016"
   ms.author="amanbha"/>


# <a name="actor-events"></a>動作項目事件
動作項目事件提供最佳通知傳送的動作項目，用戶端的方法。 動作項目事件專為動作項目用戶端的通訊和不應該用於動作項目至動作項目通訊。

下列程式碼片段顯示如何在您的應用程式中使用動作項目事件。

定義說明發佈動作項目事件介面。 這個介面必須衍生自`IActorEvents`介面。 其中一種方法的引數必須是[資料合約序列化](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)。 方法必須傳回 void，為事件通知是一種方式與最佳效果。

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```

宣告發佈動作項目動作項目介面中的事件。

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```

在用戶端，實作事件處理常式。

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

在用戶端，建立發佈事件的動作項目至 proxy 和訂閱的事件。

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

發生錯誤後移轉，動作項目可能無法在不同的程序或節點。 動作項目 proxy 管理作用中的訂閱和自動重新訂閱。 您可以控制重新訂閱間隔透過`ActorProxyEventExtensions.SubscribeAsync<TEvent>`API。 若要取消訂閱，使用`ActorProxyEventExtensions.UnsubscribeAsync<TEvent>`API。

在 [動作項目，只要發佈事件發生。 如果您有訂閱者事件，請動作項目執行階段會傳送給他們的通知。

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```

## <a name="next-steps"></a>後續步驟
 - [動作項目重新進入](service-fabric-reliable-actors-reentrancy.md)
 - [動作項目診斷和效能監視](service-fabric-reliable-actors-diagnostics.md)
 - [動作項目 API 參考文件](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [程式碼範例](https://github.com/Azure/servicefabric-samples)
