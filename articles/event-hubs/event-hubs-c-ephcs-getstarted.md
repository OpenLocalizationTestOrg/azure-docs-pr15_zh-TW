<properties
    pageTitle="快速入門中 C 及 C# 事件集線器 |Microsoft Azure"
    description="請遵循此教學課程以開始使用 Azure 事件集線器;傳送 C 及 C# 使用 EventProcessorHost 接收 hem 事件。"
    services="event-hubs"
    documentationCenter=""
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="c"
    ms.devlang="csharp"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>開始使用事件集線器

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>簡介

事件集線器是 free ingestion 系統，可以吸收量數百萬秒的事件，讓應用程式 [處理程序及分析大量資料所產生的連線的裝置及應用程式。 一旦收集到事件集線器，您可以轉換，並使用任何即時分析提供者或儲存空間叢集儲存資料。

如需詳細資訊，請參閱[事件集線器概觀][]。

在本教學課程中，您將學習如何使用主控台應用程式中 C，事件中心將內嵌的郵件，以及可擷取平行使用 C#[事件處理器主機][]文件庫中。

若要完成此教學課程中，您將需要下列項目︰

+ C 開發環境。 在此教學課程中，我們會假設與 Ubuntu 14.04 [Azure Linux VM](../virtual-machines/virtual-machines-linux-quick-create-cli.md) gcc 堆疊。 外部連結將提供其他環境的相關指示。

+ 在 Windows 版的 Microsoft Visual Studio Express

+ 使用中的 Azure 帳戶。 如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>執行應用程式

現在您已經準備好執行應用程式。

1.  執行內 Visual Studio 中，從**收件者**專案，然後用來開始的所有磁碟分割區接收器等候。

    ![][21]

2.  執行**寄件者**的程式，並查看出現在 [收件者] 視窗中的事件。

    ![][24]

## <a name="next-steps"></a>後續步驟

現在，您曾經建立事件中心和傳送與接收的使用應用程式，您可以移動以下情況︰

- 完成[範例應用程式的使用事件集線器][]。
- [延展事件處理事件集線器][]樣本。
- [事件集線器概觀][]

<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[事件處理器主機]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[事件集線器概觀]: event-hubs-overview.md
[使用事件集線器範例應用程式]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[不按比例縮放出事件處理事件集線器]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
