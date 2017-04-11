<properties 
    pageTitle="Azure 行動互動實作遊戲應用程式"
    description="實作 Azure 行動互動遊戲應用程式案例" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile" 
    ms.date="08/19/2016"
    ms.author="piyushjo"/>

#<a name="implement-mobile-engagement-with-gaming-app"></a>實作行動遊戲應用程式的互動

## <a name="overview"></a>概觀

遊戲啟動時已啟動新的基礎釣魚 role play/策略遊戲應用程式。 遊戲已執行 6 個月。 此遊戲龐大的成功、 和有數百萬下載，而保留會更高相較於其他啟動遊戲應用程式。 每季檢閱會議中，專案關係人同意需要增加每位使用者 (ARPU) 的平均收益。 進階版遊戲套件，可為特殊優惠。 這些遊戲套件可讓使用者升級的外觀和其釣魚線和 lures 或 tackles 遊戲中的效能。 不過，套件銷售有極低。 因此，決定第一次分析的客戶體驗，使用 [分析] 工具中，然後開發互動程式，以增加銷售使用進階分割。

根據上[Azure 行動互動入門指南與最佳做法](mobile-engagement-getting-started-best-practices.md)他們建立互動策略。

##<a name="objectives-and-kpis"></a>目標及 Kpi

遊戲重要專案關係人開會即可。 所有同意一個主要目標-以增加進階版套件銷售 15%。 在建立商務關鍵效能指標 (Kpi) 量值和磁碟機這項目標

* 這些套件購買的遊戲層級上？
* 什麼是營收每位使用者、 工作階段、 週和月？
* 最愛的購買類型為何？

[快速入門指南 》](mobile-engagement-getting-started-best-practices.md)的第 1 部分會說明如何定義目標及 Kpi。 

現在已定義之商務 kpi，行動產品經理所建立的互動 Kpi，來判斷新使用者趨勢和保留。

* 監控保留和使用過的下列間隔︰ 每日、 每個 2 天，每週、 月及每 3 個月
* 作用中的使用者數量
* 在市集中的應用程式評等

根據建議 IT 小組，下列技術 Kpi 加入回答下列問題︰

* 什麼是我的使用者路徑 （瀏覽的頁面，使用者花多少上）
* 當機或每個工作階段遇到的問題的數字
* 我的使用者執行何種作業系統版本？
* 什麼是螢幕的平均的使用者大小？
* 我的使用者是否有哪一類的網際網路連線？

每個 KPI 行動產品經理指定的資料，她需要並在其 playbook 位置是。

## <a name="engagement-program-and-integration"></a>互動程式 」 和 「 整合

建立進階的互動程式之前, 決定專案行動專案主管應該深入了解的方式，與產品由使用者。

3 個月之後, 行動專案主管已收集足夠的資料，以增強他們的應用程式推入通知銷售。 他學習的︰

* 第一個購買通常會發生 14 層級。 購買 90%的情況下，新的傳奇武器是 $ 3。
* 在這些情況下 80%，完成購買，繼續進行產品，並讓其他使用者購買。
* 使用者過去之後的層級 20，開始花超過 $10/週。
* 購買 16、 24 和 32 的層級的進階版套件似乎經常犯的使用者。

此分析獎狀行動專案主管決定建立特定的推入通知順序來增加應用程式的銷售。 建立三個推入順序會他呼叫︰ 歡迎使用程式、 銷售程式，以及非作用中的程式。 如需詳細資訊請參閱[Playbooks](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks)
    ![][1]

<!--Image references-->

[1]: ./media/mobile-engagement-game-scenario/notification-scenario.png

<!--Link references-->
