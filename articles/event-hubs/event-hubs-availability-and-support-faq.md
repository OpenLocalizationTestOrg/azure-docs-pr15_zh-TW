<properties 
    pageTitle="事件集線器常見問題集 (faq) |Microsoft Azure"
    description="事件集線器常見問題集。"
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/01/2016"
    ms.author="sethm" />

# <a name="event-hubs-faq"></a>事件集線器常見問題集

事件集線器提供大規模引入口、 保存及處理高處理量資料來源的資料事件及/或數百萬種裝置。 在搭配服務匯流排佇列 」 和 「 主題，事件集線器可讓常設部署] 命令，並控制針對[網際網路的項目 (IoT)](https://azure.microsoft.com/services/iot-hub/)案例。

本文討論價格資訊，並將回答有關事件集線器一些常見問題集的問題︰

## <a name="pricing-information"></a>價格資訊

如需事件集線器價格的完整資訊，請參閱[事件集線器定價詳細資料](https://azure.microsoft.com/pricing/details/event-hubs/)。

## <a name="how-are-event-hubs-ingress-events-calculated"></a>事件集線器輸入事件的計算方式？

傳送至事件中心每項事件會計算為計費訊息。 *輸入事件*定義為單位的小於或等於 64 KB 的資料。 小於或等於大小 64 KB 的任何事件會被視為一個計費事件。 如果事件大於 64 KB，計費事件的數目的計算方式根據事件大小，64 KB 的多重圖表中。 例如，8 KB 事件傳送至 [事件] 中心計費為某一事件，但 96 KB 郵件傳送至 [事件] 中心計費為兩個事件。

使用 [事件] 中心內，從為的事件以及管理作業及控制通話，例如檢查點、 不會計算為計費輸入事件，但最處理量單位金額累算。

## <a name="what-are-event-hubs-throughput-units"></a>事件集線器處理量單位是什麼？

您明確選取事件集線器處理量單位，透過 Azure 入口網站或事件集線器資源管理員範本。 處理量單位套用到所有事件在事件集線器命名空間，每個處理量單位賦與命名空間，下列功能︰

- 設定為每秒的輸入事件 （事件傳送到 [事件] 中心內），但不超過 1000 個輸入事件、 管理作業或控制項 1 MB API 通話秒。

- 最多 2 MB 秒的出口事件 （使用從事件中心的事件）。

- 84 GB 的事件儲存空間 （滿足所需的預設 24 小時制保留期）。

事件集線器處理量單位被付費每小時，根據指定小時期間所選取單位的數上限。

## <a name="how-are-event-hubs-throughput-unit-limits-enforced"></a>如何強制執行事件集線器處理單位限制？

如果總輸入處理量或跨命名空間中的所有事件集線器的總輸入事件率超過彙總處理量單位配額，寄件者會經流速控制和收到錯誤訊息，表示已超過輸入配額。

如果總出口處理量或跨命名空間中的所有事件集線器的總事件出口率超過彙總處理量單位配額，接收器會經流速控制和收到錯誤訊息，表示已超過出口配額。 輸入與輸出強制執行配額分開，讓沒有寄件者可能會導致事件消耗慢，也可以接收者防止事件傳送到事件中心。

請注意，處理量單位選取獨立事件集線器磁碟分割區的數目。 每個資料分割提供 1 MB （含最大值 1000年秒的事件），第二個輸入每和第二個出口每 2 MB 的最大處理量，同時有固定的免費的磁碟分割區本身。 收費適用於事件集線器命名空間中的所有事件集線器中彙總處理量單位。 使用這個模式中，您可以建立足夠的磁碟分割區，而不會任何處理量單位費用，直到系統上的事件負載實際需要較高的處理量數目，而不必變更的結構及系統的架構，為系統上的載入增加其系統支援預期的最大負載。

## <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-selected"></a>是否有一些限制，您可以選取的處理量單位的數？

有預設的配額命名空間每 20 處理量單位。 您可以要求較大的配額的處理量單位來歸檔的支援票證。 20 處理量單位限制，超出配搭可在 20 到 100 處理量單位。 請注意，使用 20 個以上的處理量單位移除能夠變更的處理量單位數量，而不歸檔的支援票證。

## <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>有超過 24 小時保留事件集線器事件的費用嗎？

事件集線器標準層可以讓郵件保留期間超過 24 小時，最大值為 30 天。 如果已儲存的事件總數的大小超過選取處理量單位 (84 GB 每處理量單位) 的數字的儲存空間金額，大小超過金額是發佈 Azure Blob 儲存體工資率支付薪資。 在每個處理量單位的儲存空間金額 24 小時的保留涵蓋所有的儲存空間成本 （預設） 如果仍然處理量單位來的最大輸入金額。

## <a name="what-is-the-maximum-retention-period"></a>什麼是保留期限？

事件集線器標準層目前支援 7 天的最大保留週期。 請注意，事件集線器並不是永久性的資料存放區。 保留期間超過 24 小時的時間供的情況中很容易在相同的系統; 重新顯示活動串流例如，如果訓練或驗證對現有資料的新電腦學習模型。

## <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>事件集線器儲存空間大小如何計算以及支付嗎？

每天開始計算所有已儲存的事件，所有事件集線器，都包括任何內部開銷事件標題或磁碟儲存結構的總大小。 一天的結尾，會計算最大使用量儲存空間大小。 每日的儲存空間金額會根據選取期間 （每個處理量單位提供 84 GB 的金額） 的天處理量單位的最小的數字計算。 如果總大小超過計算的每日儲存金額，為付費多餘的儲存空間，使用 Azure Blob 儲存體工資率 （**本機多餘的儲存空間**工資率）。

## <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-event-hubs-and-service-bus-queuestopics"></a>可以使用單一 AMQP 連線傳送及接收事件集線器與服務匯流排佇列/主題嗎？

是的只要所有事件集線器、 佇列和主題都位於相同的命名空間。 因此，您可以執行許多裝置的 subsecond 延遲的雙向、 仲介連線效益和彈性的方式。

## <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>套用到事件仲介的連線費用嗎？

[寄件者，連線費用只有在使用 AMQP 通訊協定時才。 有 http，無論傳送系統或裝置的數字的事件傳送沒有連線費用。 如果您打算使用 AMQP （例如，若要獲得更有效率的活動串流或啟用 IoT] 命令，並控制案例的雙向通訊），請參閱構成仲介的連線，及計量付費方式的相關資訊的[服務匯流排價格資訊](https://azure.microsoft.com/pricing/details/service-bus/)頁面。

## <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>事件集線器基本與標準層級之間的差異為何？

事件集線器標準層提供適用於事件集線器基本，以及某些競爭力系統 」 的功能。 這些功能包括保持期限超過 24 小時，還可將傳送給大量的 subsecond 延遲裝置的命令，以及從這些裝置的遙測傳送到事件集線器，請使用單一 AMQP 連線。 功能清單，請參閱[事件集線器定價詳細資料](https://azure.microsoft.com/pricing/details/event-hubs/)。

## <a name="geographic-availability"></a>地理可用性

在下列區域內有事件集線器︰

|地理|區域|
|---|---|
|美國|美國中部東亞美國、 東亞美國 2、 南美洲美國中部、 西美制]。|
|在 Europe|北美歐洲西歐|
|亞太地區|中式地址東南亞|
|日本|日本東部，西日本|
|巴西|巴西南部|
|澳大利亞|澳大利亞東亞，澳洲 Southeast|

## <a name="support-and-sla"></a>支援與 SLA

使用[社群論壇](https://social.msdn.microsoft.com/forums/azure/home)透過事件集線器的技術支援。 免費提供帳單與訂閱管理支援。

若要深入瞭解我們 SLA，請參閱[服務等級協定](https://azure.microsoft.com/support/legal/sla/)頁面。

## <a name="next-steps"></a>後續步驟

若要進一步瞭解事件集線器，請參閱下列文章︰

- [事件集線器概觀][]。
- 完成[的使用事件集線器範例應用程式][]。

[事件集線器概觀]: event-hubs-overview.md
[使用事件集線器範例應用程式]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
