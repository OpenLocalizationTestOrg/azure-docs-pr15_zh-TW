<properties
    pageTitle="使用 Linux 型 HDInsight Hadoop Sqoop |Microsoft Azure"
    description="了解如何執行 Sqoop 匯入及匯出 HDInsight 叢集上的 Linux 型 Hadoop 和 Azure SQL 資料庫之間。"
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="larryfr"/>

#<a name="use-sqoop-with-hadoop-in-hdinsight-ssh"></a>使用中 HDInsight (SSH) Hadoop Sqoop

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

瞭解如何使用 Sqoop 匯入及匯出之間的 Linux 型 HDInsight 叢集和 Azure SQL 資料庫或 SQL Server 資料庫。

> [AZURE.NOTE] 本文中的步驟，使用 SSH Linux 型 HDInsight 叢集連線。 Windows 用戶端也可以使用 PowerShell 的 Azure 及 HDInsight.NET SDK 若要使用的 Sqoop Linux 型叢集。 使用 [定位點選取器，以開啟這些文件。

##<a name="prerequisites"></a>必要條件

本教學課程之前，您必須具備下列項目︰

- **Hadoop 叢集中 HDInsight**和__Azure SQL 資料庫__︰ 根據這份文件中的步驟叢集，並使用 「[建立叢集和 SQL 資料庫](hdinsight-use-sqoop.md#create-cluster-and-sql-database)文件建立的資料庫。 如果您已經有的 HDInsight 叢集和 SQL 資料庫時，您可以用於文件中的值取代。
- **工作站**︰ SSH 用戶端的電腦。

##<a name="install-freetds"></a>安裝 FreeTDS

1. 使用 SSH Linux 型 HDInsight 叢集連線。 連接時要使用的地址是`CLUSTERNAME-ssh.azurehdinsight.net`和連接埠是`22`。

    如需有關使用 SSH 連線至 HDInsight 的詳細資訊，請參閱下列文件︰

    * **Linux、 Unix 或 OS X 的用戶端**︰ 請參閱[連線至 Linux 型 HDInsight 叢集 Linux、 OS X 或 Unix](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)

    * **Windows 用戶端**︰ 請參閱[從 Windows Linux 型 HDInsight 叢集的連線](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster)

3. 若要安裝 FreeTDS 使用下列命令︰

        sudo apt-get --assume-yes install freetds-dev freetds-bin

    FreeTDS 會用於幾個步驟，以連線至 SQL 資料庫。

##<a name="create-the-table-in-sql-database"></a>SQL 資料庫中建立表格

> [AZURE.IMPORTANT] 如果您使用 HDInsight 叢集和 SQL 資料庫建立使用[建立叢集和 SQL 資料庫](hdinsight-use-sqoop.md)中的步驟，忽略資料庫及表格所建立的文件中的步驟，在此區段中的步驟。

1. 從 HDInsight SSH 連線，請連線至 SQL 資料庫伺服器和花費會使用這些步驟的其餘部分中的資料表中使用下列命令︰

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    您會收到下列類似的輸出︰

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. 在`1>`提示中，輸入下列行︰

        CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50),
        [querytime] [nvarchar](50),
        [market] [nvarchar](50),
        [deviceplatform] [nvarchar](50),
        [devicemake] [nvarchar](50),
        [devicemodel] [nvarchar](50),
        [state] [nvarchar](50),
        [country] [nvarchar](50),
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder] [bigint])
        GO
        CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
        GO

    當`GO`輸入陳述式時，將會評估前一個陳述式。 首先，建立**mobiledata**資料表，然後叢集的索引會新增至其 （需要 SQL 資料庫）。

    您可以使用下列，確認已建立資料表︰

        SELECT * FROM information_schema.tables
        GO

    您應該會看到類似以下的輸出︰

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE

8. 輸入`exit`在`1>`提示結束 tsql 公用程式。

##<a name="sqoop-export"></a>Sqoop 匯出

3. 從 HDInsight，se 下列命令以確認 Sqoop 可以看見您 SQL 資料庫 SSH 連線︰

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    這應該會傳回資料庫，包括您先前建立的**sqooptest**資料庫的清單。

4. 若要從**hivesampletable**匯出資料至**mobiledata**表格中使用下列命令︰

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasbs:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1

    這會指示到 SQL 資料庫連線到**sqooptest**資料庫，並從匯出資料的 Sqoop **wasbs: / 登錄區/倉庫/hivesampletable** （ *hivesampletable*，實體檔案） 至**mobiledata**資料表。

