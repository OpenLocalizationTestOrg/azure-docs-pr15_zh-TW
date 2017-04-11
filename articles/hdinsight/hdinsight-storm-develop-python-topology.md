<properties
   pageTitle="在 HDinsight 的大量拓撲中使用 Python 元件 |Microsoft Azure"
   description="瞭解如何使用從 Python 元件與 Apache 大量上 Azure HDInsight。 您將學習如何使用 Python 元件從兩個 Java，而 Clojure 大量拓撲。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="python"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/27/2016"
   ms.author="larryfr"/>

#<a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>開發 HDInsight 上使用 Python Apache 大量拓撲

Apache 大量支援多種語言，甚至可讓您合併元件從一個拓撲中的多種語言。 在此文件中，您將學習如何使用您 Java 和 Clojure 型大量的拓撲上 HDInsight Python 元件。

##<a name="prerequisites"></a>必要條件

* Python 2.7 或更新版本

* Java JDK 1.7 或更新版本

* [Leiningen](http://leiningen.org/)

##<a name="storm-multi-language-support"></a>大量多語言支援

大量是設計用來搭配 [撰寫使用任何的程式設計語言，不過這需要元件，了解如何使用[大量 Thrift 定義](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift)元件。 Python，模組，提供為可讓您輕鬆地使用大量介面 Apache 大量專案的一部分。 您可以在[https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py)上找到本單元。

由於 Apache 大量執行上 Java 虛擬機器 (JVM) Java 程序，以其他語言撰寫的元件執行為子程序。 在 JVM 中執行的大量位元通訊與使用 JSON 訊息透過 stdin/stdout 傳送這些子程序。 在元件之間的通訊的更多詳細資料可以是文件中[多語系通訊協定](https://storm.apache.org/documentation/Multilang-protocol.html)。

###<a name="the-storm-module"></a>大量模組

大量模組 (https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py，) 提供建立使用大量的 Python 元件時所需的位元。

這會提供像是`storm.emit`發出 tuple，及`storm.logInfo`以寫入記錄檔。 我想要建議您閱讀，此檔案，並瞭解它所提供。

##<a name="challenges"></a>挑戰

使用__storm.py__模組，您可以建立 Python spouts 使用資料和螺栓處理資料，不過使用 Java 或 Clojure 仍撰寫的整體大量拓撲定義線路元件之間的通訊設定。 此外，如果您使用 Java，您必須同時建立做為 Python 元件介面 Java 元件。

另外，由於大量叢集執行分散式方式，您必須確定 Python 元件所需的任何模組可在叢集內的所有工作者節點。 大量不提供任何輕鬆完成這項工作的多語系資源-您可能必須屬於 jar 檔案的拓撲，包括所有的相依性或手動安裝叢集中每個工作者節點相依性。

###<a name="java-vs-clojure-topology-definition"></a>Java 與 Clojure 拓撲定義

兩種方法的定義拓撲，Clojure 是最簡單/乾淨，您可以直接在拓撲定義參考 python 元件。 Java 拓撲定義，您也必須定義 Java 處理等項目宣告 tuple 中的欄位所傳回的 Python 元件的元件。

此文件，以及範例專案說明這兩種方法。

##<a name="python-components-with-a-java-topology"></a>Java 拓撲 Python 元件

> [AZURE.NOTE] 此範例中的[https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) __JavaTopology__目錄中。 這是根據 Maven 專案。 如果您不熟悉 Maven，資訊，請參閱[使用上 HDInsight Apache 大量的開發 java 拓撲](hdinsight-storm-develop-java-topology.md)建立 Maven 專案進行大量拓撲。

Java 為基礎的拓撲使用 Python （或其他 JVM 語言元件） 一開始顯示使用 Java 元件。但如果您看在每一個 Java spouts/螺栓，您會看到類似以下的程式碼︰

    public SplitBolt() {
        super("python", "countbolt.py");
    }

這是 Java 叫用 Python 的位置，並執行指令碼包含實際螺栓邏輯。 Java spouts/螺栓 （適用於此範例中，） 只要宣告 tuple 會發出基礎 Python 元件中的欄位。

實際 Python 檔案會儲存在`/multilang/resources`在此範例中的目錄。 `/multilang`目錄中__pom.xml__參照︰

<resources>
    <resource>
        <!-- Where the Python bits are kept -->
        <directory>${basedir} / multilang</directory>
    </resource>
</resources>

這包含的所有檔案`/multilang`糖將建置從這個專案中的資料夾。

> [AZURE.IMPORTANT] 請注意，這只會指定`/multilang`目錄並不`/multilang/resources`。 大量預期中的非 JVM 資源`resources`目錄，讓內部已讀取。 將此資料夾中的元件可讓您只參照 Java 程式碼中的名稱。 例如， `super("python", "countbolt.py");`。 另一種方法可以把它是大量可看到`resources`根 （/） 存取多語系資源時為目錄。
>
> 此範例專案的`storm.py`模組包含在`/multilang/resources`目錄。

###<a name="build-and-run-the-project"></a>建立並執行的專案

若要從本機執行此專案，就是建立並執行本機模式中使用下列 Maven 命令︰

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCount

若要刪除程序，使用 ctrl + c。

若要執行 Apache 大量 HDInsight 叢集部署專案，請使用下列步驟︰

1. 建立烏柏 jar:

        mvn package

    這會建立一個名為__WordCount-1.0 SNAPSHOT.jar__中檔案`/target`此專案的目錄。

2. Jar 檔案上傳到 Hadoop 叢集使用下列方法之一︰

    * __Linux 型__HDInsight 叢集︰ 使用`scp WordCount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:WordCount-1.0-SNAPSHOT.jar`jar 檔案複製到叢集，使用者名稱與 SSH 使用者名稱和 CLUSTERNAME 取代 HDInsight 叢集名稱。

        上傳完成檔案之後，連線到使用 SSH 叢集，並開始拓撲使用`storm jar WordCount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount wordcount`

    * 若為__Windows 型__HDInsight 叢集︰ 在瀏覽器中，前往 HTTPS://CLUSTERNAME.azurehdinsight.net/ 連線到大量儀表板。 CLUSTERNAME 取代 HDInsight 叢集名稱，並提供的管理員名稱和密碼出現提示時。

        使用表單，請執行下列動作︰

        * __Jar 檔案__︰ 選取 [__瀏覽]__，然後選取 [ __WordCount-1.0-SNAPSHOT.jar__檔案
        * __類別名稱__︰ 輸入`com.microsoft.example.WordCount`
        * __其他參數__︰ 輸入好記的名稱，例如`wordcount`識別拓撲

        最後，選取 [啟動拓撲__送出__]。

> [AZURE.NOTE] 大量拓撲啟動之後，會執行，直到停止 （已清除）。若要停止拓撲，請使用 [`storm kill TOPOLOGYNAME`指令從命令列 （SSH 工作階段 Linux 叢集，例如） 或使用大量使用者介面，來選取拓撲，，然後選取 [__刪除__] 按鈕。

##<a name="python-components-with-a-clojure-topology"></a>Python Clojure 拓撲元件

> [AZURE.NOTE] 此範例中的[https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) __ClojureTopology__目錄中。

若要[建立新的 Clojure 專案](https://github.com/technomancy/leiningen/blob/stable/doc/TUTORIAL.md#creating-a-project)使用[Leiningen](http://leiningen.org)建立此拓撲。 之後，所做修改下列 scaffolded 專案︰

* __project.clj__︰ 新增相依性大量，] 和 [排除項目可能會造成問題部署到 HDInsight 伺服器上的項目。
* __資源/資源__︰ Leiningen 會建立預設`resources`目錄，不過檔案儲存在這裡顯示為新增至 [此專案中，從建立 jar 檔案的根目錄，而且大量預期命名子目錄中的檔案`resources`。 以便新增子目錄及 Python 檔案會儲存在`resources/resources`。 在執行階段，這將會視為根 （/） 來存取 Python 元件。
* __src/wordcount/core.clj__︰ 此檔案包含拓撲定義，並從__project.clj__檔案參照。 如需有關如何使用 Clojure 定義大量拓撲的詳細資訊，請參閱[Clojure DSL](https://storm.apache.org/documentation/Clojure-DSL.html)。

###<a name="build-and-run-the-project"></a>建立並執行的專案

__若要建立並執行本機專案__，請使用下列命令︰

    lein clean, run

若要停止拓撲，使用__Ctrl + C__。

__若要建立 uberjar 並部署至 HDInsight__，請使用下列步驟︰

1. 建立包含拓撲及必要的相依性 uberjar:

        lein uberjar

    這會建立新的檔名為`wordcount-1.0-SNAPSHOT.jar`中`target\uberjar+uberjar`目錄。
    
2. 您可以使用下列兩種方法之一來部署和執行 HDInsight 叢集拓撲︰

    * __Linux 型 HDInsight__
    
        1. 將檔案複製到 HDInsight 叢集主節點使用`scp`。 例如︰
        
                scp wordcount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:wordcount-1.0-SNAPSHOT.jar
                
            取代叢集，和 CLUSTERNAME SSH 使用者使用您的 HDInsight 叢集名稱的使用者名稱。
            
        2. 一旦檔案已複製到叢集，使用 SSH 連線到叢集並送出工作。 使用 HDInsight SSH 的詳細資訊，請參閱下列其中一項︰
        
            * [使用 Linux 為基礎的 HDInsight Linux、 Unix，或 OS X SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
            * [從 Windows Linux 為基礎的 HDInsight 搭配使用 SSH](hdinsight-hadoop-linux-use-ssh-windows.md)
            
        3. 連線之後，請使用下列啟動拓撲︰
        
                storm jar wordcount-1.0-SNAPSHOT.jar wordcount.core wordcount
    
    * __Windows 型 HDInsight__
    
        1. 在瀏覽器中，前往 HTTPS://CLUSTERNAME.azurehdinsight.net/ 連線到大量儀表板。 CLUSTERNAME 取代 HDInsight 叢集名稱，並提供的管理員名稱和密碼出現提示時。

        2. 使用表單，請執行下列動作︰

            * __Jar 檔案__︰ 選取 [__瀏覽]__，然後選取 [ __wordcount-1.0-SNAPSHOT.jar__檔案
            * __類別名稱__︰ 輸入`wordcount.core`
            * __其他參數__︰ 輸入好記的名稱，例如`wordcount`識別拓撲

            最後，選取 [啟動拓撲__送出__]。

> [AZURE.NOTE] 大量拓撲啟動之後，會執行，直到停止 （已清除）。若要停止拓撲，請使用 [`storm kill TOPOLOGYNAME`指令從命令列 （SSH 工作階段 Linux 叢集） 或使用大量使用者介面，來選取拓撲，，然後選取 [__刪除__] 按鈕。

##<a name="next-steps"></a>後續步驟

在此文件中您將學會如何使用大量拓撲 Python 元件。 請參閱以其他方式來使用 HDInsight Python 下列文件︰

* [如何使用 Python 串流 MapReduce 工作](hdinsight-hadoop-streaming-python.md)
* [如何使用 Python 使用者定義函數 (UDF) 的豬和登錄區](hdinsight-python.md)
