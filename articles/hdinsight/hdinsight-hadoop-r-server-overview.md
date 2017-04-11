<properties
    pageTitle="什麼是上 HDInsight R？ 在 HDInsight （預覽版本） 的 R Server 簡介 |Microsoft Azure"
    description="什麼是 R 伺服器 HDInsight （預覽版本），以及如何建立應用程式，以顯示較大的資料分析中使用 R 伺服器上。"
    services="hdinsight"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/17/2016"
   ms.author="jeffstok"/>


# <a name="overview-of-r-server-on-hdinsight-preview"></a>R 伺服器上 HDInsight 概觀\(預覽\)

Microsoft Azure HDInsight 進階版，請使用 Microsoft R Server 現在可用選項之一是當您建立 Azure HDInsight 叢集時。 這項新功能提供資料科學家、 統計學家，並視需要調整，存取 R 程式設計分散式 HDInsight 在分析資料的方法。

叢集可以調整為專案與工作和破壞不再需要的是它們。 因為它們是 Azure HDInsight 的一部分，這些叢集隨附於企業層級 24/7 支援、 SLA 的 99.9%的執行時間和彈性整合與 Azure 生態系統中的其他元件。

>[AZURE.NOTE] 只有在 HDInsight 進階版中使用 R 伺服器。 目前，HDInsight 進階版只適用於 Hadoop 和火花叢集。 因此，目前您可以使用 R 伺服器只能與 Hadoop 和火花叢集 HDInsight。 如需詳細資訊，請參閱[不同的服務等級與 Hadoop 元件提供 HDInsight 是什麼？](hdinsight-component-versioning.md)。

R 伺服器上 HDInsight 提供 R 型分析的最新功能的載入至 Azure Blob 儲存體大型資料集。 內建 R 伺服器上開啟來源 R，因為您建立的 R 型應用程式可以利用任何 8000 + 開啟來源 R 套件，以及 ScaleR，包含 R 伺服器的 Microsoft 的大型資料分析套件的常式。

進階版叢集的邊緣節點提供便於連線到叢集，並執行 R 指令碼。 邊緣節點，您可以在邊緣節點伺服器的核心之間執行 ScaleR 的平行化分散式的函數的選擇。 您也可以選擇透過 ScaleR 的 Hadoop 地圖減少整個叢集節點執行這些或火花計算內容。

