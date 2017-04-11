<properties
   pageTitle="監控和管理 HDInsight 叢集使用 Apache Ambari REST API |Microsoft Azure"
   description="瞭解如何使用 Ambari 監控和管理 Linux 型 HDInsight 叢集。 在此文件中，您將學習如何使用隨附 HDInsight 叢集 Ambari REST API。"
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
   ms.date="09/20/2016"
   ms.author="larryfr"/>

#<a name="manage-hdinsight-clusters-by-using-the-ambari-rest-api"></a>使用 Ambari REST API 來管理 HDInsight 叢集

[AZURE.INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

管理並提供容易使用的網頁 UI 和 REST API 監視 Hadoop 叢集，可簡化 Apache Ambari。 Ambari 包含在 Linux 型 HDInsight 叢集，用來監視叢集，並變更的設定。 在此文件中，您將學會使用 Ambari REST API 來執行一般工作使用捲曲的基本概念。

##<a name="prerequisites"></a>必要條件

* [cURL](http://curl.haxx.se/): cURL 是一個跨平台公用程式，可以用來從命令列使用 REST Api。 在此文件中使用與 Ambari REST API 通訊。
* [jq](https://stedolan.github.io/jq/): jq 是跨平台命令列公用程式的使用 JSON 文件。 在此文件中，將其用於剖析 Ambari REST API 所傳回的 JSON 文件。
* [Azure CLI](../xplat-cli-install.md)︰ 使用 Azure 服務的跨平台命令列公用程式。

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

##<a id="whatis"></a>什麼是 Ambari？

[Apache Ambari](http://ambari.apache.org)讓 Hadoop 管理簡單提供容易使用 web 可用於佈建、 管理及監視 Hadoop 叢集的使用者介面。 開發人員可以將這些功能整合到他們的應用程式，使用[Ambari REST Api](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)。

預設 Linux 型 HDInsight 叢集提供 Ambari。

##<a name="rest-api"></a>REST API

在 HDInsight Ambari REST API 的基底 URI 是 https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME，其中__CLUSTERNAME__是叢集的名稱。

> [AZURE.IMPORTANT] 雖然 URI (CLUSTERNAME.azurehdinsight.net) 的完整的網域名稱 (FQDN) 部分中的叢集名稱是不區分大小寫，URI 中的其他項目會區分大小寫。 例如，如果叢集名稱為 MyCluster，以下是有效的 Uri:
>
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster`
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
>
> 下列 Uri 會傳回錯誤，因為第二個項目名稱不正確的大小寫。
>
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster`
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

連線到 Ambari HDInsight 上需要 HTTPS。 驗證時連線，您必須使用的管理員帳戶的名稱 （預設為__管理員__，） 和建立叢集時所提供的密碼。

下列範例會使用捲曲，請針對 REST API GET 要求。 取代叢集的管理員密碼的__密碼__。 __CLUSTERNAME__取代叢集的名稱︰

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
    
回應是 JSON 文件的開頭類似下列資訊︰

    {
    "href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
    "Clusters" : {
        "cluster_id" : 2,
        "cluster_name" : "CLUSTERNAME",
        "health_report" : {
        "Host/stale_config" : 0,
        "Host/maintenance_state" : 0,
        "Host/host_state/HEALTHY" : 7,
        "Host/host_state/UNHEALTHY" : 0,
        "Host/host_state/HEARTBEAT_LOST" : 0,
        "Host/host_state/INIT" : 0,
        "Host/host_status/HEALTHY" : 7,
        "Host/host_status/UNHEALTHY" : 0,
        "Host/host_status/UNKNOWN" : 0,
        "Host/host_status/ALERT" : 0

這是 JSON，因為很容易使用 JSON 剖析器處理資料。 例如，下列範例會使用 jq 以僅顯示`health_report`項目。

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME" | jq '.Clusters.health_report'

##<a name="example-get-the-fqdn-of-cluster-nodes"></a>範例︰ 取得叢集節點的 FQDN

使用 HDInsight 工作時，您可能需要知道叢集節點的完整的網域名稱 (FQDN)。 您可以輕鬆擷取各種中使用下列叢集節點的 FQDN:

* __不對節點__︰`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'`
* __工作者節點__︰`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/DATANODE" | jq '.host_components[].HostRoles.host_name'`
* __動物園管理員節點__︰`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq '.host_components[].HostRoles.host_name'`

這些範例遵循相同的模式附註︰

1. 查詢我們知道元件執行這些節點。
2. 擷取`host_name`項目，其中包含這些節點的 FQDN。

`host_components`傳回的文件項目包含多個項目。 使用`.host_components[]`，然後指定的項目內路徑會迴圈每個項目，並且拉出特定的路徑中的值。 如果您只想一個值，例如第一個的 FQDN 項目，您可以將項目還原為集合，然後選取 [特定的項目︰

    jq '[.host_components[].HostRoles.host_name][0]'

從集合傳回第一個的 FQDN。

##<a name="example-get-the-default-storage-account-and-container"></a>範例︰ 取得的預設儲存帳戶及容器

當您建立 HDInsight 叢集時，您必須使用 Azure 儲存體帳戶與 blob 容器為預設儲存空間叢集。 您可以使用 Ambari 建立叢集後擷取這項資訊。 例如，如果您想要以程式設計方式資料寫入直接容器。

以下將會擷取 WASB URI 叢集預設儲存空間︰

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
> [AZURE.NOTE] 這會傳回第一個設定套用至伺服器 (`service_config_version=1`，) 也包含這項資訊。 如果您擷取值的已修改叢集建立之後，您可能需要清單設定版本，並可擷取的最新的項目。

這會傳回一個值類似下面的範例，其中__容器__是預設容器，而__ACCOUNTNAME__是 Azure 儲存體帳戶名稱︰

    wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net

然後，您可以使用[Azure CLI](../xplat-cli-install.md)使用這項資訊上傳或下載容器中的資料。

1. 取得儲存帳戶中的 [資源] 群組。 從 Ambari 擷取儲存體帳戶名稱取代__ACCOUNTNAME__ :

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    這會傳回該帳戶的資源群組名稱。
    
    > [AZURE.NOTE] 如果沒有傳回此命令，您可能需要變更 Azure CLI Azure 資源管理員模式，然後再次執行命令。 若要切換至 Azure 資源管理員模式，請使用下列命令︰
    >
    > `azure config mode arm`
    
2. 取得儲存帳戶的金鑰。 取代上述步驟中的 [資源] 群組中的__群組名稱__。 儲存體帳戶名稱取代__ACCOUNTNAME__ :

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.storageAccountKeys.key1'

    此範例會傳回該帳戶的主索引鍵。
    
3. 使用 [上傳] 命令來儲存檔案容器中︰

        azure storage blob upload -a ACCOUNTNAME -k ACCOUNTKEY -f FILEPATH --container __CONTAINER__ -b BLOBPATH
        
    取代__ACCOUNTNAME__儲存體帳戶名稱。 __ACCOUNTKEY__取代先前擷取的金鑰。 __檔案路徑__是您想要上傳、 __BLOBPATH__時容器中的路徑檔案的路徑。

    例如，如果您想要顯示在 HDInsight wasbs://example/data/filename.txt 的檔案，然後__BLOBPATH__應`example/data/filename.txt`。

##<a name="example-update-ambari-configuration"></a>範例︰ 更新 Ambari 設定

1. 取得目前的設定，Ambari 會將儲存為 「 所需的設定]:

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_configs"
        
    此範例會傳回包含目前設定 （由_標籤_值） 的 JSON 文件叢集上已安裝的元件。 下列範例是摘錄自火花叢集類型所傳回的資料。
    
        "spark-metrics-properties" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-fairscheduler" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        },
        "spark-thrift-sparkconf" : {
            "tag" : "INITIAL",
            "user" : "admin",
            "version" : 1
        }

    從清單中，您需要將複製元件的名稱 (例如，__火花\_thrift\_sparkconf__及__標籤__值。
    
2. 使用下列命令，以擷取元件和標籤的設定。 取代__火花-thrift-sparkconf__和__初始__的元件和標記，您想要擷取的設定。

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    
    捲曲擷取 JSON 文件中，然後才能建立範本 jq 用來進行修改資料。 範本然後用於新增/修改設定的值。 特別是執行下列動作︰
    
    * 建立包含字串 「 版本 」 及日期，就會儲存在__newtag__唯一值。
    * 建立新所需的設定根文件。
    * 取得內容`.items[]`陣列，並將它加入__desired_config__項目。
    * 刪除__href__、__版本__及__設定__的項目，這些項目不需要提交新的設定。
    * 新增新的__標記__項目，並將其值設為__版本 # # #__。 數字的部分根據目前的日期。 每個設定，必須具備唯一的標記。
    
    最後，資料會儲存到__newconfig.json__文件。 文件結構看起來應該類似下面的範例︰
    
        {
            "Clusters": {
                "desired_config": {
                "tag": "version1459260185774265400",
                "type": "spark-thrift-sparkconf",
                "properties": {
                    ....
                 },
                 "properties_attributes": {
                     ....
                 }
            }
        }

3. 開啟__newconfig.json__文件] 和 [修改/新增值__屬性__物件中。 下列範例會__」 spark.yarn.am.memory 」__的值從__「 1 g 」__變更為__「 3 g 」__ ，並新增新的__「 spark.kryoserializer.buffer.max 」__與__「 256 m 」__的值的項目。

        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",

    將檔案儲存在您完成之後修改。

4. 使用下列命令提交 Ambari 更新的設定。

        cat newconfig.json | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
        
    這個命令管道捲曲要求會提交到新所需的設定為叢集__newconfig.json__檔案的內容。 捲曲要求會傳回 JSON 文件。 __VersionTag__中的項目這份文件的版本，應符合您送出，並__設定__物件包含您要求的設定變更。

###<a name="example-restart-a-service-component"></a>範例︰ 重新啟動的服務元件

此時，如果您看看 Ambari 網頁 UI，火花服務就會指出需要重新啟動新的設定才會生效。 若要重新啟動服務中使用下列步驟。

1. 若要啟用火花服務進行的維修作業模式中使用下列︰

        echo '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

    這個命令來傳送至伺服器 JSON 文件 (包含在`echo`陳述式) 的開啟進行的維修作業模式。
    您可以驗證服務現在位於維護模式下使用下列的要求︰
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK" | jq .ServiceInfo.maintenance_state
        
    這會傳回的值`"ON"`。

3. 接下來，若要關閉服務使用下列動作︰

        echo '{"RequestInfo": {"context" :"Stopping the Spark service"}, "Body": {"ServiceInfo": {"state": "INSTALLED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"
        
    這個命令會傳回類似以下的回應。
    
        {
            "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
            "Requests" : {
                "id" : 29,
                "status" : "Accepted"
            }
        }
    
    `href`此 URI 所傳回的值使用叢集節點的內部 IP 位址。 若要使用其叢集外，取代叢集的 FQDN '10.0.0.18:8080 」 部分。 例如，下列命令會擷取要求的狀態。
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME/api/v1/clusters/CLUSTERNAME/requests/29" | jq .Requests.request_status
    
    如果這會傳回的值`"COMPLETED"`然後要求已完成。

4. 先前的要求完成後，請使用下列啟動服務。

        echo '{"RequestInfo": {"context" :"Restarting the Spark service"}, "Body": {"ServiceInfo": {"state": "STARTED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

    一旦服務重新啟動時，則新設定的設定。

5. 最後，請使用下列關閉進行的維修作業] 模式。

        echo '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SPARK"

##<a name="next-steps"></a>後續步驟

如 REST API 的完整參照，請參閱[Ambari API 參考 V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)。

> [AZURE.NOTE] 某些 Ambari 功能已停用，它是由 HDInsight 雲端服務; 管理例如，新增或移除叢集主機或新增新的服務。
