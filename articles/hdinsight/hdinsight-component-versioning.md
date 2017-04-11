<properties
    pageTitle="什麼是不同的元件使用 HDInsight 叢集？ |Microsoft Azure"
    description="HDInsight 支援多個可部署 Hadoop 叢集元件和版本。 請參閱支援 Hadoop 和 HortonWorks 資料平台 (HDP) 發佈版本。"
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    authors="mumian"
    tags="azure-portal"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="jgao"/>


# <a name="what-are-the-different-hadoop-components-available-with-hdinsight"></a>有哪些可用的 HDInsight 的不同 Hadoop 元件？

瞭解 HDInsight，以及隨附 HDInsight 不同 hadoop 元件版本所提供的其他服務等級。

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight 標準和 HDInsight 進階版

Azure HDInsight 提供兩種類型的大型資料雲端方案︰**標準**和**進階版**。 ] 區段下的表列出可用**只屬於進階版**的功能。 不明確地提出以下表格中的功能可在標準的一部分。

>[AZURE.NOTE] 提供 HDInsight 進階版且目前在預覽中僅適用於 Linux 叢集。

| HDInsight 進階功能 | 描述 |
|--------------|---------------|
| 加入網域的 HDInsight 叢集       | 加入 HDInsight 叢集企業層級安全性的 Azure Active Directory (AAD) 網域。 您現在可以從您可以透過登入 HDInsight 叢集的 Azure Active Directory 驗證的企業設定員工的清單。 企業系統管理員也可以設定基礎的角色存取控制登錄區安全性使用[Apache Ranger](http://hortonworks.com/apache/ranger/)，因此限制存取至只需要與方法很類似的資料。 最後，管理員可以稽核資料存取的員工，以及完成] 以存取控制項原則，因此可達到高度的管理其企業資源的任何變更。 如需詳細資訊，請參閱[設定網域的 HDInsight 叢集](hdinsight-domain-joined-configure)。

### <a name="cluster-types-supported-for-premium"></a>支援的進階版的叢集類型

下表列出的 HDInsight 叢集類型及進階版支援矩陣。

| 叢集類型 | 標準  | 進階版 |
|--------------|---------------|--------------|
| Hadoop       | [是]           | 是 (僅限 HDInsight 3.5)         |
| 火花        | [是]           | 無          |
| HBase        | [是]           | 無           |
| 大量        | [是]           | 無           |
| 互動式登錄區 （預覽版本） | [是] | 無 |
| R Server （預覽版本） | [是] | 無 |

更多叢集類型會包含在 HDInsight 進階版時，就會更新此表格。

### <a name="pricing-and-sla"></a>定價和 SLA

定價和 SLA HDInsight 進階版的資訊，請參閱[HDInsight 價格](https://azure.microsoft.com/pricing/details/hdinsight/)。

## <a name="hadoop-components-available-with-different-hdinsight-versions"></a>Hadoop 元件提供不同的 HDInsight 版本

Azure HDInsight 支援多個可隨時部署 Hadoop 叢集版本。 每個版本的選擇會建立 Hortonworks 資料平台 (HDP) 分配的特定版本及一組中的通訊群組所包含的元件。 下表中，是分項 HDInsight 叢集版本相關聯的元件版本。 請注意，Azure HDInsight 所使用的預設叢集版本是目前 3.4，，迄 09/14/2016年根據 HDP 2.4。

> [AZURE.NOTE] 預設版本，從服務可能會變更不另行通知。 我們建議您指定的版本，當您建立使用.NET SDK/Azure PowerShell 和 Azure CLI 叢集，如果您有版本相依性。 

元件|HDInsight 3.5 版 | HDInsight 版本 3.4 （預設值） | HDInsight 版本 3.3 | HDInsight 版本 3.2 捨位 |HDInsight 版本 3.1 |HDInsight 3.0 版|
---|---|---|---|---|---|---
Hortonworks 資料平台|2.5|2.4|2.3|2.2|2.1.7|2.0|
Apache Hadoop 與 YARN|2.7.3|2.7.1|2.7.1|2.6.0|2.4.0|2.2.0|
Apache Tez|0.7.0|0.7.0|0.7.0 | 0.5.2|0.4.0||
Apache 的豬|0.16.0|0.15.0|0.15.0|0.14.0|0.12.1|0.12.0|
Apache 登錄區與 HCatalog|1.2.1.2.5|1.2.1|1.2.1|0.14.0|0.13.1|0.12.0|
Apache HBase |1.1.2|1.1.2|1.1.1|0.98.4|0.98.0||
Apache Sqoop|1.4.6|1.4.6|1.4.6|1.4.5|1.4.4|1.4.4|1.4.3
Apache Oozie|4.2.0|4.2.0|4.2.0|4.1.0|4.0.0|4.0.0|
Apache 動物園管理員|3.4.6|3.4.6|3.4.6|3.4.6|3.4.5|3.4.5|
Apache 大量|1.0.1|0.10.0|0.10.0|0.9.3|0.9.1||
Apache Mahout|0.9.0+|0.9.0+|0.9.0+|0.9.0|0.9.0||
Apache 鳳凰|4.7.0|4.4.0|4.4.0|4.2.0|4.0.0.2.1.7.0-2162||
Apache 火花|1.6.2 + 2.0 (僅限 Linux)|1.6.0 (僅限 Linux)|1.5.2 （Linux 僅/Experimental 組建）|1.3.1 （僅限 Windows）|||

**取得目前的元件版本資訊**

HDInsight 叢集版本相關聯的元件版本可能會在未來變更更新至 HDInsight。 若要決定可用的元件，並確認哪些版本時使用的是叢集的其中一個方法是使用 Ambari REST API。 [ **GetComponentInformation** ] 命令可用來擷取服務元件的相關資訊。 如需詳細資訊，請參閱[Ambari 文件][ambari-docs]。 若要取得這項資訊的另一個方法是使用遠端桌面登入叢集，並檢查的內容 」 C:\apps\dist\"直接目錄。


**版本資訊**

HDInsight 的最新版本上的其他版本資訊，請參閱[HDInsight 版本資訊](hdinsight-release-notes.md)。


## <a name="supported-hdinsight-versions"></a>支援的 HDInsight 版本
下表列出目前可用的 HDInsight 版本與對應的 Hortonworks 資料平台版本使用，其發行日期。 已知，也會提供其支援的到期和取代日期。 請注意下列事項︰

* 具有兩個標頭節點的高度可用叢集部署預設 HDInsight 2.1 和上方。 這些都無法使用的 HDInsight 1.6 叢集。
* 一旦支援已過期特定版本，它可能無法使用透過 Azure 入口網站。 下表會指出 Azure 傳統入口網站上有可用的版本。 叢集版本會繼續會提供使用`Version`中的 Windows PowerShell[新增 AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx)指令和.NET SDK，直到其取代日期參數。

HDInsight 版本|HDP 版本|VM OS|可用性|發行日期|Azure 入口網站上使用|支援到期日|取代日期
---|---|---|---|---|---|---|---
HDI 3.5 | HDP 2.5| Ubuntu 16 | [是] | 9/30/2016 | [是] | 
HDI 3.4|HDP 2.4|Ubuntu 14.0.4 LTS|[是]|03/29 2016|[是]|12/29/2016|1/9/2018|
3.3 HDI|HDP 2.3|Ubuntu 14.0.4 LTS 或 Windows Server 2012R2|[是]|2015 年 12/02|[是]|06/27/2016|2017/07/31
HDI 3.2 捨位|HDP 2.2|Ubuntu 12.04 LTS 或 Windows Server 2012R2|[是]|2015 年 2/18|[是]|3/1/2016|2017/04/01
HDI 3.1|HDP 2.1|Windows Server 2012R2|[是]|2014/6/24|無|2015 年 05/18|06/30/2016
HDI 3.0|HDP 2.0|Windows Server 2012R2|[是]|2014/02/11|無|2014/09/17|06/30/2015
HDI 2.1|HDP 1.3|Windows Server 2012R2|[是]|2013/10/28|無|05/12/2014|05/31/2015
HDI 1.6|HDP 1.1 （英文)||無|2013/10/28|無|2014/04/26|05/31/2015

**非預設叢集的部署**

### <a name="the-service-level-agreement-for-hdinsight-cluster-versions"></a>服務等級協定，HDInsight 叢集版本

「 支援視窗] 以定義 SLA。 在支援視窗指的是一段時間的 HDInsight 叢集版本支援的 Microsoft 客戶服務及支援。 HDInsight 叢集超出 [支援] 視窗中，如果其版本具有過去的目前日期的**支援到期日**。 上述表格中找支援 HDInsight 叢集版本的清單。 支援的到期日指定的 HDInsight 版本 X （使用較新版的 X + 1 後） 的計算方式為較晚的︰  

