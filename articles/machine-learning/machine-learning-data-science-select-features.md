<properties
    pageTitle="小組資料科學程序中的選取範圍的功能 |Microsoft Azure" 
    description="說明的功能選擇用途，並提供在電腦學習的資料增強程序中的角色的範例。"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="zhangya;bradsev" />


# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>選取範圍中小組資料科學程序 (TDSP) 的功能

本文說明的功能選取項目，並提供其角色的電腦學習資料增強程序中的範例。 這些範例是從 Azure 電腦學習 Studio 繪製。 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


本主題說明的功能選擇用途，並提供其角色的電腦學習資料增強程序中的範例。 這些範例是從 Azure 電腦學習 Studio 繪製。 

工程和，選取範圍的功能是一組件的大綱 TDSP[小組資料科學程序為何？](data-science-process-overview.md)。 功能工程和選取範圍是 TDSP 的**開發功能**步驟的部分。

* **工程功能**︰ 從現有的原始功能的資料，建立其他的相關功能，以增加至學習演算法的預測 power，嘗試此程序。

* **功能選項**︰ 此程序選取原始資料功能鍵子集，以減少訓練問題的維度。

正常**功能工程**會先套用至產生其他功能，而且然後**功能選擇**步驟以利消除無關、 多餘或高度的關聯的功能。


## <a name="filtering-features-from-your-data---feature-selection"></a>從您的資料集功能的選取範圍的篩選功能 

功能選項是常用的訓練資料集的預測模型工作，例如分類或迴歸工作程序。 目標是要從使用一組最小的功能來代表資料中的變異數的最大縮小的尺寸的原始資料集選取功能的子集合。 此功能的子集，然後只功能，包括訓練模型。 功能選項有兩個主要的目的。

* 首先，功能選擇通常會增加分類正確性消除無關、 多餘或高度相關功能。
* 第二，它將會減少讓模型訓練程序更有效率的功能數目。 這是的例如支援向量機器訓練成本的學習特別重要。

雖然功能選取項目會搜尋可減少中用來訓練模型的資料集的功能，但不通常就是依字詞 」 維度縮減 」。 功能選取方法會擷取原始的功能，在資料的子集，而不變更其。  維度縮減方法採用工程可以轉換的原始的功能，因此加以修改的功能。 維度縮減方法包括本金元件分析、 標準的相互關聯分析及單數值分解。

和其他功能選取範圍中的方法受監督內容廣泛套用的類別稱為 「 基礎的篩選功能選項 」。 評估每一個功能與目標屬性之間的關聯性，這些方法套用統計的量值，以將分數指派給每個功能。 然後後面功能由分數，這可能會用來幫助您設定保留或排除特定功能的臨界值的資料。 使用這些方法統計量值的範例包含人員相互關聯、 相互的詳細資訊及 Chi 平方的測試。

在 Azure 電腦學習 Studio 中，有提供功能的選取範圍的模組。 下圖所示，這些模組包含[篩選型功能選擇][filter-based-feature-selection]及[Fisher 線性判別分析][fisher-linear-discriminant-analysis]。

![功能選取範例](./media/machine-learning-data-science-select-features/feature-Selection.png)


例如，考慮使用[篩選型功能選擇][filter-based-feature-selection]模組。 為了方便，我們要繼續使用上述文字採礦範例。 假設我們要建立迴歸模型，建立一組 256 功能的[功能雜湊]之後[feature-hashing]模組和回應變數是 「 欄 1 」，並代表活頁簿檢閱評等範圍從 1 到 5。 設定 」 功能計分方法 「 是 」 Pearson 相互關聯 」，「 目標資料行 」 「 欄 1 」 與 「 所需的功能數目 」 為 50。 然後模組[篩選型功能選擇][filter-based-feature-selection]會產生包含 50 功能的目標屬性 「 欄 1 」 與資料集。 下圖顯示的這個實驗和我們先前所提到的輸入的參數。

![功能選取範例](./media/machine-learning-data-science-select-features/feature-Selection1.png)

下圖顯示的結果的資料集。 每個功能會根據 Pearson 間的關聯性它本身和目標屬性 「 欄 1 」 計分。 使用上方的分數功能會保留。

![功能選取範例](./media/machine-learning-data-science-select-features/feature-Selection2.png)

下圖顯示所選的功能的相對應的分數。

![功能選取範例](./media/machine-learning-data-science-select-features/feature-Selection3.png)

套用此[篩選型功能選擇][filter-based-feature-selection]模組，50 的 256，因為他們有與目標變數 「 欄 1 」 最相關的功能，已選取功能根據計分方法 」 Pearson 相互關聯 」。

## <a name="conclusion"></a>結束時
功能工程和功能選項是兩個經常工程和所選的功能提升效率的訓練課程程序其中包含在資料中嘗試擷取的資訊。 他們也改善 power，這些模型對於分類輸入的資料與其他強行預測感興趣的結果。 功能工程和選取範圍也可以讓更多運算容易處理學習合併。 它會以增強與然後減少的校正或訓練模型所需的功能。 以數學方式讀出]，選取訓練模型的功能是變數的一組最小獨立說明資料中的模式和成功預測結果。

請注意，它不一定不必執行功能工程或功能的選項。 是否需要或不取決於我們有或收集的資料與我們挑選，演算法的實驗目標。

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/
 
