<properties
    pageTitle="DocumentDB 中的一致性層級 |Microsoft Azure"
    description="DocumentDB 有四個的一致性階層，以協助一致性平衡、 可用性和延遲折衷方案。"
    keywords="最終的一致性，documentdb azure，Microsoft azure"
    services="documentdb"
    authors="syamkmsft"
    manager="jhubbard"
    editor="cgronlun"
    documentationCenter=""/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="syamk"/>

# <a name="consistency-levels-in-documentdb"></a>DocumentDB 中的一致性層級

Azure DocumentDB 設計從頭設定記住全域通訊群組。 用來提供如預期呈現低延遲的保證與 99.99%可用性 SLA，多個定義完善的寬鬆的一致性的模型。 目前，DocumentDB 提供四個一致性層級︰ 限制過時的工作階段，並最終。 除了**強式**和**一致性**模型經常所提供的其他 NoSQL 資料庫，DocumentDB 也提供兩種仔細編碼和 operationalized 一致性模式 –**限制過時**和**工作階段**，並已經驗證真實使用情況下針對其用途。 集體這四個一致性層級可讓您將之間的一致性、 可用性及延遲的完善事物的折衷方案。 

## <a name="scope-of-consistency"></a>範圍的一致性

資料粒度的一致性限定為單一使用者的要求。 寫入要求可能會對應到插入取代、 了，或刪除交易 （包含或不相關聯的前置或文章觸發的執行）。 或寫入要求可能會對應到交易在磁碟分割內的多份文件 JavaScript 預存程序執行。 就跟寫入，已讀取/查詢交易也被範圍單一使用者的要求。 使用者可能需要在大型分頁結果集、 橫跨多個資料分割，但是每讀取交易限於單一頁面，並從單一的磁碟分割中提供。

## <a name="consistency-levels"></a>一致性層級

您可以在您套用到所有的集合 （之間的所有資料庫） 您資料庫的帳號] 之下的資料庫帳戶設定預設的一致性等級。 根據預設，所有讀取及使用者定義資源對發出的查詢會都使用預設的一致性層級指定資料庫帳戶。 不過，您也可以藉由指定的[[x-ms-一致性-層級]](https://msdn.microsoft.com/library/azure/mt632096.aspx)要求標頭放寬特定的讀取查詢要求的一致性層級。 有四種類型的一致性層級支援 DocumentDB 複寫通訊協定提供清除取捨特定的一致性保證及效能，如下所述。

![DocumentDB 提供多個，以及定義選擇的一致性 （寬鬆） 模型][1]

**強式**︰ 

- 加強的一致性保證傳回最新版本的文件的讀取與提供[linearizability](https://aphyr.com/posts/313-strong-consistency-models)保證。 
- 加強的一致性保證它由複本的大部分仲裁長期認可後才看得到寫入。 撰寫 [同步認可長期主要與次要連結，仲裁或中止。 閱讀仲裁大部分永遠通知讀取，用戶端可以永遠不會看到未認可或部分的撰寫一定閱讀最新的經過的寫入。 
- DocumentDB 帳戶設定為使用強式的一致性無法將一個以上的 Azure 區域以 DocumentDB 帳戶。 
- 了解作業 （而言耗用的[要求單位](documentdb-request-units.md)） 的成本強式一致且高於工作階段最終，但受限制的過時相同。
 

**Bounded 過時**︰ 

- 限制的讀取可能延隔後方寫入最*K*版本或您的文件或*t*過時一致性保證的時間間隔。 
- 因此時選擇繫結失效，, 您可以設定 「 失效 」 兩種方法︰ 
    - 依據讀取延隔後方寫入的文件版本*K*數目
    - 時間間隔*t* 
- 限制過時優惠總全域順序除了 「 過時視窗]。 請注意，monotonic 閱讀保證存在內內部和外部 「 過時視窗] 的區域。 
- 受限制的過時提供加強的一致性保證比工作階段，或是一致性。 全域分散式應用程式，我們建議您使用限制的過時的案例在您想要有強式的一致性，但也想要 99.99%可用性和低延遲。 
- 使用限制的過時一致性設定 DocumentDB 帳戶可以其 DocumentDB 帳戶關聯 Azure 區域的任何數字。 
- 已讀取 （而言 RUs 耗用) 作業的成本限制過時就高於階段及一致性，但強式一致性相同。

**工作階段**︰ 

- 全域一致性模型中所提供的強式和封閉過時一致性層級，跟工作階段的一致性被範圍的用戶端工作階段。 
- 工作階段的一致性適用於所有案例，因為它保證 monotonic 讀取、 monotonic 寫入及 [讀取您自己寫 (RYW) 保證的位置相關的裝置或使用者的工作階段。 
- 工作階段的一致性提供工作階段，如預期呈現的一致性，最大值讀取處理量時提供的最低延遲寫入及讀取。 
- 設定工作階段一致的 DocumentDB 帳戶可以與其 DocumentDB 帳戶建立關聯的 Azure 區域的任何數字。 
- 已讀取 （而言 RUs 耗用) 作業的成本與工作階段的一致性等級是小於強式和封閉失效，但多個一致性
 

