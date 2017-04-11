<properties
    pageTitle="使用以時間為基礎的 Hadoop Oozie 協調器中 HDInsight |Microsoft Azure"
    description="使用以時間為基礎的 Hadoop Oozie 協調器中的 HDInsight，大型資料服務。 瞭解如何定義 Oozie 工作流程與協調，並提交工作。"
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
    ms.date="07/25/2016"
    ms.author="jgao"/>


# <a name="use-time-based-oozie-coordinator-with-hadoop-in-hdinsight-to-define-workflows-and-coordinate-jobs"></a>使用以時間為基礎的 Oozie 協調者 Hadoop HDInsight 中定義工作流程和進行共同作業工作

本文中，您將學習如何定義工作流程與協調，以及如何觸發協調器工作，根據時間。 還是有幫助審核[HDInsight 與使用 Oozie] [hdinsight-use-oozie]閱讀本文之前。 除了 Oozie，您也可以排程工作使用 Azure 資料工廠。 若要瞭解 Azure 資料工廠，請參閱[使用豬和與資料工廠登錄區](../data-factory/data-factory-data-transformation-activities.md)。

> [AZURE.NOTE] 這份文件需要 Windows 型 HDInsight 叢集。 如需使用 Oozie，包括以時間為基礎的工作，在 Linux 型叢集上，請參閱[使用 Oozie 與 Hadoop 來定義並執行 Linux 型 HDInsight 上的工作流程](hdinsight-use-oozie-linux-mac.md)

##<a name="what-is-oozie"></a>什麼是 Oozie

Apache Oozie 是工作流程/配合系統管理 Hadoop 工作。 Hadoop 堆疊，整合，以及用於 Apache MapReduce Apache 豬、 Apache 登錄區，與 Apache Sqoop 支援 Hadoop 工作。 也可排程工作的特定系統，例如 Java 程式或殼層指令碼。

下圖顯示執行工作流程︰

![工作流程圖表][img-workflow-diagram]

工作流程包含兩個動作︰

1. 登錄區巨集指令執行 HiveQL 指令碼，來計算 log4j 記錄檔中每個層級的記錄類型的項目。 每個 log4j 記錄檔包含一行] 欄位包含 [記錄層級] 欄位來顯示類型及嚴重性，例如︰

        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...

    登錄區指令碼輸出非常類似︰

        [DEBUG] 434
        [ERROR] 3
        [FATAL] 1
        [INFO]  96
        [TRACE] 816
        [WARN]  4

    如需有關群組的詳細資訊，請參閱[使用登錄區與 HDInsight][hdinsight-use-hive]。

2.  Sqoop 動作會將 HiveQL 動作輸出匯出至 Azure SQL 資料庫中的資料表。 如需有關 Sqoop 的詳細資訊，請參閱[使用 Sqoop 與 HDInsight][hdinsight-use-sqoop]。

> [AZURE.NOTE] HDInsight 叢集上支援 Oozie 版本，請參閱[HDInsight 所提供之叢集版本中的新功能？][hdinsight-versions].


##<a name="prerequisites"></a>必要條件

本教學課程之前，您必須具備下列項目︰

