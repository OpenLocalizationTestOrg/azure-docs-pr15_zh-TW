<properties
    pageTitle="瞭解如何使用 Visual Studio Hadoop 工具的 HDInsight |Microsoft Azure"
    description="瞭解如何安裝和使用 Visual Studio Hadoop 工具 HDInsight 連線到 Hadoop 叢集，並執行登錄區查詢。"
    keywords="hadoop 工具，登錄區查詢 visual studio"
    services="HDInsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="08/10/2016"
    ms.author="jgao"/>

# <a name="get-started-using-visual-studio-hadoop-tools-for-hdinsight-to-run-a-hive-query"></a>開始使用 Visual Studio Hadoop HDInsight 工具執行登錄區查詢

瞭解如何連線至 HDInsight 叢集並送出登錄區查詢中使用 Visual Studio HDInsight 工具。 如需有關如何使用 HDInsight 的詳細資訊，請參閱[簡介 HDInsight] [hdinsight.introduction]並[開始使用 HDInsight][hdinsight.get.started]。 如需有關如何連接到大量叢集的詳細資訊，請參閱[的 Apache 大量上 HDInsight 使用 Visual Studio 開發 C# 拓撲][hdinsight.storm.visual.studio.tools]。

**必要條件**

若要完成此教學課程，並在 Visual Studio 中使用 Hadoop 工具，您會需要下列項目︰

- Azure HDInsight 叢集︰ Linux 或 Windows 叢集會使用這份文件中的步驟進行。 建立叢集查看資訊下列其中一項︰

    - [開始使用 Linux 型 HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
    - [開始使用 Windows 型 HDInsight](hdinsight-hadoop-tutorial-get-started-windows.md)

- 使用下列軟體工作站︰

    - Windows 8.1、 Windows 8 或 Windows 7
    - Visual Studio （與下列版本之一）︰
        - Visual Studio 2013 社群/專業/進階/Ultimate 與[更新 4](https://www.microsoft.com/download/details.aspx?id=44921)
        - Visual Studio 2015 年 （社群/企業版）

    >[AZURE.NOTE] 目前，Visual Studio HDInsight 工具僅隨附的英文版本。


## <a name="install-hdinsight-tools-for-visual-studio"></a>安裝 Visual Studio HDInsight 工具

Visual Studio 與 Microsoft 登錄區 ODBC 驅動程式的 HDInsight 工具是使用 Microsoft Azure SDK.NET 版本 2.5.1 封裝或更新版本。 您可以安裝並使用[Web 平台安裝程式](http://go.microsoft.com/fwlink/?LinkId=255386)。 您必須選擇的 Visual Studio 版本相符的項目。 如果您沒有安裝的 Visual Studio，您可以安裝最新的 Visual Studio 社群和 Azure SDK 使用[Web 平台安裝程式](http://go.microsoft.com/fwlink/?LinkId=255386)，或使用下列連結︰

- [Microsoft Azure SDK 的 visual Studio 社群 2015](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/VS2015CommunityAzurePack.appids)
- [Microsoft Azure SDK 的 visual Studio 社群 2013](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/VS2013CommunityAzurePack.appids)
- [Microsoft Azure SDK.net (與 2015)](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/VWDOrVs2015AzurePack.appids)
- [Microsoft Azure SDK.net (與 2013)](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/VWDOrVs2013AzurePack.appids)

![Hadoop 工具︰ HDinsight 工具的 Visual Studio Web 平台安裝程式。][1]

## <a name="connect-to-azure-subscriptions"></a>連線至 Azure 訂閱
Visual Studio HDInsight 工具可讓您連線到您的 HDInsight 叢集執行一些基本管理作業]，並執行登錄區查詢。

>[AZURE.NOTE] 連接到一般的 Hadoop 叢集的詳細資訊，請參閱[撰寫並提交使用 Visual Studio 的登錄區查詢](http://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx)。


**連線到您 Azure 的訂閱**

1.  開啟 Visual Studio。
2.  從 [**檢視**] 功能表中，按一下 [開啟伺服器總管] 視窗的**伺服器總管**]。
3.  展開**Azure**，然後再展開 [ **HDInsight**。

    >[AZURE.NOTE]請注意**HDInsight 工作清單**視窗應該已開啟。 如果您沒有看到它，從 [**檢視**] 功能表中，按一下 [**其他視窗**，然後按一下 [ **HDInsight 工作清單] 視窗**。  
4.  輸入您 Azure 訂閱的認證，然後再按一下 [**登入**]。 這只是必要如果在連接至 Azure 訂閱從這個工作站 Visual Studio。
5.  中伺服器總管] 中，您會看到現有的 HDInsight 叢集的清單。 如果您沒有安裝任何叢集，您可以使用 Azure 入口網站的 PowerShell 的 Azure HDInsight SDK 提供。 如需詳細資訊，請參閱[佈建 HDInsight 叢集][hdinsight-provision]。

    ![Hadoop 工具︰ HDInsight Visual Studio 伺服器總管叢集清單工具][5]
6.  展開 HDInsight 叢集。 您會看到**登錄區資料庫**、 預設儲存帳戶、 連結的儲存空間帳戶和**Hadoop 服務記錄檔**。 您可以進一步展開項目。

您已連線至 Azure 訂閱之後，您就可以執行下列動作︰

**若要從 Visual Studio 連線至 Azure 入口網站**

- 從伺服器總管] 中，展開 [ **Azure** > **HDInsight**，HDInsight 叢集，以滑鼠右鍵按一下，然後按一下 [ **Azure 入口網站中的 [管理叢集**。

**提出問題，並從 Visual Studio 中提供意見反應**

- 從 [**工具**] 功能表中，按一下**HDInsight**，，然後按一下**MSDN 論壇**提出問題，或按一下**提供意見反應**。

## <a name="navigate-the-linked-resources"></a>瀏覽連結的資源

從伺服器總管] 中，您可以看到預設儲存帳戶及任何連結的儲存空間的帳戶。 如果您展開預設儲存帳戶時，您可以看到容器儲存的帳戶。 預設儲存帳戶和預設容器會標示。 您可以也以滑鼠右鍵按一下任何檢視內容容器。

![HDInsight Visual Studio 伺服器檔案總管叢集清單工具][2]

之後 openning 容器，您可以使用下列按鈕上傳、 刪除及下載二進位大型物件︰

![HDInsight Visual Studio 伺服器檔案總管 blob 作業工具](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.blob.operations.png)


## <a name="run-a-hive-query"></a>執行登錄區查詢
[Apache 登錄區][apache.hive]是內建在 Hadoop，提供資料摘要、 查詢和分析資料倉庫基礎結構。 Visual Studio HDInsight 工具支援從 Visual Studio 執行登錄區查詢。 如需有關群組的詳細資訊，請參閱[使用登錄區與 HDInsight][hdinsight.hive]。

這是測試針對 HDInsight 叢集登錄區指令碼花時間。 可能需要幾分鐘以上。 Visual Studio HDInsight 工具都能夠而無需連線至即時叢集本機驗證登錄區指令碼。

Visual Studio HDInsight 工具也可讓使用者請參閱什麼是內登錄區工作收集並出現 YARN 記錄的特定登錄區工作。

### <a name="view-the-hivesampletable"></a>檢視**hivesampletable**
所有的 HDInsight 叢集了稱為*hivesampletable*範例登錄區資料表。 我們將使用下表顯示您如何列出登錄區資料表與檢視的資料表結構描述，清單中的登錄區資料表的資料列。



**若要列出登錄區資料表及檢視登錄區資料表結構描述**

1.  從**伺服器總管**] 中，展開 [ **Azure** > **HDInsight** > 您所選擇的叢集 >**登錄區資料庫** > **預設** > **hivesampletable**若要查看的表格結構描述。
4.  **Hivesampletable**，以滑鼠右鍵按一下，然後按一下 [清單列**檢視前 100 資料列**。 執行下列登錄區查詢使用登錄區 ODBC 驅動程式相當︰

        SELECT * FROM hivesampletable LIMIT 100

    您可以自訂資料列計數。

    ![Hadoop 工具︰ HDinsight 登錄區 Visual Studio 結構描述查詢][6]

### <a name="create-hive-tables"></a>建立登錄區資料表

您可以使用 GUI 建立登錄區資料表，或使用登錄區查詢。 有關如何使用登錄區查詢，請參閱[執行登錄區查詢](#run.queries)。

**若要建立的登錄區資料表**

1. 從**伺服器總管**] 中，展開 [ **Azure** > **HDInsight 叢集**HDInsight 叢集 >**登錄區資料庫**]，然後以滑鼠右鍵按一下 [**預設值**，再按一下 [**建立表格**。
2. 設定表格。
3. 按一下 [送出以建立新的登錄區資料表**建立表格**]。

    ![Hadoop 工具︰ hdinsight visual studio 工具建立的登錄區資料表][7]

### <a name="run.queries"></a>驗證和執行登錄區查詢
有兩種方式可以建立和執行登錄區查詢︰

- 建立臨機操作查詢
- 建立登錄區應用程式

**若要建立、 驗證以及執行臨機操作查詢**

1. 從**伺服器總管**] 中，展開**Azure**，然後再展開**HDInsight 叢集**。
2. 以滑鼠右鍵按一下您要執行查詢，的叢集，然後按一下 [**撰寫登錄區查詢**。
3. 輸入登錄區查詢。 請注意登錄區編輯器支援 IntelliSense。 Visual Studio HDInsight 工具支援載入遠端的中繼資料，當您在編輯登錄區指令碼。 例如，當您輸入 「 選取 * 寄件者]，IntelliSense 會列出所有的建議的表格名稱。 指定表格名稱時，依照 IntelliSense 列出的資料行名稱。 此工具支援執行幾乎所有的登錄區 DML 陳述式、 子查詢和內建 Udf。

    ![Hadoop 工具︰ HDInsight Visual Studio 工具 IntelliSense][13]

    ![Hadoop 工具︰ HDInsight Visual Studio 工具 IntelliSense][14]

    > [AZURE.NOTE] 會建議只的中繼資料的叢集 HDInsight] 工具列中已選取。
4. （選用）︰ 按一下 [**驗證指令碼**檢查指令碼語法錯誤。

    ![Hadoop 工具︰ Visual Studio 本機驗證 hdinsight 工具][10]

4. 按一下 [**送出**，或**提交 （進階）**]。 進階送出選項時，您會設定**工作名稱**、**引數**、**額外設定**和**狀態目錄**的指令碼︰

    ![hdinsight hadoop 登錄區查詢][9]

    提交工作之後，您會看到**登錄區工作摘要**視窗。

    ![HDInsight Hadoop 登錄區查詢的摘要][8]
5. 使用 [**重新整理**] 按鈕來更新的狀態，直到工作狀態變更為**完成**。
6. 按一下 [底部，請參閱下列連結︰**工作查詢**、**工作輸出**、**工作記錄**或**Yarn 的記錄**。



**若要建立和執行登錄區解決方案**

1. 從 [**檔案**] 功能表中，按一下 [**新增**]，然後按一下**專案**。
2. 從左側的窗格選取**HDInsight**在中間窗格選取**登錄區應用程式**、 輸入的內容，，然後按一下**[確定]**。

    ![Hadoop 工具︰ hdinsight visual studio 工具] 的 [新群組專案][11]
3. 從**方案總管**] 中，按兩下 [ **Script.hql** ，將其開啟。
4. 若要驗證的登錄區指令碼，您可以按一下 [**驗證指令碼**] 按鈕，或以滑鼠右鍵按一下登錄區編輯器] 中的指令碼，然後按一下 [從內容功能表的 [**驗證指令碼**。


### <a name="view-hive-jobs"></a>檢視工作區
您可以檢視工作查詢與工作輸出、 工作記錄登錄區工作 Yarn 記錄。 如需詳細資訊，請參閱先前的螢幕擷取畫面。

此工具的最新版本，可讓您看不到您的工作區來收集及出現 YARN 記錄。 YARN 記錄可協助您調查效能問題。 如需有關如何 HDInsight 收集 YARN 記錄，請參閱[Access HDInsight 應用程式記錄以程式設計方式][hdinsight.access.application.logs]。

**若要檢視工作區**

1. 從**伺服器總管**] 中，展開**Azure**，然後再展開**HDInsight**。
2. HDInsight 叢集，以滑鼠右鍵按一下，然後按一下 [**檢視工作**。 您會看到叢集執行登錄區工作清單。
3. 按一下加以選取，[工作] 清單中的工作，然後使用 [**登錄區工作摘要**] 視窗中開啟**查詢工作**、**工作輸出**、**工作記錄**或**Yarn 記錄**。

    ![Hadoop 工具︰ HDInsight Visual Studio 工具檢視群組工作][12]

### <a name="faster-path-hive-execution-via-hiveserver2"></a>更快速地路徑透過 HiveServer2 登錄區執行

>[AZURE.NOTE] 這項功能只能在 HDInsight 叢集版本 3.2 及更新版本。

HDInsight 所用的工具來提交登錄區工作，透過[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (也稱為 Templeton)。 花費很長的時間，傳回工作詳細資料和錯誤資訊。
若要解決此效能問題，請 HDInsight 工具執行登錄區工作直接在 HiveServer2，透過叢集，讓它會略過 RDP/SSH。
除了較佳的效能，使用者也可以檢視登錄區 Tez 圖形和工作詳細資料。

針對 HDInsight 叢集 3.2 或更新版本，您可以看到**透過 HiveServer2 執行**] 按鈕︰

![hdinsight visual studio 工具執行透過 hiveserver2](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.execute.via.hiveserver2.png)

您可以查看並回即時資料流傳送記錄檔如果 Tez 中執行登錄區查詢，請參閱工作圖形。

![hdinsight visual studio 工具快速路徑登錄區執行](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.fast.path.hive.execution.png)

**執行查詢，透過 HiveServer2 及送出透過 WebHCat 查詢之間的差異**

即使執行查詢，透過 HiveServer2 有許多效能優點，有幾項限制。 限制的部分不適合生產使用方式。 下表顯示的差異︰

| |透過 HiveServer2 執行 |透過 WebHCat 送出|
|---|---|---|
|執行查詢|減少中 WebHCat （哪些啟動 MapReduce 工作名稱為 「 TempletonControllerJob 」）。|只要執行查詢時，透過 WebHCat，WebHCat 便會啟動 MapReduce 工作會介紹其他延遲。|
|返回串流記錄|在靠近即時。|只在工作完成時，可使用工作執行記錄檔。|
|檢視工作歷程記錄|如果透過 HiveServer2 執行查詢時，不會保留它的工作歷程記錄 （工作記錄，工作輸出）。 應用程式中可以檢視 YARN UI 有限的資訊。|如果透過 WebHCat 執行查詢時，它的工作歷程記錄 （工作記錄，工作輸出） 會保留，並可使用 Visual Studio/HDInsight SDK/PowerShell 檢視。 |
|關閉視窗|  透過 HiveServer2 執行是 「 同步 」 的方式，因此您必須將 windows 保持在開啟的;如果關閉視窗，然後將會取消查詢執行。|透過 WebHCat 送出是 「 非同步 」 的方式，讓您可以送出透過 WebHCat 查詢，並關閉 Visual Studio。 您可以完假回來，並隨時查看結果。|


### <a name="tez-hive-job-performance-graph"></a>Tez 登錄區工作效能圖形

顯示群組工作的效能圖形的 HDInsight Visual Studio 工具支援執行 Tez 執行引擎。 啟用 Tez 資訊，請參閱[使用登錄區中 HDInsight][hdinsight.hive]。 提交 Visual Studio 中的登錄區工作之後，Visual Studio 顯示該圖表完成工作。  您可能需要按一下 [**重新整理**] 按鈕，以取得最新的工作狀態。

> [AZURE.NOTE] 此功能只適用於上方 3.2.4.593，HDInsight 叢集版本，可以只為已完成的工作的工作 （如果您上傳您的工作，透過 WebHCat; 此圖形會顯示當您執行透過 HiveServer2 查詢）。 這適用於這兩個視窗而 Linux 叢集。

![hadoop 登錄區 tez 效能圖形](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.hive.tez.performance.graph.png)

若要可協助您瞭解登錄區查詢，工具新增登錄區運算子檢視此版本。 您只需要將按兩下工作圖形的頂點，而且您可以看到頂點內所有的運算子。 您也可以將特定的運算子，若要檢視更多詳細資料，此運算子的游標。

### <a name="task-execution-view-for-hive-on-tez-jobs"></a>任務執行登錄區上檢視 Tez 工作

Tez 工作區的任務執行檢視可用來取得結構化及視覺化資訊登錄區工作，並取得更多工作詳細資料。 效能問題時，您可以使用檢視，以取得進一步的詳細資料。 例如，每項工作的運作方式與 （資料讀/寫、 排程/開始/結束時間等），每項工作的詳細的資訊，好讓您可以調整工作設定或系統架構，根據具體的資訊。

![hdinsight visual studio 工具任務執行檢視](./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.task.execution.view.png)

## <a name="run-pig-scripts"></a>執行豬指令碼

Visual Studio HDInsight 工具支援建立，並提交 HDInsight 叢集豬指令碼。 使用者可以從範本建立的豬專案，然後再提交 HDInsight 叢集的指令碼。

## <a name="feedbacks--known-issues"></a>意見反應及已知問題

- 目前 HiveServer2 結果會顯示以純文字形式不理想。 我們正在處理的修正。

- 如果結果入門 NULL 值，目前不會顯示結果。 我們有修正此問題，如果您封鎖此問題，請在隨意來信電子郵件，或連絡支援小組。

- Visual studio 建立的 HQL 指令碼會根據使用者的本機的地區設定編碼。 如果使用者上傳到叢集為二進位的指令碼，則它可能無法正確執行。

如果您有任何建議或意見反應，或使用此工具時，會發生任何問題，請隨意來信 hdivstool 在 microsoft 點 com 在電子郵件。

## <a name="next-steps"></a>後續步驟
在本文中，您學到如何連線至 HDInsight 叢集 Visual Studio 中，使用 Hadoop 工具套件，以及如何執行登錄區查詢。 如需詳細資訊，請參閱︰

- [使用中 HDInsight Hadoop 登錄區][hdinsight.hive]
- [開始使用 HDInsight Hadoop][hdinsight.get.started]
- [提交 Hadoop HDInsight 中的工作][hdinsight.submit.jobs]
- [Twitter 使用分析資料的 HDInsight Hadoop][hdinsight.analyze.twitter.data]


<!--Anchors-->
[Installation]: #installation
[Connect to your Azure subscription]: #connect-to-your-azure-subscription
[Navigate the linked resources]: #navigate-the-linked-resources
[Run Hive queries]: #run-hive-queries
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png
[2]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png
[5]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png
[6]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png
[7]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png
[8]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png
[9]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png
[10]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png
[11]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png
[12]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.view.hive.jobs.png
[13]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.table.names.png
[14]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.column.names.png


<!--Link references-->
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight.introduction]: hdinsight-hadoop-introduction.md
[hdinsight.get.started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight.hive]: hdinsight-use-hive.md
[hdinsight.submit.jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight.analyze.twitter.data]: hdinsight-analyze-twitter-data.md
[hdinsight.storm.visual.studio.tools]: hdinsight-storm-develop-csharp-visual-studio-topology.md
[hdinsight.access.application.logs]: hdinsight-hadoop-access-yarn-app-logs.md

[apache.hive]: http://hive.apache.org
