<properties 
   pageTitle="使用 Apache 鳳凰和松鼠中 HDInsight |Microsoft Azure" 
   description="瞭解如何使用 Apache 鳳凰中 HDInsight，以及如何安裝並設定您連線至 HBase 中叢集 HDInsight 工作站松鼠。" 
   services="hdinsight" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/02/2016"
   ms.author="jgao"/>

# <a name="use-apache-phoenix-with-linux-based-hbase-clusters-in-hdinsight"></a>使用中 HDInsight 的 Linux 型 HBase 叢集 Apache 鳳凰  

瞭解如何使用[Apache 鳳凰](http://phoenix.apache.org/)中 HDInsight，以及如何使用 SQLLine。 如需有關鳳凰的詳細資訊，請參閱[鳳凰在 15 分鐘之內](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html)。 鳳凰文法檢查]，請參閱[鳳凰文法](http://phoenix.apache.org/language/index.html)。

>[AZURE.NOTE] HDInsight 鳳凰版本資訊，請參閱[HDInsight 所提供之 Hadoop 叢集版本中的新功能？][hdinsight-versions].

##<a name="use-sqlline"></a>使用 SQLLine
[SQLLine](http://sqlline.sourceforge.net/)是執行 SQL 命令列公用程式。 

###<a name="prerequisites"></a>必要條件
您可以使用 SQLLine 之前，您必須具備下列項目︰

- **HBase 叢集 HDInsight 中**。 如需的資訊佈建 HBase 叢集，請參閱[快速入門中 HDInsight Apache HBase][hdinsight-hbase-get-started]。
- **連線到 HBase 叢集透過遠端桌面通訊協定**。 如需相關指示，請參閱[HDInsight 使用 Azure 傳統入口網站中的管理 Hadoop 叢集][hdinsight-manage-portal]。


當您連線到 HBase 叢集時，您必須連線到動物園其中。 每個 HDInsight 叢集有 3 動物園管理員。 

**若要找出動物園管理員主機名稱**

1. 瀏覽] 以開啟 Ambari **https://<ClusterName>。 azurehdinsight.net**。
2. 輸入 HTTP （叢集） 的使用者名稱和密碼登入。
3. 按一下 [從左側功能表的 [**動物園管理員**]。 您應該會看到列 3**動物園管理員伺服器**。
4. 按一下其中一個列出**動物園管理員伺服器**。 在 [摘要] 窗格中，尋找 [**主機名稱**]。 則*zk1 jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*類似。

**若要使用 SQLLine**

1. 連線到使用 SSH 叢集。 如需相關指示，請參閱[使用 SSH 與 Linux 為基礎的 Hadoop HDInsight Linux、 Unix，或 OS X 上](hdinsight-hadoop-linux-use-ssh-unix.md)或[使用 SSH Linux 為基礎的 Hadoop HDInsight 從 Windows 上使用](hdinsight-hadoop-linux-use-ssh-windows.md)根據用戶端電腦 OS。

2. 從 SSH，執行下列命令以執行 SQLLine:

        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ClusterName>:2181:/hbase-unsecure

2. 執行下列命令以建立 HBase 表格，然後插入一些資料︰

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));
    
        !tables
        
        UPSERT INTO Company VALUES(1, 'Microsoft');
        
        SELECT * FROM Company;
        
        !quit

如需詳細資訊，請參閱[SQLLine 手動](http://sqlline.sourceforge.net/#manual)及[鳳凰文法檢查](http://phoenix.apache.org/language/index.html)。


 
##<a name="next-steps"></a>後續步驟
本文中，您已經學會如何使用 Apache 鳳凰 HDInsight 中。  若要深入瞭解，請參閱

- [HDInsight HBase 概觀][hdinsight-hbase-overview]: HBase 是內建在提供的非結構化及 semistructured 資料大量隨機存取及強式一致性的 Hadoop Apache 開啟來源 NoSQL 資料庫。
- [佈建 Azure 虛擬網路上的 HBase 叢集][hdinsight-hbase-provision-vnet]︰ 虛擬網路整合 HBase 叢集可以部署到相同的虛擬網路應用程式，讓應用程式可直接通訊與 HBase。
- [設定 HBase HDInsight 複寫](hdinsight-hbase-geo-replication.md)︰ 瞭解如何設定 HBase 複寫跨兩個 Azure 資料中心。 
- [分析 HBase HDInsight 中使用 Twitter 舉動][hbase-twitter-sentiment]︰ 了解如何使用 HBase Hadoop 叢集 HDInsight 中進行即時[舉動分析](http://en.wikipedia.org/wiki/Sentiment_analysis)資料的大。

[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png


 
