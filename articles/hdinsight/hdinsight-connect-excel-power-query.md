<properties
    pageTitle="將 Excel 連線到使用 Power Query Hadoop |Microsoft Azure"
    description="瞭解如何利用其商業智慧元件，並使用 Power Query for Excel 存取儲存在 Hadoop HDInsight 上的資料。"
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
    ms.date="10/19/2016"
    ms.author="jgao"/>


#<a name="connect-excel-to-hadoop-by-using-power-query"></a>使用 Power Query 連線至 Hadoop 的 Excel

Microsoft 大型資料解決方案的關鍵功能是在 Azure HDInsight Hadoop 叢集 Microsoft 商業智慧 (BI) 元件的整合。 此整合的主要範例是連線到 Azure 儲存體帳戶包含使用 Microsoft Power Query for Excel 增益集相關聯 Hadoop 叢集之資料的 Excel 的能力。 本文會引導您如何設定和使用 Power Query 查詢資料管理 HDInsight Hadoop 叢集相關聯。

> [AZURE.NOTE] 與 Linux 或 Windows 型 HDInsight 叢集，可以使用本文中的步驟，Windows 是必要的用戶端工作站。

### <a name="prerequisites"></a>必要條件

這份文件之前，您必須具備下列項目︰

- **HDInsight 叢集**。 若要設定一個，請參閱[開始使用 Azure HDInsight][hdinsight-get-started]。
- **A 工作站**正在執行 Windows 7、 Windows Server 2008 R2 或更新版本的作業系統。
- **Office 2013 專業增強版、 Office 365 專業增強版，Excel 2013 Standalone 或 Office 2010 專業增強版**。


## <a name="install-power-query"></a>安裝 Power Query

Power Query 可從 Microsoft Excel 中，它可以在此 power BI 工具，例如 PowerPivot 和 Power View 將各種不同的來源匯入資料。 特別是，Power Query 可以匯入的具有輸出或 Hadoop 工作 HDInsight 叢集上執行產生的資料。

下載 Microsoft Power Query for Excel 從[Microsoft 下載中心][powerquery-download]並將其安裝。

## <a name="import-hdinsight-data-into-excel"></a>將 Excel 資料匯入 HDInsight

[Power Query 增益集的 Excel 方便您將資料匯入至 Excel，其中的 BI 工具例如 PowerPivot 和 Power Map 可以用來檢查，分析及呈現資料 HDInsight 叢集。

**若要從 HDInsight 叢集匯入資料**

1. 開啟 Excel。

2. 建立新的空白活頁簿。

3. 按一下 [ **Power Query** ] 功能表，按一下 [**從 Azure**，再按一下 [**從 Microsoft Azure HDInsight**。

    ![HDI。PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]

    **附註︰**如果您沒有看到 [ **Power Query** ] 功能表，移至 [**檔案** > **選項** > **增益集**]，然後選取**COM 增益集]** ，從下拉式清單**管理**] 方塊底部的頁面。 選取 [**移至...** ] 按鈕，然後確認已核取 [Power query for Excel 增益集] 方塊。

    **附註︰**Power Query 也可讓您從 HDFS 匯入資料，按一下 [**從其他來源**。

3. [**帳戶名稱**] 中，輸入叢集，相關聯的 Azure Blob 儲存體帳戶名稱，然後再按一下**[確定**]。 這可以是[預設儲存帳戶](hdinsight-administer-use-management-portal.md#find-the-default-storage-account)或連結的儲存空間帳戶。  格式是*https://<StorageAccountName>.blob.core.windows.net/*。

4. **[帳戶金鑰]**，輸入 Blob 儲存體帳戶的鍵，然後再按一下 [**儲存**]。 （您需要執行此動作只有第一次存取此儲存區）。

5. 在 [左側的 [查詢編輯器] 中的 [**導覽**] 窗格中，按兩下 Blob 儲存體容器名稱。 根據預設，容器名稱是叢集名稱相同的名稱。

6. 在 [**名稱**] 欄 （資料夾路徑是**...中找出**HiveSampleData.txt**/ 登錄區/倉儲/hivesampletable/**)，然後按一下 [**二進位**HiveSampleData.txt 左側。 HiveSampleData.txt 隨附所有叢集。 或者，您可以使用您自己的檔案。

    ![HDI。PowerQuery.ImportData][image-hdi-powerquery-importdata]

7. 如果您想，您可以重新命名資料行名稱。 當您準備好時，按一下 [**關閉並載入**]。  將資料載入至您的活頁簿︰

    ![HDI。PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>後續步驟

本文中，您學到如何從 HDInsight 擷取資料，在 Excel 中使用 Power Query。 同樣地，您可以從 HDInsight 擷取資料至 Azure SQL 資料庫。 您也可上傳到 HDInsight 的資料。 若要深入瞭解，請參閱下列文章︰

* [將 Excel 連接至 Microsoft 登錄區 ODBC 驅動程式 HDInsight][hdinsight-ODBC]
* [上傳至 HDInsight 的資料][hdinsight-upload-data]

[hdinsight-ODBC]: hdinsight-connect-excel-hive-odbc-driver.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[image-hdi-powerquery-hdi-source]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
[image-hdi-powerquery-importdata]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png
[image-hdi-powerquery-imported-table]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689