[模型] 或 [分析結果可下載的預測使用內部部署。 他們可以也會 operationalized 其他位置中 Azure，例如透過[Azure 電腦學習 Studio](http://studio.azureml.net) [web 服務](../machine-learning/machine-learning-publish-a-machine-learning-web-service.md)。

## <a name="get-started-with-r-on-hdinsight"></a>快速入門上 HDInsight R

HDInsight 叢集加 R 伺服器，請您必須使用 [進階] 選項建立 Hadoop 」 或 「 火花叢集，當您建立叢集使用 Azure 入口網站。 這兩種叢集類型使用相同的設定，包括 R 伺服器叢集，資料節點，以及邊緣節點為 R 伺服器端為基礎的分析登陸區域。 請參閱[快速入門 R 伺服器上 HDInsight](hdinsight-hadoop-r-server-get-started.md)上建立叢集深入逐步解說。

## <a name="learn-about-data-storage-options"></a>深入了解資料儲存選項

預設儲存空間的 HDInsight 叢集是以對應到 blob 容器 HDFS 檔案系統的 Blob 儲存體。 這可確保任何資料是上傳至叢集存放區，或寫入叢集存放區的分析過程所做常設。 您可以使用[AzCopy](../storage/storage-use-azcopy.md)公用程式 blob 中往返複製資料。

除了使用 Blob 儲存體，必須使用叢集[Azure 資料湖儲存空間](https://azure.microsoft.com/services/data-lake-store/)的選項。 如果您使用的資料湖，然後您可以使用 Blob 儲存體和資料湖 HDFS 儲存空間。

您也可以使用[Azure 檔案](../storage/storage-how-to-use-files-linux.md)儲存選項，適用於邊緣節點。 Azure 檔案可讓您裝載 Linux 檔案系統中 Azure 儲存體所建立的檔案共用。 如需更多有關 HDInsight 叢集 R 伺服器的資料儲存選項的詳細資訊，請參閱[儲存選項 HDInsight 叢集 R 伺服器](hdinsight-hadoop-r-server-storage.md)。

## <a name="access-r-server-on-the-cluster"></a>叢集上存取 R 伺服器

R 伺服器建立叢集之後，您可以連線到 R 主控台透過 SSH/PuTTY 叢集的邊緣節點。 如果您選擇要上邊緣節點安裝選用的 RStudio 伺服器 IDE，您也可以連線透過瀏覽器。 如需有關如何安裝 RStudio 伺服器的詳細資訊，請參閱[安裝 RStudio 伺服器 HDInsight 叢集上](hdinsight-hadoop-r-server-install-r-studio.md)。   

## <a name="develop-and-run-r-scripts"></a>開發並執行 R 指令碼

建立和執行 R 指令碼可以使用任何 8000 + 開啟來源 R 套件除了平行化和分散式常式 ScaleR 文件庫中。 一般而言上邊緣節點, R 伺服器中執行上執行指令碼 R 手語翻譯中的節點。 例外是呼叫 ScaleR 這些步驟以計算內容設定 Hadoop 地圖縮小 (RxHadoopMR) 或火花 (RxSpark) 函數。

在這些情況下，函數會執行分散式方式在這些資料 （工作） 叢集節點所參照的資料與相關聯。 如需不同的計算內容選項的詳細資訊，請參閱[計算 R 伺服器上 HDInsight 進階內容選項](hdinsight-hadoop-r-server-compute-contexts.md)。

## <a name="operationalize-a-model"></a>Operationalize 模型

完成您的資料模型時，您可以 operationalize 進行預測同時 Azure 及內部部署中的新資料模型。 此程序稱為計分。 以下是一些範例。

### <a name="score-in-hdinsight"></a>HDInsight 中的成績

若要在 HDInsight 分數，寫 R 函數會撥打您的模型，讓您已載入您儲存的帳戶的新資料檔案的預測。 回到儲存帳戶，然後儲存預測。 您可以執行例行視需要在邊緣節點叢集或使用排程的工作。  

### <a name="score-in-azure-machine-learning"></a>Azure 機器學習中的成績

若要使用的 Azure 電腦學習 web 服務分數，使用開啟來源 Azure 電腦學習 R 封裝稱為[AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html)發佈為 Azure web service 模型。 為了方便，此套件是邊緣節點上預先安裝。 接下來，請使用的設備電腦學習建立 web 服務的使用者介面，並視需要為分數，然後呼叫 web 服務。

如果您選擇此選項時，必須將任何 ScaleR 模型物件轉換成相當於開啟來源模型物件與 web 服務使用。 這可以使用 ScaleR 強制型轉指令函數，例如`as.randomForest()`ensemble 為基礎的模型。


### <a name="score-on-premises"></a>成績內部部署

若要至分數內部部署建立模型之後，您可以序列化 R 中的模型、 下載、 還原序列化，並依計分新資料。 您可以在使用[中 HDInsight 計分](#scoring-in-hdinsight)稍早所述的方法，或使用[DeployR](https://deployr.revolutionanalytics.com/)分數新資料。

## <a name="maintain-the-cluster"></a>維護叢集

### <a name="install-and-maintain-r-packages"></a>安裝及維護 R 套件

您使用的 R 封裝最都必須在邊緣節點由於大部分的 R 指令碼會執行那里。 若要安裝其他 R 套件上邊緣節點，您可以使用一般`install.packages()`方法。

在大部分情況下，您不需要安裝其他 R 封包資料節點，如果您只使用常式從 ScaleR 文件庫叢集。 不過，您可能需要額外的套件，以支援使用**rxExec**或**RxDataStep**執行資料節點。

在下列情況下，其他封包必須指定透過使用指令碼動作建立叢集後。 如需詳細資訊，請參閱[建立與 R 伺服器 HDInsight 叢集](hdinsight-hadoop-r-server-get-started.md)。   

### <a name="change-hadoop-map-reduce-memory-settings"></a>變更 Hadoop 地圖減少記憶體設定

您可以修改叢集變更，就能使用 R 伺服器執行地圖減少工作的記憶體數量。 若要修改叢集，使用可透過 Azure 的入口網站刀叢集在 Apache Ambari ui。 如需有關如何存取叢集 Ambari UI 的指示，請參閱 <<c0>管理 HDInsight 叢集使用 Ambari 網路使用者介面。

此外，也可以變更是 R 伺服器使用 Hadoop 參數中**RxHadoopMR**通話，如下所示的記憶體數量︰

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>不按比例縮放叢集

現有的叢集可以向上或向下調整透過入口網站。 縮放比例，您可以取得其他功能，您可能需要較大的處理任務，或您可以調整回叢集閒置時。 如需有關如何叢集不按比例縮放的指示，請參閱[管理 HDInsight 叢集](hdinsight-administer-use-portal-linux.md)。

### <a name="maintain-the-system"></a>維護系統

在離峰時間套用 OS 修補程式及其他更新執行基礎 Linux Vm HDInsight 叢集進行的維修作業。 一般而言，將進行的維修作業完成 3:30 am （根據當地時間 vm），則每一個星期一和星期四。 不會執行更新的方式，不影響叢集的多個季一次。  

由於標頭節點重複，而且並非所有資料節點的都影響，在這段期間執行任何工作可能會降低。 他們應該仍執行完成，不過。 任何自訂軟體或您的本機資料會保留在這些進行的維修作業事件除非嚴重失敗發生需要叢集重建。

## <a name="learn-about-ide-options-for-r-server-on-an-hdinsight-cluster"></a>瞭解 R 伺服器上 HDInsight 叢集 IDE 選項

HDInsight 進階版叢集 Linux 邊緣節點是 R 型分析的登陸區域。 連線到叢集之後, 便可啟動 R 伺服器主控台介面 Linux 命令提示字元輸入**R** 。 如果您在另一個視窗中，執行文字編輯器 R 指令碼開發剪下和節的指令碼貼入 R 主控台視，增強主控台介面。

更複雜的 R 指令碼的開發工具是 R 型 IDE 適用於桌面，例如 Microsoft 的最近本項[Visual Studio R 工具](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx)(RTVS)。 這是一系列的桌面和伺服器[RStudio](https://www.rstudio.com/products/rstudio-server/)工具。 您也可以使用 Walware 的蝕型[StatET](http://www.walware.de/goto/statet)。

另一個選項是安裝 IDE Linux 邊緣節點本身。  常用的選項是[RStudio 伺服器](https://www.rstudio.com/products/rstudio-server/)，以瀏覽器為基礎的 IDE 使用遠端用戶端。 邊緣叢集的節點 HDInsight 進階版安裝 RStudio 伺服器的開發與執行 R 指令碼 R 伺服器叢集上提供完整的 IDE 體驗。 可能相當於 R 主控台提高生產力。  如果您想要使用 RStudio 伺服器，請參閱[安裝 RStudio 伺服器 HDInsight 叢集上](hdinsight-hadoop-r-server-install-r-studio.md)。

## <a name="learn-about-pricing"></a>深入了解價格

R 伺服器 HDInsight 進階版叢集與相關聯的費用是結構化同樣的標準 HDInsight 叢集費用。 它們根據基礎 Vm 的縮放名稱、 資料，以及邊緣節點，搭配核心小時 uplift 進階版。 如需關於 HDInsight 進階版價格，包括價格期間公用預覽和可用的 30 天免費試用版，請參閱[HDInsight 價格](https://azure.microsoft.com/pricing/details/hdinsight/)。

## <a name="next-steps"></a>後續步驟

請遵循下列連結，閱讀更多有關如何使用 HDInsight 叢集 R 伺服器。

- [在 HDInsight R Server 快速入門](hdinsight-hadoop-r-server-get-started.md)

- [新增 RStudio 伺服器 HDInsight premium](hdinsight-hadoop-r-server-install-r-studio.md)

- [計算 R 伺服器上 HDInsight （預覽版本） 的內容選項](hdinsight-hadoop-r-server-compute-contexts.md)

- [Azure 存放區上的選項 R 伺服器 HDInsight 進階版](hdinsight-hadoop-r-server-storage.md)
