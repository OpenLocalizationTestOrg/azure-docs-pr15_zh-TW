<properties
   pageTitle="將 Excel 連線到登錄區 ODBC 驅動程式 Hadoop |Microsoft Azure"
   description="瞭解如何設定和使用 excel 的 Microsoft 登錄區 ODBC 驅動程式 HDInsight 叢集查詢資料。"
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="jhubbard"
   tags="azure-portal"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/19/2016"
   ms.author="jgao"/>

#<a name="connect-excel-to-hadoop-with-the-microsoft-hive-odbc-driver"></a>使用 Microsoft 登錄區 ODBC 驅動程式，連線至 Hadoop 的 Excel

[AZURE.INCLUDE [ODBC-JDBC-selector](../../includes/hdinsight-selector-odbc-jdbc.md)]

Microsoft 的大型資料解決方案的 Azure HDInsight 已部署 Apache Hadoop 叢集整合 Microsoft 商業智慧 (BI) 元件。 此整合的範例是 Excel 連線至 Hadoop 叢集 HDInsight 使用 Microsoft 登錄區開放式資料庫連接 (ODBC) 驅動程式中的登錄區資料倉庫的能力。

您也可連線與 HDInsight 叢集及其他資料來源，包括其他 (非 HDInsight) Hadoop 叢集，從 Excel for Excel 使用 [Microsoft Power Query 增益集相關的資料。 安裝及使用 Power Query 的資訊，請參閱[將 Excel 連接至使用 Power Query HDInsight][hdinsight-power-query]。

> [AZURE.NOTE] 與 Linux 或 Windows 型 HDInsight 叢集，可以使用本文中的步驟，Windows 是必要的用戶端工作站。

**先決條件**︰

這份文件之前，您必須具備下列項目︰

- **HDInsight 叢集**。 若要建立一個，請參閱[開始使用 Azure HDInsight][hdinsight-get-started]。
- 使用 Office 2013 專業增強版、 Office 365 專業增強版，Excel 2013 Standalone 或 Office 2010 專業增強版**A 工作站**。


##<a name="install-microsoft-hive-odbc-driver"></a>安裝 Microsoft 登錄區 ODBC 驅動程式

下載並安裝 Microsoft 登錄區 ODBC 驅動程式[下載中心][hive-odbc-driver-download]。

此驅動程式可以安裝在 32 位元或 64 位元版本 Windows 7、 Windows 8、 Windows 10、 Windows Server 2008 R2 和 Windows Server 2012，並會允許連線 Azure HDInsight （版本 1.6 及更新版本） 和 Azure HDInsight 模擬器 (v.1.0.0.0 及更新版本)。 您應該安裝的版本，與在您將使用 「 ODBC 驅動程式的應用程式版本相符。 在此教學課程中，會從 Office Excel 使用驅動程式。

##<a name="create-hive-odbc-data-source"></a>建立登錄區 ODBC 資料來源

下列步驟將告訴您如何建立登錄區 ODBC 資料來源。

1. 從 Windows 8 或 Windows 10，請按 Windows 鍵，即可開啟 [開始] 畫面，，然後輸入 [**資料來源**。
2. 視您的 Office 版本而定，按一下 [**設定 ODBC 資料來源 （32 位元）**或**ODBC 資料來源 （64 位元）** ]。 如果您使用的 Windows 7，請從 [**系統管理工具**中選擇**ODBC 資料來源 （32 位元）**或**ODBC 資料來源 （64 位元）** 。 這會啟動 [ **ODBC 資料來源管理員**] 對話方塊。

    ![OBDC 資料來源管理員][img-hdi-simbahiveodbc-datasource-admin]

3. 從使用者 DNS，請按一下 [**新增**] 以開啟 [**建立新的資料來源**精靈]。
4. 選取**Microsoft 登錄區 ODBC 驅動程式**，然後再按一下 [**完成**]。 這會啟動 [ **Microsoft 登錄區 ODBC 驅動程式 DNS 設定**] 對話方塊。

5. 輸入或選取下列值︰

    屬性|描述
    ---|---
    資料來源名稱|給您的資料來源的名稱
    Host （主機)|輸入&lt;HDInsightClusterName >。 azurehdinsight.net。 例如，myHDICluster.azurehdinsight.net
    連接埠|使用<strong>443</strong>。 （此連接埠已變更從 563 為 443。）
    資料庫|使用<strong>預設值</strong>。
    登錄區伺服器類型|選取 [<strong>登錄區伺服器 2</strong>
    機制|選取<strong>Azure HDInsight 服務</strong>
    HTTP 路徑|保留為空白。
    使用者名稱|輸入 HDInsight 叢集使用者的使用者名稱。 這是叢集佈建程序時所建立的使用者名稱。 如果您使用快速建立選項時，預設的使用者名稱是<strong>管理員</strong>。
    密碼|輸入 HDInsight 叢集使用者的密碼。
    </table>

    有一些重要的參數，必須注意當您按一下 [**進階選項**︰

    參數|描述
    ---|---
    使用原生查詢|選取它，ODBC 驅動程式並不會嘗試將 TSQL 轉換成 HiveQL。 只有在確定您提交純粹 HiveQL 陳述式的 100%，您應該使用它。 連線到 SQL Server] 或 [Azure SQL 資料庫時，您應該保留取消核取。
    每個區塊擷取資料列|當擷取大量的記錄，調整此參數可能需要以確保最佳效能。
    預設資料行的字串長度二進位資料行的長度、 小數位數的欄小數位數|資料類型的長度和精確度可能會影響傳回資料的方式。 他們會遺失的精確度，及/或執行因為會傳回不正確的資訊。


    ![進階的選項][img-HiveOdbc-DataSource-AdvancedOptions]

6. 按一下 [**測試**] 來測試資料來源。 當資料來源設定正確時，它會顯示*測試成功完成 ！*。
7. 按一下**[確定**] 關閉 [測試] 對話方塊。 **ODBC 資料來源管理員**現在應該列出新的資料來源。
8. 按一下**[確定]**關閉精靈。

##<a name="import-data-into-excel-from-hdinsight"></a>將 Excel 從 HDInsight 匯入資料

下列步驟說明登錄區資料表的資料匯入至 Excel 活頁簿使用上述步驟中所建立的 ODBC 資料來源的方式。

1. 在 Excel 中開啟新的或現有的活頁簿。
2. 從 [**資料**] 索引標籤，請按一下 [**從其他資料來源**，，，然後按一下 [**從資料連線精靈**] 啟動 [**資料連線精靈**]。

    ![開啟資料連線精靈][img-hdi-simbahiveodbc.excel.dataconnection]

3. 選取**ODBC 資料來源名稱**做為資料來源，然後按 [**下一步**。
4. ODBC 資料來源，選取您在上一個步驟中，您建立的資料來源名稱，然後再按 [**下一步**。
5. 重新輸入叢集在精靈中的密碼，然後按一下要一次，混合式部署設定的**測試**必要。
6. 按一下**[確定**] 關閉 [測試] 對話方塊。
7. 按一下**[確定]**。 若要開啟 [**選取資料庫及表格**] 對話方塊，請等候。 這可能需要幾秒鐘。
8. 選取您想要匯入的資料表，然後按一下 [**下一步**。 *Hivesampletable*是隨附 HDInsight 叢集範例登錄區資料表。  如果您尚未建立簽名，您可以選擇將其。 如需詳細資訊，在執行登錄區查詢並建立登錄區資料表，請參閱[使用登錄區與 HDInsight][hdinsight-use-hive]。
8. 按一下 [**完成**]。
9. 在 [**匯入資料**] 對話方塊中，您可以變更或指定查詢。 若要這麼做，請按一下 [**屬性**]。 這可能需要幾秒鐘。
10. 按一下 [**定義**] 索引標籤，然後附加至登錄區 select 陳述式**命令文字**] 文字方塊中的 [**限制 200** 。 修改會限制傳回的記錄設定為 200。

    ![連線內容][img-hdi-simbahiveodbc-excel-connectionproperties]

11. 按一下**[確定**] 關閉 [連接屬性] 對話方塊。
12. 按一下**[確定**] 關閉 [**匯入資料**] 對話方塊。  
13. 重新輸入密碼，然後再按一下**[確定]**。 需要幾秒才會匯入資料至 Excel。

##<a name="next-steps"></a>後續步驟

本文中，您學到如何從 HDInsight 服務擷取資料，在 Excel 中使用 Microsoft 登錄區 ODBC 驅動程式的內容。 同樣地，您可以從 HDInsight 服務擷取資料，將 SQL 資料庫。 您也可上傳到 HDInsight 服務的資料。 若要深入瞭解，請參閱︰

- [分析使用 HDInsight 航班延遲情況資料][hdinsight-analyze-flight-data]
- [上傳至 HDInsight 的資料][hdinsight-upload-data]
- [使用 HDInsight Sqoop] [hdinsight-use-sqoop]


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md

[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698

[img-hdi-simbahiveodbc-datasource-admin]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png
[img-HiveOdbc-DataSource-AdvancedOptions]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png
[img-hdi-simbahiveodbc-excel-connectionproperties]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveODBC.Excel.ConnectionProperties1.png
[img-hdi-simbahiveodbc.excel.dataconnection]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png
