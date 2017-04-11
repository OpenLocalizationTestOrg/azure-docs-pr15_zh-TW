<properties
    pageTitle="開始使用事件集線器與 Apache 大量 java |Microsoft Azure"
    description="請遵循此教學課程以開始使用 Azure 事件集線器;java 事件傳送及接收這些 Apache 大量叢集。"
    services="event-hubs"
    documentationCenter=""
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="sethm"/>

# <a name="get-started-with-event-hubs"></a>開始使用事件集線器

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>簡介

事件集線器是 free ingestion 系統，可以吸收量數百萬秒的事件，讓應用程式 [處理程序及分析大量資料所產生的連線的裝置及應用程式。 一旦收集到事件集線器，您可以轉換，並使用任何即時分析提供者或儲存空間叢集儲存資料。

如需詳細資訊，請參閱[事件集線器概觀][]。

本教學課程說明如何將使用主控台應用程式在 Java 中，[事件] 中心收集訊息，以及如何使用 Apache 大量平行可擷取。

若要完成此教學課程中，您將需要下列項目︰

+ Java 開發環境設定為執行[Maven](http://maven.apache.org/)。 在此教學課程中，我們假設[蝕](https://www.eclipse.org/)。

+ 使用中的 Azure 帳戶。 如果您沒有帳戶，您可以建立的免費的試用帳戶在幾分鐘。 如需詳細資訊，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>執行應用程式

現在您已經準備好執行應用程式。

1.  從蝕，執行**LogTopology**類別，然後用來開始的所有磁碟分割區接收器等候]。

2.  執行**寄件者**的專案、 按下**enter 鍵**主控台在視窗中，以及查看出現在 [收件者] 視窗中的事件。

    ![][22]

> [AZURE.NOTE] 在此教學課程中只，大量模式中使用本機的開發之用。 請參閱[HDInsight 大量概觀][]」 和 「 正式[Apache 大量][]的文件的詳細資訊大量部署和模式。

## <a name="next-steps"></a>後續步驟

下列資源可供開發整合事件集線器和大量的應用程式。

- [分析大量與 HDInsight 感應器資料]是使用事件集線器大量，與 HBase 內嵌 Hadoop 叢集感應器資料的完整案例教學課程。
- 教學課程，說明如何撰寫大量管線使用 C# 的[開發串流 SCP.NET 及 C# 大量和 HDInsight 的資料處理應用程式][]。

<!-- Images. -->
[22]: ./media/event-hubs-java-storm-getstarted/receive-storm2.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[事件集線器概觀]: event-hubs-overview.md

[Apache 大量]: https://storm.incubator.apache.org
[HDInsight 大量概觀]: ../hdinsight/hdinsight-storm-overview.md
[分析大量與 HDInsight 感應器資料]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[開發串流 SCP.NET 及 C# 大量和 HDInsight 的資料處理應用程式]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md
 