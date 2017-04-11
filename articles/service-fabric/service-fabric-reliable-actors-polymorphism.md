<properties
   pageTitle="於可靠的動作項目 framework 多型 |Microsoft Azure"
   description="建立階層的.NET 介面和重複使用的功能和 API 定義於可靠的動作項目 framework 類型。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/07/2016"
   ms.author="seanmck"/>

# <a name="polymorphism-in-the-reliable-actors-framework"></a>於可靠的動作項目 framework 多型

可靠的動作項目架構可讓您建立使用許多您在物件導向設計將使用相同技巧的動作項目。 這些方法的其中一個是多型，可讓類型，並連接至繼承自其他一般化父系。 繼承於可靠的動作項目 framework 通常遵循.NET 模型的一些其他限制式。

## <a name="interfaces"></a>介面

可靠的動作項目架構需要您定義至少有一個介面，來實作依您的動作項目類型。 此介面用來產生 proxy 類別用戶端可以使用與您的動作項目。 為每個實作動作項目類型的介面，所有其父系最終衍生自 IActor 介面可以繼承其他介面。 IActor 是動作項目的平台定義基底介面。 因此，傳統多型範例使用圖形可能看起來應該像這樣︰

![介面圖形的動作項目的階層][shapes-interface-hierarchy]


## <a name="types"></a>類型

您也可以建立的動作項目類型，所衍生基底所提供的平台演員類別的階層。 在 [圖形]，您可能需要基底`Shape`類型︰

```csharp
public abstract class Shape : Actor, IShape
{
    public abstract Task<int> GetVerticeCount();

    public abstract Task<double> GetAreaAsync();
}
```

Subtypes 的`Shape`可以覆寫基底的方法。

```csharp
[ActorService(Name = "Circle")]
[StatePersistence(StatePersistence.Persisted)]
public class Circle : Shape, ICircle
{
    public override Task<int> GetVerticeCount()
    {
        return Task.FromResult(0);
    }

    public override async Task<double> GetAreaAsync()
    {
        CircleState state = await this.StateManager.GetStateAsync<CircleState>("circle");

        return Math.PI *
            state.Radius *
            state.Radius;
    }
}
```

請注意`ActorService`動作項目類型的屬性。 這個屬性會告知可靠的動作項目架構應該會自動建立的服務裝載此類型的動作項目。 在某些情況下，您可能會想要建立基本的類型，僅適用於子類型與共用功能來產生具體的動作項目不會使用。 在這些情況下，您應該使用`abstract`關鍵字，指出您將會永遠不會建立該類型為根據的動作項目。


## <a name="next-steps"></a>後續步驟

- 請參閱[可靠的動作項目架構如何利用服務布料的轉印圖樣平台](service-fabric-reliable-actors-platform.md)提供可靠性、 延展性及一致的狀態。
- 深入了解[動作項目生命週期](service-fabric-reliable-actors-lifecycle.md)。

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
