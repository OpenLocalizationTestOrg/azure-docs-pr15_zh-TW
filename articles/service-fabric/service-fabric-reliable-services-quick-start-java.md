<properties
   pageTitle="開始使用可靠的服務 |Microsoft Azure"
   description="若要建立的 Microsoft Azure 服務布料的轉印圖樣應用程式沒有狀態和狀態服務簡介。"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/26/2016"
   ms.author="vturecek"/>

# <a name="get-started-with-reliable-services"></a>開始使用可靠的服務

> [AZURE.SELECTOR]
- [C# Windows 上](service-fabric-reliable-services-quick-start.md)
- [Java linux](service-fabric-reliable-services-quick-start-java.md)

本文說明 Azure 服務布料的轉印圖樣可靠服務的基本概念，逐步引導您完成建立及部署 java 撰寫的簡單可靠的服務應用程式。

## <a name="installation-and-setup"></a>安裝和設定
在開始之前，請確定您擁有您的電腦上設定服務布料的轉印圖樣的開發環境。
如果您要將其設定，請移至 [[快速入門在 Mac](service-fabric-get-started-mac.md)或[linux 快速入門](service-fabric-get-started-linux.md)。

## <a name="basic-concepts"></a>基本概念
若要開始使用可靠的服務，您只需要瞭解幾個基本概念︰

 - **服務類型**︰ 這是您的服務實作。 藉由您所撰寫的課程延伸`StatelessService`和任何其他程式碼或使用其中，以及的名稱，以及版本號碼的相依性。

 - **命名服務執行個體**︰ 若要執行您的服務，您建立具名的執行個體您服務的類型，很像您建立的課程類型的物件執行個體。 服務執行個體實際上是您的服務類別您所撰寫的物件例項。 

 - **服務 host （主機)**: 您建立命名的服務執行個體需要主應用程式中執行。 只要您的服務的執行個體，可以執行的程序服務主機。

 - **服務註冊**︰ 註冊結合所有項目。 若要允許服務來建立的執行個體布料的轉印圖樣的服務主機中執行的服務布料的轉印圖樣執行階段必須向服務類型。  

## <a name="create-a-stateless-service"></a>建立無狀態服務

開始建立新的服務布料的轉印圖樣應用程式。 服務布料的轉印圖樣 SDK Linux 包含 Yeoman 產生器提供臨時平台服務布料的轉印圖樣應用程式與服務狀態。 首先請執行下列 Yeoman 命令︰

```bash
$ yo azuresfjava
```

請依照下列指示建立**可靠的無狀態服務**。 在此教學課程中，名稱的應用程式 」 HelloWorldApplication 」 和 「 HelloWorld 」 服務。 結果會包含目錄的`HelloWorldApplication`和`HelloWorld`。

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="implement-the-service"></a>實作服務

開啟**HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**。 這個類別定義服務類型，並可以執行的任何程式碼。 您的程式碼服務 API 提供兩個進入點︰

 - 開放式進入點方法，稱為`runAsync()`，您可以開始執行任何工作負載，包括長時間執行計算工作負載的位置。

```java
@Override
protected CompletableFuture<?> runAsync() {
    ...
}
```

 - 您可以插入您的通訊的堆疊中選擇通訊進入點。 這是您可以開始從使用者及其他服務收到要求的位置。

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

在本教學課程中，我們將焦點放在`runAsync()`進入點方法。 這是您可以立即開始執行程式碼。

### <a name="runasync"></a>RunAsync

平台服務的執行個體放置並準備好執行時，呼叫這個方法。 服務執行個體的開啟/關閉循環圖可能是整個服務的存留期間的次數。 這可能會發生的各種原因，包括︰

- 系統會將資源平衡您服務執行個體。
- 在您的程式碼中，就會發生錯誤。
- 應用程式或系統會升級。
- 基礎硬體體驗中斷。

此協調流程受管理的服務，讓您的服務可用性正確對稱布料的轉印圖樣。

#### <a name="cancellation"></a>取消

很重要的程式碼在`runAsync()`可以停止執行時收到通知的服務布料的轉印圖樣。 `CompletableFuture`傳回`runAsync()`服務布料的轉印圖樣需要您的服務，若要停止執行時，會取消。 下列範例會示範如何處理取消事件︰ 

```java
    @Override
    protected CompletableFuture<?> runAsync() {

        CompletableFuture<?> completableFuture = new CompletableFuture<>();
        ExecutorService service = Executors.newFixedThreadPool(1);
        
        Future<?> userTask = service.submit(() -> {
            while (!Thread.currentThread().isInterrupted()) {
                try
                {
                   logger.log(Level.INFO, this.context().serviceName().toString());
                   Thread.sleep(1000);
                }
                catch (InterruptedException ex)
                {
                    logger.log(Level.INFO, this.context().serviceName().toString() + " interrupted. Exiting");
                    return;
                }
            }
         });
 
        completableFuture.handle((r, ex) -> {
            if (ex instanceof CancellationException) {
                userTask.cancel(true);
                service.shutdown();
            }
            return null;
        });
 
        return completableFuture;
   }
``` 

### <a name="service-registration"></a>服務註冊

與服務布料的轉印圖樣執行階段，必須先註冊服務類型。 服務類型定義在`ServiceManifest.xml`和實作您服務類別`StatelessService`。 服務註冊被執行中的程序主要進入點。 在此範例中，為程序主要進入點`HelloWorldServiceHost.java`:

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    } 
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration: {0}", ex.toString());
        throw ex;
    }
}
```

## <a name="run-the-application"></a>執行應用程式

Yeoman 臨時平台包括 gradle 指令碼來建立應用程式，而且被指令碼部署及取消-部署應用程式。 若要執行應用程式，先建立 gradle 應用程式︰

```bash
$ gradle
```

這會產生，可使用服務布料的轉印圖樣 Azure CLI 部署服務布料的轉印圖樣應用程式套件。 Install.sh 指令碼包含部署應用程式套件的必要 Azure CLI 命令。 只要執行 install.sh 指令碼部署︰

```bask
$ ./install.sh
```