- 公式 1︰ 新增 180 天 X 發行日期 HDInsight 叢集版本。
- 公式 2︰ 新增 90 天日期 HDInsight 叢集版本 X + 1 （X 之後的後續版本） 可在入口網站。

**取代日期**是無法在 HDInsight 建立叢集版本之後的日期。

> [AZURE.NOTE] Azure 來賓 OS 系列 4，其使用 64 位元版本的 Windows Server 2012 R2，以及支援.NET Framework 4.0、 4.5、 4.5.1 及 4.5.2 執行 Windows 型 HDInsight 叢集 （包括 2.1、 3.0、 3.1、 3.2 捨位及 3.3 版）。

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Hortonworks 版本 HDInsight 版本相關聯的資訊##

* HDInsight 叢集版本 3.4 使用 Hadoop 分配[Hortonworks 資料平台 2.4](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html)為基礎。 這是**預設**Hadoop 叢集時使用入口網站所建立。



* HDInsight 叢集版本 3.3 使用 Hadoop 分配[Hortonworks 資料平台 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html)為基礎。
    * Apache 大量版本資訊可[在這裡](https://storm.apache.org/2015/11/05/storm0100-released.html)。
    * Apache 登錄區版本資訊可[在這裡](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843)。

* 3.2 捨位使用 Hadoop 分配[Hortonworks 資料平台 2.2]為基礎的 HDInsight 叢集版本[hdp-2-2]。  

    * 特定 Apache 元件-[登錄區 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450)[豬 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954)、 [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810)、[鳳凰 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581)、 [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180)、 [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181)、 [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197)、[常見](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179)、 [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742)、 [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486)、[大量 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112)、 [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620)版本資訊。


* HDInsight 叢集版本 3.1 使用 Hadoop 分配為基礎[的資料平台 Hortonworks 2.1.7][hdp-2-1-7]。建立前 11/7/2014年已根據[Hortonworks 資料平台 2.1.1]HDInsight 3.1 叢集[hdp-2-1-1]。

* HDInsight 叢集版本 3.0 使用 Hadoop 分配為基礎[Hortonworks 資料平台 2.0][hdp-2-0-8]。

* 2.1 使用 Hadoop 分配[Hortonworks 資料平台 1.3]為基礎的 HDInsight 叢集版本[hdp-1-3-0]。

* 1.6 使用 Hadoop 分配[Hortonworks 資料平台 1.1 （英文）]為基礎的 HDInsight 叢集版本[hdp-1-1-0]。


[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/
