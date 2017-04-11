<properties
    pageTitle="開始使用事件集線器 C# |Microsoft Azure"
    description="請遵循此教學課程以開始使用 Azure 事件集線器 C#，並使用 EventProcessorHost。"
    services="event-hubs"
    documentationCenter=""
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/02/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>開始使用事件集線器

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>簡介

事件集線器是處理大量事件 （遙測） 連線的裝置或應用程式資料的服務。 將事件集線器收集資料之後，您可以使用儲存資料的儲存空間叢集或轉換並使用即時分析提供者。 此大型事件收集與處理功能包括網際網路的項目 (IoT) 的現代化的應用程式架構的主要元件。

本教學課程中會顯示如何建立事件中心使用 Azure 傳統入口網站。 它也會顯示如何將使用主控台應用程式以 C# 撰寫事件中心收集郵件及如何平行使用 C#[事件處理器主機][]文件庫中的可擷取。

若要完成此教學課程中，您需要下列動作︰

+ [Microsoft Visual Studio](http://visualstudio.com)

+ 使用中的 Azure 帳戶。 如果您沒有帳戶，您可以建立在幾分鐘的免費的帳戶。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/free/)。

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>執行應用程式

現在您已經準備好執行應用程式。

1. 從內 Visual Studio 中，開啟您先前建立的**收件者**專案。

2. 以滑鼠右鍵按一下 [**收件者**的方案，然後按一下 [**新增**]，然後按一下**現有專案**。
 
3. 找出現有 Sender.csproj 檔案，然後按兩下以將其新增至的方案。
 
4. 同樣地，以滑鼠右鍵按一下 [**收件者**的方案，然後按一下 [**屬性**。 **收件者**的 [屬性] 頁面會顯示。

5. 按一下 [**啟動專案**，然後按一下 [**多個啟動專案**] 按鈕。 若要**開始**設定 [**動作**] 方塊，將 [**收件者**] 與 [**寄件者**的專案。

    ![][19]

6. 按一下 [**專案的相依性**]。 在 [**專案**] 方塊中，按一下 [**寄件者**]。 在 [**相依於**] 方塊中，請確定已核取 [**收件者**。

    ![][20]

7. 按一下**[確定**] 關閉 [**屬性**] 對話方塊。

1.  按 F5 以執行**收件者**中的專案從 Visual Studio 中，然後用來開始的所有磁碟分割區接收器等候。

    ![][21]

2.  **寄件者**專案會自動執行。 按下**enter 鍵**主控台在視窗中，並查看出現在 [收件者] 視窗中的事件。

    ![][22]

按下**Ctrl + c 鍵**在 [**寄件者**] 視窗中以結束寄件者的應用程式，再按**enter 鍵**在 [收件者] 視窗中關閉該應用程式。

## <a name="next-steps"></a>後續步驟

現在，您曾經建立事件中心和傳送與接收的使用應用程式，您可以移動以下情況︰

- 完成[範例應用程式的使用事件集線器][]。
- [延展事件處理事件集線器][]樣本。
- [事件集線器概觀][]

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[事件處理器主機]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[事件集線器概觀]: event-hubs-overview.md
[使用事件集線器範例應用程式]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[不按比例縮放出事件處理事件集線器]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[queued messaging solution]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 
