<properties 
   pageTitle="通知資料自感應器或其他系統的使用者 |Microsoft Azure"
   description="說明如何使用事件集線器通知使用者感應器資料。"
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/25/2016"
   ms.author="spyros;sethm" />

# <a name="notify-users-of-data-received-from-sensors-or-other-systems"></a>通知使用者，從感應器或其他系統所收到的資料

假設您有監視即時的資料，或產生報告排程的應用程式。 如果您看看會顯示這些即時圖表或報表的網站，您可能會看到必須採取動作的項目。 如果您需要更改這些的情況下，而依賴記得要查看的網站？ 假設您有放大 light 中 greenhouse，必須知道立即精簡是否已超出。 執行這項作業的其中一個方法是使用精簡感應器中 greenhouse，排列如果精簡傳送電子郵件。

![][1]

在另一個案例中，假設您執行寵物 boarding 功能，您必須收到低庫存供應層級。 例如，您可能會排列，如果您的倉庫庫存的狗食物降到重要的層級，才能傳送簡訊 ERP 系統。 

![][2]

問題是如何符合特定條件，您就會設法取出靜態報表時，不時取得重要資訊。 如果您使用[Azure 事件中心][]或[Azure IoT 中心][]從裝置或企業應用程式，例如[Dynamics AX][]接收資料，您會有幾個選項如何處理。 您可以檢視這些網站上可以分析，您可以儲存，與您可以使用這些觸發命令進行操作。 若要這麼做，您可以使用[Azure 網站][]、 [SQL Azure][]、 [HDInsight][]、 [Cortana 智慧套件][]、 [IoT 套件][]、[邏輯應用程式][]或[Azure 通知集線器][]等功能強大的工具。 但有時候您想要做就是將該資料傳送給某人的費用。 若要顯示若只要實際上的程式碼，我們提供了新的範例， [AppToNotifyUsers][]。 包含選項是電子郵件 (SMTP)、 簡訊及電話。

## <a name="application-structure"></a>應用程式結構

應用程式撰寫 C# 中，在這個範例中的讀我檔案包含您要修改、 建立及發佈應用程式的所有資訊。 下列各節提供高層級的應用程式的功能概觀。

我們開始，其假設您已被推入至 Azure 事件中心或 IoT 中樞的重要事件。 只要您的存取權，並知道的連接字串，會執行任何中心。

如果您還沒有事件中心或 IoT] 中心內，您可以輕鬆地設定測試平台 Arduino 盾與覆盆子 Pi[連接點](https://github.com/Azure/connectthedots)專案中的指示執行。 在 Arduino 盾精簡感應器 Pi 透過精簡的層級到[Azure 事件中心][](**ehdevices**) 與[Azure 資料流分析](https://azure.microsoft.com/services/stream-analytics/)工作推入通知到第二個事件集線器 (**ehalerts**) 如果收到的精簡等級低於特定的層級。

**AppToNotify**啟動時，它會讀取設定檔案 (App.config) 的 URL 及認證取得 [事件] 中心內收到通知。 然後會產生不斷地監控，是透過 –，只要您具備的所有郵件的 [事件] 中心內可以存取事件中心或 IoT 中樞和有效的認證的 URL，將此事件集線器讀取程式碼會持續讀取近期程序。 在啟動期間，應用程式也會讀取的 URL 及您要使用的訊息服務 （電子郵件、 簡訊，電話） 和名稱/地址的寄件者和收件者清單的認證。

一旦事件中心監視器偵測到郵件，它會觸發程序所傳送的郵件使用的設定檔中指定的方法。 請注意，不會偵測到的每封郵件。 如果您設定為指向監視器的事件集線器會收到秒的十個郵件、 寄件者會傳送十個訊息秒 – 十個電子郵件秒十簡訊秒的十個行動電話。 因此，請確定您監控事件中樞的只會收到需要送出，不會接收來自您感應器或應用程式的所有原始資料事件中心的通知。

## <a name="applicability"></a>適

如何監視事件集線器以及如何呼叫外部訊息的服務，讓您想要將這項功能新增至您的應用程式，只會顯示在這個範例中的程式碼。 請注意，此方案 DIY 時，只開發人員導向範例。 例如重複，容錯移轉，重新啟動失敗等時，它不能解決企業需求。更多的綜合和生產解決方案，請參閱下列內容︰

- 使用連接器或使用[Azure 邏輯應用程式](../app-service-logic/app-service-logic-connectors-list.md)服務的推播通知。
- 所述的部落格[的行動裝置使用 Azure 通知集線器廣播推入通知](http://weblogs.asp.net/scottgu/broadcast-push-notifications-to-millions-of-mobile-devices-using-windows-azure-notification-hubs)，請使用[Azure 通知集線器](https://msdn.microsoft.com/library/azure/jj927170.aspx)。 

## <a name="next-steps"></a>後續步驟

若要建立一個簡單的通知服務，將電子郵件或簡訊傳送到收件者] 或轉送資料收到的事件中心或 IoT 中樞的通話，簡單是。 若要部署解決方案通知使用者根據這些集線器收到的資料，請造訪[AppToNotifyUsers][]。

如需這些集線器的詳細資訊，請參閱下列文章︰

- [Azure 事件集線器]
- [Azure IoT 中心]
- 開始使用[事件集線器教學課程]。
- 完成[的使用事件集線器範例應用程式]。

若要部署方案通知使用者根據這些集線器收到的資料，請造訪︰

- [AppToNotifyUsers][]

[事件集線器教學課程]: event-hubs-csharp-ephcs-getstarted.md
[Azure IoT 中心]: https://azure.microsoft.com/services/iot-hub/
[Azure 事件集線器]: https://azure.microsoft.com/services/event-hubs/
[Azure 事件中心]: https://azure.microsoft.com/services/event-hubs/
[使用事件集線器範例應用程式]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[AppToNotifyUsers]: https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications
[Dynamics AX]: http://www.microsoft.com/dynamics/erp-ax-overview.aspx
[Azure 網站]: https://azure.microsoft.com/services/app-service/web/
[SQL Azure]: https://azure.microsoft.com/services/sql-database/
[HDInsight]: https://azure.microsoft.com/services/hdinsight/
[Cortana 智慧套件]: http://www.microsoft.com/server-cloud/cortana-analytics-suite/Overview.aspx?WT.srch=1&WT.mc_ID=SEM_lLFwOJm3&bknode=BlueKai
[IoT 套件]: https://azure.microsoft.com/solutions/iot-suite/
[邏輯應用程式]: https://azure.microsoft.com/services/app-service/logic/
[Azure 通知集線器]: https://azure.microsoft.com/services/notification-hubs/
[Azure Stream Analytics]: https://azure.microsoft.com/services/stream-analytics/
 
[1]: ./media/event-hubs-sensors-notify-users/event-hubs-sensor-alert.png
[2]: ./media/event-hubs-sensors-notify-users/event-hubs-erp-alert.png