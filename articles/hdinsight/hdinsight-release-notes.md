<properties
    pageTitle="版本資訊 Hadoop 上的元件 Azure HDInsight |Microsoft Azure"
    description="最新版本的備忘稿和 Azure HDInsight Hadoop 元件的版本。 Hadoop、 Apache 大量及 HBase 取得開發秘訣和詳細資料。"
    services="hdinsight"
    documentationCenter=""
    editor="cgronlun"
    manager="jhubbard"
    authors="nitinme"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="nitinme"/>


# <a name="release-notes-for-hadoop-components-on-azure-hdinsight"></a>Hadoop 上的元件 Azure HDInsight 的版本資訊

## <a name="notes-for-10262016-release-of-r-server-on-hdinsight"></a>備忘稿 10/26 2016 HDInsight R 伺服器版本

- 精簡的 HDInsight 叢集佈建 R 伺服器。
- R 伺服器上 HDInsight 現在是一般 HDInsight 「 R 伺服器 」 叢集類型，並為不同的 HDInsight 應用程式不會再安裝。 邊緣節點並 R 伺服器二進位現在佈建 R 伺服器叢集部署的一部分。 這會改善速度和可靠性佈建。 R 伺服器價格模型也會一併更新。
- R 伺服器叢集類型價格會立即根據標準層價格加上 R 伺服器銷售稅價格。 進階版層現在會保留可用的進階功能在不同的叢集類型，並不會使用 R 伺服器叢集類型。 這項變更並不會影響 R 伺服器的有效價格，它只是變更費用清單中顯示的方式。 所有現有的 R 伺服器叢集會繼續運作，而 ARM 範本會繼續直到取代通知函數。 **建議您雖然以更新您的指令碼的部署使用新的手錶範本。**


## <a name="notes-for-08302016-release-of-r-server-on-hdinsight"></a>備忘稿 08/30/2016 HDInsight R 伺服器版本

Linux 型 HDInsight 叢集部署在這個版本的完整版數字︰

|HDI |HDI 叢集版本   |HDP |HDP 建立   |Ambari 建置 |
|----|----------------------|----|------------|-------------|
|3.2 捨位 |3.2.1000.0.8268980    |2.2 |2.2.9.1-19  |2.2.1.12-4   |
|3.3 |3.3.1000.0.8268980    |2.3 |2.3.3.1-25  |2.2.1.12-4   |
|3.4 |3.4.1000.0.8269383    |2.4 |2.4.2.4-5   |2.2.1.12-4   |

部署在這個版本的 Windows 型 HDInsight 叢集的完整版數字︰

|HDI |HDI 叢集版本   |HDP |HDP 建立     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.1033.2559206   |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.1033.2559206    |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.1033.2559206    |2.1 |2.1.16.0-2374 |
|3.2 捨位 |3.2.7.1033.2559206    |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.1033.2559206    |2.3 |2.3.3.1-25    |

## <a name="notes-for-08172016-release-of-r-server-on-hdinsight"></a>備忘稿 08/17 2016 HDInsight R 伺服器版本

