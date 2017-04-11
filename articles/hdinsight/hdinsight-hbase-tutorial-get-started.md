<properties
    pageTitle="HBase 教學課程︰ 開始使用中 Hadoop HBase |Microsoft Azure"
    description="請遵循此 HBase 教學課程，若要開始使用 Apache HBase Hadoop HDInsight 中使用。 從 HBase 殼層建立資料表和查詢它們使用登錄區。"
    keywords="apache hbase，hbase，hbase 命令介面 hbase 教學課程"
    services="hdinsight"
    documentationCenter=""
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



# <a name="hbase-tutorial-get-started-using-apache-hbase-with-windows-based-hadoop-in-hdinsight"></a>HBase 教學課程︰ 開始使用 Windows 型的 Hadoop 中 HDInsight Apache HBase

[AZURE.INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]

瞭解如何建立 HDInsight HBase 叢集、 建立 HBase 資料表和查詢資料表使用 Apache 登錄區。 一般 HBase 資訊，請參閱[HDInsight HBase 概觀][hdinsight-hbase-overview]。

這份文件中的資訊是 Windows 型 HDInsight 叢集特定項目。 Windows 型叢集上的資訊，請使用上方的頁面] 索引標籤選擇器中切換的內容。

> [AZURE.NOTE] 在 Windows 型 HDInsight HBase （版本 0.98.0） 只適用於 HDInsight 3.1 叢集 （根據 Apache Hadoop 和 YARN 2.4.0）。 版本資訊，請參閱[HDInsight 所提供之 Hadoop 叢集版本中的新功能？][hdinsight-versions]

## <a name="before-you-begin"></a>開始之前

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

此 HBase 教學課程之前，您必須具備下列項目︰

- **Microsoft Azure 訂閱**。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- **工作站**使用 Visual Studio 2013 或更大︰ 如需相關指示，請參閱[安裝 Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx)。

### <a name="access-control-requirements"></a>存取控制需求

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-hbase-cluster"></a>建立 HBase 叢集

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**若要建立 HBase 叢集使用 Azure 入口網站**

