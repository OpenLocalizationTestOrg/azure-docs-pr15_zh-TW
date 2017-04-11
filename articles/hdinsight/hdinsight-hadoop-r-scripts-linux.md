<properties
    pageTitle="安裝 R 上 Linux 型 HDInsight |Microsoft Azure"
    description="瞭解如何安裝和使用 R 自訂 Linux 型 Hadoop 叢集。"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="larryfr"/>

# <a name="install-and-use-r-on-hdinsight-hadoop-clusters"></a>安裝並使用 R HDInsight Hadoop 叢集上

使用**指令碼動作**叢集自訂，您可以在任何類型的中叢集 Hadoop HDInsight 上安裝 R。 這可讓資料科學家和分析師使用 R 部署功能強大的 MapReduce/YARN 程式設計架構，處理 Hadoop 叢集 HDInsight 中部署上大量的資料。

> [AZURE.IMPORTANT] [進階版層](https://azure.microsoft.com/pricing/details/hdinsight/)提供的 HDInsight 包含 R 伺服器 HDInsight 叢集的一部分。 這個選項可讓使用 MapReduce 和火花來執行分散的運算 R 指令碼。 如需詳細資訊，請參閱[開始使用 HDInsight R 伺服器](hdinsight-hadoop-r-server-get-started.md)。 


## <a name="what-is-r"></a>R 是什麼？

<a href="http://www.r-project.org/" target="_blank">R 專案統計計算</a>是開啟來源語言] 和 [統計計算環境。 R 提供數百個內建統計函數和結合層面的功能和物件導向程式設計自己程式設計語言。 同時也會提供更多圖形功能。 R 是慣用的程式設計環境最專業統計學家和科學家在各種不同的欄位。

您可以執行自訂安裝 R 環境中使用指令碼動作時所建立的 Hadoop 叢集中 HDInsight R 指令碼。 R 是相容的 Azure Blob 儲存體 (WASB)，使其可以使用 HDInsight R 處理有儲存的資料。

## <a name="what-the-script-does"></a>指令碼功能

用來安裝 R HDInsight 叢集上的指令碼動作安裝提供基本的 R 安裝下列 Ubuntu 套件︰

* [r 基底](http://packages.ubuntu.com/precise/r-base)︰ 基底 GNU R 套件
* [r-基底-開發](http://packages.ubuntu.com/precise/r-base-dev)︰ Auxilliary GNU R 套件

下列 RHadoop 套件，也會安裝，其中提供 MapReduce 與 HDFS 的整合︰

* [rmr2](https://github.com/RevolutionAnalytics/rmr2)︰ 可讓 R 開發人員使用 Hadoop MapReduce
* [rhdfs](https://github.com/RevolutionAnalytics/rhdfs)︰ 可讓 R 開發人員使用 Hadoop HDFS (WASB HDInsight 的)

此外，已安裝下列 R 套件︰

| R 套件 | 它所提供 |
| --------- | ---------------- |
| [rJava](https://cran.r-project.org/web/packages/rJava/index.html) | 低層級 R Java 介面。 |
| [Rcpp](https://cran.r-project.org/web/packages/Rcpp/index.html) | R 和 c + + 整合。 |
| [RJSONIO](https://cran.r-project.org/web/packages/RJSONIO/index.html) | 序列化/還原序列化 JSON R 物件 |
| [bitops](https://cran.r-project.org/web/packages/bitops/index.html) | 位元整數向量運算的函數。 |
| [摘要](https://cran.r-project.org/web/packages/digest/index.html) | 建立密碼雜湊摘要的 R 物件。 |
| [功能](https://cran.r-project.org/web/packages/functional/index.html) | Curry、 撰寫和其他高順序函數 |
| [reshape2](https://cran.r-project.org/web/packages/reshape2/index.html) | 彈性重建及彙總資料。 |
| [stringr](https://cran.r-project.org/web/packages/stringr/index.html) | 常見的字串運算的簡單且一致包裝。 |
| [plyr](https://cran.r-project.org/web/packages/plyr/index.html) | 分割、 套用及合併資料的工具。 |
| [caTools](https://cran.r-project.org/web/packages/caTools/index.html) | 移動視窗統計資料，GIF、 Base64 巨鵬 AUC 工具等。 |
| [stringdist](https://cran.r-project.org/web/packages/stringdist/index.html) | 估算符合字串與字串距離函數。 |

## <a name="install-r-using-script-actions"></a>安裝 R 使用指令碼的動作

下列指令碼動作用來安裝 R HDInsight 叢集上。 https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
    
本節提供有關如何建立新的叢集使用 Azure 入口網站時，使用指令碼的指示。 

> [AZURE.NOTE] Azure PowerShell 的 Azure CLI、 HDInsight.NET sdk，您可以 Azure 資源管理員範本也可套用指令碼動作。 您也可以執行叢集套用指令碼動作。 如需詳細資訊，請參閱[自訂 HDInsight 叢集指令碼動作](hdinsight-hadoop-customize-cluster-linux.md)。

1. 開始佈建叢集，依照步驟中[佈建 Linux 型 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md#portal)，但未完成佈建中。

2. 在**選擇性組態**刀中，選取 [**指令碼動作**]，然後提供以下資訊︰

    * __名稱__︰ 輸入指令碼動作易記的名稱。
    * __指令碼 URI__: https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
    * __不對__︰ 核取此選項
    * __工作__︰ 核取此選項
    * __動物園管理員__︰ 核取此選項即可在動物園管理員節點上安裝。
    * __參數__︰ 將此欄位保留空白

3. 在**指令碼動作**底部，使用 [**選取**] 按鈕儲存設定。 最後，使用**選用設定**刀底部的 [**選取**] 按鈕，儲存的選用的設定資訊。

4. 繼續佈建叢集，[佈建 Linux 型 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md#portal)所述。

## <a name="run-r-scripts"></a>執行 R 指令碼

叢集完成佈建後，請使用下列步驟執行 MapReduce 運算叢集上的使用 R。

1. 連線到使用 SSH HDInsight 叢集︰

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    如需有關如何使用 HDInsight SSH 的詳細資訊，請參閱下列各項︰

    * [使用上 HDInsight Linux、 Unix，或 OS X 的 Linux 型 Hadoop SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [使用上從 Windows HDInsight Linux 為基礎的 Hadoop SSH](hdinsight-hadoop-linux-use-ssh-windows.md)

2. 從`username@hn0-CLUSTERNAME:~$`提示中，輸入下列命令以啟動互動式 R 工作階段︰

        R

3. 輸入下列 R 程式。 這會產生的數字 1 到 100，然後將其乘以 2。

        library(rmr2)
        ints = to.dfs(1:100)
        calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))

    第一行通話 RHadoop 文件庫 rmr2，用於 MapReduce 作業。

    第二行會產生值 1-100，然後將其儲存到 Hadoop 檔案系統使用`to.dfs`。

    第三行建立 MapReduce 程序使用 rmr2，所提供的功能，並開始進行處理。 您應該會看到幾行捲動過去的處理程序開始。

4. 接下來，請使用下列命令以查看 MapReduce 輸出已儲存的暫時路徑︰

        print(calc())

    這個應該類似`/tmp/file5f615d870ad2`。 若要檢視的實際輸出，請使用下列動作︰

        print(from.dfs(calc))

    輸出看起來應該像這樣︰

        [1,]  1 2
        [2,]  2 4
        .
        .
        .
        [98,]  98 196
        [99,]  99 198
        [100,] 100 200

5. 若要結束 R，輸入下列資訊︰

        q()


## <a name="next-steps"></a>後續步驟

- [色調 HDInsight 上的安裝並使用叢集](hdinsight-hadoop-hue-linux.md)。 色調是 web 輕鬆建立、 執行及儲存的豬和登錄區工作，以及您 HDInsight 瀏覽預設儲存空間叢集的使用者介面。

- [安裝的 Giraph HDInsight 叢集上](hdinsight-hadoop-giraph-install.md)。 使用叢集自訂安裝 Giraph HDInsight Hadoop 叢集上。 Giraph 可讓您執行圖形處理使用 Hadoop，並使用 Azure HDInsight。

- [安裝的 Solr HDInsight 叢集上](hdinsight-hadoop-solr-install.md)。 使用叢集自訂安裝 Solr HDInsight Hadoop 叢集上。 Solr 可讓您執行強大的搜尋作業上儲存的資料。

- [HDInsight 叢集上安裝的色調](hdinsight-hadoop-hue-linux.md)。 使用叢集自訂安裝選項可設定色調 HDInsight Hadoop 叢集上。 色調是一組用來與 Hadoop 叢集互動的 Web 應用程式。

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
