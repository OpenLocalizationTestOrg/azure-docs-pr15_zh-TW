<properties
    pageTitle="在網域的 HDInsight 中設定登錄區原則 |Microsoft Azure"
    description="了解..."
    services="hdinsight"
    documentationCenter=""
    authors="saurinsh"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/25/2016"
    ms.author="saurinsh"/>

# <a name="configure-hive-policies-in-domain-joined-hdinsight-preview"></a>在網域的 HDInsight （預覽版本） 中設定登錄區原則

瞭解如何設定登錄區 Apache Ranger 原則。 在本文中，您可以建立 hivesampletable 限制存取的兩個 Ranger 原則。 Hivesampletable 隨附 HDInsight 叢集。 您已設定原則之後，您可以使用 Excel 和 ODBC 驅動程式連線至 HDInsight 登錄區資料表。


## <a name="prerequisites"></a>必要條件

- 加入網域的 HDInsight 叢集。 請參閱[設定網域 HDInsight 叢集](hdinsight-domain-joined-configure.md)。
- Office 2016、 Office 2013 專業增強版、 Office 365 專業增強版，Excel 2013 Standalone，或 Office 2010 專業增強版工作站。


## <a name="connect-to-apache-ranger-admin-ui"></a>連線至 Apache Ranger 管理員使用者介面

**連線至 Ranger 管理員使用者介面**

1. 從瀏覽器中，連線到 Ranger 管理員使用者介面。 URL 是 https://&lt;ClusterName >.azurehdinsight.net/Ranger/。 

    >[AZURE.NOTE] Ranger 會使用不同於 Hadoop 叢集的認證。 若要防止瀏覽器使用快取的 Hadoop 認證，請使用新 inprivate 瀏覽器視窗連線至 Ranger 管理員使用者介面。