1. [Azure 入口網站]登入[azure-management-portal]。
2. 按一下 [**新增**] 或 [**+**左右上角，，然後按一下 [**資料 + 分析**， **HDInsight**。
3. 輸入下列值︰

    - **叢集名稱**-輸入名稱來識別這個叢集。
    - **叢集類型**︰ 選取**HBase**。
    - **叢集作業系統**-選取**Windows**。  建立 Linux 型 HBase 叢集，請參閱[HBase 教學課程︰ 開始使用 HDInsight (Linux) 中 Hadoop Apache HBase](hdinsight-hbase-tutorial-get-started-linux.md)。
    - **版本**-選取 HBase 版本。
    - **訂閱**-選取用來建立這個叢集 Azure 訂閱。
    - **資源群組**-建立新的 Azure 資源群組，或選取現有的項目。 如需詳細資訊，請參閱[Azure 資源管理員概觀](azure-resource-manager/resource-group-overview.md)
    - **認證**-為基礎的 Windows 叢集，您可以建立叢集使用者 （又名 HTTP 使用者、 HTTP web 服務使用者） 與遠端桌面使用者。 按一下 [**啟用遠端桌面**] 來新增遠端桌面使用者認證]。 下一節需要 RDP。
    - **資料來源**-建立新的 Azure 儲存體帳戶，或選取要作為預設檔案系統叢集現有 Azure 儲存體帳戶。 預設儲存帳戶位置會決定叢集位置的位置。 預設儲存帳戶和叢集必須共同找出在相同的資料中心。
    - **節點價格層**選取地區伺服器 HBase 叢集數目

        > [AZURE.WARNING] 高 HBase 服務的可用性，您必須建立叢集至少包含**三個**節點。 如此一來，可確保，一個節點當機，HBase 資料區域上是否有可用的其他節點。

        > 如果您會學習 HBase，永遠選擇 1 叢集大小的欄位，並刪除叢集之後降低成本每次使用。

    - **選擇性組態**設定 Azure 虛擬網路設定指令碼動作並新增額外的儲存空間的帳戶。

4. 按一下 [**建立**]。

>[AZURE.NOTE] 刪除 HBase 叢集之後，您可以使用相同的預設儲存帳戶和預設 blob 容器來建立另一個 HBase 叢集。 您建立原始叢集 HBase 資料表挑選新叢集。 若要避免不一致，我們建議您先刪除叢集，停用 HBase 資料表。

## <a name="create-tables-and-insert-data"></a>建立資料表，並插入資料

目前存取 HBase 有兩種方式。 本節說明使用 HBase 命令介面。 下一節說明使用.NET SDK。

大部分的人顯示資料以表格的格式︰

![hdinsight hbase 表格式資料][img-hbase-sample-data-tabular]

這是 BigTable 實作 HBase，在相同的資料看起來像︰

![hdinsight hbase bigtable 資料][img-hbase-sample-data-bigtable]

在您完成程序之後，這會使更有意義。  

**若要使用 HBase 命令介面**

1. 使用 RDP 連線到您 HBase 中叢集 HDInsight。 RDP 指示，請參閱[HDInsight 使用 Azure 入口網站中的管理 Hadoop 叢集][hdinsight-manage-portal]。
2. 您 RDP 工作階段中，按一下位於桌面**Hadoop 命令列**捷徑。
3. 開啟 HBase 命令介面︰

        cd %HBASE_HOME%\bin
        hbase shell

4. 建立兩個資料行系列 HBase:

        create 'Contacts', 'Personal', 'Office'
        list
5. 插入一些資料︰

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        scan 'Contacts'

    ![hdinsight hadoop hbase 命令介面][img-hbase-shell]

6. 取得單一資料列

        get 'Contacts', '1000'

    您會看到相同的結果為使用掃描] 命令，因為沒有只有一個資料列。

    如需有關 Hbase 資料表結構描述的詳細資訊，請參閱[簡介 HBase 結構描述設計][hbase-schema]。 取得更多 HBase 命令，請參閱[Apache HBase 參考指南][hbase-quick-start]。


6. 結束命令介面

        exit

**若要將連絡人 HBase 表格大量載入資料**

HBase 包含資料載入資料表有數種的方法。 如需詳細資訊，請參閱[大量載入](http://hbase.apache.org/book.html#arch.bulk.load)。


已上傳至公用 blob 容器的範例資料檔案wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt。 資料檔案的內容是︰

    8396    Calvin Raji     230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu        646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie        508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson    674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller   397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile    592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee       870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes      599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander 670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander   998-555-0171    230-555-0200    771 Northridge Drive

您可以建立文字檔案，然後上傳儲存帳戶檔案，如果您想要。 如需相關指示，請參閱[上傳的 HDInsight Hadoop 工作資料][hdinsight-upload-data]。

> [AZURE.NOTE] 此程序會使用您在最後一個程序中建立連絡人 HBase 資料表。

1. 您 RDP 工作階段中，按一下位於桌面**Hadoop 命令列**捷徑。
2. 變更目錄中︰

        cd %HBASE_HOME%\bin

3. 執行下列命令以轉換資料檔案的 StoreFiles 及 Dimporttsv.bulk.output 所指定的相對路徑存放區︰

        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:Phone, Office:Phone, Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. 執行下列命令以上傳 /example/data/storeDataFileOutput HBase 資料表的資料︰

        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. 您可以開啟 HBase 命令介面，並使用掃描的命令] 清單中的表格內容。



## <a name="use-hive-to-query-hbase-tables"></a>使用查詢 HBase 表格登錄區

您可以查詢使用登錄區 HBase 中儲存的資料。 本節會建立對應到 HBase 表格，並使用它來查詢您的 HBase 資料表中的資料的登錄區資料表。

