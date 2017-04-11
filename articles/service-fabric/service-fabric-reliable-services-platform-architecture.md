<properties
   pageTitle="可靠的服務架構 |Microsoft Azure"
   description="設定狀態和狀態服務可靠的服務架構概觀"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/30/2016"
   ms.author="alanwar"/>

# <a name="architecture-for-stateful-and-stateless-reliable-services"></a>如需狀態和狀態可靠的服務架構

Azure 服務布料的轉印圖樣可靠服務可能會有狀態或無。 每一種服務執行中的特定架構。 本文說明這些架構。
請參閱[可靠的服務概觀](service-fabric-reliable-services-introduction.md)有關狀態和狀態服務之間的差異。

## <a name="stateful-reliable-services"></a>設定狀態可靠的服務

### <a name="architecture-of-a-stateful-service"></a>架構的狀態服務
![設定狀態服務的架構圖表](./media/service-fabric-reliable-services-platform-architecture/reliable-stateful-service-architecture.png)

### <a name="stateful-reliable-service"></a>設定狀態可靠的服務

設定狀態可靠的服務可以衍生自 [StatefulService] 或 [StatefulServiceBase 類別。 兩個這些基底類別所提供的服務布料的轉印圖樣。 提供不同等級的支援與抽象狀態服務介面與服務布料的轉印圖樣，並參與服務布料的轉印圖樣叢集內的服務。

StatefulService 衍生自 StatefulServiceBase。 StatefulServiceBase 提供服務的更多的彈性，但需要詳細了解服務布料的轉印圖樣的內部資訊。
撰寫服務使用的 StatefulService 和 StatefulServiceBase 類別的詳細資訊的詳細資訊，請參閱[可靠的服務概觀](service-fabric-reliable-services-introduction.md)與[可靠的服務的進階用法](service-fabric-reliable-services-advanced-usage.md)。

兩種基本類別管理存留時間及服務實作的角色。 服務實作可能會覆寫虛擬方法的其中一個基本類別，如果服務實作有要在這些服務實作生命週期-中執行的工作，或是想要建立通訊接聽程式物件。 請注意雖然服務實作可能實作圖中，公開 ICommunicationListener，自己通訊接聽物件服務布料的轉印圖樣-由實作通訊接聽服務實作使用通訊接聽程式實作服務布料的轉印圖樣。

狀態可靠服務使用可靠的狀態管理員，以善用可靠的集合。 可靠的集合的高度可用的服務，是本機資料結構，一律是可用的圖片，不論服務容錯移轉。 可靠的狀態提供者來執行每一種可靠的集合。
如需有關可靠的集合的詳細資訊，請參閱[可靠的集合概觀](service-fabric-reliable-services-reliable-collections.md)。

### <a name="reliable-state-manager-and-state-providers"></a>可靠的狀態管理員和狀態提供者

可靠的狀態管理員是管理可靠的狀態提供者的物件。 有來建立、 刪除、 列舉，並確保可靠的狀態提供者保存，而且高度可用的功能。 可靠的狀態提供者執行個體代表保存，而且高度可用的資料結構，例如字典或佇列中的執行個體。

每個可靠的狀態提供者公開介面狀態服務所用來與可靠的狀態提供者互動。 IReliableDictionary 會使用，例如可靠的字典，而用 IReliableQueue 介面與可靠佇列介面。 所有可靠的狀態提供者實作 IReliableState 介面。

可靠的狀態管理員擁有一個名為 IReliableStateManager，狀態服務可讓 access 的介面。 透過 IReliableStateManager 傳回可靠的狀態提供者的介面。

可靠的狀態管理員使用的外掛程式架構，讓新類型的可靠的集合可插入動態。

可靠的字典和可靠的佇列中會建立在高效能、 版本差異 store 實作。

### <a name="transactional-replicator"></a>交易複寫

交易複寫元件會負責上執行服務的所有複本的服務 （也就是可靠的狀態管理員和可靠的集合中的狀態） 的狀態是一致。 也可確保狀態會保留在記錄中。 透過私人機制交易複寫可靠的狀態管理員介面。

交易複寫使用網路通訊協定通訊與其他複本的服務執行個體的狀態，讓所有複本都有最新版本的陳述式資訊。

交易複寫使用記錄，以保存陳述式資訊，以便陳述式資訊便會存在著程序或節點當機。 記錄檔的介面是透過私人機制。

### <a name="log"></a>記錄檔

記錄元件提供可寫入，最佳化高效能持續存放區到旋轉或 solid-state 磁碟。  登入的設計是常設的儲存空間 （亦即硬碟） 是本機的執行狀態服務的節點。 這個選項可讓低延遲和高流量與遠端持續存放，不是本機的節點。

記錄檔元件使用多個記錄檔。 有整個節點的共用的記錄檔，也可以提供的最低延遲及最高處理量儲存狀態資料的作為所有複本。 預設共用的記錄會放在服務布料的轉印圖樣節點公司目錄，但也可能會設定為放置在另一個位置，最好是在共用的記錄保留磁碟上。 每個服務的複本也有專用的記錄檔，而且專用的記錄放在該服務的公司目錄。 沒有設定專用的記錄放置在不同的位置機制。

共用的記錄時，複本的狀態資訊，可轉換區域專用的記錄檔的目的地保存的位置。 在這種設計，是第一次寫入共用的記錄檔陳述式資訊，並將其然後延遲移到背景中的專用的記錄檔中。 如此一來，最低的延遲，讓服務進行更快的最高處理量有寫入共用的記錄。

讀取和寫入共用記錄完成透過直接 IO 預先配置共用的記錄檔磁碟空間。 若要允許的空間的磁碟機的專用的記錄檔的最佳使用，請在為 NTFS 疏鬆檔案建立專用的記錄檔。 請注意，這可讓 overprovisioning 磁碟空間 OS 會顯示使用更多的磁碟空間實際所用的專用的記錄檔。

除了登入的最小的使用者模式介面，以核心驅動程式寫入記錄檔。 為核心驅動程式執行，在記錄檔可以提供使用的所有服務最高的效能。

如需有關如何設定記錄的詳細資訊，請參閱[設定狀態可靠的服務](service-fabric-reliable-services-configuration.md)。

## <a name="stateless-reliable-service"></a>無可靠的服務

### <a name="architecture-of-a-stateless-service"></a>無服務的架構
![無服務的架構圖表](./media/service-fabric-reliable-services-platform-architecture/reliable-stateless-service-architecture.png)

### <a name="stateless-reliable-service"></a>無可靠的服務

無服務實作衍生自 StatelessService 或 StatelessServiceBase 類別。 StatelessServiceBase 類別可讓更多的彈性，比 StatelessService 類別。
兩種基本類別管理生命週期與服務的角色。

服務實作可能會覆寫虛擬方法的其中一個基本類別，如果服務要在服務生命週期-中執行的工作，或要建立通訊接聽程式物件。 請注意，雖然服務可能實作圖中，公開 ICommunicationListener，自己通訊接聽物件通訊接聽實作服務布料的轉印圖樣，該服務的實作使用通訊接聽程式實作服務布料的轉印圖樣。

撰寫服務使用的 StatelessService 和 StatelessServiceBase 類別的詳細資訊的詳細資訊，請參閱[可靠的服務概觀](service-fabric-reliable-services-introduction.md)與[可靠的服務的進階用法](service-fabric-reliable-services-advanced-usage.md)。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>後續步驟

如需有關服務布料的轉印圖樣的詳細資訊，請參閱︰

[可靠的服務概觀](service-fabric-reliable-services-introduction.md)

[快速入門](service-fabric-reliable-services-quick-start.md)

[可靠的集合概觀](service-fabric-reliable-services-reliable-collections.md)

[進階用法可靠的服務](service-fabric-reliable-services-advanced-usage.md)

[可靠的服務設定](service-fabric-reliable-services-configuration.md)  
