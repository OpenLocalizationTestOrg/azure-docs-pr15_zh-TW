<properties
    pageTitle="Azure 事件集線器封存 |Microsoft Azure"
    description="Azure 事件集線器封存功能的概觀。"
    services="event-hubs"
    documentationCenter=""
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="darosa;sethm"/>

# <a name="azure-event-hubs-archive"></a>Azure 事件集線器封存

Azure 事件集線器封存可讓您自動傳送至指定的時間，或調整大小您所選擇的間隔的彈性與您所選擇的 Blob 儲存體帳戶您事件集線器串流的資料。 設定 [封存快速、 有管理費用執行，以及調整自動與您的事件集線器[處理量單位](event-hubs-overview.md#capacity-and-security)。 事件集線器封存是串流資料載入 Azure 最簡單的方法，並允許您將焦點放在處理資料，而不是資料擷取。

Azure 事件集線器封存可讓您在相同的資料流即時和以批次為基礎的管線 [處理程序。 這可讓您建立的解決方案，可以放大與您的需求，一段時間。 不論您建立批次系統今天與未來的即時處理的角度，或您想要新增至現有的即時方案的有效的低溫路徑，事件集線器封存可以讓串流資料更容易使用。

## <a name="how-event-hubs-archive-works"></a>事件集線器封存的運作方式

事件集線器是遙測輸入，類似於分散式記錄的時間保留長期緩衝。 若要在事件集線器縮放，關鍵是[分割的消費者模型](event-hubs-overview.md#partition-key)。 每個資料分割獨立的資料區段，而單獨使用。 一段時間此資料過時關閉，根據可設定保留期限。 如此一來，指定的事件中樞永遠不會 」 太完整 」。

事件集線器封存可讓您指定您自己的 Azure Blob 儲存體帳戶和容器會用來儲存封存的資料。 此帳戶可為您的事件中心相同的區域，或在另一個區域中，新增至事件集線器封存功能的彈性。

封存的資料寫入[Apache Avro][]格式。精簡、 快速二進位格式內嵌結構描述提供豐富的資料結構。 在 Hadoop 生態系統和資料流分析及 Azure 資料工廠廣泛使用這個格式。 使用本文稍後的使用 Avro 的詳細資訊。

### <a name="archive-windowing"></a>封存視窗

事件集線器封存可讓您控制封存設定] 視窗。 這個視窗是最小的大小和使用 「 第一個獲勝原則，「 表示遇到的第一個觸發程序會導致封存作業的時間設定。 如果您有十五分鐘/100 MB 封存視窗，並傳送 1 MB/s，[大小] 視窗會前次視窗觸發程序。 每個資料分割獨立封存，並將已完成的區塊 blob 封存中，同時命名時遇到封存間隔的時間。 命名慣例如下所示︰

```
<Namespace>/<EventHub>/<Partition>/<YYYY>/<MM>/<DD>/<HH>/<mm>/<ss>
```

### <a name="scaling-to-throughput-units"></a>處理量單位縮放比例

[處理量單位](event-hubs-overview.md#capacity-and-security)控制事件集線器流量。 1 MB/s 或 1000年的事件，每秒的輸入與兩次該出口的數量，則可讓單一處理量單位。 標準的事件集線器可以使用 1-20 處理量單位來設定，您可以透過配額增加[支援要求][]購買更多。 除了購買處理量單位的使用方式是經流速控制。 略過處理量單位出口配額和儲存資料流分析或火花等其他處理讀者您出口事件集線器封存直接從內部事件集線器儲存空間，複製資料。

設定之後，傳送您的第一個事件時，會自動執行事件集線器封存。 它會一直繼續執行。 若要讓您更容易以知道程序使用您下游處理，事件集線器，當沒有資料，則將空白檔案。 此提供可預測頻率，以及可以摘要您批次處理器的標記。

## <a name="setting-up-event-hubs-archive"></a>設定事件集線器封存

您可以在事件中心建立時，透過入口網站或 Azure 資源管理員設定事件集線器封存。 您只要啟用封存，按一下 [**上**] 按鈕。 您可以設定的儲存空間帳戶與容器即可刀 [**容器**] 區段。 因為事件集線器封存使用服務-服務驗證的儲存空間，您不需要指定儲存空間的連接字串。 資源選擇器會自動選取資源 URI 儲存帳戶。 如果您使用 Azure 資源管理員，您必須為字串明確提供此 URI。

預設的 [時間] 視窗為 5 分鐘。 最小值為 1，最大 15。 [**大小**] 視窗某一範圍的 10 500 MB。

![][1]

## <a name="adding-archive-to-an-existing-event-hub"></a>將封存新增至現有的事件中心

在現有的事件集線器事件集線器命名空間中，您可以設定封存。 此功能無法使用較舊的訊息或混合類型命名空間。 若要啟用封存上現有的事件中心內，或若要變更您的封存設定，請按一下您要載入**基礎**刀，然後按一下 [事件] 中心內，您想要啟用或變更封存設定的命名空間。 最後，按一下 [**屬性**] 區段的 [開啟刀如下圖所示。

![][2]

您也可以透過 Azure 資源管理員範本設定事件集線器封存。 如需詳細資訊，請參閱[本文](event-hubs-resource-manager-namespace-event-hub-enable-archive.md)。

## <a name="exploring-the-archive-and-working-with-avro"></a>探索封存與使用 Avro

設定之後，事件集線器封存檔案在建立 Azure 儲存體帳戶並設定的時間視窗上提供的容器。 您可以在任何工具，例如[Azure 儲存檔案總管][]中檢視這些檔案。 您可以下載至本機進行處理的檔案。

所產生的事件集線器封存的檔案有下列 Avro 結構描述︰

![][3]

使用從 Apache [Avro 工具][]糖就能輕鬆瀏覽 Avro 檔案。 下載此 jar 之後，執行下列命令，即可看到特定的 Avro 檔案的結構描述︰

```
java -jar avro-tools-1.8.1.jar getschema \<name of archive file\>
```

這個命令會傳回

```
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

您也可以使用 Avro 工具，將檔案轉換為 JSON 格式，以及執行其他處理程序。

若要執行更多進階的處理，下載並安裝 Avro 用於您所選擇的平台。 在撰寫時，有可用的實作 C、 c + +、 C\#，Java、 NodeJS、 Perl、 PHP、 Python 及 [注音標示。

Apache Avro [Java][]和[Python][]已完成的快速入門指南。 您也可以閱讀的[快速入門事件集線器封存](event-hubs-archive-python.md)文件。

## <a name="how-event-hubs-archive-is-charged"></a>事件集線器封存的負責

事件集線器封存是計量付費同樣處理量單位為每小時的費用。 費用是直接按比例的命名空間購買的處理量單位數。 增加和減少處理量單位時，事件集線器封存會增加，並將會減少提供相符的效能。 在一起，會公尺。 事件集線器封存的費用是 $0.10 每小時每處理量單位，提供 50%折扣預覽期間。

事件集線器封存真正是取得資料至 Azure 最簡單的方法。 使用 Azure 資料湖、 Azure 資料工廠和 Azure HDInsight，您可以執行批次處理和其他分析您所選擇的任何縮放比例，您需要使用熟悉的工具與平台。

## <a name="next-steps"></a>後續步驟

您可以進一步瞭解事件集線器瀏覽下列連結︰

- 完成[範例應用程式的使用事件集線器][]。
- [延展事件處理事件集線器][]樣本。
- [事件集線器概觀][]

[Apache Avro]: http://avro.apache.org/
[支援要求]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[1]: ./media/event-hubs-archive-overview/event-hubs-archive1.png
[2]: media/event-hubs-archive-overview/event-hubs-archive2.png
[Azure 儲存檔案總管]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-archive-overview/event-hubs-archive3.png
[Avro 工具]: http://www-us.apache.org/dist/avro/avro-1.8.1/java/avro-tools-1.8.1.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[事件集線器概觀]: event-hubs-overview.md
[使用事件集線器範例應用程式]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[不按比例縮放出事件處理事件集線器]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3