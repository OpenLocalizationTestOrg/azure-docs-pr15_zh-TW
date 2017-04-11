<properties 
   pageTitle="設定兩個資料中心之間的 HBase 複寫 |Microsoft Azure" 
   description="瞭解如何設定 HBase 複寫，兩個資料中心，以及如何叢集複寫的使用案例。" 
   services="hdinsight,virtual-network" 
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
   ms.date="07/25/2016"
   ms.author="jgao"/>

# <a name="configure-hbase-geo-replication-in-hdinsight"></a>設定 HDInsight HBase 地理複寫

> [AZURE.SELECTOR]
- [設定 VPN 連線能力](../hdinsight-hbase-geo-replication-configure-vnets.md)
- [設定 DNS](hdinsight-hbase-geo-replication-configure-dns.md)
- [設定 HBase 複寫](hdinsight-hbase-geo-replication.md) 
 
瞭解如何設定 HBase 複寫兩個資料中心。 部分使用情況下，叢集複寫包含︰

- 備份與損毀的復原
- 資料彙總
- 地理資料通訊群組
- 線上資料 ingestion 加上離線資料分析

叢集複寫使用來源推入方法。 HBase 叢集可能會產生目的，或可以一次滿足兩個角色。 複寫非同步，而複寫的目標是一致性。 當來源接收到欄系列編輯複寫啟用時，該編輯，會傳播到所有的目的地叢集。 資料的一個複製到另一個時，會以避免複寫重複追蹤來源叢集和所有業已用完資料。 如需詳細資訊，在此教學課程中，您就會設定來源目的地複寫。  其他叢集拓撲，請參閱[Apache HBase 參考指南](http://hbase.apache.org/book.html#_cluster_replication)。

這是一系列的第三部分︰

- [設定兩個虛擬網路之間 VPN 連線能力][hdinsight-hbase-replication-vnet]
- [虛擬網路設定 DNS][hdinsight-hbase-replication-dns]
- 設定 HBase 地理複製 （此教學課程）

下圖顯示兩個虛擬網路和[兩個虛擬網路之間 VPN 連線設定]中所建立的網路連線[hdinsight-hbase-geo-replication-vnet]和[虛擬網路的設定 DNS][hdinsight-hbase-replication-dns]: 

![HDInsight HBase 複寫虛擬網狀圖][img-vnet-diagram]

## <a id="prerequisites"></a>必要條件

本教學課程之前，您必須具備下列項目︰

- **Azure 訂閱**。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- **使用 PowerShell 的 Azure 工作站**。

    若要執行 PowerShell 指令碼，您必須以系統管理員身分執行 PowerShell 的 Azure，並將*RemoteSigned*執行原則。 請參閱使用設定 ExecutionPolicy 指令程式。
    
    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **兩個 Azure 虛擬網路與 VPN 連線，以及與 DNS 設定**。  如需相關指示，請參閱[設定兩個 Azure 虛擬網路之間的 VPN 連線][hdinsight-hbase-replication-vnet]，和[兩個 Azure 虛擬網路之間的設定 DNS][hdinsight-hbase-replication-dns]。


    之前執行的 PowerShell 指令碼，請先確認您已連線到您 Azure 的訂閱，請使用下列 cmdlet:

        Add-AzureAccount

    如果您有多個 Azure 訂閱，請使用下列 cmdlet 設定目前的訂閱︰

        Select-AzureSubscription <AzureSubscriptionName>



## <a name="provision-hbase-clusters-in-hdinsight"></a>佈建 HBase 叢集 HDInsight 中

在[兩個 Azure 虛擬網路之間的連線設定][hdinsight-hbase-replication-vnet]，建立虛擬網路歐洲地區資料中心及虛擬網路中的 US 資料中心。 透過 VPN 連線兩個虛擬網路。 在這個工作階段，您將提供在每一個虛擬網路 HBase 叢集。 稍後在本教學課程中，您將一個 HBase 叢集複寫其他 HBase 叢集。

Azure 傳統入口網站不支援佈建 HDInsight 叢集自訂設定選項。 例如，將*hbase.replication*設定為*true*。 如果叢集已佈建後，您可以設定設定檔中的值，您將遺失設定後叢集會被重新建立影像。 如需詳細資訊，請參閱[在 HDInsight 佈建 Hadoop 叢集][hdinsight-provision]。 佈建自訂選項的 HDInsight 叢集選項的其中一個使用 PowerShell 的 Azure。


**佈建 HBase 中叢集 Contoso-VNet-歐盟** 

1. 從您的工作站開啟 Windows PowerShell ise [以系統。
2. 在 [指令碼，開頭設定變數，然後執行 [指令碼。

        # create hbase cluster with replication enabled
        
        $azureSubscriptionName = "[AzureSubscriptionName]"
        
        $hbaseClusterName = "Contoso-HBase-EU" # This is the HBase cluster name to be used.
        $hbaseClusterSize = 1   # You must provision a cluster that contains at least 3 nodes for high availability of the HBase services.
        $hadoopUserLogin = "[HadoopUserName]"
        $hadoopUserpw = "[HadoopUserPassword]"
        
        $vNetName = "Contoso-VNet-EU"  # This name must match your Europe virtual network name.
        $subNetName = 'Subnet-1' # This name must match your Europe virtual network subnet name.  The default name is "Subnet-1".
        
        $storageAccountName = 'ContosoStoreEU'  # The script will create this storage account for you.  The storage account name doesn't support hyphens. 
        $storageAccountName = $storageAccountName.ToLower() #Storage account name must be in lower case.
        $blobContainerName = $hbaseClusterName.ToLower()  #Use the cluster name as the default container name.
        
        #connect to your Azure subscription
        Add-AzureAccount 
        Select-AzureSubscription $azureSubscriptionName
        
        # Create a storage account used by the HBase cluster
        $location = Get-AzureVNetSite -VNetName $vNetName | %{$_.Location} # use the virtual network location
        New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location
        
        # Create a blob container used by the HBase cluster
        $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{$_.Primary}
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        New-AzureStorageContainer -Name $blobContainerName -Context $storageContext
        
        # Create provision configuration object
        $hbaseConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHBaseConfiguration'
            
        $hbaseConfigValues.Configuration = @{ "hbase.replication"="true" } #this modifies the hbase-site.xml file
        
        # retrieve vnet id based on vnetname
        $vNetID = Get-AzureVNetSite -VNetName $vNetName | %{$_.id}
        
        $config = New-AzureHDInsightClusterConfig `
                        -ClusterSizeInNodes $hbaseClusterSize `
                        -ClusterType HBase `
                        -VirtualNetworkId $vNetID `
                        -SubnetName $subNetName `
                    | Set-AzureHDInsightDefaultStorage `
                        -StorageAccountName $storageAccountName `
                        -StorageAccountKey $storageAccountKey `
                        -StorageContainerName $blobContainerName `
                    | Add-AzureHDInsightConfigValues `
                        -HBase $hbaseConfigValues
        
        # provision HDInsight cluster
        $hadoopUserPassword = ConvertTo-SecureString -String $hadoopUserpw -AsPlainText -Force     
        $credential = New-Object System.Management.Automation.PSCredential($hadoopUserLogin,$hadoopUserPassword)
        
        $config | New-AzureHDInsightCluster -Name $hbaseClusterName -Location $location -Credential $credential



**佈建 HBase 中叢集 Contoso VNet-適用於美國** 

- 使用下列的值相同的指令碼︰

        $hbaseClusterName = "Contoso-HBase-US" # This is the HBase cluster name to be used.
        $vNetName = "Contoso-VNet-US"  # This name must match your Europe virtual network name.
        $storageAccountName = 'ContosoStoreUS'  

    因為您已有連線至 Azure 帳戶，您不必再執行下列 comlets:

        Add-AzureAccount 
        Select-AzureSubscription $azureSubscriptionName




## <a name="configure-dns-conditional-forwarder"></a>設定 DNS 條件轉寄站

在[虛擬網路的設定 DNS][hdinsight-hbase-replication-dns]，您已設定為兩個網路的 DNS 伺服器。 HBase 叢集有不同的網域尾碼。 因此，您需要設定其他 DNS 條件轉寄站。

若要設定條件轉寄站，您必須知道兩個 HBase 叢集的網域尾碼。 

**若要尋找網域的尾碼兩個 HBase 叢集**

1. 將**Contoso-HBase-歐盟**RDP。  如需相關指示，請參閱[HDInsight 使用 Azure 傳統入口網站中的管理 Hadoop 叢集][hdinsight-manage-portal]。 這是實際 headnode0 叢集。
2. 開啟 Windows PowerShell 主控台或命令提示字元。
3. 執行**ipconfig**，並記下**連線特定尾碼**。
4. 請不要關閉 RDP 工作階段。  您必須將其稍後若要測試的網域名稱解析。
5. 重複相同步驟，找出**連線特定尾碼** **Contoso HBase-適用於美國**。


**若要設定 DNS 轉寄站**
 
1.  將**Contoso-DNS-歐盟**RDP。 
2.  按一下左下角的 Windows 鍵。
2.  按一下 [**系統管理工具**]。
3.  按一下 [ **DNS**]。
4.  在左窗格中，展開 [ **DSN**，**限 DNS Contoso 歐盟**]。
5.  **條件轉寄站**，以滑鼠右鍵按一下，然後按一下 [**新增條件轉寄站**。 
5.  輸入下列資訊︰
    - **DNS 網域**︰ 輸入 Contoso HBase-適用於美國的尾碼。 例如︰ Contoso-HBase-US.f5.internal.cloudapp.net。
    - **主控伺服器 IP 位址**︰ 輸入 10.2.0.4，也就是 Contoso DNS-適用於美國的 IP 位址。 請確認 IP。 您的 DNS 伺服器可以有不同的 IP 位址。
6.  按下**enter 鍵**，，然後按一下**[確定]**。  現在您可以解決 Contoso-DNS-歐盟 Contoso DNS-適用於美國的 IP 位址。
7.  重複步驟以新增條件 DNS 轉寄站 DNS 服務 Contoso DNS-適用於美國的虛擬機器，使用下列的值︰
    - **DNS 網域**︰ 輸入 Contoso-HBase 歐盟的尾碼。 
    - **主控伺服器 IP 位址**︰ 輸入 10.2.0.4，也就是 Contoso-DNS-歐盟的 IP 位址。

**若要測試的網域名稱解析**

1. 切換至 [Contoso-HBase-歐盟 RDP] 視窗。
2. 開啟命令提示字元。
3. 執行偵測 （ping） 命令時︰

        ping headnode0.[DNS suffix of Contoso-HBase-US]

    ICM 通訊協定已開啟的 HBase 叢集工作者節點

4. 不關閉 RDP 工作階段。 您仍會需要稍後的教學課程。
5. 重複相同步驟，Contoso HBase 歐盟從 Contoso HBase-適用於美國的 headnode0 偵測 （ping）。

>[AZURE.IMPORTANT] 您可以繼續到下一個步驟之前，必須使用 DNS。

## <a name="enable-replication-between-hbase-tables"></a>啟用 HBase 資料表之間的複寫

現在，您可以建立範例 HBase 資料表，啟用複寫，然後再將它測試含有一些資料。 您將使用的範例資料表有兩個資料行系列︰ 個人和 Office。 

在本教學課程中，將來源叢集，以及適用於美國 HBase 叢集和目的地叢集歐洲 HBase 叢集。

使用相同的名稱和資料行系列來源與目的地叢集上的建立 HBase 表格目的叢集知道儲存的資料就會收到的位置。 如需有關如何使用 HBase 命令介面的詳細資訊，請參閱[快速入門中 HDInsight Apache HBase][hdinsight-hbase-get-started]。

**若要在歐盟-HBase Contoso-建立 HBase 表格**

1. 切換到**Contoso-HBase-歐盟**RDP 視窗。
2. 從桌面中，按一下 [ **Hadoop 命令列**]。
2. 變更 HBase 主目錄的資料夾︰

        cd %HBASE_HOME%\bin
3. 開啟 HBase 殼層︰

        hbase shell
4. 建立 HBase 表格︰

        create 'Contacts', 'Personal', 'Office'
5. 不關閉 RDP 工作階段或 Hadoop 命令列視窗。 您仍然需要這些教學課程中的更新版本。
    
**在 [美國 HBase Contoso 建立 HBase 資料表**

- 重複相同步驟，Contoso HBase-適用於美國上建立相同的資料表。


**若要新增 Contoso HBase-適用於美國為複寫等**

1. 切換到**Contso HBase_EU** RDP 視窗。
2. HBase 殼層視窗中，新增目的叢集 （Contoso HBase-適用於美國） 為對等，例如︰

        add_peer '1', 'zookeeper0.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper1.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper2.contoso-hbase-us.d4.internal.cloudapp.net:2181:/hbase'

    在此範例中，網域後置字元會是*contoso-hbase-us.d4.internal.cloudapp.net*。 您需要更新，以符合您網域的尾碼美國 HBase 叢集。 請確定 hostname 間有不加空格。

**若要設定複寫來源叢集上每個資料行系列**

1. 從 [HBase 命令介面**限 HBase Contso 歐盟**RDP 工作階段] 視窗設定複寫每個資料行系列︰

        disable 'Contacts'
        alter 'Contacts', {NAME => 'Personal', REPLICATION_SCOPE => '1'}
        alter 'Contacts', {NAME => 'Office', REPLICATION_SCOPE => '1'}
        enable 'Contacts'

**若要大量上傳至 HBase 資料表的資料**

範例資料檔案已上傳至公用 Azure Blob 容器 url 如下︰

        wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

檔案的內容︰

        8396    Calvin Raji 230-555-0191    5415 San Gabriel Dr.
        16600   Karen Wu    646-555-0113    9265 La Paz
        4324    Karl Xie    508-555-0163    4912 La Vuelta
        16891   Jonathan Jackson    674-555-0110    40 Ellis St.
        3273    Miguel Miller   397-555-0155    6696 Anchor Drive
        3588    Osarumwense Agbonile    592-555-0152    1873 Lion Circle
        10272   Julia Lee   870-555-0110    3148 Rose Street
        4868    Jose Hayes  599-555-0171    793 Crawford Street
        4761    Caleb Alexander 670-555-0141    4775 Kentucky Dr.
        16443   Terry Chander   998-555-0171    771 Northridge Drive

您可以將相同的資料檔案上傳到 HBase 叢集，並從該處匯入資料。

1. 切換到**Contoso-HBase-歐盟**RDP 視窗。
2. 從桌面中，按一下 [ **Hadoop 命令列**]。
3. 變更資料夾的 HBase 主目錄︰

        cd %HBASE_HOME%\bin

4. 上傳資料︰

        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:HomePhone, Office:Address" -Dimporttsv.bulk.output=/tmpOutput Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput Contacts


## <a name="verify-that-data-replication-is-taking-place"></a>驗證資料複寫正在進行中

您可以驗證複寫正在進行掃描從下列 HBase 殼層指令具有兩個叢集資料表︰

        Scan 'Contacts'


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已經學會如何設定 HBase 複寫跨兩個資料中心。 若要瞭解 HDInsight 和 HBase 的詳細資訊，請參閱︰

- [HDInsight 服務] 頁面](https://azure.microsoft.com/services/hdinsight/)
- [HDInsight 文件](https://azure.microsoft.com/documentation/services/hdinsight/)
- [快速入門中 HDInsight Apache HBase][hdinsight-hbase-get-started]
- [HDInsight HBase 概觀][hdinsight-hbase-overview]
- [佈建 HBase 叢集 Azure 虛擬網路上][hdinsight-hbase-provision-vnet]
- [分析與 HBase 即時 Twitter 舉動][hdinsight-hbase-twitter-sentiment]
- [分析感應器大量與 HBase HDInsight (Hadoop) 中的資料][hdinsight-sensor-data]

[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-geo-replication-dns]: ../hdinsight-hbase-geo-replication-configure-VNet.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication/HDInsight.HBase.Replication.Network.diagram.png

[powershell-install]: powershell-install-configure.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-hbase-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-dns.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[hdinsight-sensor-data]: hdinsight-storm-sensor-data-analysis.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
