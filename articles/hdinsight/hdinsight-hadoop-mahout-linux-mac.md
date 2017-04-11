<properties
    pageTitle="產生建議使用 Mahout 和 Linux 型 HDInsight |Microsoft Azure"
    description="瞭解如何使用學習文件庫的 Apache Mahout 機器產生影片建議使用 Linux HDInsight (Hadoop)。"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="larryfr"/>

#<a name="generate-movie-recommendations-by-using-apache-mahout-with-linux-based-hadoop-in-hdinsight"></a>使用中 HDInsight Linux 為基礎的 Hadoop Apache Mahout 來產生影片建議

[AZURE.INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

瞭解如何使用學習文件庫與 Azure HDInsight [Apache Mahout](http://mahout.apache.org)機器產生影片建議。

Mahout 是[電腦學習][ ml] Apache Hadoop 的文件庫。 Mahout 包含演算法來處理資料，例如篩選、 分類和叢集。 本文中，您會使用建議引擎產生影片建議，為您的朋友過的影片。

> [AZURE.NOTE] 這份文件中的步驟需要 HDInsight 叢集上的 Linux 型 Hadoop。 在使用 Windows 型叢集 Mahout 資訊，請參閱[使用與 windows 中 HDInsight 的 Hadoop Apache Mahout 產生影片建議](hdinsight-mahout.md)

##<a name="prerequisites"></a>必要條件

* HDInsight 叢集上 Linux 型 Hadoop。 建立一個的相關資訊，請參閱[使用 Linux 為基礎的 Hadoop HDInsight 中快速入門][getstarted]

##<a name="mahout-versioning"></a>Mahout 版本設定

如需有關 Mahout HDInsight 叢集隨附的版本的詳細資訊，請參閱[HDInsight 版本和 Hadoop 元件](hdinsight-component-versioning.md)。

> [AZURE.WARNING] 雖然可以在不同版本的 Mahout 其上傳到 HDInsight 叢集，完全支援 HDInsight 叢集所提供的元件，以及 Microsoft 支援服務會協助找出並解決這些元件的相關問題。
>
> 自訂元件收到合理的支援，以協助您進一步疑難排解問題。 這可能會導致解決問題，或詢問要加入找到該技術深專業知識的位置開啟來源技術可用的頻道。 例如，有許多都可以使用，這樣的社群網站︰[適用於 HDInsight MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight)、 [http://stackoverflow.com](http://stackoverflow.com)。 也 Apache 專案有[http://apache.org](http://apache.org)，在專案網站，例如︰ [Hadoop](http://hadoop.apache.org/)、[火花](http://spark.apache.org/)。

##<a name="recommendations"></a>了解建議

其中一個函數所提供的 Mahout 是建議引擎。 這個引擎接受的格式中的資料`userID`， `itemId`，及`prefValue`（項目使用者喜好設定）。 Mahout 可以再執行共同所分析，以決定︰_使用者擁有的項目喜好設定也有的偏好設定，這些項目_。 Mahout 再決定類似項目喜好設定，可以用來進行建議的使用者。

以下是使用影片非常簡單範例︰

* __共同所__︰ 王 Alice，與百勝所有按讚_星形戰爭_、_帝國它回_，並_傳回的 Jedi_。 Mahout 會決定也喜歡這些影片的任何一個使用者等其他兩個。

* __共同所__︰ 百勝並 Alice 也按讚_造成虛設威脅__的複製攻擊_，與_復仇 Sith_。 Mahout 會決定使用者對按讚先前的三個影片也想這三個。

* __相似性建議__︰ 因為王按讚的前三個影片，Mahout 查看影片的其他具有類似的喜好設定按讚，但王不監視 （按讚/評等）。 在此情況下，Mahout 建議_造成虛設威脅__的複製攻擊_，與_復仇 Sith_。

###<a name="understanding-the-data"></a>了解資料

在此便利地， [GroupLens 研究][movielens]提供影片與 Mahout 相容的格式的評等資料。 此資料才會提供您的叢集預設儲存空間`/HdiSamples/HdiSamples/MahoutMovieData`。

有兩個檔案， `moviedb.txt` （影片，瞭解） 和`user-ratings.txt`。 在分析期間，使用使用者 ratings.txt 檔案，而 moviedb.txt 用來顯示分析的結果時提供方便使用的文字資訊。

在 [使用者 ratings.txt 中所包含的資料結構的`userID`， `movieID`， `userRating`，和`timestamp`，它會告訴我們如何高度每位使用者評等影片。 以下是資料的範例︰


    196 242 3   881250949
    186 302 3   891717742
    22  377 1   878887116
    244 51  2   880606923
    166 346 1   886397596

##<a name="run-the-analysis"></a>執行分析

使用下列命令以執行建議工作︰

    mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp

> [AZURE.NOTE] 工作可能需要幾分鐘才能完成此項目，並可能會執行多個 MapReduce 工作。

##<a name="view-the-output"></a>檢視輸出

1. 工作完成後，使用下列命令來檢視所產生的輸出︰

        hdfs dfs -text /example/data/mahoutout/part-r-00000

    輸出會出現，如下所示︰

        1   [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2   [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3   [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4   [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    第一欄是`userID`。 中包含的值 」 [「 及 」] 」 會`movieId`:`recommendationScore`。

2. 您可以使用的輸出，以及 moviedb.txt，以顯示使用者好記的詳細資訊。 首先，我們需要複製檔案的本機使用下列命令︰

        hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
        hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .

    這會將輸出資料複製到名稱為**recommendations.txt**目前的目錄，以及影片資料檔案中的檔案。

3. 若要建立新的 Python 指令碼的影片名稱建議輸出中的資料將查詢中使用下列命令︰

        nano show_recommendations.py

    編輯器] 中開啟時，請使用下列檔案的內容為︰

        #!/usr/bin/env python

        import sys

        if len(sys.argv) != 5:
                print "Arguments: userId userDataFilename movieFilename recommendationFilename"
                sys.exit(1)

        userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

        print "Reading Movies Descriptions"
        movieFile = open(movieFilename)
        movieById = {}
        for line in movieFile:
                tokens = line.split("|")
                movieById[tokens[0]] = tokens[1:]
        movieFile.close()

        print "Reading Rated Movies"
        userDataFile = open(userDataFilename)
        ratedMovieIds = []
        for line in userDataFile:
                tokens = line.split("\t")
                if tokens[0] == userId:
                        ratedMovieIds.append((tokens[1],tokens[2]))
        userDataFile.close()

        print "Reading Recommendations"
        recommendationFile = open(recommendationFilename)
        recommendations = []
        for line in recommendationFile:
                tokens = line.split("\t")
                if tokens[0] == userId:
                        movieIdAndScores = tokens[1].strip("[]\n").split(",")
                        recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
                        break
        recommendationFile.close()

        print "Rated Movies"
        print "------------------------"
        for movieId, rating in ratedMovieIds:
                print "%s, rating=%s" % (movieById[movieId][0], rating)
        print "------------------------"

        print "Recommended Movies"
        print "------------------------"
        for movieId, score in recommendations:
                print "%s, score=%s" % (movieById[movieId][0], score)
        print "------------------------"

    按下**Ctrl X**、 **Y**和最後**Enter**儲存的資料。

3. 若要將可執行檔案中使用下列命令︰

        chmod +x show_recommendations.py

4. 執行 Python 指令碼。 下列步驟假設您已在目錄已下載的所有檔案的位置︰

        ./show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt

    這會看到系統的使用者識別碼 4 產生的建議。

    * **使用者 ratings.txt**檔案用來擷取使用者已按比例分配的影片
    * **Moviedb.txt**檔案用來擷取影片的名稱
    * **Recommendations.txt**用來擷取此使用者的影片建議

    這個命令的輸出會類似下列動作︰

        Reading Movies Descriptions
        Reading Rated Movies
        Reading Recommendations
        Rated Movies
        ------------------------
        Mimic (1997), rating=3
        Ulee's Gold (1997), rating=5
        Incognito (1997), rating=5
        One Flew Over the Cuckoo's Nest (1975), rating=4
        Event Horizon (1997), rating=4
        Client, The (1994), rating=3
        Liar Liar (1997), rating=5
        Scream (1996), rating=4
        Star Wars (1977), rating=5
        Wedding Singer, The (1998), rating=5
        Starship Troopers (1997), rating=4
        Air Force One (1997), rating=5
        Conspiracy Theory (1997), rating=3
        Contact (1997), rating=5
        Indiana Jones and the Last Crusade (1989), rating=3
        Desperate Measures (1998), rating=5
        Seven (Se7en) (1995), rating=4
        Cop Land (1997), rating=5
        Lost Highway (1997), rating=5
        Assignment, The (1997), rating=5
        Blues Brothers 2000 (1998), rating=5
        Spawn (1997), rating=2
        Wonderland (1997), rating=5
        In & Out (1997), rating=5
        ------------------------
        Recommended Movies
        ------------------------
        Seven Years in Tibet (1997), score=5.0
        Indiana Jones and the Last Crusade (1989), score=5.0
        Jaws (1975), score=5.0
        Sense and Sensibility (1995), score=5.0
        Independence Day (ID4) (1996), score=5.0
        My Best Friend's Wedding (1997), score=5.0
        Jerry Maguire (1996), score=5.0
        Scream 2 (1997), score=5.0
        Time to Kill, A (1996), score=5.0
        Rock, The (1996), score=5.0
        ------------------------

##<a name="delete-temporary-data"></a>刪除暫存的資料

Mahout 工作不會移除暫存處理工作時所建立的資料。 `--tempDir`範例工作到特定的路徑，輕鬆地刪除隔離暫存檔案中指定參數。 若要移除的暫存檔案，請使用下列命令︰

    hdfs dfs -rm -f -r /temp/mahouttemp

> [AZURE.WARNING] 如果您想要一次執行命令，您也必須刪除輸出目錄。 若要刪除此目錄使用下列動作︰
>
> ```hdfs dfs -rm -f -r /example/data/mahoutout```

## <a name="next-steps"></a>後續步驟

現在，您已經學會如何使用 Mahout，探索 HDInsight 的資料使用的其他的方法︰

* [使用 HDInsight 登錄區](hdinsight-use-hive.md)
* [HDInsight 的豬](hdinsight-use-pig.md)
* [使用 HDInsight MapReduce](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
 