**最終**︰ 

- 一致性保證的沒有任何進一步寫入時，在群組內的複本會最後會減少。 
- 一致性是一致性的弱形式用戶端存取早於過之前的項目之值的位置。
- 一致性提供弱讀的一致性，但同時讀取和寫入提供的最低的延遲。
- 設定與一致性的 DocumentDB 帳戶可以其 DocumentDB 帳戶關聯 Azure 區域的任何數字。 
- 已讀取 （而言 RUs 耗用) 作業的成本與一致性層級是所有 DocumentDB 一致性層級的最低。


## <a name="consistency-guarantees"></a>一致性保證

下表會擷取各種對應到四個一致性層級的一致性保證。

| 保證                                                         |    強式                                       |    受限制的失效                                                                           |    工作階段                                       |    最終                                 |
|----------------------------------------------------------|-------------------------------------------------|------------------------------------------------------------------------------------------------|--------------------------------------------------|--------------------------------------------------|
|    **總全域的順序**                                |    [是]                                          |    是的外部 」 過時視窗]                                                      |    否，部分的 「 活動 」 順序                   |    無                                            |
|    **一致的前置詞保證**                       |    [是]                                          |    [是]                                                                                         |    [是]                                           |    [是]                                           |
|    **Monotonic 讀取**                                   |    [是]                                          |    是的在區域以外過時視窗，並持續的區域。     |    可以，適用於指定的工作階段                    |    無                                            |
|    **Monotonic 寫入**                                  |    [是]                                          |    [是]                                                                                         |    [是]                                           |    [是]                                           |
|    **閱讀您寫入**                                  |    [是]                                          |    [是]                                                                                         |    是 （在寫入區域）                      |    無                                            |


## <a name="configuring-the-default-consistency-level"></a>設定預設的一致性層級

1.  在[Azure 入口網站](https://portal.azure.com/)，在 Jumpbar 中，按一下 [ **DocumentDB (NoSQL)**]。

2. 在**DocumentDB (NoSQL)**刀中，選取要修改的資料庫帳戶。

3. 在 [帳戶刀中，按一下 [**預設的一致性**]。


4. 在**預設一致性**刀中，選取新的一致性層級，按一下 [**儲存**]。

    ![螢幕擷取畫面醒目提示的 [設定] 圖示和預設一致性項目](./media/documentdb-consistency-levels/database-consistency-level-1.png)

## <a name="consistency-levels-for-queries"></a>查詢的一致性層級

根據預設，為使用者定義] 資源的一致性層級查詢是相同的一致性層級的讀取。 根據預設，更新索引同步在每個插入、 [取代] 或 [刪除的文件集合。 這可讓執行相同的一致性層級的文件讀取的查詢。 當 DocumentDB 寫入最佳化，且支援持續的大量文件寫入同步索引維護與做一致的查詢時，您可以設定某些延遲更新其索引的集合。 延遲編製索引作業進一步提升寫入效能，而且適用於大量 ingestion 案例工作負載時主要的閱讀大量。  

編製索引的模式|  讀取|  查詢  
-------------|-------|---------
一致 （預設值）|   選取從強式、 界限過時工作階段，或最終|    選取從強式、 界限過時工作階段，或最終|
延遲|   選取從強式、 界限過時工作階段，或最終|    最終  

為已讀取的要求，您可以藉由指定[x ms-一致性等級](https://msdn.microsoft.com/library/azure/mt632096.aspx)要求標頭降低的特定查詢要求的一致性層級。

## <a name="next-steps"></a>後續步驟

如果您想要執行更多的閱讀，瞭解一致性層級和必須做的取捨，我們建議您下列資源︰

-   Doug 張泰睿 「。 複製的資料的一致性說明透過棒球 （影片）。   
[https://www.youtube.com/watch?v=gluIh8zd26I](https://www.youtube.com/watch?v=gluIh8zd26I)
-   Doug 張泰睿 「。 複製的資料的一致性說明透過棒球。   
[http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
-   Doug 張泰睿 「。 弱一致的複製資料的工作階段保證。   
[http://dl.acm.org/citation.cfm?id=383631](http://dl.acm.org/citation.cfm?id=383631)
-   楊新細。 新式散發資料庫系統設計中一致性必須做的取捨︰ 首字放大是部分本文 」。   
[http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html)
-   彼得 Bailis Shivaram 卡塔拉曼、 Michael j。 細、 約瑟夫 M Hellerstein、 離子 Stoica。 Probabilistic 限制實用的部分 Quorums 過時 (PBS)。   
[http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
-   Werner Vogels。 最終一致-檢查。    
[http://allthingsdistributed.com/2008/12/eventually_consistent.html](http://allthingsdistributed.com/2008/12/eventually_consistent.html)


[1]: ./media/documentdb-consistency-levels/consistency-tradeoffs.png
