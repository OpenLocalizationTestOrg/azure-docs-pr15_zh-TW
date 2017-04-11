<properties
   pageTitle="秘訣 Linux 型 HDInsight 上使用 Hadoop |Microsoft Azure"
   description="取得在 Azure 雲端中執行熟悉 Linux 環境中使用 Linux HDInsight (Hadoop) 叢集實作秘訣。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/13/2016"
   ms.author="larryfr"/>

# <a name="information-about-using-hdinsight-on-linux"></a>使用 Linux HDInsight 的相關資訊

Linux 型 Azure HDInsight 叢集提供 Hadoop Azure 雲端中執行熟悉 Linux 環境。 針對大部分項目，它應該會在任何其他 Hadoop 上 Linux 安裝運作。 您應該知道的特定差異呼叫此文件。

##<a name="prerequisites"></a>必要條件

許多這份文件中的步驟，使用下列公用程式，可能需要安裝在您的系統。

* [cURL](https://curl.haxx.se/) -用來與 web 服務
* [jq](https://stedolan.github.io/jq/) -用來剖析 JSON 文件
* [Azure CLI](../xplat-cli-install.md) -用來從遠端管理 Azure 服務

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)] 

## <a name="domain-names"></a>網域名稱

從網際網路連線到叢集時要使用的完整的網域名稱 (FQDN) 是**&lt;clustername >。 azurehdinsight.net**或 （如果只 SSH) ** &lt;clustername-ssh >。 azurehdinsight.net**。

在內部，叢集中的每個節點有時叢集設定指派的名稱。 若要尋找叢集名稱，您可以造訪 [__主辦城市__] 頁面上 Ambari 網路使用者介面，或使用下列從 Ambari REST API 傳回主機清單︰

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

取代密碼的管理員帳戶及__CLUSTERNAME__叢集的名稱與__密碼__。 這會傳回包含叢集，主機清單的 JSON 文件，然後 jq 拉`host_name`叢集中每個主機的項目值。

如果您需要先尋找特定的服務節點的名稱，您可以查詢 Ambari 元件。 例如，主機 HDFS 名稱節點，請使用下列步驟。

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

這會傳回描述服務，JSON 文件，然後 jq 拉只有`host_name`主機的值。

## <a name="remote-access-to-services"></a>遠端存取服務

