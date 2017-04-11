<properties 
    pageTitle="Azure 行動互動實作媒體應用程式"
    description="媒體應用程式案例實作 Azure 行動互動" 
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

#<a name="implement-mobile-engagement-with-media-app"></a>實作行動媒體應用程式的互動

## <a name="overview"></a>概觀

John 是顯示較大的媒體公司的行動裝置的專案經理。 最近，他會啟動新的應用程式的更高的下載計數。 他已經達到下載他目標，但其傳回上 Investment(ROI) 每位使用者仍不符合其需求。 

為什麼他投資報酬率有太低，具有已識別 John。 使用者經常停止只 2 週後，使用其應用程式，這些最不完假回來。 他想要增加保留其應用程式。

部分初始測試之後，他發現當他會請他使用者推入通知，他們似乎經常犯繼續使用其應用程式。 也非作用中的使用者通常會傳回根據其傳送給他們的通知應用程式。 John 決定投資某種互動程式使用進階選取目標推入通知他們應用程式。

John 具有最近讀取[Azure 行動互動入門指南最佳作法](mobile-engagement-getting-started-best-practices.md)，決定實作從輔助線的建議。

##<a name="objectives-and-kpis"></a>目標及 Kpi

John 的應用程式的重要專案關係人開會即可。 商務產生廣告為使用者使用他的媒體。 藉由增加每位使用者使用的內容，John 會增加他的收益。 所有同意一個主要目標︰ 若要從廣告銷售增加 25%。 在建立商務關鍵效能指標 (Kpi) 量值和磁碟機這項目標

* 按一下每個使用者的廣告的數字
* （每個使用者 / 每個工作階段 / 每週 / 每月...），瀏覽過多少文章頁面
* 什麼是最愛的類別

根據重要專案關係人與 John 會議他已定義其商務 Kpi。 他遵循[Azure 行動互動的最佳作法快速入門指南 》](mobile-engagement-getting-started-best-practices.md)的第 1 部分。 

接下來，他會建立下列互動 Kpi，以確保的達到目標︰

* 在下列時間間隔監控保留︰ 每日、 每週、 bi 每週及每月。
* 作用中的使用者數量
* 在應用程式的應用程式等級儲存

根據建議 IT 小組，下列技術 Kpi 加入回答下列問題︰

* 什麼是我的使用者路徑 （瀏覽的頁面，多少時間使用者都要花在其上）
* 當機或每個工作階段遇到的問題的數字嗎？
* 我的使用者執行何種作業系統版本？
* 什麼是螢幕的平均的使用者大小？
* 我的使用者是否有哪一類的網際網路連線？

每個 KPI，他會將所需的資料分類，他記錄，其 playbook 的適當位置。

## <a name="engagement-program-and-integration"></a>互動程式 」 和 「 整合

現在該 John 已完成定義他 Kpi，他會啟動其互動策略階段定義 4 互動程式和他們的方針︰    ![][1]

然後 John 說明提供更深入的詳細描述每個程式的推播通知。 推入通知定義由五個項目︰

1. 目標︰ 什麼是通知的目標
2. 如何將已達到目標
3. 目標︰ 人員會收到通知？
4. 內容︰ 什麼是文字和通知 （在 App/Out 的應用程式） 的格式
5. 何時︰ 什麼是最佳森積差傳送此推入通知

    ![][2]

如需詳細資訊，請參閱[Playbooks](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks)。

根據部分白皮書 （英文） John 的第 2 使用目標區段來定義他的 「 收集的資料，並將其標記計劃共同與 IT 小組實作解決方案。 1 週的實作與使用者接受測試後 John 最後可啟動他的程式。

##<a name="program-results"></a>程式結果

4 個月之後，John 檢閱演唱的程式。 歡迎使用程式與每週程式會議他的目標。 使用單一工作階段的使用者數目會減少正在使用的應用程式的其他功能與加倍連線每週的數目。

**非使用中的程式**有助瞭解使用者喜好傾向有關的名字。 它會出現 15%的非作用中使用者回來應用程式。 不過這些最不維持作用中多個 1 個月。 John foresees 可能最佳化此順序與其他告知展開其內容的選項。

**探索程式**不佳。 增加交叉掌控但沒有足夠達到其目標。 John 識別他沒有足夠的資料，讓相關目標與建議適當的內容。 他停止此程式，並解釋傳送與 Azure 行動互動的 [編輯推入通知]。 他記者已 CMS 方案傳送推入通知，他們不想要變更。

決定使用達到 API 這是可讓您管理達到行銷活動，而不必使用 AZME Web 介面 HTTP REST API John。 使用這個方法 John 可以收集他需求，允許他們撰寫者繼續使用 CMS 解決方案的資料。

若要確保這項功能正常運作，John 要求 IT 小組可以在下列點警戒︰

1. **作業系統**︰ 它們都有自己的規則，以管理推入通知，以便 John 決定] 清單中所有的情況下，並檢查是否 Api 的處理方式。
例如︰ Android 推入系統允許一窺全貌不是使用 iOS 大小寫。

2. **時間範圍**︰ John 想要的 API 設定的時間範圍，並設定為 [行銷活動的結束時間。 他想要保留使用者，從任何擊破壞的通知。

3. **類別**︰ 行銷小組會準備每種類型的警示中的範本。 John 要求 IT 小組設定類別內 API。

某些測試後 John 是沒有問題。 此 API，獎狀記者仍然可以傳送 CMS 與 Azure 行動互動的推入通知，收集行為的所有資料

這些 4 後第一次月，結果反映很好的整體效能，並提供信賴 John 及他的區，每個使用者增加每 15%的投資報酬率，行動業務代表 17.5%的總銷售額，只有四個月的 7.5%增加。

<!--Image references-->
[1]: ./media/mobile-engagement-media-scenario/engagement-strategy.png
[2]: ./media/mobile-engagement-media-scenario/push-scenarios.png

<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks
