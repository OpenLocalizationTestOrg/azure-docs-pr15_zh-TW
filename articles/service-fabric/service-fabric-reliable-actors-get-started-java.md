<properties
   pageTitle="開始使用服務布料的轉印圖樣可靠的動作項目 |Microsoft Azure"
   description="本教學課程中會引導您建立、 偵錯，及部署使用服務布料的轉印圖樣可靠的動作項目一個簡單的動作項目型服務的步驟。"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="vturecek"/>

# <a name="getting-started-with-reliable-actors"></a>開始使用可靠的動作項目

> [AZURE.SELECTOR]
- [C# Windows 上](service-fabric-reliable-actors-get-started.md)
- [Java linux](service-fabric-reliable-actors-get-started-java.md)

本文說明 Azure 服務布料的轉印圖樣可靠動作項目中的基本概念，逐步引導您完成建立及部署 java 簡單可靠的動作項目應用程式。

## <a name="installation-and-setup"></a>安裝和設定
在開始之前，請確定您擁有您的電腦上設定服務布料的轉印圖樣的開發環境。
如果您要將其設定，請移至 [[快速入門在 Mac](service-fabric-get-started-mac.md)或[linux 快速入門](service-fabric-get-started-linux.md)。

## <a name="basic-concepts"></a>基本概念
若要開始使用可靠的動作項目，您只需要瞭解幾個基本概念︰

 * **動作項目服務**。 可靠的動作項目被封裝在可靠的服務，可以部署服務布料的轉印圖樣基礎結構。 在指定的服務執行個體中啟動動作項目執行個體。
 
 * **動作項目登錄**。 為可靠的服務，可靠的動作項目服務需要註冊服務布料的轉印圖樣執行階段。 此外，必須註冊的動作項目執行階段動作項目類型。
 
 * **動作項目介面**。 動作項目介面用來定義的動作項目強公用介面。 在可靠的動作項目模型術語中，動作項目介面定義動作項目能夠瞭解的訊息與程序的類型。 動作項目介面使用其他的動作項目和用戶端應用程式 」 」 （非同步） 將訊息傳送至動作項目。 可靠的動作項目可以實作多個介面。
 
 * **ActorProxy 類別**。 用戶端應用程式會使用 ActorProxy 類別叫用的動作項目介面透過公開的方法。 ActorProxy 課程提供兩個重要功能︰
    * 名稱解析︰ 可在叢集 （尋找叢集其裝載的節點） 中找出動作項目。
    * 錯誤處理︰ 可以再試一次方法引動並重新解析，例如需要重新放置到另一個節點叢集動作項目失敗後的 [動作項目位置。

值得，是屬於動作項目介面下列規則︰

- 無法超載動作項目介面方法。
- 方法不得出的動作項目介面，ref 或選擇性參數。
- 不支援一般介面。

## <a name="create-an-actor-service"></a>建立動作項目服務
開始建立新的服務布料的轉印圖樣應用程式。 服務布料的轉印圖樣 SDK Linux 包含 Yeoman 產生器提供臨時平台服務布料的轉印圖樣應用程式與服務狀態。 首先請執行下列 Yeoman 命令︰

```bash
$ yo azuresfjava
```

請依照下列指示建立**可靠的動作項目服務**。 在此教學課程中，名稱 「 HelloWorldActorApplication 」 的應用程式和動作項目 」 HelloWorldActor 」。 將會建立下列臨時平台︰

```bash
HelloWorldActorApplication/
├── build.gradle
├── HelloWorldActor
│   ├── build.gradle
│   ├── settings.gradle
│   └── src
│       └── reliableactor
│           ├── HelloWorldActorHost.java
│           └── HelloWorldActorImpl.java
├── HelloWorldActorApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldActorPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   ├── _readme.txt
│       │   └── Settings.xml
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── HelloWorldActorInterface
│   ├── build.gradle
│   └── src
│       └── reliableactor
│           └── HelloWorldActor.java
├── HelloWorldActorTestClient
│   ├── build.gradle
│   ├── settings.gradle
│   ├── src
│   │   └── reliableactor
│   │       └── test
│   │           └── HelloWorldActorTestClient.java
│   └── testclient.sh
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="reliable-actors-basic-building-blocks"></a>可靠的動作項目基本建置組塊

稍早所述的基本概念轉換成可靠的動作項目服務的基本的建置組塊。

### <a name="actor-interface"></a>動作項目介面

此頁面包含介面定義的動作項目。 這個介面定義的動作項目實作和，通常是合理進行定義的動作項目實作另一個位置，並可以使用多個其他服務或用戶端應用程式共用，讓呼叫動作項目，用戶端共用的動作項目合約。

`HelloWorldActorInterface/src/reliableactor/HelloWorldActor.java`:

```java
public interface HelloWorldActor extends Actor {
    @Readonly   
    CompletableFuture<Integer> getCountAsync();

    CompletableFuture<?> setCountAsync(int count);
}
```

### <a name="actor-service"></a>動作項目服務 
此頁面包含您的動作項目實作和動作項目註冊程式碼。 在動作項目課程實作動作項目介面。 這是您的動作項目進行工作的位置。

`HelloWorldActor/src/reliableactor/HelloWorldActorImpl`:

```java
@ActorServiceAttribute(name = "HelloWorldActor.HelloWorldActorService")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class HelloWorldActorImpl extends ReliableActor implements HelloWorldActor {
    Logger logger = Logger.getLogger(this.getClass().getName());

    protected CompletableFuture<?> onActivateAsync() {
        logger.log(Level.INFO, "onActivateAsync");

        return this.stateManager().tryAddStateAsync("count", 0);
    }

    @Override
    public CompletableFuture<Integer> getCountAsync() {
        logger.log(Level.INFO, "Getting current count value");
        return this.stateManager().getStateAsync("count");
    }

    @Override
    public CompletableFuture<?> setCountAsync(int count) {
        logger.log(Level.INFO, "Setting current count value {0}", count);
        return this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value);
    }
}
```

### <a name="actor-registration"></a>動作項目登錄

動作項目服務必須註冊與服務布料的轉印圖樣執行階段中服務類型。 若要執行您的動作項目執行個體的動作項目服務的順序，您的動作項目類型必須註冊動作項目服務。 `ActorRuntime`註冊方法執行這項工作的動作項目。

`HelloWorldActor/src/reliableactor/HelloWorldActorHost`:

```java
public class HelloWorldActorHost {
    
    public static void main(String[] args) throws Exception {
        
        try {
            ActorRuntime.registerActorAsync(HelloWorldActorImpl.class, (context, actorType) -> new ActorServiceImpl(context, actorType, ()-> new HelloWorldActorImpl()), Duration.ofSeconds(10));

            Thread.sleep(Long.MAX_VALUE);
            
        } catch (Exception e) {
            e.printStackTrace();
            throw e;
        }
    }
}
```

### <a name="test-client"></a>測試用戶端

這是簡易測試用戶端應用程式可執行分別以測試您的動作項目服務的服務布料的轉印圖樣應用程式。 這是一個範例的位置 ActorProxy 可以用來啟動，並與動作項目執行個體進行通訊。 它不會使用您的服務取得部署。

### <a name="the-application"></a>應用程式 

最後，應用程式套件的動作項目服務及任何其他您可能會新增在未來放在一起以供部署的服務。 它所含動作項目服務套件*ApplicationManifest.xml*和位置持有的者。

## <a name="run-the-application"></a>執行應用程式

Yeoman 臨時平台包括 gradle 指令碼來建立應用程式，而且被指令碼部署及取消-部署應用程式。 若要執行應用程式，先建立 gradle 應用程式︰

```bash
$ gradle
```

這會產生，可使用服務布料的轉印圖樣 Azure CLI 部署服務布料的轉印圖樣應用程式套件。 Install.sh 指令碼包含部署應用程式套件的必要 Azure CLI 命令。 只要執行 install.sh 指令碼部署︰

```bask
$ ./install.sh
```