- R 的 Server 8.0.5 – 主要錯誤修正的發行版本。 [R 伺服器版本資訊](https://msdn.microsoft.com/microsoft-r/notes/r-server-notes)的詳細資訊，請參閱。 
- 在邊緣節點 –[這個 R 套件](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html)AzureML 套件可讓 R 模型發佈並為 Azure 毫升 web 服務使用。  請參閱我們的[「 概觀的 R 伺服器上 HDInsight 」](hdinsight-hadoop-r-server-overview.md)文章，取得詳細資訊[」 Operationalize 模型 」](hdinsight-hadoop-r-server-overview.md#operationalize-a-model)區段。
- 的[最常用的 100 上方 R 套件](https://github.com/metacran/cranlogs)-現在已預先安裝套件相依性這些 Linux Linux 相依性。  
- 可以使用 CRAN repo 新增 R 套件到資料節點的選項。 請參閱我們[開始使用 HDInsight R 伺服器]](hdinsight-hadoop-r-server-get-started.md)的文章，取得詳細資訊[」 安裝 R 套件 」](hdinsight-hadoop-r-server-get-started.md#install-r-packages)區段。
- 改善可靠性 R 伺服器佈建建立叢集時。


## <a name="notes-for-08012016-release-of-hdinsight"></a>備忘稿 HDInsight 08/01/2016年版本

Linux 型 HDInsight 叢集部署在這個版本的完整版數字︰

|HDI |HDI 叢集版本   |HDP |HDP 建立   |Ambari 建立 |
|----|----------------------|----|------------|-------------|
|3.2 捨位 |3.2.1000.0.8028416    |2.2 |2.2.9.1-19  |2.2.1.12-4   |
|3.3 |3.3.1000.0.8028416    |2.3 |2.3.3.1-25  |2.2.1.12-4   |
|3.4 |3.4.1000.0.8053402    |2.4 |2.4.2.4-5   |2.2.1.12-4   |

部署在這個版本的 Windows 型 HDInsight 叢集的完整版數字︰

|HDI |HDI 叢集版本   |HDP |HDP 建立     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.1005.2488842   |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.1005.2488842    |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.1005.2488842    |2.1 |2.1.16.0-2374 |
|3.2 捨位 |3.2.7.1005.2488842    |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.1005.2488842    |2.3 |2.3.3.1-25    |

在這個版本包含下列的更新。

| 標題                                           | 描述                                          | 受影響的區域 （例如，服務、 元件或 SDK） | 叢集類型 （例如火花、 Hadoop、 HBase 或大量） | JIRA （如果適用的話） |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| HDInsight 3.4 叢集的變更 | 下列登錄區設定的預設值變更為較佳的效能 <ul><li>`hive.vectorized.execution.reduce.enabled=true`</li><li>`hive.tez.min.partition.factor=1f`</li><li>`hive.tez.max.partition.factor=3f`</li><li>`tez.shuffle-vertex-manager.min-src-fraction=0.9`</li><li>`tez.shuffle-vertex-manager.max-src-fraction=0.95`</li><li>`tez.runtime.shuffle.connect.timeout= 30000`</li></ul>| 服務    | 所有| N/A|
| 在這個版本中包含以下的修正方式 | 登錄區 13632、 HIVE-12897,HIVE-12907,HIVE-12908,HIVE-12988,HIVE-13510,HIVE-13572,HIVE-13716,HIVE-13726,HIVE-12505,HIVE-13632,HIVE-13661,HIVE-13705,HIVE-13743,HIVE-13810,HIVE-13857,HIVE-13902,HIVE-13911,HIVE-13933| 服務    | 所有| N/A

## <a name="notes-for-07142016-release-of-hdinsight"></a>備忘稿 HDInsight 07/14/2016年版本

Linux 型 HDInsight 叢集部署在這個版本的完整版數字︰

|HDI |HDI 叢集版本   |HDP |HDP 建立   |Ambari 建置 |
|----|----------------------|----|------------|-------------|
|3.2 捨位 |3.2.1000.0.7932505    |2.2 |2.2.9.1-11  |2.2.1.12-2   |
|3.3 |3.3.1000.0.7932505    |2.3 |2.3.3.1-18  |2.2.1.12-2   |
|3.4 |3.4.1000.0.7933003    |2.4 |2.4.2.0     |2.2.1.12-2   |

部署在這個版本的 Windows 型 HDInsight 叢集的完整版數字︰

|HDI |HDI 叢集版本   |HDP |HDP 建立     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.989.2441725    |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.989.2441725     |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.989.2441725     |2.1 |2.1.16.0-2374 |
|3.2 捨位 |3.2.7.989.2441725     |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.989.2441725     |2.3 |2.3.3.1-21    |

## <a name="notes-for-07072016-release-of-hdinsight"></a>備忘稿 HDInsight 07/07/2016年版本

Linux 型 HDInsight 叢集部署在這個版本的完整版數字︰

|HDI |HDI 叢集版本   |HDP |HDP 建立   |
|----|----------------------|----|------------|
|3.2 捨位 |3.2.1000.0.7864996    |2.2 |2.2.9.1-11  |
|3.3 |3.3.1000.0.7864996    |2.3 |2.3.3.1-18  |
|3.4 |3.4.1000.0.7861906    |2.4 |2.4.2.0     |

部署在這個版本的 Windows 型 HDInsight 叢集的完整版數字︰

|HDI |HDI 叢集版本   |HDP |HDP 建立     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.977.2413853    |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.977.2413853     |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.977.2413853     |2.1 |2.1.16.0-2374 |
|3.2 捨位 |3.2.7.977.2413853     |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.977.2413853     |2.3 |2.3.3.1-21    |

在這個版本包含下列的更新。

| 標題                                           | 描述                                          | 受影響的區域 （例如，服務、 元件或 SDK） | 叢集類型 （例如火花、 Hadoop、 HBase 或大量） | JIRA （如果適用的話） |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| [IntelliJ HDInsight 工具](hdinsight-apache-spark-intellij-tool-plugin.md) | 現在的 IntelliJ Azure 工具組整合 HDInsight 火花叢集 IntelliJ 想法外掛程式。 它支援 Azure SDK v2.9.1，最新的 Java Sdk，並包含從獨立的 IntelliJ HDInsight 外掛程式的所有功能。| 工具    | 火花| N/A|
| [蝕 HDInsight 工具](hdinsight-apache-spark-eclipse-tool-plugin.md) | Azure 工具組的蝕現在支援 HDInsight 火花叢集。 它會啟用下列功能。 <ul><li>建立並與撰寫支援 IntelliSense，自動套用格式、 「 錯誤檢查 」 等的第一個類別撰寫輕鬆地在 Scala 和 Java 火花應用程式。</li><li>測試本機火花應用程式。</li><li>提交到 HDInsight 火花叢集的工作，並擷取結果。</li><li>登入 Azure，存取您的 Azure 訂閱相關聯的所有火花叢集。</li><li>瀏覽 HDInsight 火花叢集的所有相關聯的儲存空間資源。</li></ul>| 工具    | 火花| N/A

開始使用此版本，我們已變更 Linux 型 HDInsight 叢集來賓 OS 修補原則。 新原則的目標是要大幅降低重新開機，因為修補次數。 新的原則會繼續修補程式虛擬機器 (Vm) Linux 叢集每個星期一或星期四開頭 12 AM UTC 交錯的方式，在任何指定叢集節點上。 不過，任何指定的 VM 只會重新一次最多因為來賓 OS 修補每 30 天。 此外，新建立的叢集的第一個重開機，將不會提早 30 天叢集建立日期。

>[AZURE.NOTE] 新建立的叢集等於或大於此版本僅會套用這些變更。

## <a name="notes-for-06062016-release-of-hdinsight"></a>備忘稿 HDInsight 06/06/2016年版本

HDInsight 叢集部署在這個版本的完整版數字︰

|HDP    |HDI 版本    |火花版本  |Ambari 建立數字    |HDP 建立數字|
|-------|---------------|---------------|-----------------------|----------------|
|2.3    |3.3.1000.0.7702215|    1.5.2|  2.2.1.8-2|  2.3.3.1-18|
|2.4    |3.4.1000.0.7702224|    1.6.1|  2.2.1.8-2|  2.4.2.0|


在這個版本包含下列的更新。

| 標題                                           | 描述                                          | 受影響的區域 （例如，服務、 元件或 SDK） | 叢集類型 （例如火花、 Hadoop、 HBase 或大量） | JIRA （如果適用的話） |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| 在 HDInsight 火花是推出 | 此版本會顯示改良項目中的可用性、 延展性及開啟來源上 HDInsight Apache 火花生產力。 <ul><li>產業前置 SLA 99.9%，讓它適用於要求企業工作負載的可用性。</li><li>使用 Azure 資料湖存放可調整儲存空間的圖層。</li><li>每個階段的資料瀏覽和開發生產力工具。 與自訂火花核心 Jupyter 筆記本啟用互動式資料探索、 與 Power BI、 Tableau 等 Qlik BI 儀表板整合很適合快速資料共用及連續報告，IntelliJ 外掛程式長期程式碼的成品開發和偵錯可靠的選項。</li></ul>| 服務    | 火花| N/A|
| IntelliJ HDInsight 工具 | 這是 HDInsight 火花叢集 IntelliJ 想法外掛程式。 它會啟用下列功能。<ul><li>建立並與撰寫支援 IntelliSense，自動套用格式、 「 錯誤檢查 」 等的第一個類別撰寫輕鬆地在 Scala 和 Java 火花應用程式。</li><li>測試本機火花應用程式。</li><li>提交到 HDInsight 火花叢集的工作，並擷取結果。</li><li>登入 Azure，存取您的 Azure 訂閱相關聯的所有火花叢集。</li><li>瀏覽 HDInsight 火花叢集的所有相關聯的儲存空間資源。</li><li>瀏覽您所有的工作歷程記錄及 HDInsight 火花叢集工作資訊。</li><li>偵錯火花工作，從您的桌上型電腦。</li></ul>| 工具    | 火花| N/A

## <a name="notes-for-05132016-release-of-hdinsight"></a>備忘稿 HDInsight 05/13 2016年版本

HDInsight 叢集部署在這個版本的完整版數字︰

* HDInsight (Windows) 2.1.10.875.2159884 (HDP 1.3.12.0-01795-不變)
* HDInsight (Windows) 3.0.6.875.2159884 (HDP 2.0.13.0-2117-不變)
* HDInsight (Windows) 3.1.4.922.2266903 (HDP 2.1.15.0-2374-不變)
* HDInsight (Windows) 3.2.7.922.2266903 (HDP 2.2.9.1-11)
* HDInsight (Windows) 3.3.0.922.2266903 (HDP 2.3.3.1-18)
* HDInsight (Linux) 3.2.1000.0.7565644 (HDP 2.2.9.1-11)
* HDInsight (Linux) 3.3.1000.0.7565644 (HDP 2.3.3.1-18)
* HDInsight (Linux) 3.4.1000.0.7548380 (HDP 2.4.2.0)

在這個版本包含下列的更新。

| 標題                                           | 描述                                          | 受影響的區域 （例如，服務、 元件或 SDK） | 叢集類型 （例如火花、 Hadoop、 HBase 或大量） | JIRA （如果適用的話） |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| 激起版本更新和其他錯誤修正 | 此版本 1.6.1，以更新 HDInsight 叢集火花版本和其他錯誤的修正| 服務    | 火花| N/A

## <a name="notes-for-04112016-release-of-hdinsight"></a>備忘稿 HDInsight 04/11/2016年版本

HDInsight 叢集部署在這個版本的完整版數字︰

* HDInsight (Windows) 2.1.10.889.2191206 (HDP 1.3.12.0-01795-不變)
* HDInsight (Windows) 3.0.6.889.2191206 (HDP 2.0.13.0-2117-不變)
* HDInsight (Windows) 3.1.4.889.2191206 (HDP 2.1.15.0-2374-不變)
* HDInsight (Windows) 3.2.7.889.2191206 (HDP 2.2.9.1-10)
* HDInsight (Windows) 3.3.0.889.2191206 (HDP 2.3.3.1-16-不變)
* HDInsight (Linux) 3.2.1000.0.7339916 (HDP 2.2.9.1-10)
* HDInsight (Linux) 3.3.1000.0.7339916 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.4.1000.0.7338911 (HDP 2.4.1.1-3)
* SDK 1.5.8

在這個版本包含下列的更新。

| 標題                                           | 描述                                          | 受影響的區域 （例如，服務、 元件或 SDK） | 叢集類型 （例如，Hadoop、 HBase 或大量） | JIRA （如果適用的話） |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| 自訂 metastore 升級問題的 HDI 3.4 | 如果您使用自訂的 metastore，先前使用其他 HDInsight 叢集的舊版本，就會失敗叢集建立。 這是因為現在已固定的升級指令碼錯誤| 叢集建立    | 所有 | N/A
| 晚總損毀修復 | 透過晚總送出任何工作提供工作狀態 | 可靠性 | 在 Linux 火花| N/A
| Jupyter 內容 HA | 提供儲存並載入與儲存相關聯的帳戶與叢集 Jupyter 筆記本內容的能力。 如需詳細資訊，請參閱[核心適用於 Jupyter 筆記本](hdinsight-apache-spark-jupyter-notebook-kernels.md)。| 筆記本 | 在 Linux 火花| N/A
| HiveContext Jupter 在筆記本中的 [移除 | 使用`%%sql`神奇而不是`%%hive`個神奇。 SqlContext 等於 hiveContext。 如需詳細資訊，請參閱[適用於 Jupyter 筆記本核心](hdinsight-apache-spark-jupyter-notebook-kernels.md)| 筆記本    | 在 Linux 火花叢集| N/A
| 取代了火花舊版 | 會在 5/31 服務移除舊的版本火花 1.3.1 | 服務 | 在 Windows 上火花叢集 | N/A

## <a name="notes-for-03292016-release-of-hdinsight"></a>備忘稿 HDInsight 03/29 2016年版本

HDInsight 叢集部署在這個版本的完整版數字︰

* HDInsight (Windows) 2.1.10.875.2159884 (HDP 1.3.12.0-01795-不變)
* HDInsight (Windows) 3.0.6.875.2159884 (HDP 2.0.13.0-2117-不變)
* HDInsight (Windows) 3.1.4.875.2159884 (HDP 2.1.15.0-2374-不變)
* HDInsight (Windows) 3.2.7.875.2159884 (HDP 2.2.9.1-7-不變)
* HDInsight (Windows) 3.3.0.875.2159884 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.2.1000.0.7193255 (HDP 2.2.9.1-8-不變)
* HDInsight (Linux) 3.3.1000.0.7193255 (HDP 2.3.3.1-7-不變)
* HDInsight (Linux) 3.4.1000.0.7195842 (HDP 2.4.1.0-327)
* SDK 1.5.8

在這個版本包含下列的更新。

| 標題                                           | 描述                                          | 受影響的區域 （例如，服務、 元件或 SDK） | 叢集類型 （例如，Hadoop、 HBase 或大量） | JIRA （如果適用的話） |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| 新增的 HDInsight 3.4 版本和更新的 HDP 版本，所有的 HDInsight 叢集 | 在這個版本中，我們已新增 HDInsight v3.4 （根據 HDP 2.4），並也更新其他 HDP 版本。 HDP 2.4 版本資訊會提供[以下](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html)並詳細資訊 HDInsight 版本可找到[以下](hdinsight-component-versioning.md)。| 服務    | 所有 Linux 叢集| N/A
| HDInsight 進階版 | HDInsight 現在有兩種類型的標準和進階版。 HDInsight 進階版目前預覽中，並且僅適用於 Hadoop 及火花叢集 Linux 上。 如需詳細資訊，請參閱[以下](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)。| 服務    | Hadoop 和 linux 火花| N/A
| Microsoft R 伺服器 | HDInsight 進階版提供可包含 Hadoop 和火花叢集 Linux 的 Microsoft R 伺服器。 如需詳細資訊請參閱[概觀的 R 伺服器上 HDInsight](hdinsight-hadoop-r-server-overview.md)。| 服務    | Hadoop 和 linux 火花| N/A
| 火花 1.6.0 | HDInsight 3.4 叢集現在包含火花 1.6.0| 服務    | 在 Linux 火花叢集| N/A
| Jupyter 筆記本增強功能 | 用於火花叢集 Jupyter 筆記本現在提供其他火花核心。 同時包含等增強功能的用途 %個神奇、 自動視覺效果和整合 Python 視覺效果 （例如 matplotlib) 的文件庫。 如需詳細資訊，請參閱[核心適用於 Jupyter 筆記本](hdinsight-apache-spark-jupyter-notebook-kernels.md)。 | 服務 | 在 Linux 火花叢集 | N/A

## <a name="notes-for-03222016-release-of-hdinsight"></a>備忘稿 HDInsight 03/22 2016年版本

HDInsight 叢集部署在這個版本的完整版數字︰

* HDInsight (Windows) 2.1.10.875.2159884 (HDP 1.3.12.0-01795-不變)
* HDInsight (Windows) 3.0.6.875.2159884 (HDP 2.0.13.0-2117-不變)
* HDInsight (Windows) 3.1.4.875.2159884 (HDP 2.1.15.0-2374-不變)
* HDInsight (Windows) 3.2.7.875.2159884 (HDP 2.2.9.1-7-不變)
* HDInsight (Windows) 3.3.0.875.2159884 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.2.1000.0.7193255 (HDP 2.2.9.1-8-不變)
* HDInsight (Linux) 3.3.1000.0.7193255 (HDP 2.3.3.1-7-不變)
* SDK 1.5.8

在這個版本包含下列的更新。

| 標題                                           | 描述                                          | 受影響的區域 （例如，服務、 元件或 SDK） | 叢集類型 （例如，Hadoop、 HBase 或大量） | JIRA （如果適用的話） |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| 更新所有 HDInsight 叢集 HDInsight 版本 | 在這個版本中，我們已更新所有 HDInsight 叢集 HDInsight 的版本| 服務    | 所有| N/A


## <a name="notes-for-03102016-release-of-hdinsight"></a>備忘稿 HDInsight 03/10/2016年版本

HDInsight 叢集部署在這個版本的完整版數字︰

* HDInsight (Windows) 2.1.10.859.2123216 (HDP 1.3.12.0-01795-不變)
* HDInsight (Windows) 3.0.6.859.2123216 (HDP 2.0.13.0-2117-不變)
* HDInsight (Windows) 3.1.4.859.2123216 (HDP 2.1.15.0-2374-不變)
* HDInsight (Windows) 3.2.7.859.2123216 (HDP 2.2.9.1-7)
* HDInsight (Windows) 3.3.0.859.2123216 (HDP 2.3.3.1-5-不變)
* HDInsight (Linux) 3.2.1000.7076817 (HDP 2.2.9.1-8)
* HDInsight (Linux) 3.3.1000.7076817 (HDP 2.3.3.1-7)
* SDK 1.5.8

在這個版本包含下列的更新。

| 標題                                           | 描述                                          | 受影響的區域 （例如，服務、 元件或 SDK） | 叢集類型 （例如，Hadoop、 HBase 或大量） | JIRA （如果適用的話） |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| 更新所有 HDInsight 叢集 HDInsight 版本 | 在這個版本中，我們已更新所有 HDInsight 叢集 HDInsight 的版本| 服務    | 所有| N/A

## <a name="notes-for-01272016-release-of-hdinsight"></a>備忘稿 HDInsight 01/27/2016 版本

HDInsight 叢集部署在這個版本的完整版數字︰

* HDInsight (Windows) 2.1.10.817.2028315 (HDP 1.3.12.0-01795-不變)
* HDInsight (Windows) 3.0.6.817.2028315 (HDP 2.0.13.0-2117-不變)
* HDInsight (Windows) 3.1.4.817.2028315 (HDP 2.1.15.0-2374-不變)
* HDInsight (Windows) 3.2.7.817.2028315 (HDP 2.2.9.1-1)
* HDInsight (Windows) 3.3.0.817.2028315 (HDP 2.3.3.1-5-不變)
* HDInsight (Linux) 3.2.1000.4072335 (HDP 2.2.9.1-1)
* HDInsight (Linux) 3.3.1000.4072335 (HDP 2.3.3.1-1)
* SDK 1.5.8

在這個版本包含下列的更新。

| 標題                                           | 描述                                          | 受影響的區域 （例如，服務、 元件或 SDK） | 叢集類型 （例如，Hadoop、 HBase 或大量） | JIRA （如果適用的話） |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| 更新所有 HDInsight 叢集 HDInsight 版本 | 在這個版本中，我們已更新所有 HDInsight 叢集 HDInsight 的版本| 服務    | 所有| N/A

## <a name="notes-for-12022015-release-of-hdinsight"></a>備忘稿 HDInsight 12/02/2015年版本

HDInsight 叢集部署在這個版本的完整版數字︰

* HDInsight (Windows) 2.1.10.763.1931434 (HDP 1.3.12.0-01795-不變)
* HDInsight (Windows) 3.0.6.763.1931434 (HDP 2.0.13.0-2117-不變)
* HDInsight (Windows) 3.1.4.763.1931434 (HDP 2.1.15.0-2374-不變)
* HDInsight (Windows) 3.2.7.763.1931434 (HDP 2.2.7.1-34-不變)
* HDInsight (Windows) 3.3.1000.0 (HDP 2.3.3.1-5)
* HDInsight (Linux) 3.2.1000.0.6392801 (HDP 2.2.7.1-34-不變)
* HDInsight (Linux) 3.3.1000.0 (HDP 2.3.3.0-3039)
* SDK 1.5.8

在這個版本包含下列的更新。

| 標題                                           | 描述                                          | 受影響的區域 （例如，服務、 元件或 SDK） | 叢集類型 （例如，Hadoop、 HBase 或大量） | JIRA （如果適用的話） |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| 新增的 HDInsight 3.3 版本和更新的 HDP 版本，所有的 HDInsight 叢集 | 在這個版本中，我們已新增 HDInsight v3.3 （根據 HDP 2.3），並也更新其他 HDP 版本。 HDP 2.3 版本資訊會提供[以下](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html)並詳細資訊 HDInsight 版本可找到[以下](hdinsight-component-versioning.md)。| 服務    | 所有| N/A

## <a name="notes-for-11302015-release-of-hdinsight"></a>備忘稿 HDInsight 11/30/2015年版本

HDInsight 叢集部署在這個版本的完整版數字︰

* HDInsight (Windows) 2.1.10.757.1923908 (HDP 1.3.12.0-01795-不變)
* HDInsight (Windows) 3.0.6.757.1923908 (HDP 2.0.13.0-2117-不變)
* HDInsight (Windows) 3.1.4.757.1923908 (HDP 2.1.15.0-2374-不變)
* HDInsight (Windows) 3.2.7.757.1923908 (HDP 2.2.7.1-34)
* HDInsight (Linux) 3.2.1000.0.6392801 (HDP 2.2.7.1-34)
* SDK 1.5.8

在這個版本包含下列的更新。

| 標題                                           | 描述                                          | 受影響的區域 （例如，服務、 元件或 SDK） | 叢集類型 （例如，Hadoop、 HBase 或大量） | JIRA （如果適用的話） |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| 更新的 HDInsight 版本，所有 HDInsight 叢集和 HDP 版本 HDInsight 3.2 捨位叢集 （Windows 和 Linux） | 在這個版本中，已更新 HDInsight 與 HDP 版本 | 服務    | 所有| N/A


## <a name="notes-for-10272015-release-of-hdinsight"></a>備忘稿 HDInsight 10/27/2015年版本

HDInsight 叢集部署在這個版本的完整版數字︰

* HDInsight (Windows) 2.1.10.726.1866228 (HDP 1.3.12.0-01795-不變)
* HDInsight (Windows) 3.0.6.726.1866228 (HDP 2.0.13.0-2117-不變)
* HDInsight (Windows) 3.1.4.726.1866228 (HDP 2.1.15.0-2374-不變)
* HDInsight (Windows) 3.2.7.726.1866228 (HDP 2.2.7.1-33)
* HDInsight (Linux) 3.2.1000.0.6035701 (HDP 2.2.7.1-33)
* SDK 1.5.8

在這個版本包含下列的更新。

| 標題                                           | 描述                                          | 受影響的區域 （例如，服務、 元件或 SDK） | 叢集類型 （例如，Hadoop、 HBase 或大量） | JIRA （如果適用的話） |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| 更新的 HDInsight 版本的所有 HDInsight 叢集 （Windows 和 Linux） | 在這個版本中，已更新 HDInsight 與 HDP 版本 | 服務    | 所有| N/A
| 固定的 Windows 火花 Jupyter 叢集的大寫字母叢集 | 指定大寫字母 DNS 名稱的叢集有 Jupyter 筆記本，因為原點要求核取的問題。 修正不 Jupyter 的設定 DNS 名稱變更為較低的大小寫。 | 服務    | HDInsight 火花 (Windows)| N/A


## <a name="notes-for-10202015-release-of-hdinsight"></a>備忘稿 HDInsight 10/20/2015年版本

HDInsight 叢集部署在這個版本的完整版數字︰

* HDInsight 2.1.10.716.1846990 (Windows) (HDP 1.3.12.0-01795-不變)
* HDInsight 3.0.6.716.1846990 (Windows) (HDP 2.0.13.0-2117-不變)
* HDInsight 3.1.4.716.1846990 (Windows) (HDP 2.1.16.0-2374)
* HDInsight 3.2.7.716.1846990 (Windows) (HDP 2.2.7.1-0004)
* HDInsight 3.2.1000.0.5930166 (Linux) (HDP 2.2.7.1-0004)
* SDK 1.5.8

在這個版本包含下列的更新。

| 標題                                           | 描述                                          | 受影響的區域 （例如，服務、 元件或 SDK） | 叢集類型 （例如，Hadoop、 HBase 或大量） | JIRA （如果適用的話） |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| 變更為 HDP 2.2 預設 HDP 版本 | HDInsight Windows 叢集的預設版本會變更為 HDP 2.2。 2015 年 2 月自已通常用於 HDInsight 版本 3.2 捨位 (HDP 2.2)。 這項變更只會跟著預設叢集版本，當未時提供使用 Azure 入口網站、 PowerShell cmdlet 或 SDK 叢集做明確的選取範圍。 | 服務    | 所有| N/A                  |
|部署 Linux 叢集，在單一虛擬網路上的多個 HDInsight VM 姓名格式變更 | 在這個版本中已加入的部署單一虛擬網路中的多個 HDInsight Linux 叢集支援。 從 headnode 叢集虛擬機器名稱的格式已變更的一部分，\*，workernode\*和 zookeepernode\*至 hn\*，wn\*，及 zk\*分別。 不建議才會直接相依性的虛擬機器名稱的格式，因為這會有所變更。 請決定主機及的元件至 [主辦城市對應的清單，在本機電腦或 Ambari Api 使用 「 f 主機名稱]。 您可以找到[https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/hosts.md](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/hosts.md)和[https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/host-components.md](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/host-components.md)的詳細資訊。 | 服務 | HDInsight 叢集 linux | N/A |
| 設定變更 | 針對 HDInsight 3.1 叢集，現在會啟用下列設定︰ <ul><li>tez.yarn.ats.enabled 和 yarn.log.server.url。 這可讓應用程式時間表伺服器和記錄伺服器能夠做出記錄。</li></ul>HDInsight 3.2 捨位叢集已修改下列設定︰ <ul><li>mapreduce.fileoutputcommitter.algorithm.version 已設定為 2。 這可讓 FileOutputCommitter V2 版本的使用。</li></ul> | 服務 | 所有 | N/A |


## <a name="notes-for-09092015-release-of-hdinsight"></a>備忘稿 HDInsight 09/09/2015年版本

HDInsight 叢集部署在這個版本的完整版數字︰

* HDInsight 2.1.10.675.1768697 (HDP 1.3.12.0-01795-不變)
* HDInsight 3.0.6.675.1768697 (HDP 2.0.13.0-2117-不變)
* HDInsight 3.1.4.675.1768697 (HDP 2.1.15.0-2334-不變)
* HDInsight 3.2.6.675.1768697 (HDP 2.2.6.1-0012-不變)
* SDK 1.5.8

在這個版本包含下列的更新。

| 標題                                           | 描述                                          | 受影響的區域 （例如，服務、 元件或 SDK） | 叢集類型 （例如，Hadoop、 HBase 或大量） | JIRA （如果適用的話） |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| 更新所有 HDInsight 叢集 HDInsight 版本 | 在這個版本中，已更新 HDInsight 版本 | 服務    | 所有| N/A                  |

## <a name="notes-for-07312015-release-of-hdinsight"></a>備忘稿 HDInsight 07/31/2015年版本

HDInsight 叢集部署在這個版本的完整版數字︰

* HDInsight 2.1.10.640.1695824 (HDP 1.3.12.0-01795-不變)
* HDInsight 3.0.6.640.1695824 (HDP 2.0.13.0-2117-不變)
* HDInsight 3.1.4.640.1695824 (HDP 2.1.15.0-2334-不變)
* HDInsight 3.2.6.640.1695824 (HDP 2.2.6.1-0012-不變)
* SDK 1.5.8

在這個版本包含下列的更新。

| 標題                                           | 描述                                          | 受影響的區域 （例如，服務、 元件或 SDK） | 叢集類型 （例如，Hadoop、 HBase 或大量） | JIRA （如果適用的話） |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| 修正火花叢集節點重新影像工作流程 | 修正造成火花叢集節點無法復原之後重新圖像到某個錯誤 | 服務    | 火花| N/A                  |


## <a name="notes-for-07312015-release-of-hdinsight"></a>備忘稿 HDInsight 07/31/2015年版本

HDInsight 叢集部署在這個版本的完整版數字︰

* HDInsight 2.1.10.635.1684502 (HDP 1.3.12.0-01795-不變)
* HDInsight 3.0.6.635.1684502 (HDP 2.0.13.0-2117-不變)
* HDInsight 3.1.4.635.1684502 (HDP 2.1.15.0-2334-不變)
* HDInsight 3.2.6.635.1684502 (HDP 2.2.6.1-0012-不變)
* SDK 1.5.8

在這個版本包含下列的更新。

| 標題                                           | 描述                                          | 受影響的區域 （例如，服務、 元件或 SDK） | 叢集類型 （例如，Hadoop、 HBase 或大量） | JIRA （如果適用的話） |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| 更新所有 HDInsight 叢集 HDInsight 版本 | 在這個版本中，已更新 HDInsight 版本 | 服務    | 所有| N/A                  |


## <a name="notes-for-07072015-release-of-hdinsight"></a>備忘稿 HDInsight 07/07/2015年版本

HDInsight 叢集部署在這個版本的完整版數字︰

* HDInsight 2.1.10.610.1630216 (HDP 1.3.12.0-01795-不變)
* HDInsight 3.0.6.610.1630216 (HDP 2.0.13.0-2117-不變)
* HDInsight 3.1.4.610.1630216 (HDP 2.1.15.0-2334-不變)
* HDInsight 3.2.4.610.1630216 (HDP 2.2.6.1-0012)
* SDK 1.5.8


在這個版本包含下列的更新。

| 標題                                           | 描述                                          | 受影響的區域 （例如，服務、 元件或 SDK） | 叢集類型 （例如，Hadoop、 HBase 或大量） | JIRA （如果適用的話） |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| HDInsight 3.2 捨位叢集更新的 HDP 版本 | 在這個版本中，HDInsight 3.2 捨位部署 HDP 2.2.6.1-0012 | 服務    | 所有                                                 | N/A                  |


## <a name="notes-for-06262015-release-of-hdinsight"></a>備忘稿 HDInsight 06/26/2015年版本

HDInsight 叢集部署在這個版本的完整版數字︰

* HDInsight 2.1.10.601.1610731 (HDP 1.3.12.0-01795-不變)
* HDInsight 3.0.6.601.1610731 (HDP 2.0.13.0-2117-不變)
* HDInsight 3.1.4.601.1610731 (HDP 2.1.15.0-2334-不變)
* HDInsight 3.2.4.601.1610731 (HDP 2.2.6.1-0011)
* SDK 1.5.8


在這個版本包含下列的更新。

<table border="1">
<tr>
<th>標題</th>
<th>描述</th>
<th>受影響的區域 （例如，服務、 元件或 SDK）</p></th>
<th>叢集類型 （例如，Hadoop、 HBase 或大量）</th>
<th>JIRA （如果適用的話）</th>
</tr>


<tr>
<td>HDInsight 3.2 捨位叢集更新的 HDP 版本</td>
<td>在這個版本中，HDInsight 3.2 捨位部署 HDP 2.2.6.1</td>
<td>服務</td>
<td>所有</td>
<td>N/A</td>
</tr>

</table>

## <a name="notes-for-06182015-release-of-hdinsight"></a>備忘稿 HDInsight 06/18/2015年版本

HDInsight 叢集部署在這個版本的完整版數字︰

* HDInsight 2.1.10.596.1601657 (HDP 1.3.12.0-01795-不變)
* HDInsight 3.0.6.596.1601657 (HDP 2.0.13.0-2117-不變)
* HDInsight 3.1.4.596.1601657 (HDP 2.1.15.0-2334)
* HDInsight 3.2.4.596.1601657 (HDP 2.2.6.1-0002)
* SDK 1.5.8


在這個版本包含下列的更新。

<table border="1">
<tr>
<th>標題</th>
<th>描述</th>
<th>受影響的區域 （例如，服務、 元件或 SDK）</p></th>
<th>叢集類型 （例如，Hadoop、 HBase 或大量）</th>
<th>JIRA （如果適用的話）</th>
</tr>


<tr>
<td>開啟其他 HTTPS 連接埠</td>
<td>雲端服務目前開啟的 5 的連接埠 8001 至 8005 叢集 E.g.上 在 [https://<clustername>.azurehdinsight.net:8001/。 使用相同的基本驗證密碼機制為連接埠 443 驗證要求這些的 url。 下列連接埠繫結至作用中的 headnode 上相同的連接埠。 指令碼動作可用來讓接聽 headnode 和路由叢集外的下列連接埠的客戶服務。</td>
<td>雲端服務</td>
<td>所有</td>
<td>N/A</td>
</tr>

<tr>
<td>HDInsight 3.2 捨位間歇性 MapReduce 隨機問題</td>
<td>修正導致非經常性工作失敗的大型叢集上地圖減少隨機少見、 間歇性競爭情形。 如需詳細資訊，請參閱<a href="https://issues.apache.org/jira/browse/MAPREDUCE-6361" target="_blank">MAPREDUCE 6361</a> 。</td>
<td>Hadoop 核心</td>
<td>所有</td>
<td><a href="https://issues.apache.org/jira/browse/MAPREDUCE-6361" target="_blank">MAPREDUCE 6361</a></td>
</tr>

<tr>
<td>移至最新的 Azure Java SDK 2.2 HDInsight 3.2 捨位</td>
<td>移至最新版 Azure SDK 的 Java WASB 驅動程式使用。 最新的 SDK 有一些其他修正方式，且在 https://github.com/Azure/azure-storage-java/blob/master/ChangeLog.txt 相同的版本資訊。</td>
<td>Hadoop 核心</td>
<td>所有</td>
<td><a href="https://issues.apache.org/jira/browse/HADOOP-11959" target="_blank">HADOOP 11959</a></td>
</tr>

<tr>
<td>移至 HDP 2.1.15 HDInsight 3.1 叢集</td>
<td>Hortonworks 發行版本的備忘稿，可<a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.15-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.15.html" target="_blank">在這裡</a>。</td>
<td>HDP</td>
<td>所有</td>
<td>N/A</td>
</tr>

</table>

## <a name="notes-for-06042015-release-of-hdinsight"></a>備忘稿 HDInsight 06/04/2015年版本

HDInsight 叢集部署在這個版本的完整版數字︰

* HDInsight 2.1.10.583.1575584 (HDP 1.3.12.0-01795-不變)
* HDInsight 3.0.6.583.1575584 (HDP 2.0.13.0-2117-不變)
* HDInsight 3.1.3.583.1575584 (HDP 2.1.12.1-0003-不變)
* HDInsight 3.2.4.583.1575584 (HDP 2.2.6.1-1)
* SDK 1.5.8


在這個版本包含下列的更新。

<table border="1">
<tr>
<th>標題</th>
<th>描述</th>
<th>受影響的區域 （例如，服務、 元件或 SDK）</p></th>
<th>叢集類型 （例如，Hadoop、 HBase 或大量）</th>
<th>JIRA （如果適用的話）</th>
</tr>


<tr>
<td>大量叢集 502 不正確的閘道錯誤修正</td>
<td>此版本可修正錯誤，會影響工作送出 API 造成網站已關閉後重新啟動電腦。</td>
<td>服務</td>
<td>大量</td>
<td>N/A</td>
</tr>

</table>

## <a name="notes-for-06012015-release-of-hdinsight"></a>備忘稿 HDInsight 06/01/2015年版本

HDInsight 叢集部署在這個版本的完整版數字︰

* HDInsight 2.1.10.577.1563827 (HDP 1.3.12.0-01795-不變)
* HDInsight 3.0.6.577.1563827 (HDP 2.0.13.0-2117-不變)
* HDInsight 3.1.3.577.1563827 (HDP 2.1.12.1-0003-不變))
* HDInsight 3.2.4.577.1563827 (HDP 2.2.6.0-2800-不變)
* SDK 1.5.8


在這個版本包含下列的更新。

<table border="1">
<tr>
<th>標題</th>
<th>描述</th>
<th>受影響的區域 （例如，服務、 元件或 SDK）</p></th>
<th>叢集類型 （例如，Hadoop、 HBase 或大量）</th>
<th>JIRA （如果適用的話）</th>
</tr>


<tr>
<td>各種錯誤修正</td>
<td>此版本可修正叢集佈建與相關的錯誤。</td>
<td>服務</td>
<td>所有叢集類型</td>
<td>N/A</td>
</tr>

</table>

## <a name="notes-for-05272015-release-of-hdinsight"></a>備忘稿 HDInsight 05/27/2015年版本

HDInsight 叢集部署在這個版本的完整版數字︰

* HDInsight 3.2.4.570.1554102 (HDP 2.2.6.0-2800)
* 不屬於此版本部署其他叢集版本和 SDK。


在這個版本包含下列的更新。

<table border="1">
<tr>
<th>標題</th>
<th>描述</th>
<th>受影響的區域 （例如，服務、 元件或 SDK）</p></th>
<th>叢集類型 （例如，Hadoop、 HBase 或大量）</th>
<th>JIRA （如果適用的話）</th>
</tr>


<tr>
<td>HDP 2.2 更新</td>
<td>此版本的 HDInsight 3.2 捨位包含 HDP 2.2.6，並為 HDInsight 的幾個重要的錯誤修正。 完整版本資訊會在<a href="http://dev.hortonworks.com.s3.amazonaws.com/HDPDocuments/HDP2/HDP-2.2.6/HDP_RelNotes_v226/index.html">HDP 2.2.6 放開筆記</a>。</td>
<td>HDP</td>
<td>所有叢集類型</td>
<td>N/A</td>
</tr>

<tr>
<td>變更預設 Yarn 容器記憶體設定</td>
<td>在此更新，預設可用的記憶體 YARN 容器 （yarn.nodemanager.resource.memory mb 及 yarn.scheduler.maximum 配置 mb） 啟動節點管理員，以增加 5632 mb。 先前這精簡 4608 mb，但根據各種工作執行，新的值必須提供更有效地可靠性和效能大部分的工作，因此是較佳的預設值。 為主要，如果您有此記憶體設定的要徑相依性，請建立叢集時中明確設定。</td>
<td>HDP</td>
<td>所有叢集類型</td>
<td>N/A</td>
</tr>

<tr>
<td>預設設定的不一致性致歉 HBase 和大量叢集</td>
<td>此更新還原 Hbase 和大量叢集作為 Hadoop 叢集 YARN 設定的相同的值。 這是為了在所有叢集類型的不一致性致歉。</td>
<td>HDP</td>
<td>HBase 大量</td>
<td>N/A</td>
</tr>

</table>

## <a name="notes-for-05202015-release-of-hdinsight"></a>備忘稿 HDInsight 05/20/2015年版本

HDInsight 叢集部署在這個版本的完整版數字︰

* HDInsight 2.1.10.564.1542093 (HDP 1.3.12.0-01795-不變)
* HDInsight 3.0.6.564.1542093 (HDP 2.0.13.0-2117-不變)
* HDInsight 3.1.3.564.1542093 (HDP 2.1.12.1-0003)
* HDInsight 3.2.4.564.1542093 (HDP 2.2.4.6-2)
* SDK 1.5.8

在這個版本包含下列的更新。

<table border="1">
<tr>
<th>標題</th>
<th>描述</th>
<th>受影響的區域 （例如，服務、 元件或 SDK）</p></th>
<th>叢集類型 （例如，Hadoop、 HBase 或大量）</th>
<th>JIRA （如果適用的話）</th>
</tr>


<tr>
<td>SCP.NET EventHub 的支援</td>
<td>HDInsight 大量的更新的叢集套件 SCP.NET 將新功能。 您現在必須拓撲產生器] 中，可讓您更輕鬆地使用 EventHubSpout 或 Java Spouts 新 api 的存取權。 您必須更新 SCP.NET 客戶合約已更新為使用新的叢集 SDK。 如需詳細資訊 （包括錯誤修正） 新 Api、 使用和發行筆記，請參閱 SCP.NET nuget 套件中包含的讀我檔案。</td>
<td>與工具</td>
<td>大量 HDInsight 3.2 捨位叢集</td>
<td>N/A</td>
</tr>

<tr>
<td>更新 JDBC 驅動程式</td>
<td>支援的 SQL Server 版本中 sqljdbc_4.1.5605.100 更新驅動程式。</td>
<td>Metastore</td>
<td>所有</td>
<td>N/A</td>
</tr>
</table>

## <a name="notes-for-04272015-release-of-hdinsight"></a>備忘稿 HDInsight 04/27/2015年版本

HDInsight 叢集部署在這個版本的完整版數字︰

* HDInsight 2.1.10.537.1486660 (HDP 1.3.12.0-01795-不變)
* HDInsight 3.0.6.537.1486660 (HDP 2.0.13.0-2117-不變)
* HDInsight 3.1.3.537.1486660 (HDP 2.1.12.0-2329-不變)
* HDInsight 3.2.3.537.1486660 (HDP 2.2.2.2-4)
* SDK 1.5.8

在這個版本包含下列的更新。

<table border="1">
<tr>
<th>標題</th>
<th>描述</th>
<th>受影響的區域 （例如，服務、 元件或 SDK）</p></th>
<th>叢集類型 （例如，Hadoop、 HBase 或大量）</th>
<th>JIRA （如果適用的話）</th>
</tr>


<tr>
<td>修正 DLL 相依性</td>
<td>移除與單元測試架構的 HDInsight 相依性。</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>競爭錯誤修正</td>
<td>叢集建立邀請現在等待上放入邀請接受之前的投票的狀態</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>
</table>

## <a name="notes-for-04142015-release-of-hdinsight"></a>備忘稿 HDInsight 04/14/2015年版本

HDInsight 叢集部署在這個版本的完整版數字︰

* HDInsight 2.1.10.521.1453250 (HDP 1.3.12.0-01795-不變)
* HDInsight 3.0.6.521.1453250 (HDP 2.0.13.0-2117-不變)
* HDInsight 3.1.3.521.1453250 (HDP 2.1.12.0-2329-不變)
* HDInsight 3.2.3.525.1459730 (HDP 2.2.2.2-2)
* SDK 1.5.6

在這個版本包含下列的更新。

<table border="1">
<tr>
<th>標題</th>
<th>描述</th>
<th>受影響的區域 （例如，服務、 元件或 SDK）</p></th>
<th>叢集類型 （例如，Hadoop、 HBase 或大量）</th>
<th>JIRA （如果適用的話）</th>
</tr>


<tr>
<td>Tez 錯誤修正</td>
<td>修正 Apache TEZ 2214 和 TEZ 1923 包含在此版本的 HDI 3.2 捨位。 這些特別所需的特定登錄區上的查詢 Tez 需要隨機播放大量的資料。
</td>
<td>HDP</td>
<td>Hadoop</td>
<td><a href="https://issues.apache.org/jira/browse/TEZ-2214">TEZ 2214</a></br><a href="https://issues.apache.org/jira/browse/TEZ-1923">TEZ 1923</a></td>
</tr>
</table>

## <a name="notes-for-04062015-release-of-hdinsight"></a>備忘稿 HDInsight 04/06/2015年版本

HDInsight 叢集部署在這個版本的完整版數字︰

* HDInsight 2.1.10.521.1453250 (HDP 1.3.12.0-01795-不變)
* HDInsight 3.0.6.521.1453250 (HDP 2.0.13.0-2117-不變)
* HDInsight 3.1.3.521.1453250 (HDP 2.1.12.0-2329-不變)
* HDInsight 3.2.3.521.1453250 (HDP 2.2.2.2-1)
* SDK 1.5.6

在這個版本包含下列的更新。

<table border="1">
<tr>
<th>標題</th>
<th>描述</th>
<th>受影響的區域 （例如，服務、 元件或 SDK）</p></th>
<th>叢集類型 （例如，Hadoop、 HBase 或大量）</th>
<th>JIRA （如果適用的話）</th>
</tr>


<tr>
<td>HDInsight.NET SDK 1.5.6</td>
<td>若要移除某些內部類別 linux HDInsight 的更新。</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>1.5.6 Avro 文件庫</td>
<td>方法<b>GetAllKnownTypes</b>新增的<b>KnownTypeAttribute</b> 。 固定的 NullReferenceException 當類型 null GetAllKnownTypes 方法。</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>錯誤修正</td>
<td>服務的各種錯誤修正</td>
<td>服務</td>
<td>所有</td>
<td>N/A</td>
</tr>

</table>
<br>

## <a name="notes-for-04012015-release-of-hdinsight"></a>備忘稿 HDInsight 04/01/2015年版本

HDInsight 叢集部署在這個版本的完整版數字︰

* HDInsight 2.1.10.513.1431705 (HDP 1.3.12.0-01795)
* HDInsight 3.0.6.513.1431705 (HDP 2.0.13.0-2117)
* HDInsight 3.1.3.513.1431705 (HDP 2.1.12.0-2329)
* HDInsight 3.2.3.513.1431705 (HDP 2.2.2.1-2600)
* SDK 1.5.5

在這個版本包含下列的更新。

<table border="1">
<tr>
<th>標題</th>
<th>描述</th>
<th>受影響的區域 （例如，服務、 元件或 SDK）</p></th>
<th>叢集類型 （例如，Hadoop、 HBase 或大量）</th>
<th>JIRA （如果適用的話）</th>
</tr>


<tr>
<td>啟用或停用 Windows 叢集.NET SDK 透過遠端桌面認證的能力</td>
<td>啟用或停用 Windows 叢集 RDP 認證的程式設計支援。</td>
<td>SDK</td>
<td>所有</td>
<td>N/A</td>
</tr>

<tr>
<td>若要啟用遠端桌面叢集認證時它們會佈建的能力</td>
<td>支援以程式設計方式建立的即叢集啟用遠端桌面的認證。 這樣會移除第一次佈建叢集，然後啟用遠端桌面執行的程序。</td>
<td>SDK</td>
<td>所有</td>
<td>N/A</td>
</tr>

<tr>
<td>若要 2.7.8 升級的 Python</td>
<td>升級的 Python 上 HDInsight 叢集 Python 2.7.8，其中包含一些重要的安全性修正 HDInsight 版本 2.1、 3.0、 3.1 及 3.2 捨位</td>
<td>服務</td>
<td>所有</td>
<td>N/A</td>
</tr>

<tr>
<td>YARN 項設定變更</td>
<td>變更的 YARN 設定 yarn.resourcemanager.max-完成-應用程式，以 1000 所有叢集類型 HDInsight 版本 3.1 及 3.2 捨位。 此值只會控制 YARN ui 上完成的應用程式的清單。 若要取得應用程式使用者介面上顯示的應用程式清單之前已提交的相關資訊，您可以直接移至 [歷程記錄伺服器。</td>
<td>YARN</td>
<td>所有</td>
<td>N/A</td>
</tr>

<tr>
<td>調整大小的 HBase 叢集節點</td>
<td>HBase 叢集現在允許調整大小的節點 （向上和向下） HDInsight 版本 3.1 及 3.2 捨位</td>
<td>服務</td>
<td>HBase</td>
<td>N/A</td>
</tr>

<tr>
<td>JDBC 升級</td>
<td>SQL JDBC 驅動程式的 HDInsight 3.2 版升級為版本 sqljdbc_4.0.2206.100。 這個版本包含重要的安全性增強功能。</td>
<td>HDP</td>
<td>所有</td>
<td>N/A</td>
</tr>

<tr>
<td>JVM 更新設定</td>
<td>更新的 JVM 組態 networkaddress.cache.ttl 到 300 秒從 HDInsight 版本 3.1 及 3.2 捨位-1 的預設值。 此設定值控制項名稱服務的成功的名稱對應的快取原則。 這可修正錯誤相關放大或縮小 HBase 叢集。</td>
<td>服務</td>
<td>HBase</td>
<td>N/A</td>
</tr>

<tr>
<td>升級至 Azure 儲存體 SDK java 2.0</td>
<td>HDInsight 版本 3.2 捨位升級為最新版 Azure 儲存體 SDK 用於 Java。 此頁面包含幾個重要的錯誤修正移到目前 0.6.0 版本。</td>
<td>HDP</td>
<td>所有</td>
<td>N/A</td>
</tr>

<tr>
<td>升級至 Apache WASB 原始程式碼</td>
<td>HDInsight 版本 3.2 捨位現在會使用最新版的程式碼的 Apache Hadoop WASB 檔案系統驅動程式。 使用這項變更，WASB 驅動程式現在封裝為不同的 jar。 這是完全包裝的變更，並不包含 WASB 驅動程式行為的任何變更。 此 JAR 檔案的名稱是 hadoop-azure-2.6.0.jar。</td>
<td>HDP</td>
<td>所有</td>
<td>N/A</td>
</tr>

<tr>
<td>Jar HDInsight 3.2 捨位在檔案名稱更新</td>
<td>HDInsight 版本 3.2 捨位到此更新包含多個修正錯誤，並已升級幾個內部 （每瓶） 封裝為 HDP 的一部分。 請注意，下列 JAR 檔案內部 HDP 套件，而不是直接客戶應用程式使用。 應用程式應該封裝自己版本的 （每瓶），以便升級到 HDP 的內部 （每瓶） 不會中斷客戶應用程式。</td>
<td>HDP</td>
<td>所有</td>
<td>N/A</td>
</tr>

</table>
<br>

## <a name="notes-for-03032015-release-of-hdinsight"></a>備忘稿 HDInsight 03/03/2015年版本

HDInsight 叢集部署在這個版本的完整版數字︰

* HDInsight 2.1.10.488.1375841 (HDP 1.3.9.0-01351-不變)
* HDInsight 3.0.6.488.1375841 (HDP 2.0.9.0-2097-不變)
* HDInsight 3.1.3.488.1375841 (HDP 2.1.10.0-2290-不變)
* HDInsight 3.2.3.488.1375841 (HDP-2.2.10.0-2340-不變)
* SDK 1.5.0 （沒有變更）

在這個版本包含下列的更新。

<table border="1">
<tr>
<th>標題</th>
<th>描述</th>
<th>受影響的區域 （例如，服務、 元件或 SDK）</p></th>
<th>叢集類型 （例如，Hadoop、 HBase 或大量）</th>
<th>JIRA</th>
</tr>


<tr>
<td>改善可靠性</td>
<td>我們進行修正程式，讓服務更有效地隨著負載增加提供叢集建立解答。</td>
<td>服務</td>
<td>所有</td>
<td>N/A</td>
</tr>



</table>
<br>

## <a name="notes-for-02182015-release-of-hdinsight"></a>備忘稿 HDInsight 02/18/2015年版本

HDInsight 叢集部署在這個版本的完整版數字︰

* HDInsight 2.1.10.471.1342507 (HDP 1.3.9.0-01351-不變)
* HDInsight 3.0.6.471.1342507 (HDP 2.0.9.0-2097-不變)
* HDInsight 3.1.3.471.1342507 (HDP 2.1.10.0-2290-不變)
* HDInsight 3.2.3.471.1342507 (HDP-2.2.10.0-2340)
* SDK 1.5.0

在這個版本包含下列的更新。

<table border="1">
<tr>
<th>標題</th>
<th>描述</th>
<th>受影響的區域 （例如，服務、 元件或 SDK）</p></th>
<th>叢集類型 （例如，Hadoop、 HBase 或大量）</th>
<th>JIRA （如果適用的話）</th>
</tr>


<tr>
<td>HDInsight 3.2 捨位叢集</td>
<td>Hadoop 2.6/HDP2.2 皆可取得 HDInsight 3.2 捨位叢集。 它所含所有開啟來源元件的重大更新。 如需詳細資訊，請參閱什麼是 HDInsight 和<a href ="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html" target="_blank">HDP 2.2.0.0 版本資訊</a>的新功能。</td>
<td>開啟來源軟體</td>
<td>所有</td>
<td>N/A</td>
</tr>

<tr>
<td>HDinsight linux （預覽版本）</td>
<td>叢集，可以執行 Ubuntu linux 部署。 如需詳細資訊，請參閱上 Linux 的快速入門 HDInsight。</td>
<td>服務</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>大量一般的顯示狀態</td>
<td>Apache 大量叢集通常可。 如需詳細資訊，請參閱開始使用大量 HDInsight。</td>
<td>服務</td>
<td>大量</td>
<td>N/A</td>
</tr>

<tr>
<td>虛擬機器大小</td>
<td>Azure HDInsight 會提供更多的虛擬機器類型與大小。 HDInsight 可以利用 A2 到 A7 的目的; 的內建的大小D 數列節點的功能 solid-state 機 (SSDs) 與 60%更快速的處理器;與 A8 和 A9 大小的 InfiniBand 支援的速度的網路。</td>
<td>服務</td>
<td>所有</td>
<td>N/A</td>
</tr>

<tr>
<td>叢集縮放比例</td>
<td>您可以變更資料執行 HDInsight 叢集的節點數目，而不需要刪除或重新建立。 目前，只 Hadoop 查詢和 Apache 大量的叢集類型有這項功能，但 Apache HBase 叢集類型推出所要遵循的支援。 如需詳細資訊，請參閱管理 HDInsight 叢集。</td>
<td>服務</td>
<td>Hadoop 大量</td>
<td>N/A</td>
</tr>

<tr>
<td>Visual Studio 工具</td>
<td>除了完成 Apache 大量的工具，以納入陳述式完成、 本機驗證] 和改良的偵錯支援已更新 Apache 登錄區 Visual Studio 中的工具。 如需詳細資訊，請參閱開始使用 HDInsight Hadoop 工具 Visual Studio。</td>
<td>工具</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>Hadoop DocumentDB 的連接器</td>
<td>Hadoop 連接線的 DocumentDB，您可以執行複雜的彙總、 分析和操作透過無結構描述的 JSON 文件儲存在 DocumentDB 集合或跨資料庫帳戶。 如需詳細資訊和教學課程，請參閱使用 DocumentDB 和 HDInsight 執行 Hadoop 工作。</td>
<td>服務</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>錯誤修正</td>
<td>我們所做的各種 HDInsight 服務的次要錯誤修正。 預期客戶具行為變更。</td>
<td>服務</td>
<td>所有</td>
<td>N/A</td>
</tr>

</table>
<br>

## <a name="notes-for-02062015-release-of-hdinsight"></a>備忘稿 HDInsight 02/06/2015年版本

HDInsight 叢集部署在這個版本的完整版數字︰

* HDInsight 2.1.10.463.1325367 (HDP 1.3.9.0-01351-不變)
* HDInsight 3.0.6.463.1325367 (HDP 2.0.9.0-2097-不變)
* HDInsight 3.1.2.463.1325367 (HDP 2.1.10.0-2290)
* SDK N/A

在這個版本包含下列的更新。

<table border="1">
<tr>
<th>標題</th>
<th>描述</th>
<th>受影響的區域 （例如，服務、 元件或 SDK）</p></th>
<th>叢集類型 （例如，Hadoop、 HBase 或大量）</th>
<th>JIRA （如果適用的話）</th>
</tr>


<tr>
<td>錯誤修正</td>
<td>我們所做的各種 HDInsight 服務的次要錯誤修正。 預期客戶具行為變更。</td>
<td>服務</td>
<td>所有</td>
<td>N/A</td>
</tr>

<tr>
<td>HDP 2.1 進行的維修作業更新</td>
<td>HDInsight 3.1 會更新部署 HDP 2.1.10.0。 如需詳細資訊，請參閱<a href ="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.10/bk_releasenotes_hdp_2.1/content/ch_relnotes-HDP-2.1.10.html" target="_blank">版本資訊 HDP 2.1.10</a>。 </td>
<td>開啟來源軟體</td>
<td>所有</td>
<td>N/A</td>
</tr>

<tr>
<td>HDP 二進位更新</td>
<td>HBase 已更新的檔案名稱中有幾個 JAR 檔案。 這些 JAR 檔案會使用內部 HBase，因此無法如預期客戶有相依性，這些 JAR 檔案的名稱。 這些功能包括︰
<ul>
<li>./lib/jetty-6.1.26.hwx.jar</li>
<li>./lib/jetty-sslengine-6.1.26.hwx.jar</li>
<li>./lib/jetty-util-6.1.26.hwx.jar</li>
</ul>
</td>
<td>開啟來源軟體</td>
<td>HBase</td>
<td>N/A</td>
</tr>

</table>
<br>

## <a name="notes-for-1292015-release-of-hdinsight"></a>備忘稿 HDInsight 1/29/2015年版本

HDInsight 叢集部署在這個版本的完整版數字︰

* HDInsight 2.1.10.455.1309616 (HDP 1.3.9.0-01351-不變)
* HDInsight 3.0.6.455.1309616 (HDP 2.0.9.0-2097-不變)
* HDInsight 3.1.2.455.1309616 (HDP 2.1.9.0-2196-不變)
* SDK N/A

在這個版本包含下列的更新。

<table border="1">
<tr>
<th>標題</th>
<th>描述</th>
<th>受影響的區域 （例如，服務、 元件或 SDK）</p></th>
<th>叢集類型 （例如，Hadoop、 HBase 或大量）</th>
<th>JIRA （如果適用的話）</th>
</tr>


<tr>

<td>錯誤修正</td>
<td>我們已經改善可靠性的 HDInsight 叢集 Azure 升級期間的幾個重要錯誤修正。</td>
<td>服務</td>
<td>所有</td>
<td>N/A</td>
</tr>



</table>
<br>

## <a name="notes-for-152015-release-of-hdinsight"></a>備忘稿 HDInsight 1/5/2015年版本

HDInsight 叢集部署在這個版本的完整版數字︰

* HDInsight 2.1.10.420.1246118 (HDP 1.3.9.0-01351-不變)
* HDInsight 3.0.6.420.1246118 (HDP 2.0.9.0-2097-不變)
* HDInsight 3.1.2.420.1246118 (HDP 2.1.9.0-2196-不變)


在這個版本包含下列的更新。

<table border="1">
<tr>
<th>標題</th>
<th>描述</th>
<th>元件</th>
<th>叢集類型</th>
<th>JIRA （如果適用的話）</th>
</tr>


<tr>
<td>Twitter 趨勢分析及 Mahout 為基礎的影片建議的範例</td>
<td><p>在這個版本中，HDInsight 查詢主控台會有兩個額外的範例︰</p>

<p><b>Twitter 分析趨勢</b><br>
公用網站，例如 Twitter 所提供的 Api 是很有用的資料來源來分析及瞭解常用的趨勢。 在本教學課程，瞭解如何使用登錄區取得 Twitter 使用者傳送大部分的推文包含特定文字的清單。 </p>

<p><b>Mahout 影片建議</b><br>
Apache Mahout 是 Apache Hadoop 電腦，學習文件庫。 Mahout 包含演算法來處理資料 （例如篩選、 分類及叢集）。 在本教學課程中，使用建議引擎產生影片建議根據您的朋友過的影片。</p></td>
<td>查詢主控台</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>變更登錄區設定的預設值為︰ hive.auto.convert.join.noconditionaltask.size</td>
<td><p>此大小設定適用於自動轉換的地圖連接。 值表示的可以轉換成雜湊地圖適合在記憶體中的表格大小的總和。 在先前的版本中，此值增加 10 MB 的預設值為 128 MB。 不過，新的值 128 mb 造成缺乏記憶體失敗到期的工作。 此版本會還原至 10 MB 預設值。 客戶仍然可以選擇叢集建立期間，覆寫此值指定其查詢及表格大小。 如需這項設定，以及如何將其覆寫的詳細資訊，請參閱<a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.0.2/ds_Hive/optimize-joins.html#JoinOptimization-OptimizeAutoJoinConversion" target="_blank">最佳化自動加入轉換</a>Hortonworks 文件。 </p></td>
<td>登錄區</td>
<td>Hadoop Hbase</td>
<td>N/A</td>
</tr>

</table>
<br>

## <a name="notes-for-12232014-release-of-hdinsight"></a>備忘稿 HDInsight 2014/12/23 版本

HDInsight 叢集部署在這個版本的完整版數字是︰

* HDInsight 2.1.10.420.1246783 （不變更 HDP 版本）
* HDInsight 3.0.6.420.1246783 （不變更 HDP 版本）
* HDInsight 3.1.1.420.1246783 （不變更 HDP 版本）

在這個版本包含下列的更新。

<table border="1">
<tr>
<th>標題</th>
<th>描述</th>
<th>元件</th>
<th>叢集類型</th>
<th>JIRA （如果適用的話）</th>
</tr>


<tr>
<td>間歇性叢集建立失敗，因為超出負荷</td>
<td><p>改良的演算法叢集建立期間下載 HDP 套件可讓您更強大的失敗次數，因為超出負荷的處理。</p></td>
<td>服務</td>
<td>Hadoop，Hbase，大量</td>
<td>N/A</td>
</tr>



</table>
<br>

## <a name="notes-for-12182014-release-of-hdinsight"></a>備忘稿 HDInsight 2014/12/18 版本

在這個版本包含下列元件更新。

<table border="1">
<tr>
<th>標題</th>
<th>描述</th>
<th>元件</th>
<th>叢集類型</th>
<th>JIRA （如果適用的話）</th>
</tr>

<tr>
<td><a href = "hdinsight-hadoop-customize-cluster.md" target="_blank">叢集自訂一般 Avalability</a></td>
<td><p>自訂，讓您自訂 Azure HDInsight 叢集 Apache Hadoop 生態系統提供的專案。 這項新功能，您可以嘗試與 Hadoop 專案部署至 Azure HDInsight。 這被啟用透過**指令碼的巨集指令**] 功能，可以修改 Hadoop 叢集任意方式使用自訂指令碼。 使用所有類型的 HDInsight 叢集包括 Hadoop、 HBase，以及大量這個自訂。 若要示範這項功能的功能，我們記錄安裝熱門<a href = "hdinsight-hadoop-spark-install.md" target="_blank">火花</a>、 <a href = "hdinsight-hadoop-r-scripts.md" target="_blank">R</a>， <a href = "hdinsight-hadoop-solr-install.md" target="_blank">Solr</a>和<a href = "hdinsight-hadoop-giraph-install.md" target="_blank">Giraph</a>模組的程序。 讓客戶指定自訂指令碼動作透過 Azure 入口網站中，提供原則，以及如何建立自訂指令碼動作使用協助方法的最佳作法，並提供有關如何測試指令碼的巨集指令的指導方針，也會加入此版本。 </p></td>
<td>一般功能可用性</td>
<td>所有</td>
<td>N/A</td>
</tr>


</table>
<br>

## <a name="notes-for-12052014-release-of-hdinsight"></a>備忘稿 HDInsight 2014/12/05 版本

HDInsight 叢集部署在這個版本的完整版數字是︰

* HDInsight 2.1.9.406.1221105 (HDP 1.3.9.0-01351)
* HDInsight 3.0.5.406.1221105 (HDP 2.0.9.0-2097)
* HDInsight 3.1.1.406.1221105 (HDP 2.1.9.0-2196)
* HDInsight SDK n/A

在這個版本包含下列元件更新。

<table border="1">
<tr>
<th>標題</th>
<th>描述</th>
<th>元件</th>
<th>叢集類型</th>
<th>JIRA （如果適用的話）</th>
</tr>

<tr>
<td>修正︰ 在登錄區 DDL 中為表格新增大量的磁碟分割區間歇性發生錯誤 </td>
<td><p>如果有斷斷續續連線錯誤與登錄區 metastore 資料庫分割許多加入登錄區資料表時，可能會失敗登錄區 DDL。 如果發生此錯誤，會看到下列陳述式登錄區錯誤記錄檔中︰ </p><p>「 錯誤 [主要]: ql.驅動程式 」 (SessionState.java:printError(547))-失敗︰ 執行錯誤，org.apache.hadoop.hive.ql.exec.DDLTask 傳回碼 1。 MetaException (message:java.lang.RuntimeException: commitTransaction 稱為但 openTransactionCalls = 0。 這可能表示有不對稱的來電至 openTransaction/commitTransaction） 」</p></td>
<td>登錄區</td>
<td>Hadoop Hbase</td>
<td>登錄區 482 （這是內部 JIRA，因此無法外部引號。 請注意以下供參考。）</td>
</tr>

<tr>
<td>修正︰ 偶爾掛斷 HDInsight 查詢主控台中檢視</td>
<td>發生這種情況下，可以看到下列陳述式 WebHCat 啟動器工作 WebHCat 記錄中︰ <p>「 org.apache.hive.hcatalog.templeton.CatchallExceptionMapper |org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.yarn.exceptions.YarnRuntimeException)︰ 無法載入歷程記錄檔 {wasb url 的歷程記錄檔} 」</p></td>
<td>WebHCat</td>
<td>Hadoop</td>
<td>登錄區 482 （這是內部 JIRA，因此無法外部引號。 請注意以下供參考。）</td>
</tr>

<tr>
<td>修正︰ 在 Hbase 查詢延遲偶爾特殊圖文集</td>
<td>如果這種情況，使用者會注意到 3 秒的偶爾特殊圖文集 Hbase 查詢的延遲時間。 </td>
<td>HDInsight 叢集閘道器</td>
<td>HBase</td>
<td>N/A</td>
</tr>

<tr>
<td>HDP JAR 檔案名稱變更</td>
<td>針對 HDI 叢集版本 3.0，那里幾個安裝 HDP 的內部 JAR 檔案的變更。 jetty 6.1.26.jar 已經取代 jetty 6.1.26.hwx.jar。 jetty-但是-6.1.26.jar 已經取代 jetty-但是-6.1.26.hwx.jar。 這些變更會套用到 Hadoop、 Mahout、 WebHCat 和 Oozie 專案。</td>
<td>Hadoop，Mahout，WebHCat Oozie</td>
<td>Hadoop HBase</td>
<td>N/A</td>
</tr>

</table>
<br>


## <a name="notes-for-11212014-release-of-hdinsight"></a>備忘稿 HDInsight 2014/11/21 版本

HDInsight 叢集部署在這個版本的完整版數字是︰

* HDInsight 2.1.9.382.1169709 （從 11/14/2014年沒有變更）
* HDInsight 3.0.5.382.1169709 （從 11/14/2014年沒有變更）
* HDInsight 3.1.1.382.1169709 （從 11/14/2014年沒有變更）
* HDINsight SDK 1.4.0

在這個版本包含下列元件更新。

<table border="1">
<tr>
<th>標題</th>
<th>描述</th>
<th>元件</th>
<th>叢集類型</th>
<th>JIRA （如果適用的話）</th>
</tr>

<tr>
<td>存取應用程式的記錄</td>
<td>若要以程式設計方式列舉叢集執行的應用程式，並下載相關應用程式特定或特定容器的記錄檔以協助偵錯有問題的應用程式的能力。</td>
<td>SDK</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>能夠在 IHdInsightClient.DeleteCluster 指定地區名稱 </td>
<td>Azure HDInsight SDK 能夠使用**DeleteCluster**時，指定地區名稱。 這可協助解除封鎖具有相同名稱的兩個資源在不同區域中，已無法刪除其中的客戶。</td>
<td>SDK</td>
<td>所有</td>
<td>N/A</td>
</tr>

<tr>
<td>ClusterDetails.DeploymentId</td>
<td>**ClusterDetails**物件傳回**DeploymentID**欄位代表叢集的唯一識別碼。 它一定是唯一叢集建立嘗試使用相同的名稱。</td>
<td>SDK</td>
<td>所有</td>
<td>N/A</td>
</tr>
</table>
<br>

## <a name="notes-for-11142014-release-of-hdinsight"></a>備忘稿 HDInsight 11/14/2014年版本

HDInsight 叢集部署在這個版本的完整版數字是︰

* HDInsight 2.1.9.382.1169709
* HDInsight 3.0.5.382.1169709
* HDInsight 3.1.1.382.1169709

在這個版本包含下列新功能、 元件更新，以及錯誤修正。

<table border="1">
<tr>
<th>標題</th>
<th>描述</th>
<th>元件</th>
<th>叢集類型</th>
<th>JIRA （如果適用的話）</th>
</tr>

<tr>
<td>指令碼動作 （預覽版本）</td>
<td>使用自訂指令碼，以任何方式叢集叢集自訂功能，可讓 Hadoop 修改的預覽。 使用此功能，可嘗試使用使用者，並將其部署 Apache Hadoop 生態 Azure HDInsight 叢集提供的專案中。 使用所有類型的 HDInsight 叢集，包括 Hadoop、 HBase，以及大量自訂功能。</td>
<td>新功能</td>
<td>所有</td>
<td>N/A</td>
</tr>

<tr>
<td>Azure 的網站和儲存記錄檔分析的預先建立的工作</td>
<td>HDInsight 查詢主控台有支援運作的解決方案，您的資料或範例資料的快速入門] 庫。
<p>**解決方案的處理您的資料**︰<br>
我們建立工作的一些最常見的資料分析案例提供開始建立您自己的解決方案。 您可以使用與工作的資料，瞭解其運作方式。 接著當您準備好時，會使用您已經學會如何建立預先建立的工作模型的解決方案。</p>
<p>**解決方案的處理範例資料**︰<br>
瞭解如何使用 HDInsight 的運作方式 （例如網頁記錄和感應器資料分析） 的一些基本案例說明。 您將學習如何使用 HDInsight 分析資料，以及連線其他應用程式與服務這個資料。 只要連接至 Microsoft Excel 中視覺化資料提供這種方法的範例。</p></td>
<td>查詢主控台</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>在 [Templeton 記憶體遺漏修正</td>
<td>在受影響客戶必須長時間執行叢集，或已送出工作的 100 s Templeton 記憶體遺漏要求已處理秒。 若要重新啟動服務都是顯示為 Templeton 5xx 錯誤與因應措施此問題。 已不再需要的因應措施。</td>
<td>Templeton</td>
<td>所有</td>
<td>https://issues.apache.org/jira/browse/HADOOP-11248</td>
</tr>
</table>
<br>


**附註**︰ 示範叢集自訂所提供的新功能，沒有記載叢集上安裝火花和 R 模組使用指令碼的巨集指令的程序。 如需進一步資訊，請參閱︰

* [安裝並使用火花 1.0 HDInsight 叢集上](hdinsight-hadoop-spark-install.md)
* [安裝並使用 R HDInsight Hadoop 叢集上](hdinsight-hadoop-r-scripts.md)




## <a name="notes-for-11072014-release-of-hdinsight"></a>備忘稿 HDInsight 2014/11/07 版本

部署在這個版本的 HDInsight 叢集的完整版數字是︰

* HDInsight 2.1 2.1.9.374.1153876
* HDInsight 3.0 3.0.5.374.1153876
* HDInsight 3.1 3.1.1.374.1153876

在這個版本包含下列元件更新。

<table border="1">
<tr>
<th>標題</th>
<th>描述</th>
<th>元件</th>
<th>叢集類型</th>
<th>JIRA （如果適用的話）</th>
</tr>

<tr>
<td>HDP 2.1.7</td>
<td>此版本依據 Hortonworks 資料平台 (HDP) 2.1.7 上。 如需詳細資訊，請參閱<a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html" target="_blank">HDP 2.1.7 的版本資訊</a>。</td>
<td>HDP</td>
<td>所有</td>
<td>N/A</td>
</tr>

<tr>
<td>YARN 時間表伺服器</td>
<td>依預設已啟用 YARN 時間表伺服器 （也稱為一般應用程式記錄）。 時間表伺服器提供完成的應用程式 （例如應用程式識別碼、 應用程式的名稱、 應用程式狀態、 應用程式提交時間與應用程式的完成時間） 的一般資訊。

存取 URI http://headnodehost:8188，或執行 YARN 命令，可以從主節點擷取此應用程式的資訊︰ yarn 應用程式 – 清單 – appStates 所有。

這項資訊也擷取遠端雖然在 https://{ClusterDnsName REST API}。 azurehdinsight.net/ws/v1/applicationhistory/。

如需詳細資訊，請參閱<a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">YARN 時間表伺服器</a>。</td>
<td>服務 YARN</td>
<td>Hadoop HBase</td>
<td>N/A</td>
</tr>

<tr>
<td>叢集部署識別碼</td>
<td>開始使用 SDK 版本 1.3.3.1.5426.29232，使用者可以存取每個叢集，不會發出 HDInsight 的唯一的識別碼。 這可讓客戶重複使用的 DNS 名稱時，瞭解唯一的執行個體的叢集橫向建立或卸除案例。</td>
<td>SDK</td>
<td>所有</td>
<td>N/A</td>
</tr>
</table>
<br>

**附註**︰ 這個版本中已修正無法完整版本號碼，顯示在入口網站，或從 SDK 或 Windows PowerShell 傳回錯誤。

## <a name="notes-for-10152014-release"></a>備忘稿 2014/10/15 版本

此 hotfix 版本修正記憶體遺漏 Templeton 受影響的 Templeton 大量使用者。 在某些情況下，執行 Templeton 大量的使用者會看到顯示為 500 錯誤碼，因為要求不會有足夠的記憶體，若要執行的錯誤。 此問題的因應措施是重新啟動 Templeton 服務。 已經修正此問題。


## <a name="notes-for-1072014-release"></a>備忘稿 2014/10/7 版本

* 使用 Ambari 端點時 「 https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname} 」， *host_name*欄位會傳回的完整的網域名稱 (FQDN)，而不是只有主機名稱的節點。 例如，傳回 「**headnode0**」，而不是您取得 FQDN 」**headnode0。 {ClusterDNS}.azurehdinsight.net**」。 以利於進行分析藍本位置 （例如 HBase 和 Hadoop） 的多個叢集類型可以部署虛擬網路需要這項變更。 這種情況，例如，使用 Hadoop 做為後端平台 HBase 時。