- **使用 PowerShell 的 Azure 工作站**。

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **HDInsight 叢集**。 建立 HDInsight 叢集的相關資訊，請參閱[建立 HDInsight 叢集][hdinsight-provision]，或[快速入門 HDInsight][hdinsight-get-started]。 您將需要教學課程的完整下列資料︰

    <table border = "1">
    <tr><th>叢集屬性</th><th>Windows PowerShell 變數名稱</th><th>值</th><th>描述</th></tr>
    <tr><td>HDInsight 叢集名稱</td><td>$clusterName</td><td></td><td>在您將會執行本教學課程 HDInsight 叢集。</td></tr>
    <tr><td>HDInsight 叢集使用者名稱</td><td>$clusterUsername</td><td></td><td>HDInsight 叢集使用者名稱。 </td></tr>
    <tr><td>HDInsight 叢集使用者的密碼 </td><td>$clusterPassword</td><td></td><td>HDInsight 叢集使用者的密碼。</td></tr>
    <tr><td>Azure 儲存體帳戶名稱</td><td>$storageAccountName</td><td></td><td>Azure 儲存體帳戶叢集可用的 HDInsight。 在此教學課程中，使用 [叢集佈建程序期間所指定的預設儲存帳戶。</td></tr>
    <tr><td>Azure Blob 容器名稱</td><td>$containerName</td><td></td><td>此範例中，使用所用的預設 HDInsight 叢集檔案系統的 Azure Blob 儲存容器。 根據預設，它有 HDInsight 叢集相同的名稱。</td></tr>
    </table>

- **Azure SQL 資料庫**。 您必須設定防火牆規則，允許從工作站存取 SQL 資料庫伺服器。 如需建立 Azure SQL 資料庫，並設定防火牆，請參閱[開始使用 Azure SQL 資料庫][sqldatabase-get-started]。 本文提供的 Windows PowerShell 指令碼建立 Azure SQL 資料庫表格，您需要在此教學課程。

    <table border = "1">
    <tr><th>SQL 資料庫屬性</th><th>Windows PowerShell 的變數名稱</th><th>值</th><th>描述</th></tr>
    <tr><td>SQL 資料庫伺服器名稱</td><td>$sqlDatabaseServer</td><td></td><td>SQL 資料庫伺服器 Sqoop 會將資料匯出。 </td></tr>
    <tr><td>SQL 資料庫登入名稱</td><td>$sqlDatabaseLogin</td><td></td><td>SQL 資料庫登入名稱。</td></tr>
    <tr><td>SQL 資料庫登入密碼</td><td>$sqlDatabaseLoginPassword</td><td></td><td>SQL 資料庫登入密碼。</td></tr>
    <tr><td>SQL 資料庫名稱</td><td>$sqlDatabaseName</td><td></td><td>Sqoop 會將資料匯出 Azure SQL 資料庫。 </td></tr>
    </table>

    > [AZURE.NOTE] 依預設 Azure SQL 資料庫，請從 Azure 服務，例如 Azure HDInsight 允許連線。 如果此防火牆設定已停用，您必須從 Azure 入口網站中啟用它。 建立 SQL 資料庫及設定防火牆規則的相關指示，請參閱[建立及設定 SQL 資料庫][sqldatabase-get-started]。


> [AZURE.NOTE] 填入資料表中的值。 就會很有幫助的進行本教學課程。


##<a name="define-oozie-workflow-and-the-related-hiveql-script"></a>定義 Oozie 工作流程和相關的 HiveQL 指令碼

HPDL （XML 程序定義語言） 撰寫 Oozie 工作流程定義。 預設工作流程的檔案名稱是*workflow.xml*。  您會儲存在本機工作流程檔案，然後將其部署到 HDInsight 叢集，稍後在本教學課程中使用 PowerShell 的 Azure。

工作流程中的登錄區動作通話 HiveQL 指令碼檔。 這個指令碼檔包含三個 HiveQL 陳述式︰

1. 如果有的話，**放 TABLE 陳述式**刪除 log4j 登錄區資料表。
2. **CREATE TABLE 陳述式**會建立一個 log4j 登錄區外部表格，指向 [log4j 記錄檔的位置。
3.  **Log4j 記錄檔的位置**。 欄位分隔符號是 「，」。 預設線條分隔符號是 「 \n 」。 若要避免從原始位置，移除的資料檔案，以防您想要執行 Oozie 工作流程多次使用登錄區外部表格。
3. **覆寫插入陳述式**計算 log4j 登錄區資料表中，從每個層級的記錄類型的項目，並將其儲存輸出 Azure Blob 儲存位置。

**附註**︰ 已知的登錄區路徑問題。 提交 Oozie 工作時，會遇到此問題。 可以在 TechNet Wiki 上，找到修正問題的指示︰ [HDInsight 登錄區錯誤︰ 無法重新命名][technetwiki-hive-error]。

**若要定義工作流程呼叫 HiveQL 指令碼檔**

1. 建立文字檔案，下列內容︰

        DROP TABLE ${hiveTableName};
        CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
        INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

    有三個變數用於指令碼︰

    - $ {hiveTableName}
    - $ {hiveDataFolder}
    - $ {hiveOutputFolder}

    工作流程定義檔案 (在本教學課程 workflow.xml) 會將這些值傳遞給這個 HiveQL 指令碼，在執行階段。

2. 使用的 ANSI (ASCII) 編碼來**C:\Tutorials\UseOozie\useooziewf.hql**儲存的檔案。 （使用 [記事本] 如果您在文字編輯器不提供此選項時）。稍後在本教學課程中，將 HDInsight 叢集部署這個指令碼檔。



**若要定義工作流程**

1. 建立文字檔案，下列內容︰

        <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
            <start to = "RunHiveScript"/>

            <action name="RunHiveScript">
                <hive xmlns="uri:oozie:hive-action:0.2">
                    <job-tracker>${jobTracker}</job-tracker>
                    <name-node>${nameNode}</name-node>
                    <configuration>
                        <property>
                            <name>mapred.job.queue.name</name>
                            <value>${queueName}</value>
                        </property>
                    </configuration>
                    <script>${hiveScript}</script>
                    <param>hiveTableName=${hiveTableName}</param>
                    <param>hiveDataFolder=${hiveDataFolder}</param>
                    <param>hiveOutputFolder=${hiveOutputFolder}</param>
                </hive>
                <ok to="RunSqoopExport"/>
                <error to="fail"/>
            </action>

            <action name="RunSqoopExport">
                <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                    <job-tracker>${jobTracker}</job-tracker>
                    <name-node>${nameNode}</name-node>
                    <configuration>
                        <property>
                            <name>mapred.compress.map.output</name>
                            <value>true</value>
                        </property>
                    </configuration>
                <arg>export</arg>
                <arg>--connect</arg>
                <arg>${sqlDatabaseConnectionString}</arg>
                <arg>--table</arg>
                <arg>${sqlDatabaseTableName}</arg>
                <arg>--export-dir</arg>
                <arg>${hiveOutputFolder}</arg>
                <arg>-m</arg>
                <arg>1</arg>
                <arg>--input-fields-terminated-by</arg>
                <arg>"\001"</arg>
                </sqoop>
                <ok to="end"/>
                <error to="fail"/>
            </action>

            <kill name="fail">
                <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
            </kill>

           <end name="end"/>
        </workflow-app>

    有兩個工作流程中所定義的動作。 [開始] 動作是*RunHiveScript*。 如果巨集指令執行*[確定]*下, 一個動作是*RunSqoopExport*。

    RunHiveScript 有幾個變數。 當您從工作站提交 Oozie 工作使用 PowerShell 的 Azure，您會傳遞的值。

    <table border = "1">
    <tr><th>工作流程變數</th><th>描述</th></tr>
    <tr><td>$ {jobTracker}</td><td>指定 Hadoop 工作追蹤器的 URL。 使用<strong>jobtrackerhost:9010</strong> HDInsight 叢集版本 3.0 和 2.0 上。</td></tr>
    <tr><td>$ {nameNode}</td><td>指定 Hadoop 名稱節點的 URL。 使用預設檔案系統 wasbs: / / 地址，例如， <i>wasbs: / /&lt;containerName&gt;@&lt;storageAccountName&gt;。 blob.core.windows.net</i>。</td></tr>
    <tr><td>$ {queueName}</td><td>指定的工作會送出到佇列名稱。 使用<strong>預設值</strong>。</td></tr>
    </table>


    <table border = "1">
    <tr><th>登錄區動作變數</th><th>描述</th></tr>
    <tr><td>$ {hiveDataFolder}</td><td>登錄區建立表格] 命令的來源目錄。</td></tr>
    <tr><td>$ {hiveOutputFolder}</td><td>插入覆寫陳述式輸出資料夾。</td></tr>
    <tr><td>$ {hiveTableName}</td><td>登錄區資料表參照 log4j 資料檔案的名稱。</td></tr>
    </table>


    <table border = "1">
    <tr><th>Sqoop 動作變數</th><th>描述</th></tr>
    <tr><td>$ {sqlDatabaseConnectionString}</td><td>SQL 資料庫連線字串。</td></tr>
    <tr><td>$ {sqlDatabaseTableName}</td><td>Azure SQL 資料庫表格會匯出資料的位置。</td></tr>
    <tr><td>$ {hiveOutputFolder}</td><td>輸出資料夾的登錄區插入覆寫陳述式。 這是 Sqoop 匯出 (匯出 dir) 相同的資料夾。</td></tr>
    </table>

    如需有關 Oozie 工作流程和使用工作流程動作的詳細資訊，請參閱[Apache Oozie 4.0 文件][ apache-oozie-400] （適用於 HDInsight 叢集版本 3.0） 或[Apache Oozie 3.3.2 文件][ apache-oozie-332] （適用於 2.1 HDInsight 叢集版）。

2. 使用的 ANSI (ASCII) 編碼來**C:\Tutorials\UseOozie\workflow.xml**儲存的檔案。 （使用 [記事本] 如果您在文字編輯器不提供此選項時）。

**若要定義協調器**

1. 建立文字檔案，下列內容︰

        <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
           <action>
              <workflow>
                 <app-path>${wfPath}</app-path>
              </workflow>
           </action>
        </coordinator-app>

    有五個變數定義檔案中使用︰

  	| 變數          | 描述 |
  	| ------------------|------------ |
  	| $ {coordFrequency} | 工作暫停的時間。 Frequency 一律會以表示分鐘。 |
  	| $ {coordStart}     | 工作的開始時間。 |
  	| $ {coordEnd}       | 工作的結束時間。 |
  	| $ {coordTimezone}  | Oozie 處理協調器工作在固定的時區與 （通常是使用 UTC 表示） 沒有日光節約時間。 這個時區稱為 「 Oozie 處理時區]。 |
  	| $ {wfPath}         | Workflow.xml 的路徑。  如果工作流程檔案名稱不是預設檔案名稱 (workflow.xml)，您必須將其指定。 |