**若要開啟叢集儀表板**

1. 瀏覽至**https://<HDInsight Cluster Name>.azurehdinsight.net/**。
5. 輸入 Hadoop 使用者帳戶使用者名稱和密碼。 預設的使用者名稱**管理員**，而且密碼是您在建立程序時所輸入的內容。 隨後便會開啟新的瀏覽器索引標籤。
6. 按一下頁面頂端的**登錄區編輯器**]。 登錄區編輯器] 中看起來像這樣︰

    ![HDInsight 叢集儀表板。][img-hdinsight-hbase-hive-editor]

**若要執行登錄區查詢**

1. 將登錄區編輯器] 中輸入下列 HiveQL 指令碼，然後按一下 [**提交**]，建立對應到 HBase 表格的登錄區資料表。 請確定您建立參照之前在本教學課程中，使用 HBase 命令介面，才能執行這個陳述式的範例資料表。

        CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
        TBLPROPERTIES ('hbase.table.name' = 'Contacts');

    等到**完成****狀態**更新。

2. 將登錄區編輯器] 中，輸入下列 HiveQL 指令碼，然後按一下 [**提交]**。 登錄區查詢查詢 HBase 資料表中的資料︰

        SELECT count(*) FROM hbasecontacts;

4. 若要擷取登錄區查詢的結果，請按一下 [**檢視詳細資料]**視窗中的連結**工作階段**時執行的工作完成。 因為 HBase 表格放入一筆記錄時，則會是只有一個工作輸出檔案。




**瀏覽輸出檔案**