* 我們提供新的記憶體設定預設的部署 HDInsight 叢集。 先前的預設記憶體設定未累積負責處理部署的 CPU 核心數目的指引。 這些新的記憶體設定應提供更佳的預設值 （依照 Hortonworks 建議）。 若要變更這些，請參閱 SDK 參照文件，瞭解如何變更叢集設定。 新的記憶體設定預設 4 CPU 核心 （8 容器） HDInsight 叢集所使用的是下表中分項。 （此版本之前所使用的值是也提供併入）。

<table border="1">
<tr><th>元件</th><th>記憶體配置</th></tr>
<tr><td> yarn.scheduler.minimum 配置</td><td>768 MB (先前為 512 MB)</td></tr>
<tr><td> yarn.scheduler.maximum 配置</td><td>6144 MB （沒有變更）</td></tr>
<tr><td>yarn.nodemanager.resource.memory</td><td>6144 MB （沒有變更）</td></tr>
<tr><td>mapreduce.map.memory</td><td>768 MB (先前為 512 MB)</td></tr>
<tr><td>mapreduce.map.java.opts</td><td>選擇從 = Xmx512m (先前-Xmx410m)</td></tr>
<tr><td>mapreduce.reduce.memory</td><td>1536 MB (先前為 1024 MB)</td></tr>
<tr><td>mapreduce.reduce.java.opts</td><td>選擇從 = Xmx1024m (先前-Xmx819m)</td></tr>
<tr><td>yarn.app.mapreduce.am.resource</td><td>768 MB (先前為 1024 MB)</td></tr>
<tr><td>yarn.app.mapreduce.am.command</td><td>選擇從 = Xmx512m (先前-Xmx819m)</td></tr>
<tr><td>mapreduce.task.io.sort</td><td>256 MB (先前為 200 MB)</td></tr>
<tr><td>tez.am.resource.memory</td><td>1536 MB （沒有變更）</td></tr>

