<properties
    pageTitle="範例 Azure HDInsight 登錄區資料表中的資料 |Microsoft Azure"
    description="向下取樣 Azure HDInsight (Hadopop) 登錄區資料表中的資料"
    services="machine-learning,hdinsight"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="hangzh;bradsev" />

# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Azure HDInsight 登錄區資料表中的範例資料

在本文中，我們會說明如何向下範例使用登錄區查詢 Azure HDInsight 登錄區資料表中儲存的資料。 介紹 popularly 使用的取樣的三種方法︰

* 統一的隨機範例
* 依群組的隨機範例
* Stratified 的範例

**為什麼範例資料？**
如果您要分析的資料集很大，通常是最好的方法向下範例資料，以縮減為較小但代表且更容易管理的大小。 這有助於功能工程、 探索和資料的瞭解程度。 啟用快速原型處理資料函數及電腦學習模型可小組資料科學程序中的角色。

下方的**功能表**主題說明如何將來自各種不同的儲存空間環境的連結。

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

此範例工作是[小組資料科學程序 (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)中的步驟。


## <a name="how-to-submit-hive-queries"></a>提交登錄區查詢的方式
登錄區查詢可以從 Hadoop 命令列主控台在 Hadoop 叢集的送出。 若要這麼做，登入 Hadoop 叢集主節點、 開啟 Hadoop 命令列主控台，並提交從該處的登錄區查詢。 送出登錄區查詢 Hadoop 命令列主控台中的指示，請參閱[如何送出登錄區查詢](machine-learning-data-science-move-hive-tables.md#submit)。

## <a name="uniform"></a>統一的隨機範例
統一的隨機取樣表示在資料集中的每個資料列的取樣相等的機率。 這可以實作藉由新增額外的欄位 rand 資料集內部的 「 選取 」 查詢，和外部的 「 選取 」 查詢中的條件的隨機選取的欄位。

以下是查詢範例︰

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, …, fieldN
    from
        (
        select
            field1, field2, …, fieldN, rand() as samplekey
        from <hive table name>
        )a
    where samplekey<='${hiveconf:sampleRate}'

在這裡，`<sample rate, 0-1>`指定之使用者想要範例的記錄的比例。

## <a name="group"></a>依群組的隨機範例

當取樣分類的資料，您可能要包含或排除所有執行個體的分類變數的一些特定的值。 這是 「 取樣依群組 」 代表什麼意思。
例如，如果您有一個分類的變數 「 狀態 」，NY、 台中、 CA，NJ、 PA 等具有的值，您記錄的相同的狀態會永遠在一起，他們會取樣或不。

以下是範例查詢的範例依群組︰

    SET sampleRate=<sample rate, 0-1>;
    select
        b.field1, b.field2, …, b.catfield, …, b.fieldN
    from
        (
        select
            field1, field2, …, catfield, …, fieldN
        from <table name>
        )b
    join
        (
        select
            catfield
        from
            (
            select
                catfield, rand() as samplekey
            from <table name>
            group by catfield
            )a
        where samplekey<='${hiveconf:sampleRate}'
        )c
    on b.catfield=c.catfield

## <a name="stratified"></a>Stratified 的範例

隨機取樣被 stratified 提供分類變數解答時取得範例會使用的值的分類的位於相同的比例，如上層母體的取得範例所示。 使用相同的範例為上方，假設您的資料具有子母體的狀態，例如 NJ 100 觀察、 NY 60 觀察值，而 「 台北 」 有 300 觀察值。 如果您指定要 0.5 stratified 取樣率，然後取得範例應有大約 50、 30 和 NJ、 NY，以及 「 台北 」 150 觀察分別。

以下是查詢範例︰

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, field3, ..., fieldN, state
    from
        (
        select
            field1, field2, field3, ..., fieldN, state,
            count(*) over (partition by state) as state_cnt,
            rank() over (partition by state order by rand()) as state_rank
        from <table name>
        ) a
    where state_rank <= state_cnt*'${hiveconf:sampleRate}'


更多進階取樣方法，可在登錄區上的資訊，請參閱[LanguageManual 範例](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling)。
