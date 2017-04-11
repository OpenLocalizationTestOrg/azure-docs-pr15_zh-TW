<properties
   pageTitle="計算 R 伺服器上 HDInsight （預覽版本） 的內容選項 |Microsoft Azure"
   description="瞭解不同的計算內容提供的選項與 R 伺服器的使用者 HDInsight （預覽版本）"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="jhubbard"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="10/18/2016"
   ms.author="jeffstok"
/>

# <a name="compute-context-options-for-r-server-on-hdinsight-preview"></a>計算 R 伺服器上 HDInsight （預覽版本） 的內容選項

Microsoft Azure HDInsight （預覽版本） 上的 R Server 提供 R 型分析的最新功能。 它會使用 HDFS 的容器中您[Azure Blob](../storage/storage-introduction.md "Azure Blob 儲存體")儲存的帳戶或本機 Linux 檔案系統中所儲存的資料。 內建 R 伺服器上開啟來源 R，因為您建立的 R 型應用程式可以利用任何 8000 + R 的開啟來源套件。 他們也可以使用的常式[ScaleR](http://www.revolutionanalytics.com/revolution-r-enterprise-scaler "大革命分析 ScaleR")，包含 R 伺服器的 Microsoft 的大型資料分析套件。  

進階版叢集邊緣節點提供叢集連線並進行 R 指令碼方便的位置。 邊緣節點，您可以在邊緣節點伺服器的核心之間執行 ScaleR 的平行化分散式的函數的選擇。 您也可以選擇透過 ScaleR 的 Hadoop 地圖減少整個叢集節點執行這些或火花計算內容。

## <a name="compute-contexts-for-an-edge-node"></a>計算邊緣節點的內容

一般而言，R 上邊緣節點 R 伺服器中執行上執行指令碼 R 手語翻譯中的節點。 例外是呼叫 ScaleR 函數這些步驟。 設定 ScaleR 計算內容的方式取決於計算環境中，執行 ScaleR 來電。  當您從邊緣節點執行 R 指令碼時，計算內容的可能值是本機連續 （「 本機 」），本機平行 (「 localpar 」)、 地圖降低，及火花。

「 區域 」 和 「 localpar 」 選項只有不同執行 rxExec 來電的方式。 這兩個其他接收函數呼叫平行能在所有可用的核心之間除非執行透過使用 ScaleR numCoresToUse 選項，例如 rxOptions(numCoresToUse=6) 否則指定。 以下摘要列出各種不同的計算內容選項

| 計算的內容  | 如何設定                      | 執行內容                                                                     |
|------------------|---------------------------------|---------------------------------------------------------------------------------------|
| 本機循序進行 | rxSetComputeContext('local')    | 平行化的執行跨核心邊緣節點伺服器，除了 rxExec 通話的循序執行 |
| 本機平行   | rxSetComputeContext('localpar') | 平行化的執行跨核心節點 edge server                                 |
| 火花            | RxSpark()                       | 平行處理分散式透過火花跨節點的執行 HDI 叢集      |
| 減少地圖       | RxHadoopMR()                    | 平行處理分散式透過地圖減少跨節點的執行 HDI 叢集 |


假設您想要平行化的執行效能的然後有三個選項。 您選擇哪個選項取決於性質分析工作，以及大小及位置的資料。

## <a name="guidelines-for-deciding-on-a-compute-context"></a>決定的計算內容的指導方針

目前尚不會告訴您的計算內容若要使用的公式。 有，不過，可協助您做出適當的選擇，或至少有幫助您縮小您的選擇，才能執行基準一些指導原則。 這些引導原則功能包括︰

1.  本機 Linux 檔案系統是較快，HDFS。
2.  重複的分析為更快速地和如果資料是本機，如果 XDF]。
3.  最好是串流少量的資料來源的文字; 的資料如果較大資料量，將它轉換 XDF 之前分析。
4.  複製或串流到邊緣節點的分析資料的負荷會變成無法管理為有大量的資料。
5.  火花會比地圖減少的分析 Hadoop 快所示。

指定原則，某些一般法則選取的計算內容是︰

### <a name="local"></a>本機

- 如果要分析的資料量小，不需要重複的分析，然後將它直接將分析常式串流，並使用 「 本機' 或 'localpar'。
- 如果要分析的資料量有小型或中型，因而必須重複的分析，將其複製到本機檔案系統、 匯入 XDF，然後透過本機' 或 'localpar' 進行分析。

### <a name="hadoop-spark"></a>Hadoop 火花

- 如果要分析的資料量很大，再匯入至 XDF 中 HDFS （除非儲存空間是問題），及分析透過 「 火花 」。

### <a name="hadoop-map-reduce"></a>減少 Hadoop 地圖

- 由於通常會變慢，使用使用盡如克服問題發生時，才會計算內容。  

## <a name="inline-help-on-rxsetcomputecontext"></a>內嵌 rxSetComputeContext 說明

如需的資訊與範例 ScaleR 計算內容，請參閱協助 R rxSetComputeContext 方法，例如內嵌的︰

    > ?rxSetComputeContext

您也可以參閱 「 ScaleR 分散運算指南 」，您也可以從[R 伺服器 MSDN](https://msdn.microsoft.com/library/mt674634.aspx "R 伺服器 MSDN 上")的文件庫。


## <a name="next-steps"></a>後續步驟

本文中，您學到如何建立新的 HDInsight 叢集包含 R 伺服器。 您也可以瞭解使用 R 主控台 SSH 工作階段中的基本概念。 現在您可以閱讀下列文章，探索 HDInsight R 伺服器所使用的其他方法︰

- [Hadoop R 伺服器的概觀](hdinsight-hadoop-r-server-overview.md)
- [開始使用 R 伺服器 Hadoop 版](hdinsight-hadoop-r-server-get-started.md)
- [新增 RStudio 伺服器 HDInsight premium](hdinsight-hadoop-r-server-install-r-studio.md)
- [Azure 存放區上的選項 R 伺服器 HDInsight 進階版](hdinsight-hadoop-r-server-storage.md)