4. 使用登入叢集管理員網域的使用者名稱和密碼︰

    ![HDInsight 網域 Ranger 首頁](./media/hdinsight-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    目前，Ranger 只適用於 Yarn 和登錄區。

## <a name="create-domain-users"></a>建立網域使用者

在[設定網域 HDInsight 叢集](hdinsight-domain-joined-configure.md#create-and-configure-azure-ad-ds-for-your-azure-ad)，您建立了 hiveruser1 和 hiveuser2。 您會在此教學課程中使用的兩個使用者帳戶。

## <a name="create-ranger-policies"></a>建立 Ranger 原則

在此區段中，您將建立存取 hivesampletable 的兩個 Ranger 的原則。 您在資料行的不同設定為選取的權限。 在[設定網域 HDInsight 叢集](hdinsight-domain-joined-configure.md#create-and-configure-azure-ad-ds-for-your-azure-ad)中建立兩個使用者。  在下一步] 區段中，您將測試在 Excel 中的兩個原則。

**若要建立 Ranger 原則**

1. 開啟 Ranger 管理員使用者介面。 請參閱[連線至 Apache Ranger 管理員使用者介面](#connect-to-apache-ranager-admin-ui)。
2. 按一下 [ ** &lt;ClusterName > _hive**下**登錄區**。 您應該會看到兩個預先設定原則。
3. 按一下 [**新增原則**]，然後輸入下列值︰

    - 原則名稱︰ 閱讀 hivesampletable 所有
    - 登錄區資料庫︰ 預設
    - 表格︰ hivesampletable
    - 登錄區欄: *
    - 選取使用者︰ hiveuser1
    - 權限︰ 選取

    ![HDInsight 網域 Ranger 登錄區原則設定](./media/hdinsight-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

    >[AZURE.NOTE] 如果網域使用者不會在 [選取使用者，請等候等候幾分鐘，Ranger AAD 與同步處理。

4. 按一下 [**新增**] 以儲存原則]。
5. 重複使用下列屬性中建立另一個原則兩個步驟︰

    - 原則名稱︰ 閱讀-hivesampletable-devicemake
    - 登錄區資料庫︰ 預設
    - 表格︰ hivesampletable
    - 登錄區欄︰ clientid，devicemake
    - 選取使用者︰ hiveuser2
    - 權限︰ 選取

## <a name="create-hive-odbc-data-source"></a>建立登錄區 ODBC 資料來源

可以[建立登錄區 ODBC 資料來源](hdinsight-connect-excel-hive-odbc-driver.md)中找到相關指示。  

    屬性|描述
    ---|---
    資料來源名稱|給您的資料來源的名稱
    Host （主機)|輸入&lt;HDInsightClusterName >。 azurehdinsight.net。 例如，myHDICluster.azurehdinsight.net
    連接埠|使用<strong>443</strong>。 （此連接埠已變更從 563 為 443。）
    資料庫|使用<strong>預設值</strong>。
    登錄區伺服器類型|選取 [<strong>登錄區伺服器 2</strong>
    機制|選取<strong>Azure HDInsight 服務</strong>
    HTTP 路徑|保留為空白。
    使用者名稱|輸入hiveuser1@contoso158.onmicrosoft.com。 如果不同，請更新的網域名稱。
    密碼|輸入 hiveuser1 的密碼。
    </table>

請務必將之前儲存的資料來源中按一下 [**測試]** 。


##<a name="import-data-into-excel-from-hdinsight"></a>將 Excel 從 HDInsight 匯入資料

在最後一個區段中，您已設定兩個原則。  hiveuser1 都有選取的權限的所有資料行，且 hiveuser2 選取的權限兩個資料行。 在此區段中，您可以模擬將 Excel 資料匯入的兩個使用者。


1. 在 Excel 中開啟新的或現有的活頁簿。
2. 從 [**資料**] 索引標籤，請按一下 [**從其他資料來源**，，，然後按一下 [**從資料連線精靈**] 啟動 [**資料連線精靈**]。

    ![開啟資料連線精靈][影像-hdi-simbahiveodbc.excel.dataconnection]

3. 選取**ODBC 資料來源名稱**做為資料來源，然後按 [**下一步**。
4. ODBC 資料來源，選取您在上一個步驟中，您建立的資料來源名稱，然後再按 [**下一步**。
5. 重新輸入叢集在精靈中的密碼，然後按一下**[確定]**。 若要開啟 [**選取資料庫及表格**] 對話方塊，請等候。 這可能需要幾秒鐘。
8. 選取**hivesampletable**，，然後按一下 [**下一步**。 
8. 按一下 [**完成**]。
9. 在 [**匯入資料**] 對話方塊中，您可以變更或指定查詢。 若要這麼做，請按一下 [**屬性**]。 這可能需要幾秒鐘。 
10. 按一下 [**定義**] 索引標籤。 命令文字是︰

        SELECT * FROM "HIVE"."default"."hivesampletable"

    由您定義 Ranger 原則，hiveuser1 具有的所有資料行選取的權限。  此查詢搭配 hiveuser1 的認證，因此，但這個查詢不不適用於 hiveuser2 的認證。

    ![連線內容][影像-hdi-simbahiveodbc-excel-connectionproperties]

11. 按一下**[確定**] 關閉 [連接屬性] 對話方塊。
12. 按一下**[確定**] 關閉 [**匯入資料**] 對話方塊。  
13. 重新輸入 hiveuser1 的密碼，然後按一下**[確定]**。 需要幾秒才會匯入資料至 Excel。 完成之後，您應該看到 11 資料的資料行。

若要測試的第二個原則 (閱讀-hivesampletable-devicemake) 您建立的最後一節

1. 在 Excel 中加入新的工作表。
2. 遵循匯入資料的最後一個程序。  只會進行的變更是使用 hiveuser2 的認證，而不是 hiveuser1 的。 這將會失敗 hiveuser2 只有查看兩個資料行的權限。 您應該會收到下列錯誤︰

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.

3. 請遵循匯入資料的相同程序。 這次請使用 hiveuser2 的認證，也修改從 select 陳述式︰

        SELECT * FROM "HIVE"."default"."hivesampletable"

    若要︰

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    完成之後，您應該看到兩個資料行的 [匯入的資料。

## <a name="next-steps"></a>後續步驟

- 設定網域的 HDInsight 叢集，請參閱[設定網域 HDInsight 叢集](hdinsight-domain-joined-configure.md)]。
- 管理網域的 HDInsight 叢集，請參閱[管理網域的 HDInsight 叢集](hdinsight-domain-joined-manage.md)。
- 執行網域 HDInsight 叢集上使用 SSH 登錄區查詢，請參閱[使用 SSH Linux 為基礎的 Hadoop HDInsight Linux、 Unix，或 OS X 上使用](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-domain-joined-hdinsight-cluster)。
- 連線登錄區使用登錄區 JDBC，請參閱[連線至登錄區上使用登錄區 JDBC 驅動程式 Azure HDInsight](hdinsight-connect-hive-jdbc-driver.md)
- Hadoop 使用登錄區 ODBC 連線 Excel，請參閱[將 Excel 連接至 Microsoft 登錄區 ODBC 磁碟機 Hadoop](hdinsight-connect-excel-hive-odbc-driver.md)
- Hadoop 使用 Power Query 連線 Excel，請參閱[將 Excel 連接至 Hadoop 使用 Power Query](hdinsight-connect-excel-power-query.md)