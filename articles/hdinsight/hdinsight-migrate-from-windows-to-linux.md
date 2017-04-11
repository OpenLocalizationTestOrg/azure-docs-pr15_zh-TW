<properties
pageTitle="從 Windows 型 HDInsight 移轉至 Linux 型 HDInsight |Azure"
description="瞭解如何從 Windows 型 HDInsight 叢集移轉至 Linux 型 HDInsight 叢集。"
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="10/28/2016"
ms.author="larryfr"/>

#<a name="migrate-from-a-windows-based-hdinsight-cluster-to-a-linux-based-cluster"></a>從 Windows 型 HDInsight 叢集移轉至 Linux 叢集

雖然 Windows 型 HDInsight 提供容易使用 Hadoop 在雲端，您可能會發現您需要 Linux 型叢集利用的工具和技術所需的方案。 Hadoop 生態系統中的許多開發 Linux 系統和一些可能無法用於 Windows 型 HDInsight。 此外，許多書籍、 視訊及其他訓練課程教材假設您使用 Hadoop 時使用 Linux 系統。

這份文件的詳細 HDInsight 在視窗和 Linux 及如何將現有的工作負載移轉到 Linux 叢集之間的差異。

> [AZURE.NOTE] HDInsight 叢集作為 Ubuntu 長期支援 (LTS) 作業系統中叢集節點。 以及其他元件版本資訊的使用 HDInsight，可用的 Ubuntu 版本上的資訊，請參閱[HDInsight 元件版本](hdinsight-component-versioning.md)。

## <a name="migration-tasks"></a>移轉工作

移轉的一般工作流程如下所示。