* **Ambari (web)** -https://&lt;clustername >。 azurehdinsight.net

    驗證使用叢集系統管理員使用者和密碼，然後登入 Ambari。 這也會使用叢集系統管理員使用者和密碼。

    驗證是純文字-使用 HTTPS 來協助確保安全連線。

    > [AZURE.IMPORTANT] 時在網際網路上直接存取 Ambari 叢集，某些功能需要存取節點叢集使用內部網域名稱。 因為這是內部網域名稱，而不是公用，您會收到 「 找不到伺服器 」 錯誤時嘗試透過網際網路存取某些功能。
    >
    > 若要使用 Ambari 網頁 UI 的完整功能，請使用叢集主節點 proxy web 流量導向 SSH 通道。 請參閱[使用 SSH 通道存取 Ambari 網頁 UI、 ResourceManager、 JobHistory、 NameNode、 Oozie 及使用者介面的其他網頁](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari （剩餘）** -https://&lt;clustername >.azurehdinsight.net/ambari

    > [AZURE.NOTE] 使用叢集系統管理員使用者和密碼進行驗證。
    >
    > 驗證是純文字-使用 HTTPS 來協助確保安全連線。

* **WebHCat (Templeton)** -https://&lt;clustername >.azurehdinsight.net/templeton

    > [AZURE.NOTE] 使用叢集系統管理員使用者和密碼進行驗證。
    >
    > 驗證是純文字-使用 HTTPS 來協助確保安全連線。

* **SSH** - &lt;clustername >-ssh.azurehdinsight.net 22 或 23 的連接埠。 連接埠 22 用來連線到主要 headnode，，而 23 用來連線到次要。 標頭節點的詳細資訊，請參閱[可用性及可靠性 Hadoop 叢集 HDInsight 中](hdinsight-high-availability-linux.md)。

    > [AZURE.NOTE] 您只能從用戶端電腦到 SSH 存取叢集標頭節點。 連線之後，您就可以使用 headnode SSH，然後存取工作者節點。

## <a name="file-locations"></a>檔案位置

Hadoop 相關的檔案位於叢集節點在`/usr/hdp`。 此目錄包含下列子目錄︰

* __2.2.4.9-1__︰ 此目錄命名 Hortonworks 資料平台 HDInsight，因此叢集上的數字可能會不同於列於此處的項目使用的版本。
* __目前__︰ 此目錄包含連結至__2.2.4.9-1__目錄下的目錄，並且存在，讓沒有輸入的版本號碼 （可能會變更，），每當您想要存取檔案。

範例資料以及 JAR 檔案，請參閱在 Hadoop 分散式檔案系統 (HDFS) 或 Azure Blob 儲存體在 「 / 範例 '或' wasbs: / / 範例 」。

## <a name="hdfs-azure-blob-storage-and-storage-best-practices"></a>HDFS、 Azure Blob 儲存體及儲存空間的最佳作法

在大部分的 Hadoop 散佈 HDFS 已叢集電腦上儲存本機備份。 雖然這是有效的它可能會高雲端方案，您會每小時或分鐘計算資源的。

HDInsight 使用 Azure Blob 儲存體為預設儲存，可提供下列優點︰

* 長期儲存

* 從外部的服務，例如網站、 檔案上傳/下載公用程式、 各種不同的語言 Sdk 和網頁瀏覽器的協助工具

由於 HDInsight 的預設儲存區，，您通常不需要執行任何動作來使用。 例如，下列命令會列出儲存在 Azure Blob 儲存體的 [ **/example/data** ] 資料夾中的檔案︰

    hdfs dfs -ls /example/data

有些命令可能會要求您指定您使用的 Blob 儲存體。 這些項目，您可以加上字首包含命令**wasb: / /**，或**wasbs: / /**。

HDInsight 也可讓您建立多個 Blob 儲存體帳戶與叢集之間的關聯。 若要存取資料在非預設 Blob 儲存體帳戶，您可以使用格式**wasbs: / /&lt;container-name>@&lt;帳戶名稱 >.blob.core.windows.net/**。 例如，下列將會列出指定的容器和 Blob 儲存體帳戶**/example/data**目錄的內容︰

    hdfs dfs -ls wasbs://mycontainer@mystorage.blob.core.windows.net/example/data

### <a name="what-blob-storage-is-the-cluster-using"></a>哪些 Blob 儲存體叢集使用為何？

叢集建立期間，您選取 [使用現有的 Azure 儲存體帳戶和容器，或建立新的範本。 然後，您可能忘記您的意見。 您可以使用 Ambari REST API 找到的預設儲存帳戶及容器。

1. 使用下列命令來擷取 HDFS 設定資訊使用捲曲，並使用[jq](https://stedolan.github.io/jq/)篩選︰

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
    > [AZURE.NOTE] 這會傳回第一個設定套用至伺服器 (`service_config_version=1`，) 這會包含此資訊。 如果您所擷取值的已修改叢集建立之後，您可能需要清單設定版本，並可擷取的最新的項目。

    這會傳回一個值類似下列，其中__容器__是預設容器，而__ACCOUNTNAME__是 Azure 儲存體帳戶名稱︰

        wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net

1. 取得儲存帳戶中的 [資源] 群組，請使用[Azure CLI](../xplat-cli-install.md)。 在以下命令，將__ACCOUNTNAME__更換從 Ambari 擷取儲存體帳戶名稱︰

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    這會傳回該帳戶的資源群組名稱。
    
    > [AZURE.NOTE] 如果沒有傳回此命令，您可能需要變更 Azure CLI Azure 資源管理員模式，然後再次執行命令。 若要切換至 Azure 資源管理員模式，請使用下列命令。
    >
    > `azure config mode arm`
    
2. 取得儲存帳戶的金鑰。 取代上述步驟中的 [資源] 群組中的__群組名稱__。 儲存體帳戶名稱取代__ACCOUNTNAME__ :

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.[0].value'

    這會傳回該帳戶的主索引鍵。

您也可以找到使用 Azure 入口網站的儲存空間資訊︰

1. 在[Azure 入口網站](https://portal.azure.com/)中，選取 [HDInsight 叢集]。

2. 從 [__基本資訊__] 區段中，選取 [__所有設定__]。

3. 從 [__設定__] 中，選取 [ __Azure 儲存金鑰__]。

4. 從__Azure 儲存金鑰__，選取其中一個列出的儲存空間帳戶。 這會顯示儲存帳戶的相關資訊。

5. 選取 [重要] 圖示。 這會顯示此儲存帳戶的按鍵。

### <a name="how-do-i-access-blob-storage"></a>我要如何存取 Blob 儲存體？

以外的 Hadoop 命令，透過有各種不同的存取二進位大型物件的方法︰

* [For Mac、 Linux 和 Windows azure CLI](../xplat-cli-install.md)︰ 使用 Azure 命令的命令列介面。 安裝後，使用`azure storage`使用儲存空間，說明的命令或`azure blob`blob 特定的命令。

* [blobxfer.py](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): python 指令碼的二進位大型物件 Azure 儲存體中的工作。

* 各種不同的 Sdk:

    * [Java](https://github.com/Azure/azure-sdk-for-java)

    * [Node.js](https://github.com/Azure/azure-sdk-for-node)

    * [PHP](https://github.com/Azure/azure-sdk-for-php)

    * [Python](https://github.com/Azure/azure-sdk-for-python)

    * [[注音標示](https://github.com/Azure/azure-sdk-for-ruby)

    * [.NET](https://github.com/Azure/azure-sdk-for-net)

* [儲存 REST API](https://msdn.microsoft.com/library/azure/dd135733.aspx)

##<a name="scaling"></a>縮放比例叢集

叢集縮放功能可讓您變更資料在 Azure HDInsight 中執行，而不需要刪除和重新建立 [叢集叢集使用的節點數目。

您可以執行其他工作時的縮放比例作業，或在叢集上執行的程序。

不同的叢集類型是受影響的縮放比例，如下所示︰

* __Hadoop__︰ 當規模集中的節點數目，部分叢集服務會重新啟動。 這可能會導致工作使用] 或 [擱置的 [縮放比例] 作業完成時失敗。 您可以重新送出工作，一旦完成作業。

* __HBase__: [縮放比例] 作業完成後數分鐘內自動對稱地區的伺服器。 若要手動平衡地區的伺服器，請使用下列步驟︰

    1. 連線到使用 SSH HDInsight 叢集。 使用 HDInsight SSH 的詳細資訊，請參閱下列文件︰

        * [使用 Linux、 Unix，與在 Mac OS X 的 HDInsight SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

        * [從 Windows HDInsight 搭配使用 SSH](hdinsight-hadoop-linux-use-ssh-windows.md)

    1. 若要開始 HBase 命令介面中使用下列︰

            hbase shell

    2. HBase 命令介面具有載入之後，請使用下列要手動平衡地區伺服器︰

            balancer

* __大量__︰ 執行縮放比例的作業後，您應該重新平衡任何執行大量拓撲。 這個選項可讓拓撲重新調整平行根據新的叢集的節點數目的 [設定]。 若要重新平衡執行拓撲，使用下列選項之一︰

    * __SSH__︰ 連線到伺服器，並重新平衡拓撲使用下列命令︰

            storm rebalance TOPOLOGYNAME

        您也可以指定參數會覆寫原始提供拓撲平行提示。 例如，`storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10`會重新設定 5 背景工作處理程序，為藍色 spout 元件，3 執行程式黃色螺栓元件 10 執行程式拓撲。

    * __大量 UI__︰ 若要重新平衡使用大量使用者介面的拓撲使用下列步驟。

        1. __Https://CLUSTERNAME.azurehdinsight.net/stormui__您網頁瀏覽器中開啟，其中 CLUSTERNAME 是大量叢集的名稱。 如果出現提示，請輸入 HDInsight 叢集系統管理員 （系統） 名稱和密碼時建立叢集所指定。

        3. 選取您想要重新平衡，拓撲，然後選取 [__重新平衡__] 按鈕。 執行 rebalance 作業之前，請輸入延遲。

特定縮放比例 HDInsight 叢集的詳細資訊，請參閱︰

* [使用 [Azure 入口網站管理 Hadoop 叢集 HDInsight 中](hdinsight-administer-use-portal-linux.md#scaling)

* [使用 PowerShell 的 Azure 管理 Hadoop 叢集 HDinsight 中](hdinsight-administer-use-command-line.md#scaling)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>如何安裝色調 （或其他 Hadoop 元件）？

HDInsight 是受管理的服務，這表示叢集節點可能會損毀和 Azure 來自動 reprovisioned，如果偵測到問題。 因此，建議您不要若要手動安裝直接在叢集節點上的 [項目。 當您需要安裝下列時，不過，使用[HDInsight 指令碼動作](hdinsight-hadoop-customize-cluster.md)︰

* 服務或網站，例如火花或色調。
* 需要在多個叢集節點的設定變更元件。 例如，所需的環境變數，建立的記錄目錄或建立設定檔。

指令碼動作艦隊的指令碼執行期間叢集佈建，而可以用來安裝和設定叢集上的其他元件。 範例指令碼提供安裝下列元件︰

* [色調](hdinsight-hadoop-hue-linux.md)
* [Giraph](hdinsight-hadoop-giraph-install-linux.md)
* [Solr](hdinsight-hadoop-solr-install-linux.md)

如需開發您自己的指令碼的動作，請參閱[使用 HDInsight 的指令碼動作開發](hdinsight-hadoop-script-actions-linux.md)。

###<a name="jar-files"></a>Jar 檔案

獨立的 jar 檔案會包含用來做為 MapReduce 工作，或從函數中所提供的一些 Hadoop 技術內的豬或登錄區。 這些可以使用指令碼動作安裝，而他們通常不需要任何設定可以只會佈建之後上傳至叢集，並直接使用。 如果您想要確定元件便會存在著因叢集的 makle，您可以儲存 WASB jar 檔案。

例如，如果您想要使用[DataFu](http://datafu.incubator.apache.org/)的最新版本，您可以對其進行下載糖包含專案及上傳至 HDInsight 叢集。 如何使用它從豬或登錄區，然後依照 DataFu 文件。

> [AZURE.IMPORTANT] 獨立 jar 檔案的部分元件 HDInsight，會提供，但不是在路徑。 如果您要尋找特定的元件，您可以使用追蹤叢集上搜尋︰
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> 這會傳回任何相符的 jar 檔案的路徑。

如果叢集已提供版本的元件為獨立 jar 檔案，但您想要使用不同版本，您可以上傳到叢集的元件的新版本，並嘗試在您的工作中使用。

> [AZURE.WARNING] 完全支援元件提供 HDInsight 叢集，以及 Microsoft 支援服務會協助找出並解決這些元件的相關問題。
>
> 自訂元件收到合理的支援，以協助您進一步疑難排解問題。 這可能會導致解決問題，或詢問要加入找到該技術深專業知識的位置開啟來源技術可用的頻道。 例如，有許多都可以使用，這樣的社群網站︰[適用於 HDInsight MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight)、 [http://stackoverflow.com](http://stackoverflow.com)。 也 Apache 專案有[http://apache.org](http://apache.org)，在專案網站，例如︰ [Hadoop](http://hadoop.apache.org/)、[火花](http://spark.apache.org/)。

## <a name="next-steps"></a>後續步驟

* [從 Windows 型 HDInsight 移轉到 Linux 型](hdinsight-migrate-from-windows-to-linux.md)
* [使用 HDInsight 的登錄區](hdinsight-use-hive.md)
* [使用 HDInsight 的豬](hdinsight-use-pig.md)
* [使用 HDInsight MapReduce 工作](hdinsight-use-mapreduce.md)
