<properties
    pageTitle="開始使用事件集線器 C# |Microsoft Azure"
    description="請遵循此教學課程以開始使用 Azure 事件集線器;在 C# 和接收 java 使用 EventProcessorHost 傳送事件。"
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
    ms.date="09/27/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>開始使用事件集線器

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>簡介

事件集線器是處理大量事件 （遙測） 連線的裝置或應用程式資料的服務。 將事件集線器收集資料之後，您可以使用儲存資料的儲存空間叢集或轉換並使用即時分析提供者。 此大型事件收集與處理功能包括網際網路的項目 (IoT) 的現代化的應用程式架構的主要元件。

本教學課程中會顯示如何建立事件中心使用 Azure 傳統入口網站。 它也會顯示如何將使用主控台應用程式以 C# 撰寫事件中心收集郵件及如何平行使用 Java 事件處理器主機文件庫中的可擷取。

若要完成此教學課程中，您需要下列動作︰

+ [Microsoft Visual Studio](http://visualstudio.com)

+ 使用中的 Azure 帳戶。 <br/>如果您沒有帳戶，您可以建立在幾分鐘的免費的帳戶。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F target="_blank")。

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## <a name="run-the-applications"></a>執行應用程式

現在您已經準備好執行應用程式。

1.  執行 [**收件者**的專案。

    ![][21]

2.  執行 [**寄件者**的專案。

    ![][22]

## <a name="next-steps"></a>後續步驟

現在，您曾經建立事件中心和傳送與接收的使用應用程式，您可以移動以下情況︰

- 完成[的使用事件集線器範例應用程式][]。
- [延展事件處理事件集線器][]樣本。
- [事件集線器概觀][]

<!-- Images. -->
[21]: ./media/event-hubs-csharp-ephjava-getstarted/ephjava.png
[22]: ./media/event-hubs-csharp-ephjava-getstarted/cs-send.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[事件集線器概觀]: event-hubs-overview.md
[使用事件集線器範例應用程式]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[不按比例縮放出事件處理事件集線器]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