2. 使用的 ANSI (ASCII) 編碼來**C:\Tutorials\UseOozie\coordinator.xml**儲存的檔案。 （使用 [記事本] 如果您在文字編輯器不提供此選項時）。

##<a name="deploy-the-oozie-project-and-prepare-the-tutorial"></a>部署 Oozie 專案，並準備教學課程

您會執行 Azure PowerShell 指令碼來執行下列動作︰

- 複製 HiveQL 指令碼 (useoozie.hql) 至 Azure Blob 儲存體，wasbs:///tutorials/useoozie/useoozie.hql。
- 複製 wasbs:///tutorials/useoozie/workflow.xml workflow.xml。
- 複製 wasbs:///tutorials/useoozie/coordinator.xml coordinator.xml。
- 複製的資料檔案 (/ example/data/sample.log) 至 wasbs:///tutorials/useoozie/data/sample.log。
- 建立 Azure SQL 資料庫表格儲存 Sqoop 匯出資料。 資料表名稱是*log4jLogCount*。

**了解 HDInsight 儲存空間**

HDInsight 的資料儲存區使用 Azure Blob 儲存體。 wasbs: / / 是 Microsoft 的實作 Azure Blob 儲存體 Hadoop 分散式檔案系統 (HDFS)。 如需詳細資訊，請參閱[使用 Azure Blob 儲存體，與 HDInsight][hdinsight-storage]。

