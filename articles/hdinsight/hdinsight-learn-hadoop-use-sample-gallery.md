<properties
   pageTitle="瞭解在 [範例] 圖庫的 HDInsight Hadoop |Microsoft Azure"
   description="快速瞭解 Hadoop 從 HDInsight 快速入門圖庫執行範例應用程式。 使用範例資料，或提供您自己。"
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.workload="big-data"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/21/2016"
   ms.author="jgao"/>

# <a name="learn-hadoop-by-using-the-azure-hdinsight-getting-started-gallery"></a>瞭解 Hadoop 使用 Azure HDInsight 快速入門圖庫

[快速入門] 庫只適用於 Windows 型 HDInsight 叢集。 庫提供易並快速瞭解 Hadoop HDInsight 中執行範例應用程式。 某些範例了範例資料。 您可以提供剩餘的範例資料。 目前，有下列六個範例 （含多個來自）︰

- Azure 資料的解決方案
    - Microsoft Azure 網站記錄分析
    - Microsoft Azure 儲存體狀況分析
- 含有範例資料的解決方案
    - 感應器的資料分析
    - Twitter 分析趨勢
    - 網站記錄分析
    - Mahout 影片建議

![HDInsight Hadoop 大量，與 HBase 快速入門庫解決方案，包括範例資料。][hdinsight.sample.gallery]

以下影片示範如何執行 Twitter 趨勢分析範例︰

<center><a href="https://www.youtube.com/embed/7ePbHot1SN4">https://www.youtube.com/embed/7ePbHot1SN4></a></center>

儀表板可存取瀏覽至 http://<YourHDInsightClusterName>.azurehdinsight.net/ 或從 Azure 入口網站。

**若要從 [快速入門] 庫中執行樣本**

1. [Azure 入口網站]登入[azure.portal]。
2. 從左側功能表中按一下 [**瀏覽]** ，按一下 [ **HDInsight 叢集**，，再按一下 [叢集名稱。
3. 從上方的功能表中按一下 [**儀表板**]。
4. 輸入使用者名稱和密碼 HTTP 使用者 （也稱為叢集使用者）。
6. 按一下 [**快速入門圖庫**頂端的頁面]。
7. 按一下其中一個範例。 每個範例提供詳細的步驟執行。 下圖顯示 Twitter 趨勢分析範例︰

    ![HDInsight Twitter 趨勢分析範例][hdinsight.twitter.sample]

## <a name="next-steps"></a>後續步驟
若要深入瞭解 HDInsight 的其他方法，包括︰

- [HDInsight 學習地圖][hdinsight.learn.map]
- [HDInsight infographic][hdinsight.infographic]

<!--Image references-->
[hdinsight.sample.gallery]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Getting-Started-Gallery.png
[hdinsight.twitter.sample]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Twitter-Trend-Analysis-sample.png

<!--Link references-->
[hdinsight.learn.map]: https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/
[hdinsight.infographic]: http://go.microsoft.com/fwlink/?linkid=523960
[azure.portal]:https://portal.azure.com
