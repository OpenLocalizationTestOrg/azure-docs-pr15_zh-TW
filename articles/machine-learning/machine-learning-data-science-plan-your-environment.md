<properties
    pageTitle="如何識別案例及規劃的進階分析資料處理 |Microsoft Azure"
    description="規劃進階分析考慮一系列的金鑰的問題。"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="bradsev" />


# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>如何識別案例與進階的分析資料處理的計劃

您規劃如何處理資料集的進階的分析設定環境時包含哪些資源？ 本文提供建議一系列的要求，可協助您識別您的狀況的任務和資源相關的問題。 預測分析的高層級的步驟順序大綱[小組資料科學程序 (TDSP) 是什麼？](data-science-process-overview.md)。 這些步驟會要求特定的資源之任務相關特定的案例。 若要找出您的狀況金鑰的問題有關資料邏輯的特性，品質資料集的工具和您想要執行分析的語言。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="logistic-questions-data-locations-and-movement"></a>後勤問題︰ 資料位置及移動
後勤問題有關的**資料來源**、**目標目的地**Azure 中的位置和移動資料，包括排程、 數量與資源相關的需求。 資料可能需要分析程序期間移動數次。 將本機資料移到某些格式的儲存空間，Azure，然後將電腦學習 Studio 可常見的案例。

1. **什麼是您的資料來源？** 是否本機或雲端中？ 例如︰
    - 資料是在 HTTP 的位址可公開使用。
    - 資料位於本機或網路檔案位置。
    - 資料是 SQL Server 資料庫。
    - 資料儲存於 Azure 存放容器

2. **什麼是 Azure 目的地？** 位置是否需要要處理的模型？ 例如︰
    - Azure Blob 儲存體
    - SQL Azure 資料庫
    - SQL Server Azure VM 上
    - HDInsight (Hadoop 上 Azure) 或登錄區資料表
    - Azure 機器學習
    - 裝載 Azure 虛擬硬碟。

3. **您要移動資料如何？** 下列主題中說明的程序及內嵌或將資料載入至各種不同的儲存及處理環境資源。

    -  [載入分析的儲存空間環境中的資料](machine-learning-data-science-ingest-data.md)
    -  [匯入到各種資料來源的 Azure 電腦學習 Studio 訓練資料](machine-learning-data-science-import-data.md)。

4. **資料是否需要移動定期排程或修改在移轉過程中？** 請考慮使用資料需要持續移轉，特別是如果混合式案例的存取兩個內部部署和雲端資源的相關資訊，或當資料交易或需要修改，或將它在移轉過程中的商務邏輯 Azure 資料工廠 (ADF)。 如需詳細資訊，請參閱[移動從 SQL Azure Azure 資料工廠與內部部署 SQL server 資料](machine-learning-data-science-move-sql-azure-adf.md)

5. **要移至 Azure 資料中有多少是？** 很大的資料集可能會超過特定環境的儲存容量。 例如，請參閱討論的大小限制的電腦學習 Studio 下一節。 在這種情況下分析時可使用資料的範例。 如何向下範例各種 Azure 環境中的資料集的詳細資訊，請參閱[小組資料科學程序中的範例資料](machine-learning-data-science-sample-data.md)。


## <a name="data-characteristics-questions-type-format-and-size"></a>資料特性問題︰ 類型、 格式與大小
這些問題是規劃您的儲存空間及處理的環境中，每個適用於各種類型的資料，而且每個有限制特定的重要。

1. **資料類型為何？** 例如︰
    - 數字
    - 分類
    - 字串
    - 二進位

2. **您的資料設定格式的方式？** 例如︰
    - 逗點分隔 (CSV) 或 tab 字元分隔 (TSV) 一般檔案
    - 壓縮或未壓縮
    - Azure blob
    - Hadoop 登錄區資料表
    - SQL Server 資料表

2. **如何大型是您的資料？**
    - 小型︰ 小於 2 GB
    - 中型︰ 超過 2 GB 和小於 10 GB
    - 大型︰ 大於 10 GB

使用 Azure 電腦學習 Studio 環境，例如︰

- 資料格式及支援 Azure 電腦學習 Studio，請參閱[支援的資料格式及資料類型](machine-learning-data-science-import-data.md#data-formats-and-data-types-supported)] 區段的類型的清單。
- Azure 電腦學習 studio 資料限制的資訊，請參閱**如何大型資料集可以為我模組？** [匯入及匯出資料的電腦學習](machine-learning-faq.md#machine-learning-studio-questions)的區段

資訊的使用狀況分析程序中的其他 Azure 服務限制，請參閱[Azure 訂閱及服務限制，配額和限制式](../azure-subscription-service-limits.md)。

## <a name="data-quality-questions-exploration-and-pre-processing"></a>資料品質的問題︰ 探索和預先處理

1. **您知道您資料的相關資訊？** 當您需要取得瞭解基本特性時，請瀏覽資料。 什麼模式或趨勢，展示，範數功能已或遺漏的值數目。 此步驟很重要決定測試處理有需要公式也可能會建議最適當的功能，或輸入的分析，以及制定計劃的其他資料收集的範圍。 計算敘述統計和繪製視覺效果會檢查資料的技巧。 如何瀏覽各種 Azure 環境中的資料集的詳細資訊，請參閱[探索小組資料科學程序中的資料](machine-learning-data-science-explore-data.md)。

2. **資料是否需要預先處理或清除？**
預先處理和清除資料是重要工作，通常必須進行資料集可以有效地使用的電腦學習。 從原始資料經常雜訊，並且不穩定及可能會遺失值。 使用資料模型的可能會產生誤導的結果。 如需說明，請參閱[學習工作，準備好增強型電腦的資料](machine-learning-data-science-prepare-data.md)。

## <a name="tools-and-languages-questions"></a>工具和語言的問題
有很多以下根據哪些語言和開發環境或工具您需要或最 conformable 使用的選項。

1. **您想要用於分析哪些語言？**  
    - R
    - Python
    - SQL

2. **您應該使用什麼工具進行資料分析？**
    - [Microsoft Azure Powershell](powershell-install-configure.md) -管理指令碼語言 Azure 資源所用的指令碼語言。
    - [Azure 機器學習 Studio](machine-learning-what-is-ml-studio/)
    - [大革命狀況分析](http://www.revolutionanalytics.com/revolution-r-open)
    - [RStudio](http://www.rstudio.com)
    - [Visual Studio Python 工具](http://microsoft.github.io/PTVS/)
    - [Anaconda](https://www.continuum.io/why-anaconda)
    - [Jupyter 筆記本](http://jupyter.org/)
    - [Microsoft Power BI](http://powerbi.microsoft.com)


## <a name="identify-your-advanced-analytics-scenario"></a>找出您的進階的分析案例
一旦您回答前一節中，您準備好決定最佳案例符合您的個案。 範例案例所述[的進階分析中 Azure 電腦學習案例](machine-learning-data-science-plan-sample-scenarios.md)。