</table><br>

如需有關使用 YARN 和 MapReduce 所使用的 HDInsight Hortonworks 資料平台上的記憶體組態設定的詳細資訊，請參閱[判斷 HDP 記憶體設定的設定](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1-latest/bk_installing_manually_book/content/rpm-chap1-11.html)。 Hortonworks 也會提供的工具來計算適當的記憶體設定。

關於 PowerShell 的 Azure 和 HDInsight SDK 錯誤訊息: 「*叢集未設定為 HTTP 服務存取*」:

* 此錯誤是已知的[相容性問題](https://social.msdn.microsoft.com/Forums/azure/a7de016d-8de1-4385-b89e-d2e7a1a9d927/hdinsight-powershellsdk-error-cluster-is-not-configured-for-http-services-access?forum=hdinsight)，可能是因為 PowerShell 的 Azure HDInsight SDK 的版本及叢集版本的差異。 8/15 或更新版本建立的叢集有支援將虛擬網路的新佈建功能。 不過這項功能已不正確地解譯較舊版本的 HDInsight SDK 或 PowerShell 的 Azure。 結果是在某些工作送出作業失敗。 如果您使用 HDInsight SDK Api 或 Azure PowerShell cmdlet （**使用 AzureRmHDInsightCluster**或**叫用 AzureRmHDInsightHiveJob**） 提交工作，這些作業可能失敗的錯誤訊息 「*叢集<clustername>未設定為 HTTP 服務存取*。 」 或者 （根據作業），您可能會收到其他錯誤訊息，例如 「*無法連線至叢集*」。

* PowerShell 的 Azure HDInsight SDK 的最新版本中解決這些相容性問題。 我們建議您更新 HDInsight SDK 1.3.1.6 版本或更新版本和 Azure PowerShell 工具版本 0.8.8 或更新版本。 您可以存取從最新的 HDInsight sdk [](http://nuget.codeplex.com/wikipage?title=Getting%20Started) ，以及如何[安裝和設定 Azure PowerShell](../powershell-install-configure.md)的 Azure PowerShell 工具。



## <a name="notes-for-9122014-release-of-hdinsight-31"></a>備忘稿 HDInsight 3.1 2014/9/12 版本

* 此版本依據 Hortonworks 資料平台 (HDP) 2.1.5 上。 在這個版本中修正問題的清單，請參閱 Hortonworks 網站上的 [[固定在這個版本中的](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.5/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.5-fixed.html)頁面。
* 在豬文件庫資料夾中，「 avro-mapred-1.7.4.jar 「 檔案已變更為 「 avro-mapred-1.7.4-hadoop2.jar。 」 此檔案的內容的內容是不分行次要錯誤修正。 建議的客戶請勿直接相依性的 JAR 檔案，以避免分頁符號時重新命名檔案的名稱。


## <a name="notes-for-8212014-release"></a>備忘稿 2014/8/21 版本

* 我們會新增以下 WebHCat 設定 (登錄區-7155)，這會將預設的記憶體限制 Templeton 控制器工作設定為 1 GB。 （先前的預設值是 512 MB）。

     templeton.mapper.memory.mb （= 1024年）

    * 這項變更地址某些登錄區查詢之前執行至較低的記憶體限制因為下列錯誤: 「 容器執行實體記憶體限制，超出 」。
    * 若要還原舊版的預設值，您可以將此設定值為 512 透過 PowerShell 的 Azure 在叢集建立時使用下列命令︰

        新增 AzureRmHDInsightConfigValues-核心@{"templeton.mapper.memory.mb"="512";}


* 動物園管理員角色的主機名稱已變更為*動物園管理員*。 這會影響名稱解析叢集，但不會影響外部 REST Api。 如果您有使用*zookeepernode*主機名稱的元件，您需要更新他們使用新的名稱。 有三個動物園管理員節點的新名稱︰
    * zookeeper0
    * zookeeper1
    * zookeeper2
* HBase 版本支援矩陣會更新。 生產 HBase 工作負載的支援 HDInsight 版本 3.1 (HBase 版本 0.98)。 版本 3.0 （這是預覽） 不支援移動轉寄。

## <a name="notes-about-clusters-created-prior-to-8152014"></a>叢集 8/15/2014年之前所建立的相關附註

PowerShell 的 Azure 或 HDInsight SDK 錯誤訊息，「 叢集<clustername>未設定為 HTTP 服務存取 」 (或作業，根據其他錯誤訊息例如: 「 無法連線至叢集 」) 可能會遇到因為版本之間的差異 PowerShell 的 Azure HDInsight SDK 或叢集。 8/15 或更新版本建立的叢集有支援將虛擬網路的新佈建功能。 此功能無法正確地解譯較舊版本的 PowerShell 的 Azure 或 HDInsight sdk，您可以藉此失敗的工作送出作業。 如果您使用 （例如使用 AzureRmHDInsightCluster 或叫用 AzureRmHDInsightHiveJob） HDInsight SDK Api 或 Azure PowerShell cmdlet 來提交工作時，這些作業可能失敗的其中一個所述的錯誤訊息。

PowerShell 的 Azure HDInsight SDK 的最新版本中解決這些相容性問題。 我們建議您更新 HDInsight SDK 1.3.1.6 版本或更新版本和 Azure PowerShell 工具版本 0.8.8 或更新版本。 您可以存取最新的 HDInsight sdk 從[NuGet][nuget-link]。 您可以使用[Microsoft Web 平台安裝程式]來存取 Azure PowerShell 工具[webpi-link]。


## <a name="notes-for-7282014-release"></a>備忘稿 2014/7/28 版本

* **HDInsight 提供的新區域**︰ 我們會展開 HDInsight 地理位置的目前狀態，以三個區域。 HDInsight 客戶可以在下列區域內建立叢集︰
    * 中式地址
    * 北美美國中部
    * 美國中部南美洲
* HDInsight 版本 1.6 （HDP 1.1 （英文） 和 Hadoop 1.0.3） 和 HDInsight 2.1 （HDP1.3 和 Hadoop 1.2） 版正在移除從 Azure 入口網站。 您可以繼續使用 PowerShell 的 Azure cmdlet，[新增 AzureRmHDInsightCluster](http://msdn.microsoft.com/library/dn593744.aspx)或使用[HDInsight SDK](http://msdn.microsoft.com/library/azure/dn469975.aspx)建立 Hadoop 叢集這些版本。 請參閱 [ [HDInsight 元件版本設定](hdinsight-component-versioning.md)] 頁面，如需詳細資訊。
* 在這個版本中 Hortonworks 資料平台 (HDP) 變更︰

<table border="1">
<tr><th>HDP</th><th>變更</th></tr>
<tr><td>HDP 1.3 / HDI 2.1</td><td>沒有變更</td></tr>
<tr><td>HDP 2.0 / HDI 3.0</td><td>沒有變更</td></tr>
<tr><td>HDP 2.1 / HDI 3.1</td><td>動物園管理員: ['3.4.5.2.1.3.0-1948']-> ['3.4.5.2.1.3.2-0002']</td></tr>


</table><br>

## <a name="notes-for-6242014-release"></a>備忘稿 2014/6/24 版本

在這個版本包含 HDInsight 服務的增強功能︰

* **HDP 2.1 可用性**︰ HDInsight 3.1 （其中包含 HDP 2.1） 是推出，而是新的叢集的預設版本。
* **HBase – Azure 入口網站的改良功能**︰ 我們會提供 HBase 叢集供預覽中。 您可以建立 HBase 叢集從按幾下滑鼠入口網站。 

使用 HBase，您可以建立各種不同的即時工作負載 HDInsight，在使用服務儲存來自結束點感應器與遙測資料的大型資料集的互動式網站。 下一步，就是分析中的 Hadoop 工作，這些工作負載的資料，這是在 HDInsight 透過 PowerShell 的 Azure 和登錄區叢集儀表板。

### <a name="apache-mahout-preinstalled-on-hdinsight-31"></a>預先安裝在 HDInsight 3.1 Apache Mahout

 您可以執行 Mahout 工作，而不需要使用其他叢集設定[mahout](http://hortonworks.com/hadoop/mahout/)是 HDInsight 3.1 Hadoop 叢集上預先安裝。 例如，您可以遠端成 Hadoop 叢集使用遠端桌面通訊協定 (RDP) 並不額外的步驟，您可以執行下列認識全球 Mahout 命令︰

        mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

        mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

此程序更完整的說明，請參閱 Apache Mahout 網站上的文件， [Breiman 範例](https://mahout.apache.org/users/classification/breiman-example.html)。


### <a name="hive-queries-can-use-tez-in-hdinsight-31"></a>登錄區查詢可用於 HDInsight 3.1 的 Tez

登錄區 0.13 HDInsight 3.1 和提供能夠執行使用 Tez，可以利用效能大幅改善查詢。
Tez 不是預設的登錄區查詢的啟用。 若要使用它，您必須選擇加入集。 您可以藉由執行下列程式碼片段啟用 Tez:

        set hive.execution.engine=tez;
        select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

為在標準的基準已傳送 Hortonworks 發佈登錄區查詢效能增強功能與 Tez 詳細的解說。 如需詳細資訊，請參閱[效能 Apache 登錄區 13 的企業 Hadoop](http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/)。

如需詳細瞭解使用 Tez 登錄區的詳細資訊，請參閱[在 Tez 登錄區](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez)。

###<a name="global-availability"></a>全域可用性
Hadoop 2.2 上 HDInsight 版本 Microsoft 已提供 HDInsight 中所有的主要地區 Azure 有的何處。 具體來說，西部歐洲及東南亞資料中心有已連線。 這可讓客戶的資料中心，為關閉，而且可能類似的規範要求的區域中，找出叢集。


###<a name="dos--donts-between-cluster-versions"></a>該做與不要的叢集版本之間

**使用 HDInsight 3.1 叢集 Oozie metastores 不相容的 HDInsight 2.1 叢集，而無法使用此舊的版本**。

自訂 Oozie metastore 資料庫部署與 HDInsight 3.1 叢集無法與 HDInsight 2.1 叢集重複使用。 即使 metastore 起始與 HDInsight 2.1 叢集，這是大小寫。 不支援這種情況下，因為 metastore 結構描述取得升級時使用 HDInsight 3.1 叢集，因此不再與所需的 HDInsight 2.1 叢集 metastore 相容。 嘗試重複使用的使用 HDInsight 3.1 叢集 Oozie metastore 會呈現 HDInsight 2.1 叢集沒有用。

**Oozie metastores 無法叢集跨共用。**

Oozie metastores 會附加到特定叢集，而且無法橫跨叢集。

###<a name="breaking-changes"></a>重大變更

**加上字首語法**︰ 只 」 wasbs: / / 「 HDInsight 3.1 和 3.0 叢集中支援的語法。 舊版 「 asv: / / 「 語法支援，HDInsight 2.1 和 1.6 叢集，但並不支援在 HDInsight 3.1 或 3.0 叢集。 這表示明確地使用任何工作送出到 HDInsight 3.1 或 3.0 叢集 」 asv: / / 「 語法會失敗。 「 Wasbs: / / 「 語法應該改用。 此外，送到任何 HDInsight 3.1 或 3.0 叢集與現有的 metastore 包含明確的參照，使用的資源所建立的工作 「 asv: / / 「 語法會失敗。 這些 metastores 必須重新建立使用 「 wasbs: / / 「 地址資源的語法。


**連接埠**︰ HDInsight 服務所使用的連接埠已變更。 已使用連接埠號碼的暫時連接埠範圍內的 Windows 作業系統。 連接埠是自動配置，從預先定義的暫時範圍短暫網際網路通訊協定的通訊。 若要避免發生衝突可能會產生的前端節點上執行的服務所使用的連接埠此範圍之外，是全新的可容許 Hortonworks 資料平台 (HDP) 服務連接埠號碼。 新的連接埠號碼不應該會導致相關的任何變更。 使用的數字如下所示︰

 **HDInsight 1.6 (HDP 1.1 （英文))**
<table border="1">
<tr><th>名稱</th><th>值</th></tr>
<tr><td>dfs.http.address</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.secondary.http.address</td><td>0.0.0.0:30090</td></tr>
<tr><td>mapred.job.tracker.http.address</td><td>jobtrackerhost:30030</td></tr>
<tr><td>mapred.task.tracker.http.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>mapreduce.history.server.http.address</td><td>0.0.0.0:31111</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

 **HDInsight 3.1 及 3.0 (HDP 2.1 和 2.0)**
<table border="1">
<tr><th>名稱</th><th>值</th></tr>
<tr><td>dfs.namenode.http 地址</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.namenode.https 地址</td><td>headnodehost:30470</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.namenode.secondary.http 地址</td><td>0.0.0.0:30090</td></tr>
<tr><td>yarn.nodemanager.webapp.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

###<a name="dependencies"></a>相依性

新增下列相依性 HDInsight 3.x (HDP2.x):

* guice servlet
* optiq 核心
* javax.inject
* 啟動
* jsr305
* geronimo-jaspic_1.0_spec
* 年 7 月-slf4j
* java xmlbuilder
* ant
* common 編譯器
* jdo api
* common math3
* paranamer
* jaxb impl
* stringtemplate
* eigenbase xom
* 紐澤西 servlet
* common 執行
* jaxb api
* jetty 所有伺服器
* janino
* xercesImpl
* optiq avatica
* jta
* eigenbase 屬性
* groovy 所有
* hamcrest 核心
* 郵件
* linq4j
* jpam
* 紐澤西用戶端
* aopalliance
* geronimo-annotation_1.0_spec
* ant 啟動器
* 紐澤西 guice
* xml api
* stax api
* asm common
* asm 樹狀目錄
* wadl
* geronimo-jta_1.1_spec
* guice
* leveldbjni 所有
* 速度
* jettison
* 俏皮 java
* jetty 所有
* common dbcp

下列相依性無法再存在於 HDInsight 3.x (HDP2.x):

* jdeb
* kfs
* sqlline
* 長春藤
* aspectjrt
* json
* 核心
* jdo2 api
* avro mapred
* datanucleus 加強
* jsp
* common-記錄-api
* common 數學
* JavaEWAH
* aspectjtools
* javolution
* hdfsproxy
* hbase
* 俏皮

###<a name="version-changes"></a>版本變更

下列版本變更之間 HDInsight 2.x (HDP1.x) 和 HDInsight 3.x (HDP2.x):

* 指標核心: ['2.1.2']-> ['3.0.0']
* derbynet: ['10.4.2.0']-> ['10.10.1.1']
* datanucleus: ['rdbms-3.0.8']-> ['rdbms-3.2.9']
* jasper 編譯器: ['5.5.12']-> ['5.5.23']
* log4j: ['1.2.15 「，」 1.2.16']-> ['1.2.16 「，」 1.2.17']
* derbyclient: ['10.4.2.0']-> ['10.10.1.1']
* httpcore: ['4.2.4']-> ['4.2.5']
* hsqldb: ['1.8.0.10']-> ['2.0.0']
* jets3t: ['0.6.1']-> ['0.9.0']
* protobuf java: ['2.4.1']-> ['2.5.0']
* derby: ['10.4.2.0']-> ['10.10.1.1']
* jasper: [' 執行階段-5.5.12']-> [[' 執行階段-5.5.23']
* common 精靈: ['1.0.1']-> ['1.0.13']
* datanucleus 核心: ['3.0.9']-> ['3.2.10']
* datanucleus-api-jdo: ['3.0.7']-> ['3.2.6']
* 動物園管理員: ['3.4.5.1.3.9.0-01320']-> ['3.4.5.2.1.3.0-1948']
* bonecp: ['0.7.1.RELEASE']-> [」
* 0.8.0.RELEASE']


### <a name="drivers"></a>驅動程式
SQL Server 的 Java 資料庫 Connnectivity (JDBC) 驅動程式由 HDInsight 內部使用，並不會使用外部的作業。 如果您想要使用 [開放式資料庫連接 (ODBC) 連線到 HDInsight，請使用 Microsoft 登錄區 ODBC 驅動程式。 如需詳細資訊，請參閱[將 Excel 連接至 Microsoft 登錄區 ODBC 驅動程式 HDInsight](hdinsight-connect-excel-hive-odbc-driver.md)。


### <a name="bug-fixes"></a>錯誤修正

在這個版本中，我們已重新整理數個錯誤修正下列 HDInsight 版本︰

* HDInsight 2.1 (HDP 1.3)
* HDInsight 3.0 (HDP 2.0)
* HDInsight 3.1 (HDP 2.1)


## <a name="hortonworks-release-notes"></a>Hortonworks 版本資訊

在下列位置可使用 HDInsight 版本叢集 Hortonworks 資料平台 (HDPs) 的版本資訊︰

* HDInsight 版本 3.1 使用 Hadoop 分配為基礎[的資料平台 Hortonworks 2.1.7][hdp-2-1-7]。 這是使用 Azure 入口網站後 11/7/2014年時所建立的預設 Hadoop 叢集。 建立前 11/7/2014年已根據[Hortonworks 資料平台 2.1.1] HDInsight 3.1 叢集[hdp-2-1-1]

* HDInsight 3.0 版使用 Hadoop 分配為基礎[Hortonworks 資料平台 2.0][hdp-2-0-8]。

* HDInsight 2.1 版使用 Hadoop 分配為基礎[Hortonworks 資料平台 1.3][hdp-1-3-0]。

* HDInsight 1.6 版使用 Hadoop 分配[Hortonworks 資料平台 1.1 （英文)]為基礎的[hdp-1-1-0]。

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[nuget-link]: https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.HDInsight/

[webpi-link]: http://go.microsoft.com/?linkid=9811175&clcid=0x409

[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/
 