當您佈建 HDInsight 叢集時，Azure Blob 儲存體帳戶與特定的容器從該帳戶會指定為預設檔案系統中，例如 HDFS 中。 除了此儲存帳戶，您可以新增額外的儲存空間帳戶從同一份 Azure 訂閱，或從不同的 Azure 訂閱提供程序。 如需新增額外的儲存空間帳戶的相關指示，請參閱[佈建 HDInsight 叢集][hdinsight-provision]。 若要簡化在本教學課程所用的 PowerShell 的 Azure 指令碼，所有的檔案會儲存在位於*/tutorials/useoozie*的預設檔案系統容器。 根據預設，這個容器具有相同名稱的 HDInsight 叢集名稱。
語法如下︰

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE] 僅限*wasb: / /* HDInsight 叢集 3.0 版支援語法。 舊版*asv: / /* HDInsight 2.1 和 1.6 叢集中, 支援的語法，但它不支援 HDInsight 3.0 叢集。

> [AZURE.NOTE] Wasb: / / 路徑是虛擬。 如需詳細資訊，請參閱[使用 Azure Blob 儲存體，與 HDInsight][hdinsight-storage]。

可以使用下列任一動作 Uri （我使用的 workflow.xml 作為範例），從 HDInsight 存取的預設檔案系統容器中所儲存的檔案︰

    wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
    wasbs:///tutorials/useoozie/workflow.xml
    /tutorials/useoozie/workflow.xml

如果您想要直接從儲存帳戶存取檔案，檔案的 blob 名稱就會是︰

    tutorials/useoozie/workflow.xml

**了解登錄區內部和外部表格**

有幾個必須知道登錄區內部和外部表格的項目︰

- [建立表格] 命令會建立的內部的資料表，也就是受管理的資料表。 預設容器中必須位於資料檔案。
- [建立表格] 命令將資料檔案移至 /hive/倉庫/<TableName>預設容器中的資料夾。
- 建立外部表格] 命令會建立外部的資料表。 資料檔可以位於預設容器之外。
- 建立外部表格命令不會移動的資料檔案。
- 建立外部表格命令並不允許任何位置子句中指定資料夾下的子資料夾。 這是為什麼教學課程可讓您 sample.log 檔案的複本的原因。

如需詳細資訊，請參閱[HDInsight︰ 登錄區內部和外部表格簡介][cindygross-hive-tables]。

**準備教學課程**

1. 開啟 Windows PowerShell ise [以系統 （在 Windows 8 [開始] 畫面中，輸入**PowerShell_ISE**，，然後按一下**Windows PowerShell ise [以系統**。 如需詳細資訊，請參閱[在 Windows 8 和 Windows 上啟動 Windows PowerShell][powershell-start])。
2. 在下方窗格中，執行下列命令以連線至 Azure 訂閱︰

        Add-AzureAccount

    系統會提示您輸入 Azure 帳戶認證。 這個方法新增訂閱連線逾時，和 12 小時後，您就必須再次執行指令程式。

    > [AZURE.NOTE] 如果您有多個 Azure 訂閱預設訂閱不是您想要使用的項目，請選取訂閱使用<strong>選取 AzureSubscription</strong> cmdlet。

3. 在 [指令碼] 窗格中，複製下列指令碼，然後設定 [第一次六個變數︰

        # WASB variables
        $storageAccountName = "<StorageAccountName>"
        $containerName = "<BlobStorageContainerName>"

        # SQL database variables
        $sqlDatabaseServer = "<SQLDatabaseServerName>"  
        $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
        $sqlDatabaseLoginPassword = "SQLDatabaseLoginPassword>"
        $sqlDatabaseName = "<SQLDatabaseName>"  
        $sqlDatabaseTableName = "log4jLogsCount"

        # Oozie files for the tutorial
        $hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"
        $workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
        $coordDefinition =  "C:\Tutorials\UseOozie\coordinator.xml"

        # WASB folder for storing the Oozie tutorial files.
        $destFolder = "tutorials/useoozie"  # Do NOT use the long path here


    如需更多的變數的詳細說明，請參閱在本教學課程的[先決條件](#prerequisites)一節。

3. 在 [指令碼] 窗格中的指令碼加入下列︰

        # Create a storage context object
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        function uploadOozieFiles()
        {
            Write-Host "Copy HiveQL script, workflow definition and coordinator definition ..." -ForegroundColor Green
            Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
            Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
            Set-AzureStorageBlobContent -File $coordDefinition -Container $containerName -Blob "$destFolder/coordinator.xml" -Context $destContext
        }

        function prepareHiveDataFile()
        {
            Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
            Start-CopyAzureStorageBlob -SrcContainer $containerName -SrcBlob "example/data/sample.log" -Context $destContext -DestContainer $containerName -destBlob "$destFolder/data/sample.log" -DestContext $destContext
        }

        function prepareSQLDatabase()
        {
            # SQL query string for creating log4jLogsCount table
            $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                    [Level] [nvarchar](10) NOT NULL,
                    [Total] float,
                CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
                (
                [Level] ASC
                )
                )"

            #Create the log4jLogsCount table
            Write-Host "Create Log4jLogsCount table ..." -ForegroundColor Green
            $conn = New-Object System.Data.SqlClient.SqlConnection
            $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
            $conn.open()
            $cmd = New-Object System.Data.SqlClient.SqlCommand
            $cmd.connection = $conn
            $cmd.commandtext = $cmdCreateLog4jCountTable
            $cmd.executenonquery()

            $conn.close()
        }

        # upload workflow.xml, coordinator.xml, and ooziewf.hql
        uploadOozieFiles;

        # make a copy of example/data/sample.log to example/data/log4j/sample.log
        prepareHiveDataFile;

        # create log4jlogsCount table on SQL database
        prepareSQLDatabase;

4. 按一下 [**執行指令碼**，或按**F5**執行指令碼。 輸出會類似︰

    ![教學課程準備輸出][img-preparation-output]

##<a name="run-the-oozie-project"></a>執行 Oozie 專案

Azure PowerShell 目前不提供任何 cmdlet 定義 Oozie 工作。 您可以使用**叫用 RestMethod** cmdlet 叫用 Oozie web 服務。 Oozie web 服務 API 是 HTTP 其餘 JSON API。 如需有關 Oozie web 服務 API 的詳細資訊，請參閱[Apache Oozie 4.0 文件][ apache-oozie-400] （適用於 HDInsight 叢集版本 3.0） 或[Apache Oozie 3.3.2 文件][ apache-oozie-332] （適用於 2.1 HDInsight 叢集版）。

**提交 Oozie 工作**

1. 開啟 Windows PowerShell ise [以系統 （在 Windows 8 [開始] 畫面中，輸入**PowerShell_ISE**，，然後按一下 [ **Windows PowerShell ise [以系統**。 如需詳細資訊，請參閱[在 Windows 8 和 Windows 上啟動 Windows PowerShell][powershell-start])。

3. 在 [指令碼] 窗格中，複製下列指令碼，然後設定 [第一次十四變數 （不過，略過**$storageUri**）。

        #HDInsight cluster variables
        $clusterName = "<HDInsightClusterName>"
        $clusterUsername = "<HDInsightClusterUsername>"
        $clusterPassword = "<HDInsightClusterUserPassword>"

        #Azure Blob storage (WASB) variables
        $storageAccountName = "<StorageAccountName>"
        $storageContainerName = "<BlobContainerName>"
        $storageUri="wasbs://$storageContainerName@$storageAccountName.blob.core.windows.net"

        #Azure SQL database variables
        $sqlDatabaseServer = "<SQLDatabaseServerName>"
        $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
        $sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
        $sqlDatabaseName = "<SQLDatabaseName>"  

        #Oozie WF/coordinator variables
        $coordStart = "2014-03-21T13:45Z"
        $coordEnd = "2014-03-21T13:45Z"
        $coordFrequency = "1440"    # in minutes, 24h x 60m = 1440m
        $coordTimezone = "UTC"  #UTC/GMT

        $oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
        $waitTimeBetweenOozieJobStatusCheck=10

        #Hive action variables
        $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
        $hiveTableName = "log4jlogs"
        $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
        $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"

        #Sqoop action variables
        $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServer.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServer;password=$sqlDatabaseLoginPassword;database=$sqlDatabaseName"
        $sqlDatabaseTableName = "log4jLogsCount"

        $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
        $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    如需更多的變數的詳細說明，請參閱在本教學課程的[先決條件](#prerequisites)一節。

    $coordstart 和 $coordend 是工作流程開始及結束時間。 若要瞭解 UTC/GMT 時間，搜尋 「 utc 時間 」 bing.com。 $CoordFrequency 頻率位於您想要執行工作流程的分鐘。

3. 新增下列指令碼。 此組件定義 Oozie 內容︰

        #OoziePayload used for Oozie web service submission
        $OoziePayload =  @"
        <?xml version="1.0" encoding="UTF-8"?>
        <configuration>

           <property>
               <name>nameNode</name>
               <value>$storageUrI</value>
           </property>

           <property>
               <name>jobTracker</name>
               <value>jobtrackerhost:9010</value>
           </property>

           <property>
               <name>queueName</name>
               <value>default</value>
           </property>

           <property>
               <name>oozie.use.system.libpath</name>
               <value>true</value>
           </property>

           <property>
               <name>oozie.coord.application.path</name>
               <value>$oozieWFPath</value>
           </property>

           <property>
               <name>wfPath</name>
               <value>$oozieWFPath</value>
           </property>

           <property>
               <name>coordStart</name>
               <value>$coordStart</value>
           </property>

           <property>
               <name>coordEnd</name>
               <value>$coordEnd</value>
           </property>

           <property>
               <name>coordFrequency</name>
               <value>$coordFrequency</value>
           </property>

           <property>
               <name>coordTimezone</name>
               <value>$coordTimezone</value>
           </property>

           <property>
               <name>hiveScript</name>
               <value>$hiveScript</value>
           </property>

           <property>
               <name>hiveTableName</name>
               <value>$hiveTableName</value>
           </property>

           <property>
               <name>hiveDataFolder</name>
               <value>$hiveDataFolder</value>
           </property>

           <property>
               <name>hiveOutputFolder</name>
               <value>$hiveOutputFolder</value>
           </property>

           <property>
               <name>sqlDatabaseConnectionString</name>
               <value>&quot;$sqlDatabaseConnectionString&quot;</value>
           </property>

           <property>
               <name>sqlDatabaseTableName</name>
               <value>$SQLDatabaseTableName</value>
           </property>

           <property>
               <name>user.name</name>
               <value>admin</value>
           </property>

        </configuration>
        "@

    >[AZURE.NOTE] 相較於工作流程的提交內容檔案的主要差異是變數**oozie.coord.application.path**。 當您送出工作流程工作時，會改為使用**oozie.wf.application.path** 。

4. 新增下列指令碼。 此組件會檢查 Oozie web 服務的狀態︰

        function checkOozieServerStatus()
        {
            Write-Host "Checking Oozie server status..." -ForegroundColor Green
            $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus

            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $oozieServerSatus = $jsonResponse[0].("systemMode")
            Write-Host "Oozie server status is $oozieServerSatus..."

            if($oozieServerSatus -notmatch "NORMAL")
            {
                Write-Host "Oozie server status is $oozieServerSatus...cannot submit Oozie jobs. Check the server status and re-run the job."
                exit 1
            }
        }

5. 新增下列指令碼。 此組件會建立 Oozie 工作︰

        function createOozieJob()
        {
            # create Oozie job
            Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
            Write-Host "`n--------`n$OoziePayload`n--------"
            $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
            $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName -debug -Verbose

            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $oozieJobId = $jsonResponse[0].("id")
            Write-Host "Oozie job id is $oozieJobId..."

            return $oozieJobId
        }

    > [AZURE.NOTE] 當您送出工作流程工作時，您必須進行其他的 web 服務呼叫啟動後建立工作的工作。 在此情況下，依時間觸發協調器工作。 工作會自動啟動。

6. 新增下列指令碼。 此組件會檢查 Oozie 工作狀態︰

        function checkOozieJobStatus($oozieJobId)
        {
            # get job status
            Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
            Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

            Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
            $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $JobStatus = $jsonResponse[0].("status")

            while($JobStatus -notmatch "SUCCEEDED|KILLED")
            {
                Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
                Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
                $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
                $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
                $JobStatus = $jsonResponse[0].("status")
            }

            Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!"
            if($JobStatus -notmatch "SUCCEEDED")
            {
                Write-Host "Check logs at http://headnode0:9014/cluster for detais."
                exit -1
            }
        }

7. （選用）新增下列指令碼。

        function listOozieJobs()
        {
            Write-Host "Listing Oozie jobs..." -ForegroundColor Green
            $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds

            write-host "Job ID                                   App Name        Status      Started                         Ended"
            write-host "----------------------------------------------------------------------------------------------------------------------------------"
            foreach($job in $response.workflows)
            {
                Write-Host $job.id "`t" $job.appName "`t" $job.status "`t" $job.startTime "`t" $job.endTime
            }
        }

        function ShowOozieJobLog($oozieJobId)
        {
            Write-Host "Showing Oozie job info..." -ForegroundColor Green
            $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/$oozieJobId" + "?show=log"
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds
            write-host $response
        }

        function killOozieJob($oozieJobId)
        {
            Write-Host "Killing the Oozie job $oozieJobId..." -ForegroundColor Green
            $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=kill" #Valid values for the 'action' parameter are 'start', 'suspend', 'resume', 'kill', 'dryrun', 'rerun', and 'change'.
            $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders -debug
        }

7. 附加下列指令碼︰

        checkOozieServerStatus
        # listOozieJobs
        $oozieJobId = createOozieJob($oozieJobId)
        checkOozieJobStatus($oozieJobId)
        # ShowOozieJobLog($oozieJobId)
        # killOozieJob($oozieJobId)

    如果您想要執行的其他功能，請移除 # 符號。

7. 如果您的 HDinsight 叢集 2.1 版，來取代 「 https://$clusterName.azurehdinsight.net:443/oozie/v2/ 」 與 「 https://$clusterName.azurehdinsight.net:443/oozie/v1/ 」。 HDInsight 叢集 2.1 版並不支援的版本 2 web 服務。

7. 按一下 [**執行指令碼**，或按**F5**執行指令碼。 輸出會類似︰

    ![教學課程中執行的工作流程輸出][img-runworkflow-output]

8. 連線至 SQL 資料庫來查看匯出的資料。

**若要檢查的工作錯誤記錄檔**

若要疑難排解工作流程，Oozie 記錄檔可以位於 C:\apps\dist\oozie-3.3.2.1.3.2.0-05\oozie-win-distro\logs\Oozie.log 從叢集 headnode。 RDP 的詳細資訊，請參閱[使用 [Azure 入口網站管理 HDInsight 叢集][hdinsight-admin-portal]。

**若要重新執行教學課程**

若要重新執行工作流程，您必須執行下列工作︰

- 刪除登錄區指令碼輸出檔案。
- 刪除 log4jLogsCount 資料表中的資料。

以下是您可以使用範例 Windows PowerShell 指令碼︰

    $storageAccountName = "<AzureStorageAccountName>"
    $containerName = "<ContainerName>"

    #SQL database variables
    $sqlDatabaseServer = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    Write-host "Delete the Hive script output file ..." -ForegroundColor Green
    $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
    Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $containerName

    Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
    $conn.open()
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.connection = $conn
    $cmd.commandtext = "delete from $sqlDatabaseTableName"
    $cmd.executenonquery()

    $conn.close()


##<a name="next-steps"></a>後續步驟
在本教學課程中，您將學會如何定義 Oozie 工作流程和 Oozie 協調器，以及如何使用 PowerShell 的 Azure 執行 Oozie 協調器工作。 若要深入瞭解，請參閱下列文章︰

- [快速入門 HDInsight][hdinsight-get-started]
- [使用 HDInsight Azure Blob 儲存體][hdinsight-storage]
- [使用 PowerShell 的 Azure 管理 HDInsight][hdinsight-admin-powershell]
- [上傳至 HDInsight 的資料][hdinsight-upload-data]
- [使用 HDInsight Sqoop][hdinsight-use-sqoop]
- [使用 HDInsight 的登錄區][hdinsight-use-hive]
- [使用 HDInsight 的豬][hdinsight-use-pig]
- [開發 HDInsight Java MapReduce 程式][hdinsight-develop-java-mapreduce]



[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563


[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-develop-java-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md

[sqldatabase-get-started]: ../sql-database/sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: ../powershell-install-configure.md
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Preparation.Output1.png  
[img-runworkflow-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.RunCoord.Output.png  

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
