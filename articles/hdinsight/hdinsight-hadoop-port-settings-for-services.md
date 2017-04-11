<properties
pageTitle="使用 HDInsight 連接埠 |Azure"
description="HDInsight 上執行的 Hadoop 服務所使用的連接埠的清單。"
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
ms.date="10/03/2016"
ms.author="larryfr"/>

# <a name="ports-and-uris-used-by-hdinsight"></a>連接埠和使用 HDInsight Uri

這份文件提供 Linux 型 HDInsight 叢集上執行的 Hadoop 服務所使用的連接埠的清單。 同時也會提供連接埠用來連線到使用 SSH 叢集的資訊。

## <a name="public-ports-vs-non-public-ports"></a>公用與非公用連接埠的連接埠

Linux 型 HDInsight 叢集只提供三個連接埠公開網際網路上。22、 23 和 443。 這些用來安全地存取叢集 SSH 和安全 HTTPS 通訊協定公開服務使用。

在內部，HDInsight 實作以數種 Azure 虛擬機器 （叢集，內節點） Azure 虛擬網路上執行。 您可以在虛擬網路中，存取不會在網際網路上公開的連接埠。 例如，如果您連線至其中一個使用 SSH 標頭節點，主節點您可以從然後直接存取叢集節點上執行的服務。

> [AZURE.IMPORTANT] 當您建立 HDInsight 叢集，如果您沒有指定 Azure 虛擬網路作為設定選項時，將會建立;不過，您無法在此自動建立虛擬網路加入其他電腦 （例如其他 Azure 虛擬機器或您的用戶端開發電腦）。 

若要加入虛擬網路中其他電腦，您必須虛擬網路，請先建立，，然後指定其，建立 HDInsight 叢集時。 如需詳細資訊，請參閱[使用 Azure 虛擬網路的延伸 HDInsight 功能](hdinsight-extend-hadoop-virtual-network.md)

## <a name="public-ports"></a>公用連接埠

HDInsight 叢集中的所有節點位於 Azure 虛擬網路，並且無法直接從網際網路存取。 公用閘道器提供下列所有的所有 HDInsight 叢集類型的連接埠、 網際網路存取。

