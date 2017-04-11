<properties
 pageTitle="使用查詢上 Azure HDInsight 的登錄區 JDBC"
 description="瞭解如何使用 JDBC 連線至上 Azure HDInsight 的登錄區，並從遠端執行查詢，儲存在雲端的資料。"
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
    tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="java"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="08/23/2016"
 ms.author="larryfr"/>

#<a name="connect-to-hive-on-azure-hdinsight-using-the-hive-jdbc-driver"></a>連線至登錄區上使用登錄區 JDBC 驅動程式 Azure HDInsight

[AZURE.INCLUDE [ODBC-JDBC-selector](../../includes/hdinsight-selector-odbc-jdbc.md)]

在此文件中，您將學習如何使用 JDBC Java 應用程式從遠端送出到 HDInsight 叢集登錄區查詢。 您將學習如何從松鼠 SQL 用戶端連線，以及如何從 Java 以程式設計方式連線。

如需在登錄區 JDBC 介面上的詳細資訊，請參閱[HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface)。

##<a name="prerequisites"></a>必要條件

若要完成此文件中的步驟進行，您將需要下列項目︰

* Hadoop HDInsight 叢集上。 [Linux 型或 Windows 型叢集都將能夠運作。

* [松鼠 SQL](http://squirrel-sql.sourceforge.net/)。 松鼠是 JDBC 用戶端應用程式。

若要建立並執行本文連結範例 Java 應用程式，您需要下列項目。

* [Java 開發人員套件 (JDK) 版本 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)或更新版本。

* [Apache Maven](https://maven.apache.org)。 Maven 是專案建立系統 Java 專案所使用的與本文相關的專案。

##<a name="connection-string"></a>連線字串

Azure HDInsight 叢集 JDBC 連線超過 443、 並使用 SSL 保護流量。 叢集坐在前的公用閘道器會流量重新導向 HiveServer2 實際接聽的連接埠。 因此一般的連接字串想要下列動作︰

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

__CLUSTERNAME__換成您 HDInsight 叢集的名稱。

##<a name="authentication"></a>驗證

當建立連線，您必須使用 HDInsight 叢集管理員名稱和密碼驗證叢集閘道器。 當您從 JDBC 用戶端，例如松鼠 SQL 連線，您必須輸入用戶端設定] 中的管理員名稱和密碼。

從 Java 應用程式，您必須使用的名稱與密碼建立連線時。 例如，下列 Java 程式碼會開啟新的連接使用連接字串、 管理員名稱和密碼︰

    DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);

##<a name="connect-with-squirrel-sql-client"></a>使用松鼠 SQL 用戶端連線

松鼠 SQL 是可從遠端執行 HDInsight 叢集登錄區查詢 JDBC 用戶端。 下列步驟假設您已經安裝松鼠 SQL，並且會引導您下載並設定登錄區驅動程式。

1. 複製 HDInsight 叢集登錄區 JDBC 驅動程式。

    * 針對__Linux 型 HDInsight__，使用下列步驟以下載必要的 jar 檔案。

        1. 建立新的目錄會包含這些檔案。 例如， `mkdir hivedriver`。

        2. 從命令提示字元、 艦隊、 PowerShell 或其他命令提示字元中，將目錄變更為新的目錄，然後使用下列命令，將檔案從 HDInsight 叢集複製。

                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/hive-jdbc*standalone.jar .
                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-common.jar .
                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-auth.jar .

            取代叢集 SSH 使用者帳戶名稱的__使用者名稱__。 取代__CLUSTERNAME__ HDInsight 叢集名稱。

            > [AZURE.NOTE] 在 Windows 的環境中，您必須使用 PSCP 公用程式，而不是 scp。 您可以從[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)免費下載。

    * 針對__windows HDInsight__，使用下列步驟以下載 jar 檔案。

        1. 從 Azure] 入口網站中，選取您的 HDInsight 叢集，，然後選取 [__遠端桌面__圖示。

            ![遠端桌面圖示](./media/hdinsight-connect-hive-jdbc-driver/remotedesktopicon.png)

        2. 在遠端桌面刀中，使用 [__連線__] 按鈕以連線至叢集。 如果未啟用遠端桌面，請提供使用者名稱和密碼，然後選取 [__啟用__叢集啟用遠端桌面使用表單。

            ![遠端桌面刀](./media/hdinsight-connect-hive-jdbc-driver/remotedesktopblade.png)

            選取後__連線__，會下載.rdp 檔案。 若要啟動遠端桌面用戶端使用這個檔案。 出現提示時，使用的使用者名稱和密碼，您所輸入的遠端桌面存取權。

        3. 連線之後，請從遠端桌面工作階段中將下列檔案複製到您的本機電腦。 將它們放在名為的本機目錄`hivedriver`。

            * C:\apps\dist\hive-0.14.0.2.2.9.1-7\lib\hive-jdbc-0.14.0.2.2.9.1-7-standalone.jar
            * C:\apps\dist\hadoop-2.6.0.2.2.9.1-7\share\hadoop\common\hadoop-common-2.6.0.2.2.9.1-7.jar
            * C:\apps\dist\hadoop-2.6.0.2.2.9.1-7\share\hadoop\common\lib\hadoop-auth-2.6.0.2.2.9.1-7.jar

            > [AZURE.NOTE] 包含在路徑和檔名的版本號碼可能不同叢集。

        4. 中斷連線遠端桌面工作階段，當您完成之後複製檔案。

3. 啟動松鼠 SQL 應用程式。 從視窗的左側，選取 [__驅動程式__]。

    ![在視窗左側的驅動程式] 索引標籤](./media/hdinsight-connect-hive-jdbc-driver/squirreldrivers.png)

4. 從 [__驅動程式__] 對話方塊頂端的圖示，選取 [__+__圖示，即可建立新的驅動程式。

    ![驅動程式圖示](./media/hdinsight-connect-hive-jdbc-driver/driversicons.png)

5. 在 [新增驅動程式] 對話方塊中，新增下列資訊。

    * __名稱__︰ 登錄區
    * __範例 URL__: jdbc:hive2://localhost:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2
    * __額外的類別路徑__︰ 使用 [新增] 按鈕新增 jar 檔案下載較舊版本
    * __類別名稱__︰ org.apache.hive.jdbc.HiveDriver

    ![新增驅動程式] 對話方塊](./media/hdinsight-connect-hive-jdbc-driver/adddriver.png)

    按一下__[確定__] 以儲存這些設定。

6. 在 [松鼠 SQL] 視窗的左邊，選取 [__別名__]。 然後按一下 [__+__圖示，即可建立新的連線別名。

    ![新增新的別名](./media/hdinsight-connect-hive-jdbc-driver/aliases.png)

7. 使用下列的值的 [__新增別名__] 對話方塊。

    * __名稱__︰ 在 HDInsight 登錄區
    * __驅動程式__︰ 使用下拉式清單選取__登錄區__驅動程式
    * __URL__: jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

        __CLUSTERNAME__換成您 HDInsight 叢集的名稱。

    * __使用者名稱__︰ HDInsight 叢集登入帳戶名稱。 預設值是`admin`。
    * __密碼__︰ 叢集登入帳戶的密碼。 這是您所建立的 HDInsight 叢集時提供的密碼。

    ![[新增別名] 對話方塊](./media/hdinsight-connect-hive-jdbc-driver/addalias.png)

    您可以使用 [__測試__] 按鈕，請確認連線可以運作。 當__連線至︰ 在 HDInsight 登錄區__] 對話方塊隨即出現，選取 [__連線__至執行測試。 如果測試成功，您會看到__成功連線__] 對話方塊。

    使用 [__新增別名__] 對話方塊的底部的 [__確定__] 按鈕，可儲存連線 alias （別名）。

8. 從 [松鼠 SQL 頂端的 [__連線至__] 下拉式功能表中，選取 [__在 HDInsight 登錄區__]。 出現提示時，請選擇 [__連線__]。

    ![連線] 對話方塊](./media/hdinsight-connect-hive-jdbc-driver/connect.png)

9. 連線之後，將 [SQL 查詢] 對話方塊中，輸入下列查詢，然後選取 [__執行__] 圖示。 [結果] 區域應該會顯示查詢的結果。

        select * from hivesampletable limit 10;

    ![sql [查詢] 對話方塊中，包括結果](./media/hdinsight-connect-hive-jdbc-driver/sqlquery.png)

##<a name="connect-from-an-example-java-application"></a>從範例 Java 應用程式連線

使用在[https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc)HDInsight 上使用查詢登錄區 Java 用戶端的範例。 請依照建立並執行範例存放庫中的指示進行。

##<a name="troubleshooting"></a>疑難排解

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>嘗試開啟 SQL 連線時，發生意外的錯誤。

__發生上述任何症狀__︰ 連接到版本 3.3 或 3.4 HDInsight 叢集時，您可能會收到錯誤，時發生意外的錯誤。 這項錯誤的堆疊追蹤會將下列行開始︰

    java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
    at java.util.concurrent.FutureTas...(FutureTask.java:122)
    at java.util.concurrent.FutureTask.get(FutureTask.java:206)

__原因__︰ 此錯誤因為 common codec.jar 檔案松鼠和從 HDInsight 叢集下載的登錄區 JDBC 元件所需的項目所使用的版本中不相符。

__解析度__︰ 若要修正此錯誤，請使用下列步驟。

1. 從 HDInsight 叢集下載 common 轉碼器 jar 檔案。

        scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/commons-codec*.jar ./commons-codec.jar

2. 結束松鼠，，然後移至目錄松鼠安裝在您的系統上的位置。 在松鼠目錄中，在下`lib`目錄，取代現有的其中一個 common-codec.jar 下載的 HDInsight。

3. 重新啟動松鼠。 連線至上 HDInsight 的登錄區時不會再出現錯誤。

##<a name="next-steps"></a>後續步驟

現在您已經學會如何使用登錄區使用 JDBC，使用下列連結以探索 Azure HDInsight 所使用的其他方法。

* [上傳至 HDInsight 的資料](hdinsight-upload-data.md)
* [使用 HDInsight 的登錄區](hdinsight-use-hive.md)
* [使用 HDInsight 的豬](hdinsight-use-pig.md)
* [使用 HDInsight MapReduce 工作](hdinsight-use-mapreduce.md)