5. 命令完成後，請使用下列連線到使用 TSQL 資料庫︰

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    連線後，使用下列陳述式驗證的資料匯出至**mobiledata**資料表︰

        SELECT * FROM mobiledata
        GO

    您應該會看到資料表中的資料的清單。 輸入`exit`結束 tsql 公用程式。

##<a name="sqoop-import"></a>Sqoop 匯入

1. 使用下列命令以 SQL 資料庫**mobiledata**表格匯入資料**wasbs: / 教學課程/usesqoop/importeddata** HDInsight 目錄︰

        sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

    匯入的資料會有一個定位字元，以分隔的欄位，線條會終止新行字元。

2. 完成匯入後，請新增目錄中使用下列命令以查看資料的清單︰

        hadoop fs -text wasbs:///tutorials/usesqoop/importeddata/part-m-00000

##<a name="using-sql-server"></a>使用 SQL Server

您也可以使用 Sqoop 匯入及匯出資料從 SQL Server，在您的資料中心或裝載於 Azure 虛擬機器上。 使用 SQL 資料庫與 SQL Server 之間的差異如下︰

* HDInsight 和 SQL Server 都必須在相同的 Azure 虛擬網路

    > [AZURE.NOTE] HDInsight 支援只有依據位置的虛擬網路，並將其目前無法運作的相關性群組為基礎的虛擬網路。

    當您在使用 SQL Server 資料中心的時您必須設定為*網站為網站*或*點為網站*的虛擬網路。

    > [AZURE.NOTE] **點-網站**虛擬網路，SQL Server 必須執行 VPN 用戶端設定應用程式，可以從 Azure 虛擬網路設定的**儀表板**。

    如需詳細資訊 Azure 虛擬網路，請參閱[虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。

* SQL Server 必須設定為允許 SQL 驗證。 如需詳細資訊，請參閱[選擇驗證模式](https://msdn.microsoft.com/ms144284.aspx)

* 您可能必須設定為接受遠端連線的 SQL Server。 如需詳細資訊，請參閱[如何疑難排解連線至 SQL Server 資料庫引擎](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)

* 您必須建立**sqooptest**資料庫的 SQL Server 使用公用程式，例如**SQL Server Management Studio**或**tsql** -使用 Azure CLI 的步驟僅適用於 Azure SQL 資料庫

    若要建立**mobiledata**資料表 TSQL 陳述式類似所使用的 SQL 資料庫]，但建立 clusterd 索引-這不是必要的 SQL Server:

        CREATE TABLE [dbo].[mobiledata](
        [clientid] [nvarchar](50),
        [querytime] [nvarchar](50),
        [market] [nvarchar](50),
        [deviceplatform] [nvarchar](50),
        [devicemake] [nvarchar](50),
        [devicemodel] [nvarchar](50),
        [state] [nvarchar](50),
        [country] [nvarchar](50),
        [querydwelltime] [float],
        [sessionid] [bigint],
        [sessionpagevieworder] [bigint])

* 在從 HDInsight，以連線至 SQL Server，您可能必須使用 SQL Server 的 IP 位址，除非您已設定的 「 網域名稱系統 (DNS) 」，來解決 Azure 虛擬網路上的名稱。 例如︰

        sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

##<a name="limitations"></a>限制

* 大量匯出-使用 Linux 型 HDInsight、 用來將資料匯出至 Microsoft SQL Server Azure SQL 資料庫或 Sqoop 連接器目前不支援大量插入。

* 批次處理-Linux 型 HDInsight，與使用時`-batch`切換執行插入時，Sqoop 會執行，而不是批次處理插入作業的多個插入。

##<a name="next-steps"></a>後續步驟

現在您已經學會如何使用 Sqoop。 若要深入瞭解，請參閱︰

- [使用 HDInsight Oozie][hdinsight-use-oozie]: Oozie 工作流程中的使用 Sqoop 動作。
- [分析航班延遲情況資料增添使用 HDInsight][hdinsight-analyze-flight-data]︰ 使用登錄區來分析航班延遲資料，，然後使用 Sqoop 將資料匯出至 Azure SQL 資料庫。
- [上傳至 HDInsight 的資料][hdinsight-upload-data]︰ 尋找上傳至 HDInsight/Azure Blob 儲存體的資料的其他方法。



[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