![移轉工作流程圖表](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1.  閱讀以瞭解的移轉您的現有工作流程、 工作等 Linux 叢集時可能需要變更這份文件的每個區段。

2.  建立 Linux 型叢集測試/品質保證環境。 如需有關如何建立 Linux 叢集的詳細資訊，請參閱[建立 Linux 型叢集 HDInsight 中](hdinsight-hadoop-provision-linux-clusters.md)。

3.  將現有的工作、 資料來源和接收複製到新的環境。 請參閱複製資料，以測試環境區段，如需詳細資訊。

4.  執行驗證測試，以確定您的工作會在新叢集上所預期運作。

一旦確認一切運作正常，排程移轉停機時間。 在這個停機時間，請執行下列動作。

1.  備份儲存在本機上叢集節點任何暫時性資料。 例如，如果您直接在主節點上儲存的資料。

2.  刪除 Windows 型叢集。

3.  建立使用相同的預設資料存放區的使用 Windows 型叢集 Linux 型叢集。 這可讓新叢集繼續對您現有的實際執行資料的工作。

4.  您已備份任何暫時性資料匯入。

5.  開始工作/繼續處理使用新的叢集。

### <a name="copy-data-to-the-test-environment"></a>將資料複製到測試環境

有許多方法若要複製的資料和工作，不過這兩個本節所述有最簡單的方法來直接將檔案移至測試叢集。

#### <a name="hdfs-dfs-copy"></a>HDFS DFS 複本

您可以直接從儲存空間儲存為新的測試叢集使用下列步驟，將資料複製為您現有的生產叢集，使用 [Hadoop HDFS] 命令。

1. 尋找您現有的叢集儲存帳戶與預設容器資訊。 您可以使用下列 PowerShell 的 Azure 指令碼。

        $clusterName="Your existing HDInsight cluster name"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
        write-host "Default container: $clusterInfo.DefaultStorageContainer"

2. 請依照建立 Linux 型叢集 HDInsight 文件來建立新的測試環境中的步驟進行。 停止建立叢集之前，請改為選取 [**選擇性的設定**。

3. 從選用設定刀中，選取 [**連結的儲存空間帳戶**]。

4. 選取 [**新增儲存空間鍵**，並出現提示時，選取 [儲存帳戶所傳回的步驟 1 中的 PowerShell 指令碼。 按一下 [**選取**] 以關閉對話方塊，每個刀上。 最後，建立叢集。

5. 只要叢集建立之後，連線到使用**SSH。** 如果您不是使用 HDInsight SSH 的部分，請參閱下列文章。

    * [使用 Windows 的用戶端 Linux 為基礎的 HDInsight SSH](hdinsight-hadoop-linux-use-ssh-windows.md)

    * [使用來自 Linux、 Unix 和 Mac 的用戶端 Linux 為基礎的 HDInsight SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

6. 從 SSH 工作階段，使用下列命令以複製到新的預設儲存帳戶檔案連結的儲存空間帳戶。 使用容器和所傳回的步驟 1 中的 PowerShell 指令碼的帳戶資訊取代容器與帳戶。 使用資料檔案路徑取代資料的路徑。

        hdfs dfs -cp wasbs://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location

    [AZURE.NOTE] 如果目錄結構包含的資料不存在於測試環境中，您可以建立並使用下列命令。

        hdfs dfs -mkdir -p /new/path/to/create

    `-p`切換可建立路徑中所有的目錄。

#### <a name="direct-copy-between-azure-storage-blobs"></a>Azure 儲存體二進位大型物件之間的直接複本

或者，您可能會想要使用`Start-AzureStorageBlobCopy`Azure PowerShell cmdlet 來複製 blob 儲存體帳戶以外 HDInsight 之間。 如需詳細資訊，請參閱如何管理使用 Azure PowerShell 的 Azure 儲存體 Azure Blob 一節。

##<a name="client-side-technologies"></a>用戶端技術

一般而言，例如[Azure PowerShell cmdlet](../powershell-install-configure.md)、 [Azure CLI](../xplat-cli-install.md)或[Hadoop.NET SDK](https://hadoopsdk.codeplex.com/)的用戶端技術可繼續使用相同的 Linux 型叢集，為它們依賴這兩種叢集 OS 類型都是相同的 REST Api。

##<a name="server-side-technologies"></a>伺服器端技術

下表提供的 Windows 特定移轉伺服器端元件指引。

| 如果您使用這項技術... | 此動作... |
| ----- | ----- |
| **PowerShell**（伺服器端指令碼，包括叢集建立時所用的指令碼動作） | 重寫為艦隊指令碼。 指令碼動作，請參閱[的指令碼動作，以自訂 Linux 為基礎的 HDInsight](hdinsight-hadoop-customize-cluster-linux.md)和[指令碼動作開發 Linux 型 HDInsight](hdinsight-hadoop-script-actions-linux.md)。 |
| **Azure CLI**（伺服器端指令碼） | Azure CLI Linux 上時，它不會都不會預先安裝 HDInsight 叢集標頭節點。 如果您需要的伺服器端指令碼，請參閱[安裝 Azure CLI](../xplat-cli-install.md)安裝 Linux 為基礎的平台上的資訊。 |
| **.NET 元件** | .NET 不完全支援 Linux 型 HDInsight 叢集上。 在 HDInsight Linux 為基礎的大量叢集 2017/10/28 支援之後，建立 C# 大量拓撲使用 SCP.NET 架構。 其他支援的.NET 便會新增在未來的更新。 |
| **Win32 元件或其他 Windows 專用技術** | 指引取決於元件或技術。您可以尋找版本相容於 Linux，或尋找替代方案或改寫這個元件，您可能需要。 |

##<a name="cluster-creation"></a>叢集建立

本節提供差異叢集建立資訊。

### <a name="ssh-user"></a>SSH 使用者

Linux 型 HDInsight 叢集使用**安全命令介面 (SSH)**通訊協定提供叢集節點的遠端存取。 與 Windows 版遠端桌面架構的叢集，大多數 SSH 用戶端不提供圖形使用者的體驗，但改為命令列可讓您在叢集上執行命令。 某些用戶端 （例如[MobaXterm](http://mobaxterm.mobatek.net/)，) 提供遠端命令列除了圖形檔案系統瀏覽器。

叢集建立期間，您必須提供 SSH 使用者和**密碼**或**公開金鑰憑證**驗證。

我們建議使用公開金鑰憑證，因此比使用密碼更安全。 憑證驗證的運作方式產生簽署的公用/私人金鑰組，然後建立叢集時提供公開金鑰。 連接到使用 SSH 的伺服器時，在用戶端私密金鑰提供驗證連線。

如需有關如何使用 HDInsight SSH 的詳細資訊，請參閱︰

- [從 Windows 用戶端中使用 HDInsight SSH](hdinsight-hadoop-linux-use-ssh-windows.md)

- [HDInsight SSH 使用從 Linux Unix，與 OS X 的用戶端](hdinsight-hadoop-linux-use-ssh-unix.md)

### <a name="cluster-customization"></a>叢集自訂

必須在艦隊指令碼撰寫**指令碼動作**Linux 型叢集搭配使用。 而在叢集建立期間使用指令碼動作，Linux 型叢集也可以用來執行自訂設定叢集後，而且正在執行。 如需詳細資訊，請參閱[的指令碼動作，以自訂 Linux 為基礎的 HDInsight](hdinsight-hadoop-customize-cluster-linux.md)和[指令碼動作開發 Linux 型 HDInsight](hdinsight-hadoop-script-actions-linux.md)。

其他自訂功能**啟動安裝**。 針對 Windows 叢集，這可讓您使用登錄區指定用於其他文件庫的位置。 叢集建立之後，這些文件庫會自動為可供使用，而不需要使用登錄區查詢`ADD JAR`。

啟動安裝程式 Linux 型叢集不提供這項功能。 請改用[新增登錄區期間叢集建立文件庫](hdinsight-hadoop-add-hive-libraries.md)中的指令碼動作。

### <a name="virtual-networks"></a>虛擬網路

Linux 型 HDInsight 叢集需要資源管理員虛擬網路時，Windows 型 HDInsight 叢集只使用傳統的虛擬網路。 如果您有 Linux HDInsight 叢集必須連接到傳統的虛擬網路中的資源，請參閱[連線到資源管理員虛擬網路傳統虛擬網路](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)。

如需有關使用 HDInsight Azure 虛擬網路設定需求的詳細資訊，請參閱[使用虛擬網路延伸 HDInsight 功能](hdinsight-extend-hadoop-virtual-network.md)。

##<a name="management-and-monitoring"></a>管理及監視

Web Ui 可能會有使用與 Windows 型 HDInsight，例如工作歷程記錄] 或 [Yarn UI，其中許多都是透過 Ambari。 此外，Ambari 登錄區檢視提供執行使用網頁瀏覽器的登錄區查詢的方式。 使用在 https://CLUSTERNAME.azurehdinsight.net Linux 為基礎的叢集上 Ambari 網路使用者介面。

如需有關如何使用 Ambari 的詳細資訊，請參閱下列文件︰

- [Ambari 網頁](hdinsight-hadoop-manage-ambari.md)

- [Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)

### <a name="ambari-alerts"></a>Ambari 通知

Ambari 有可以告訴您與叢集潛在問題的警示系統。 不過，您也可以擷取這些透過 REST API，通知會顯示為紅色或黃色 Ambari Web 使用者介面中的項目。

> [AZURE.IMPORTANT] Ambari 通知表示該那里*可能*的問題，不該有**問題。 比方說，您可能會收到通知，就無法存取 HiveServer2，雖然您可以以正常方式存取。
>
> 許多通知實作為間隔時間為基礎的查詢，對服務，並獲得特定時間範圍內的回應。 提醒並不一定的服務已關閉，所以只會未傳回預期的時間範圍內的結果。

一般而言，您應該評估是否通知，具有已發生長時間，或鏡像先前在其上採取動作前，先報告與叢集使用者問題。

##<a name="file-system-locations"></a>檔案系統的位置

不同於 Windows 型 HDInsight 叢集配置 Linux 叢集檔案系統。 尋找常用的檔案，請使用下表。

| 我需要尋找... | 這是位於... |
| ----- | ----- |
| 設定 | `/etc`. 例如，`/etc/hadoop/conf/core-site.xml` |
| 記錄檔 | `/var/logs` |
| Hortonworks 資料平台 (HDP) | `/usr/hdp`.有兩個目錄，位於另一個則是目前 HDP 版本 (例如， `2.2.9.1-1`，) 及`current`。 `current`目錄包含符號連結檔案及版本號碼目錄中的目錄，以方便存取 HDP 的檔案版本自更新 HDP 版本時，會變更數字。 |
| hadoop streaming.jar | `/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

一般而言，如果您知道檔案的名稱，您可以使用下列命令 SSH 工作階段中的找不到檔案路徑︰

    find / -name FILENAME 2>/dev/null

您也可以使用萬用字元及檔案名稱。 例如，`find / -name *streaming*.jar 2>/dev/null`會傳回包含 「 串流 」 作為檔案名稱的一部分的單字的任何 jar 檔案的路徑。

##<a name="hive-pig-and-mapreduce"></a>登錄區豬，與 MapReduce

豬與 MapReduce 工作負載，兩者十分相似 Linux 型叢集上-主要差異是如果您使用遠端桌面連線到 windows 叢集，然後執行的工作，您會使用 SSH Linux 型叢集。

- [使用 SSH 的豬](hdinsight-hadoop-use-pig-ssh.md)

- [使用 SSH MapReduce](hdinsight-hadoop-use-mapreduce-ssh.md)

### <a name="hive"></a>登錄區

下表提供移轉您的登錄區負載指引。

| 在 Windows 型使用... | 在 Linux 型... |
| ----- | ----- |
| **登錄區編輯器** | [在 [Ambari 登錄區檢視](hdinsight-hadoop-use-hive-ambari-view.md) |
| `set hive.execution.engine=tez;`若要啟用 Tez | Tez 是預設執行引擎 Linux 型叢集，不再需要設定陳述式。 |
| CMD 檔案或叫用的登錄區工作伺服器上的指令碼 | 使用艦隊指令碼 |
| `hive`遠端桌面] 命令 | 使用[Beeline](hdinsight-hadoop-use-hive-beeline.md)或[登錄區 SSH 工作階段](hdinsight-hadoop-use-hive-ssh.md) |

##<a name="storm"></a>大量

| 在 Windows 型使用... | 在 Linux 型... |
| ----- | ----- |
| 大量儀表板 | 無法使用大量儀表板。 請參閱[在 Linux 型 HDInsight 上的部署及管理大量拓撲](hdinsight-storm-deploy-monitor-topology-linux.md)提交拓撲的方法 |
| 大量使用者介面 | 在 https://CLUSTERNAME.azurehdinsight.net/stormui 有大量使用者介面 |
| Visual Studio 建立、 部署及管理 C# 或混合式拓撲 | Visual Studio 可用來建立、 部署及管理 C# (SCP.NET) 或混合式拓撲上 Linux 為基礎的大量建立之後 2017/10/28 HDInsight 叢集上。 |

##<a name="hbase"></a>HBase

在 Linux 型叢集，是 HBase znode 上層`/hbase-unsecure`。 您必須設定此任何 Java 用戶端的設定中使用原生 HBase Java API 的應用程式。

將這個值範例用戶端，請參閱[建立 java HBase 應用程式](hdinsight-hbase-build-java-maven.md)。

##<a name="spark"></a>火花

預覽; 期間提供 Windows 叢集火花叢集不過，版本，火花僅適用於 Linux 型叢集。 沒有發行 Linux 型火花叢集從 Windows 型火花預覽叢集移轉路徑。

##<a name="known-issues"></a>已知的問題

### <a name="azure-data-factory-custom-net-activities"></a>Azure 資料工廠自訂.NET 活動

Azure 資料工廠自訂.NET 活動目前不支援 Linux 型 HDInsight 叢集上。 不過，您應該使用以下兩種方法的其中一個屬於您 ADF 的管線實作自訂活動。

-   Azure 批次集區上執行.NET 活動。 請參閱使用 Azure 批次連結服務] 區段中的 [[使用自訂活動 Azure 資料工廠管道](../data-factory/data-factory-use-custom-activities.md#AzureBatch)

-   實作做為 MapReduce 活動的活動。 如需詳細資訊，請參閱[從資料工廠叫用 MapReduce 程式](../data-factory/data-factory-map-reduce.md)。

### <a name="line-endings"></a>行尾

一般而言，在 Windows 系統行尾使用 CRLF，Linux 系統使用 LF。 如果您產生，或預期，資料與 CRLF 行尾，您可能需要修改生產者或使用者皆使用 LF 線條結尾。

比方說，使用 PowerShell 的 Azure HDInsight 查詢 Windows 型叢集會傳回 CRLF 的資料。 與 Linux 叢集相同的查詢會傳回 LF。 在許多情況下，這都無所謂資料消費者，不過應調查再移轉到 Linux 叢集。

如果您有就會直接在 Linux 叢集節點 （例如使用登錄區或 MapReduce 工作 Python 指令碼） 執行的指令碼，您應該一律使用 LF 為線條結尾。 如果您使用 CRLF 時，您可能會看到錯誤 Linux 叢集上執行指令碼時。

如果您知道自己的指令碼不包含內嵌 CR 字元的字串，您可以大量，變更行尾使用下列方法之一︰

-   **如果您有您打算將上傳至叢集的指令碼**，請使用下列 PowerShell 陳述式變更行尾從 CRLF LF 之前上傳到叢集的指令碼。

        $original_file ='c:\path\to\script.py'
        $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
        [IO.File]::WriteAllText($original_file, $text)

-   **您是否已在叢集使用儲存空間的指令碼**，您可以使用 SSH 工作階段從下列命令以 Linux 型叢集修改指令碼。

        hdfs dfs -get wasbs:///path/to/script.py oldscript.py
        tr -d '\r' < oldscript.py > script.py
        hdfs dfs -put -f script.py wasbs:///path/to/script.py

##<a name="next-steps"></a>後續步驟

-   [瞭解如何建立 Linux 型 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)

-   [連線到 Windows 用戶端上使用 SSH Linux 型叢集](hdinsight-hadoop-linux-use-ssh-windows.md)

-   [連線至 Linux 型使用叢集 SSH 從 Linux、 Unix 或 Mac 的用戶端](hdinsight-hadoop-linux-use-ssh-unix.md)

-   [管理 Linux 型叢集使用 Ambari](hdinsight-hadoop-manage-ambari.md)