1. 在 [查詢] 主控台中，按一下 [**檔案瀏覽器**。
2. 按一下 [HBase 叢集作為預設檔案系統的 Azure 儲存體帳戶]。
3. 按一下 HBase 叢集名稱。 預設 Azure 儲存體帳戶容器會使用叢集名稱。
4. 按一下 [**使用者**]，然後按一下 [**系統**。 （這是 Hadoop 使用者名稱）。
6. 按一下 [工作名稱，以符合選取登錄區查詢執行時的時間**上次修改日期**時間]。
4. 按一下 [ **stdout**]。 儲存檔案，並使用 [記事本] 中開啟的檔案。 會有一個輸出檔案。

    ![HDInsight HBase 登錄區編輯器檔案瀏覽器][img-hdinsight-hbase-file-browser]

## <a name="use-the-net-hbase-rest-api-client-library"></a>使用.NET HBase REST API 用戶端文件庫

您必須下載適用於從 GitHub.NET HBase REST API 用戶端文件庫，並建立專案，以便您可以使用 HBase.NET SDK。 下列程序包含此工作的指示進行。

1. 建立新的 C# Visual Studio Windows 桌面主控台應用程式。
2. 按一下 [**工具**開啟 NuGet 封裝管理員主控台 > **NuGet 封裝管理員** > **封裝管理員主控台**。
3. 執行下列 NuGet 命令主控台中檢視︰

        Install-Package Microsoft.HBase.Client

5. 在檔案頂端新增下列**使用**陳述式︰

        using Microsoft.HBase.Client;
        using org.apache.hadoop.hbase.rest.protobuf.generated;

6. **主要**函數取代下列動作︰

        static void Main(string[] args)
        {
            string clusterURL = "https://<yourHBaseClusterName>.azurehdinsight.net";
            string hadoopUsername= "<yourHadoopUsername>";
            string hadoopUserPassword = "<yourHadoopUserPassword>";

            string hbaseTableName = "sampleHbaseTable";

            // Create a new instance of an HBase client.
            ClusterCredentials creds = new ClusterCredentials(new Uri(clusterURL), hadoopUsername, hadoopUserPassword);
            HBaseClient hbaseClient = new HBaseClient(creds);

            // Retrieve the cluster version.
            var version = hbaseClient.GetVersion();
            Console.WriteLine("The HBase cluster version is " + version);

            // Create a new HBase table.
            TableSchema testTableSchema = new TableSchema();
            testTableSchema.name = hbaseTableName;
            testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
            testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
            hbaseClient.CreateTable(testTableSchema);

            // Insert data into the HBase table.
            string testKey = "content";
            string testValue = "the force is strong in this column";
            CellSet cellSet = new CellSet();
            CellSet.Row cellSetRow = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
            cellSet.rows.Add(cellSetRow);

            Cell value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
            cellSetRow.values.Add(value);
            hbaseClient.StoreCells(hbaseTableName, cellSet);

            // Retrieve a cell by its key.
            cellSet = hbaseClient.GetCells(hbaseTableName, testKey);
            Console.WriteLine("The data with the key '" + testKey + "' is: " + Encoding.UTF8.GetString(cellSet.rows[0].values[0].data));
            // with the previous insert, it should yield: "the force is strong in this column"

            //Scan over rows in a table. Assume the table has integer keys and you want data between keys 25 and 35.
            Scanner scanSettings = new Scanner()
            {
                batch = 10,
                startRow = BitConverter.GetBytes(25),
                endRow = BitConverter.GetBytes(35)
            };

            ScannerInformation scannerInfo = hbaseClient.CreateScanner(hbaseTableName, scanSettings);
            CellSet next = null;
            Console.WriteLine("Scan results");

            while ((next = hbaseClient.ScannerGetNext(scannerInfo)) != null)
            {
                foreach (CellSet.Row row in next.rows)
                {
                    Console.WriteLine(row.key + " : " + Encoding.UTF8.GetString(row.values[0].data));
                }
            }

            Console.WriteLine("Press ENTER to continue ...");
            Console.ReadLine();
        }

7. 設定**主要**函數中的前三個變數。
8. 按**F5**執行應用程式。

## <a name="check-cluster-status"></a>檢查叢集狀態

在 HDInsight HBase 隨附的監視叢集 Web UI。 使用網路使用者介面，您可以要求統計資料或區域的相關資訊。

若要開啟 Web 使用者介面，您必須 RDP 到叢集，然後按一下 [HMaster 資訊 Web UI 捷徑在桌面上，或在網頁瀏覽器中使用下列的 URL:

    http://zookeeper[0-2]:60010/master-status

在高可用性叢集，您會找到目前使用中 HBase 主節點裝載網站使用者介面的連結。

##<a name="delete-the-cluster"></a>刪除叢集
若要避免不一致，我們建議您先刪除叢集，停用 HBase 資料表。
[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## <a name="whats-next"></a>下一步是什麼？
在的 HDInsight 此 HBase 教學課程中，您將學會如何建立 HBase 叢集以及如何建立資料表和檢視 HBase 命令介面從這些資料表中的資料。 您也學會如何使用登錄區查詢，HBase 資料表，以及如何使用 HBase C# REST Api 來建立 HBase 資料表，並從資料表擷取資料中的資料。

如需詳細資訊，請參閱︰

- [HDInsight HBase 概觀][hdinsight-hbase-overview]。
HBase 是內建在提供的非結構化及 semistructured 資料大量隨機存取及強式一致性的 Hadoop Apache 開啟來源 NoSQL 資料庫。
- [Azure 虛擬網路上建立 HBase 叢集][hdinsight-hbase-provision-vnet]。
虛擬網路整合 HBase 叢集可以部署至相同的虛擬網路應用程式，讓應用程式可直接通訊與 HBase。
- [設定 HBase 複寫 HDInsight](hdinsight-hbase-geo-replication.md)。 瞭解如何設定 HBase 複寫跨兩個 Azure 資料中心。
- [分析 HBase HDInsight 中使用 Twitter 舉動][hbase-twitter-sentiment]。
瞭解如何使用 HBase Hadoop 叢集 HDInsight 中進行即時[舉動分析](http://en.wikipedia.org/wiki/Sentiment_analysis)資料的大。

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-bigtable.png