| 服務 | 連接埠 | 通訊協定 | 描述 |
| ---- | ---------- | -------- | ----------- | ----------- |
| sshd | 22 | SSH | 若要在主要 headnode sshd 連線用戶端。 請參閱[使用 SSH 與 Linux 型 HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) |
| sshd | 22 | SSH | 將用戶端連線到 sshd 上邊緣節點 （僅限 HDInsight 進階版） 中。 請參閱[開始使用 HDInsight R 伺服器](hdinsight-hadoop-r-server-get-started.md) |
| sshd | 23 | SSH | 若要在第二個 headnode sshd 連線用戶端。 請參閱[使用 SSH 與 Linux 型 HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) |
| Ambari | 443 | HTTPS | Ambari web ui。 請參閱[管理 HDInsight 使用 Ambari 網路使用者介面](hdinsight-hadoop-manage-ambari.md) |
| Ambari | 443 | HTTPS | Ambari REST API。 請參閱[管理 HDInsight 使用 Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat | 443 | HTTPS | HCatalog REST API。 請參閱[使用登錄區與捲曲](hdinsight-hadoop-use-pig-curl.md)、[使用叨著捲曲](hdinsight-hadoop-use-pig-curl.md)、[使用與捲曲 MapReduce](hdinsight-hadoop-use-mapreduce-curl.md) |
| HiveServer2 | 443 | ODBC | 連線到使用 ODBC 登錄區。 請參閱[將 Excel 連接至 Microsoft ODBC 驅動程式 HDInsight](hdinsight-connect-excel-hive-odbc-driver.md)。 |
| HiveServer2 | 443 | JDBC | 連線到使用 JDBC 登錄區。 請參閱[連線至登錄區上 HDInsight 使用登錄區 JDBC 驅動程式](hdinsight-connect-hive-jdbc-driver.md) |

以下是可用於特定叢集類型︰

| 服務 | 連接埠 | 通訊協定 |叢集類型 | 描述 |
| ------------ | ---- |  ----------- | --- | ----------- |
| Stargate | 443 | HTTPS | HBase | HBase REST API。 請參閱[開始使用 HBase](hdinsight-hbase-tutorial-get-started-linux.md) |
| 晚總 | 443 | HTTPS |  火花 | 火花 REST API。 請參閱[使用遠端晚總提交火花工作](hdinsight-apache-spark-livy-rest-interface.md) |
| 大量 | 443 | HTTPS | 大量 | 大量 web ui。 請參閱[部署及管理大量拓撲上 HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

### <a name="authentication"></a>驗證

在網際網路上公開的所有服務必須經過都驗證︰

| 連接埠 | 認證 |
| ---- | ----------- |
| 22 或 23 | 在叢集建立期間所指定的 SSH 使用者認證 |
| 443 | 登入名稱 (預設︰ 管理員，) 和密碼設定叢集建立期間 |

## <a name="non-public-ports"></a>非公用連接埠

> [AZURE.NOTE] 某些服務只適用於特定叢集類型。 例如，HBase 僅適用於 HBase 叢集類型。

### <a name="hdfs-ports"></a>HDFS 連接埠

| 服務 | 節點 | 連接埠 | 通訊協定 | 描述 |
| ------- | ------- | ---- | -------- | ----------- | 
| NameNode 網頁 UI | 不對節點 | 30070 | HTTPS | 網頁檢視目前狀態的使用者介面 |
| NameNode 中繼資料服務 | 標頭節點 | 8020 | IPC | 檔案系統的中繼資料 
| DataNode | 所有的工作者節點 | 30075 | HTTPS | 若要檢視狀態、 記錄等網頁 UI。 |
| DataNode | 所有的工作者節點 | 30010 | &nbsp; | 資料傳輸 |
| DataNode | 所有的工作者節點 | 30020 | IPC | 中繼資料作業 |
| 第二個 NameNode | 不對節點 | 50090 | HTTP | 重點複習 NameNode 中繼資料 |

### <a name="yarn-ports"></a>YARN 連接埠

| 服務 | 節點 | 連接埠 | 通訊協定 | 描述 |
| ------- | ------- | ---- | -------- | ----------- |
| 資源管理員網頁 UI | 不對節點 | 8088 | HTTP | 網頁 UI 如資源管理員 |
| 資源管理員網頁 UI | 不對節點 | 8090 | HTTPS | 網頁 UI 如資源管理員 |
| 資源管理員管理員介面 | 標頭節點 | 8141 | IPC | 應用程式送出 (登錄區，群組伺服器] 的豬，等等。) |
| 資源管理員排程器 | 標頭節點 | 8030 | HTTP | 管理介面 |
| 資源管理員應用程式介面 | 標頭節點 | 8050 | HTTP |在應用程式管理員介面的地址 |
| NodeManager | 所有的工作者節點 | 30050 | &nbsp; | 容器管理員的地址 |
| NodeManager 網頁 UI | 所有的工作者節點 | 30060 | HTTP | 資源管理員介面 |
| 時間表地址 | 不對節點 | 10200 | RPC | 時間表服務 RPC 服務。 |
| 時間表網頁 UI | 不對節點 | 8181 | HTTP | 時間表服務 web UI |

### <a name="hive-ports"></a>登錄區連接埠

| 服務 | 節點 | 連接埠 | 通訊協定 | 描述 |
| ------- | ------- | ---- | -------- | ----------- |
| HiveServer2 | 不對節點 | 10001 | Thrift | 以程式設計方式連線到登錄區 (Thrift/JDBC) 服務 |
| HiveServer | 不對節點 | 10000 | Thrift | 以程式設計方式連線到登錄區 (Thrift/JDBC) 服務 |
| 登錄區 Metastore | 不對節點 | 9083 | Thrift | 以程式設計方式連線到登錄區中繼資料 (Thrift/JDBC) 服務 |

### <a name="webhcat-ports"></a>WebHCat 連接埠

| 服務 | 節點 | 連接埠 | 通訊協定 | 描述 |
| ------- | ------- | ---- | -------- | ----------- |
| WebHCat 伺服器 | 不對節點 | 30111 | HTTP | 掌握 HCatalog 和其他 Hadoop 服務的網路 API |

### <a name="mapreduce-ports"></a>MapReduce 連接埠

| 服務 | 節點 | 連接埠 | 通訊協定 | 描述 |
| ------- | ------- | ---- | -------- | ----------- |
| JobHistory | 不對節點 | 19888 | HTTP | MapReduce JobHistory 網頁 UI |
| JobHistory | 不對節點 | 10020 | &nbsp; | MapReduce JobHistory 伺服器 |
| ShuffleHandler | &nbsp; | 13562 | &nbsp; | 傳輸中繼地圖輸出至要求 Reducers |

### <a name="oozie"></a>Oozie

| 服務 | 節點 | 連接埠 | 通訊協定 | 描述 |
| ------- | ------- | ---- | -------- | ----------- |
| Oozie 伺服器 | 不對節點 | 11000 | HTTP | Oozie 服務的 URL |
| Oozie 伺服器 | 不對節點 | 11001 | HTTP | Oozie 管理員的連接埠 |

### <a name="ambari-metrics"></a>Ambari 指標

| 服務 | 節點 | 連接埠 | 通訊協定 | 描述 |
| ------- | ------- | ---- | -------- | ----------- |
| 時間表 （應用程式記錄） | 不對節點 | 6188 | HTTP | 時間表服務 web UI |
| 時間表 （應用程式記錄） | 不對節點 | 30200 | RPC | 時間表服務 web UI |

### <a name="hbase-ports"></a>HBase 連接埠

| 服務 | 節點 | 連接埠 | 通訊協定 | 描述 |
| ------- | ------- | ---- | -------- | ----------- |
| HMaster | 不對節點 | 16000 | &nbsp; | &nbsp; |
| HMaster 資訊網路使用者介面 | 不對節點 | 16010 | HTTP | HBase 母片網頁 UI 的連接埠 |
| 地區伺服器 | 所有的工作者節點 | 16020 | &nbsp; | &nbsp; |
| &nbsp; | &nbsp; | 2181 | &nbsp; | 用戶端連線到動物園管理員使用連接埠 |

### <a name="kafka-ports"></a>Kafka 連接埠

| 服務 | 節點 | 連接埠 | 通訊協定 | 描述 |
| ------- | ------- | ---- | -------- | ----------- |
| 代理人  | 工作者節點 | 9092 | [Kafka 電線通訊協定](http://kafka.apache.org/protocol.html) | 用於用戶端連線 |
| &nbsp; | 動物園管理員節點 | 2181 | &nbsp; | 用戶端連線到動物園管理員使用連接埠 |
