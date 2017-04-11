<properties
    pageTitle="HBase 教學課程︰ 開始使用中 Hadoop Linux 型 HBase 叢集 |Microsoft Azure"
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
    ms.topic="get-started-article"
    ms.date="10/19/2016"
    ms.author="jgao"/>



# <a name="hbase-tutorial-get-started-using-apache-hbase-with-linux-based-hadoop-in-hdinsight"></a>HBase 教學課程︰ 使用 Apache HBase Linux 為基礎的 Hadoop HDInsight 中使用快速入門 

[AZURE.INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]

瞭解如何建立 HDInsight HBase 叢集、 建立 HBase 資料表和查詢資料表使用登錄區。 一般 HBase 資訊，請參閱[HDInsight HBase 概觀][hdinsight-hbase-overview]。

這份文件中的資訊是特定 Linux 型 HDInsight 叢集。 Windows 型叢集上的資訊，請使用上方的頁面] 索引標籤選擇器中切換的內容。

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##<a name="prerequisites"></a>必要條件

此 HBase 教學課程之前，您必須具備下列項目︰

- **Azure 訂閱**。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- [安全 Shell(SSH)](hdinsight-hadoop-linux-use-ssh-unix.md)。 
- [curl](http://curl.haxx.se/download.html)。

### <a name="access-control-requirements"></a>存取控制需求

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-hbase-cluster"></a>建立 HBase 叢集

下列程序會使用 Azure 資源管理員範本來建立版本 3.4 Linux 型 HBase 叢集和相依預設 Azure 儲存體帳戶。 若要瞭解此程序與其他叢集建立方法所用的參數，請參閱[建立 Linux 型 Hadoop 叢集 HDInsight 中](hdinsight-hadoop-provision-linux-clusters.md)。

1. 按一下以開啟範本 Azure 入口網站中的下列圖像。 範本位於公用 blob 容器。 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 從**自訂部署**刀中，輸入下列資訊︰

    - **訂閱**︰ 選取 [可用於建立叢集您 Azure 訂閱]。
    - **資源群組**︰ 建立新的 Azure 資源管理群組，或使用現有的項目。
    - **位置**︰ 指定 [資源] 群組的位置。 
    - **ClusterName**︰ 輸入您要建立 HBase 叢集的名稱。
    - **叢集登入名稱和密碼**︰ 預設的登入名稱是**管理員**。
    - **SSH 使用者名稱和密碼**︰ 預設的使用者名稱是**sshuser**。  您可以將其重新命名。
     
    其他參數是選擇性的。  
    
    每個叢集有 Azure Blob 儲存體帳戶相依性。 刪除叢集後，資料會保留在儲存的帳戶。 叢集預設儲存體帳戶名稱為 「 存放區 」 附加叢集名稱。 這是在 [範本變數] 區段中的硬式編碼。
        
3. 選取 [**我同意條款與條件上述**]，然後按一下 [**購買**。 若要建立叢集花關於 20 分鐘。


>[AZURE.NOTE] 刪除 HBase 叢集之後，您可以使用相同的預設 blob 容器來建立另一個 HBase 叢集。 您建立原始叢集 HBase 資料表挑選新叢集。 若要避免不一致，我們建議您先刪除叢集，停用 HBase 資料表。

## <a name="create-tables-and-insert-data"></a>建立資料表，並插入資料

您可以使用 SSH 以連線至 HBase 叢集，然後使用 HBase 殼層建立 HBase 資料表、 插入資料與查詢的資料。 使用 SSH 資訊，請參閱[使用 SSH 與 Linux 為基礎的 Hadoop HDInsight Linux、 Unix，或 OS X 上](hdinsight-hadoop-linux-use-ssh-unix.md)並[使用 SSH Linux 為基礎的 Hadoop HDInsight 從 Windows 上使用](hdinsight-hadoop-linux-use-ssh-windows.md)。
 

大部分的人顯示資料以表格的格式︰

![HDInsight HBase 表格式資料][img-hbase-sample-data-tabular]

這是 BigTable 實作 HBase，在相同的資料看起來像︰

![HDInsight HBase bigtable 資料][img-hbase-sample-data-bigtable]

在您完成程序之後，這會使更有意義。  


**若要使用 HBase 命令介面**

1. 從 SSH，執行下列命令︰

        hbase shell

4. 建立兩欄系列 HBase:

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

    如需有關 HBase 資料表結構描述的詳細資訊，請參閱[簡介 HBase 結構描述設計][hbase-schema]。 取得更多 HBase 命令，請參閱[Apache HBase 參考指南][hbase-quick-start]。

6. 結束命令介面

        exit



**若要將連絡人 HBase 表格大量載入資料**

HBase 包含資料載入資料表有數種的方法。  如需詳細資訊，請參閱[大量載入](http://hbase.apache.org/book.html#arch.bulk.load)。


已上傳至公用 blob 容器的範例資料檔案*wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt*。  資料檔案的內容是︰

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

> [AZURE.NOTE] 此程序會使用您的最後一個程序中所建立的連絡人 HBase 資料表。

1. 從 SSH，執行下列命令以轉換資料檔案的 StoreFiles 及 Dimporttsv.bulk.output 所指定的相對路徑存放區:。  如果您位於 HBase 命令介面時，請使用 [結束] 命令，若要結束。

        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. 執行下列命令以上傳 /example/data/storeDataFileOutput HBase 資料表的資料︰

        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. 您可以開啟 HBase 命令介面，並使用掃描的命令] 清單中的表格內容。



## <a name="use-hive-to-query-hbase"></a>使用查詢 HBase 登錄區

您可以使用登錄區查詢 HBase 表格中的資料。 本節會建立對應到 HBase 表格，並使用它來查詢您的 HBase 資料表中的資料的登錄區資料表。

1. 開啟**PuTTY**，並連線到叢集。  請參閱上述程序中的指示進行。
2. 開啟 [登錄區命令介面。

       hive
3. 執行下列 HiveQL 指令碼來建立一個對應到 HBase 表格的登錄區資料表。 請確定您已建立參考之前在本教學課程中使用 HBase 命令介面，才能執行這個陳述式的範例資料表。

        CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
        TBLPROPERTIES ('hbase.table.name' = 'Contacts');

2. 執行下列動作來查詢 HBase 資料表中的資料的 HiveQL 指令碼︰

        SELECT count(*) FROM hbasecontacts;

## <a name="use-hbase-rest-apis-using-curl"></a>使用使用捲曲 HBase REST Api

> [AZURE.NOTE] 當使用 WebHCat 捲曲或任何其他的其餘部分通訊，請您必須驗證要求提供 HDInsight 叢集系統管理員的使用者名稱和密碼。 您也必須使用叢集名稱一部分的統一資源識別項 (URI) 用來將邀請傳送至伺服器。
>
> 本節中的命令，為**使用者名稱**與叢集，驗證使用者並**密碼**取代使用者帳戶的密碼。 **CLUSTERNAME**換成您叢集的名稱。
>
> REST API 受到保護透過[基本驗證](http://en.wikipedia.org/wiki/Basic_access_authentication)。 您隨時都應該要要求使用安全 HTTP (HTTPS)，以確保您的認證安全地傳送至伺服器。

1. 從命令列中，確認您可以連線到您的 HDInsight 叢集使用下列命令︰

        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status

    您應該會收到下列類似的回應︰

        {"status":"ok","version":"v1"}

    使用這個命令中的參數如下所示︰

    * **-u** -的使用者名稱與密碼驗證要求。
    * **-G** -指出這是 GET 要求。

2. 您可以使用下列命令] 清單中現有的 HBase 資料表︰

        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/hbaserest/

3. 若要建立具有兩個資料行系列的新 HBase 表格中使用下列命令︰

        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
        -v

    JSon 格式中提供的結構描述。

4. 若要插入一些資料中使用下列命令︰

        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"Row\":{\"key\":\"MTAwMA==\",\"Cell\":{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}}}" \
        -v

    您必須 base64 編碼-d 切換中指定的值。  在範例︰

    - MTAwMA = =: 1000年
    - UGVyc29uYWw6TmFtZQ = =: 個人︰ 名稱
    - Sm9obiBEb2xl: John Dole

    [false 列鍵](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single)可讓您插入多個 （批次） 的值。

5. 若要取得該資料列中使用下列命令︰

        curl -u <UserName>:<Password> \
        -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" \
        -H "Accept: application/json" \
        -v

如需有關 HBase 其餘的詳細資訊，請參閱[Apache HBase 參考指南](https://hbase.apache.org/book.html#_rest)。

## <a name="check-cluster-status"></a>檢查叢集狀態

在 HDInsight HBase 隨附的監視叢集 Web UI。 使用網路使用者介面，您可以要求統計資料或區域的相關資訊。

SSH 可以用於通道本機的要求，例如網頁的要求，HDInsight 叢集。 如果已在 HDInsight 叢集來自，要求然後傳送要求的資源。 如需詳細資訊，請參閱[使用 SSH Linux 為基礎的 Hadoop HDInsight 從 Windows 上使用](hdinsight-hadoop-linux-use-ssh-windows.md#tunnel)。

**若要建立 SSH 通道工作階段**

1. 開啟**PuTTY**。  
2. 如果您在建立程序建立您的使用者帳戶時，您就會提供 SSH 鍵，您必須執行下列步驟來選取叢集驗證時使用的私人機碼︰

    在**類別**中，展開**連線**，請展開**SSH**，然後選取**驗證**。 最後，按一下 [**瀏覽**並選取包含您的私密金鑰.ppk 檔案。

3. 在 [**類別**中，按一下 [**工作階段**]。
4. 從您的工作階段 PuTTY 螢幕基本選項中，輸入下列值︰

    - **主機名稱**︰ HDInsight 伺服器主機名稱 （或 IP 位址） 中的欄位的 SSH 地址。 SSH 地址是您叢集的名稱，然後**-ssh.azurehdinsight.net**。 例如， *mycluster ssh.azurehdinsight.net*。
    - **連接埠**︰ 22。 Ssh 主要 headnode 的連接埠是 22。  
5. 在 [左邊] 對話方塊的 [**類別**] 區段中，展開**連線**、 展開**SSH**，，然後按一下**通道**。
6. 控制 SSH 連接埠轉接表單的選項，提供下列資訊︰

    - **來源連接埠**-您想要轉寄的用戶端的連接埠。 例如，9876。
    - **動態**-可讓路由動態 SOCKS proxy。
7. 按一下 [**新增**]，以新增設定]。
8. 按一下 [**開啟**] 對話方塊的底部開啟 SSH 連線]。
9. 出現提示時，登入使用 SSH 帳戶的伺服器。 這會建立 SSH 工作階段，並啟用通道。

**若要尋找的使用 Ambari 動物園 FQDN**

1. 瀏覽至 https://<ClusterName>.azurehdinsight.net/。
2. 按兩次輸入叢集使用者帳戶認證。
3. 從左側功能表中，按一下 [**動物園管理員**]。
4. 按一下其中一個摘要清單的三個**動物園管理員伺服器**連結。
5. 複製 [**主機名稱**。 例如，zk0-CLUSTERNAME.xxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net。

**若要設定用戶端程式 (Firefox)，並查看叢集狀態**

1. 開啟 [Firefox]。
2. 按一下 [**開啟功能表**] 按鈕。
3. 按一下 [**選項**]。
4. 按一下 [**進階]**，按一下 [**網路**]，再按一下 [**設定**。
5. 選取 [**手動 proxy 設定**]。
6. 輸入下列值︰

    - **Socks host （主機)**: 本機
    - **連接埠**︰ 使用您在中 Putty SSH 通道設定相同的連接埠。  例如，9876。
    - **SOCKS v5**: （已選取）
    - **遠端 DNS**: （已選取）
7. 按一下**[確定**] 儲存變更。
8. 瀏覽至 http://&lt;FQDN 動物園管理員 >: 60010/母片的狀態。

在高可用性叢集，您會發現目前使用中 HBase 主節點裝載網站使用者介面的連結。

##<a name="delete-the-cluster"></a>刪除叢集

若要避免不一致，我們建議您先刪除叢集，停用 HBase 資料表。

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>後續步驟

在的 HDInsight 此 HBase 教學課程中，您將學會如何建立 HBase 叢集以及如何建立資料表和檢視 HBase 命令介面從這些資料表中的資料。 您也可以瞭解如何使用登錄區查詢 HBase 資料表中的資料，以及如何使用 HBase C# REST Api 來建立 HBase 資料表，並從資料表擷取資料。

若要深入瞭解，請參閱︰

- [HDInsight HBase 概觀][hdinsight-hbase-overview]: HBase 是內建在提供的非結構化及 semistructured 資料大量隨機存取及強式一致性的 Hadoop Apache 開啟來源 NoSQL 資料庫。


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
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png
