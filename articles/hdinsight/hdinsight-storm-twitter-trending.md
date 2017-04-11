<properties
   pageTitle="Twitter 上 HDInsight Apache 大量與趨勢主題 |Microsoft Azure"
   description="瞭解如何使用戟建立 Apache 大量拓撲決定根據 hashtags Twitter 上的趨勢主題。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="larryfr"/>

#<a name="determine-twitter-trending-topics-with-apache-storm-on-hdinsight"></a>決定 Twitter 上 HDInsight Apache 大量與趨勢的主題

瞭解如何使用戟建立大量拓撲決定 Twitter 上的趨勢主題 （雜湊標籤）。

戟是提供的工具，例如連接、 彙總、 群組、 函數和篩選高層級抽象。 此外，戟將基本項目執行動作狀態、 累加處理。 此範例會示範如何建立拓撲，使用自訂的 spout、 函數與多個戟所提供的內建函數。

> [AZURE.NOTE] 此範例中重度根據[戟大量](https://github.com/jalonsoramos/trident-storm)範例來阿爾 Alonso。

##<a name="requirements"></a>需求

* <a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" target="_blank">Java 和 JDK 1.7</a>

* <a href="http://maven.apache.org/what-is-maven.html" target="_blank">Maven</a>

* <a href="http://git-scm.com/" target="_blank">給</a>

* Twitter 開發人員帳戶

##<a name="download-the-project"></a>下載的專案

您可以使用下列程式碼複製本機專案。

    git clone https://github.com/Blackmist/TwitterTrending

##<a name="topology"></a>拓撲

在這個範例拓撲如下所示︰

![拓撲](./media/hdinsight-storm-twitter-trending/trident.png)

> [AZURE.NOTE] 這是拓撲簡化的檢視。 多個元件執行個體中的分佈，叢集節點。

實作拓撲戟程式碼如下所示︰

    topology.newStream("spout", spout)
        .each(new Fields("tweet"), new HashtagExtractor(), new Fields("hashtag"))
        .groupBy(new Fields("hashtag"))
        .persistentAggregate(new MemoryMapState.Factory(), new Count(), new Fields("count"))
        .newValuesStream()
        .applyAssembly(new FirstN(10, "count"))
        .each(new Fields("hashtag", "count"), new Debug());

將此程式碼會執行下列動作︰

1. 從 spout 中建立新的資料流。 Spout 從 Twitter 擷取推文，並篩選這些特定關鍵字 （愛、 音樂和咖啡在此範例中）。

2. HashtagExtractor，自訂的函數，用於每個貼入 tweet 中擷取雜湊標籤。 這些被發出資料流。

3. 分組雜湊標籤，並傳遞至彙總資料流。 此彙總所建立每一個雜湊標籤發生的次數的計數。 此資料保存在記憶體中。 最後，新的資料流發出含有雜湊標籤與計數。

4. 我們只感興趣的某一批傳出 tweets 最常用的雜湊標籤，因為**FirstN**組件會套用至傳回只前 10 個根據的值，計算欄位。

> [AZURE.NOTE] 非 spout 和 HashtagExtractor，我們會使用內建戟功能。
>
> 內建的作業的相關資訊，請參閱<a href="https://storm.apache.org/apidocs/storm/trident/operation/builtin/package-summary.html" target="_blank">套件 storm.trident.operation.builtin</a>。
>
> 戟狀態實作 MemoryMapState 以外，請參閱下列內容︰
>
> * <a href="https://github.com/fhussonnois/storm-trident-elasticsearch" target="_blank">大量戟彈性搜尋</a>
>
> * <a href="https://github.com/kstyrc/trident-redis" target="_blank">戟意指</a>

###<a name="the-spout"></a>Spout

Spout， **TwitterSpout**，使用<a href="http://twitter4j.org/en/" target="_blank">Twitter4j</a>從 Twitter 擷取推文。 篩選會建立 （愛、 音樂和在此範例中的咖啡），並會儲存在連結封鎖佇列中傳入推文 （狀態） 符合篩選器。 （如需詳細資訊，請參閱<a href="http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/LinkedBlockingQueue.html" target="_blank">課程 LinkedBlockingQueue</a>）。最後，項目會退出佇列，拓撲發出。

###<a name="the-hashtagextractor"></a>HashtagExtractor

若要擷取雜湊標籤， <a href="http://twitter4j.org/javadoc/twitter4j/EntitySupport.html#getHashtagEntities--" target="_blank">getHashtagEntities</a>用來擷取貼入 tweet 中所包含的所有雜湊標籤。 這些然後發出資料流。

##<a name="enable-twitter"></a>啟用 Twitter

使用下列步驟以註冊新 Twitter 應用程式，並取得讀取 Twitter 所需的消費者與存取權杖資訊︰

1. 移至 [ <a href="https://apps.twitter.com" target="_blank">Twitter 應用程式</a>，然後按一下 [**建立新的應用程式**] 按鈕。 當填寫表單中，將 [**回撥 URL** ] 欄位保留空白。

2. 建立應用程式時，按一下 [**索引鍵和存取權杖**] 索引標籤。

3. 複製**消費者索引鍵**和**消費者機密**資訊。

4. 如果沒有權杖存在，請在頁面底部，選取 [**建立我存取權杖**]。 權杖建立之後，將複製的**存取權杖**和**存取權杖機密**資訊。

5. 在您先前複製**TwitterSpoutTopology**專案中，開啟**resources/twitter4j.properties**檔案、 在上一個步驟中，新增您所收集的資訊，然後儲存檔案。

##<a name="build-the-topology"></a>建立拓撲

您可以使用下列程式碼，建立專案︰

        cd [directoryname]
        mvn compile

##<a name="test-the-topology"></a>測試拓撲

若要測試拓撲本機使用下列命令︰

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.TwitterTrendingTopology

啟動拓撲後，您應該會看到偵錯資訊包含雜湊標記，並以拓撲計算發出。 輸出應如下︰

    DEBUG: [Quicktellervalentine, 7]
    DEBUG: [GRAMMYs, 7]
    DEBUG: [AskSam, 7]
    DEBUG: [poppunk, 1]
    DEBUG: [rock, 1]
    DEBUG: [punkrock, 1]
    DEBUG: [band, 1]
    DEBUG: [punk, 1]
    DEBUG: [indonesiapunkrock, 1]

##<a name="next-steps"></a>後續步驟

既然您已測試拓撲本機，瞭解如何部署拓撲︰[部署及管理 Apache 大量拓撲上 HDInsight](hdinsight-storm-deploy-monitor-topology.md)。

您也可能會感興趣的下列大量主題︰

* [在使用 Maven HDInsight 大量開發 Java 拓撲](hdinsight-storm-develop-java-topology.md)

* [大量上 HDInsight 使用 Visual Studio 開發 C# 拓撲](hdinsight-storm-develop-csharp-visual-studio-topology.md)

如需 HDinsight 的更多大量範例︰

* [在 HDInsight 大量的範例拓撲](hdinsight-storm-example-topology.md)
