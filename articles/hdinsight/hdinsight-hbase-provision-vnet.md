<properties
    pageTitle="虛擬網路中建立 HBase 叢集 |Microsoft Azure"
    description="開始使用 HBase Azure HDInsight。 瞭解如何建立 HDInsight HBase 叢集 Azure 虛擬網路上。"
    keywords=""
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
   ms.date="10/18/2016"
   ms.author="jgao"/>

# <a name="create-hbase-clusters-in-azure-virtual-network"></a>Azure 虛擬網路中建立 HBase 叢集 

瞭解如何建立[Azure 虛擬網路]中的 Azure HDInsight HBase 叢集[1]。

虛擬網路整合 HBase 叢集可以部署至相同的虛擬網路應用程式，讓應用程式可直接通訊與 HBase。 優點包括︰

- 直接 HBase 叢集，讓通訊透過 HBase Java 遠端程序的節點的 web 應用程式的網路連線呼叫 (RPC) Api。
- 改良效能，不需要將流量移移到多個閘道器與負載平衡器。
- 能夠以更安全的方式處理機密資訊，而不公開公用結束點。

###<a name="prerequisites"></a>必要條件
本教學課程之前，您必須具備下列項目︰

- **Azure 訂閱**。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- **使用 PowerShell 的 Azure 工作站**。 請參閱[安裝並使用 PowerShell 的 Azure](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)。 

## <a name="create-hbase-cluster-into-virtual-network"></a>建立到虛擬網路 HBase 叢集

在此區段中，您將建立 Linux 型 HBase 叢集相依 Azure 儲存體帳戶使用[Azure 資源管理員範本](../resource-group-template-deploy.md)Azure 虛擬網路中。 其他叢集建立方法和了解設定，請參閱[建立 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)。 如需有關如何使用範本來建立 HDInsight Hadoop 叢集的詳細資訊，請參閱[建立 Hadoop 叢集中 HDInsight 使用 Azure 資源管理員範本](hdinsight-hadoop-create-windows-clusters-arm-templates.md)

> [AZURE.NOTE] 某些屬性已固定的範本。 例如︰
>
> * __位置__︰ 東亞美國 2
> * __Cluster 版本︰ 3.4
> * __叢集工作者節點數目__︰ 4
> * __預設儲存帳戶__︰&lt;叢集名稱 > 儲存
> * __虛擬網路名稱__︰&lt;叢集名稱 >-vnet
> * __虛擬網路位址空間__︰ 10.0.0.0/16
> * __子網路名稱__︰ 預設
> * __子網路位址範圍__︰ 10.0.0.0/24
>
> &lt;叢集名稱 > 會被取代時使用的範本，您所提供的叢集名稱。

1. 按一下以開啟範本 Azure 入口網站中的下列圖像。 範本位於公用 blob 容器。 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-vnet.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 從**自訂部署**刀中，輸入下列資訊︰

    - **訂閱**︰ 選取用來建立 HDInsight 叢集、 從屬參照儲存帳戶和 Azure 虛擬網路 Azure 訂閱。
    - **資源群組**︰ 選取 [**建立新**，並指定新的資源群組名稱。
    - **位置**︰ 選取 [資源] 群組的位置。
    - **ClusterName**︰ 輸入您要建立 Hadoop 叢集的名稱。
    - **叢集登入名稱和密碼**︰ 預設的登入名稱是**管理員**。
    - **SSH 使用者名稱和密碼**︰ 預設的使用者名稱是**sshuser**。  您可以將其重新命名。 
    - **我同意條款與條件上述**: （選取）
    

3. 按一下 [**購買**]。 建立叢集需要瞭解大約 20 分鐘。 叢集建立之後，您可以按一下叢集刀，在入口網站中，將其開啟。

完成本教學課程後，您可能會想要刪除叢集。 HDInsight，與您的資料會儲存於 Azure 儲存體，以便在不使用安全地刪除叢集。 您也會針對 HDInsight 叢集，即使沒有使用。 由於叢集費用多次多儲存空間的費用，讓經濟叢集時無法使用。 如需刪除叢集的指示，請參閱[管理 Hadoop 叢集 HDInsight 使用 Azure 入口網站中](hdinsight-administer-use-management-portal.md#delete-clusters)。

若要開始使用新的 HBase 叢集，您可以使用中[開始使用 HBase 與 Hadoop HDInsight 中](hdinsight-hbase-tutorial-get-started.md)找到的程序。

## <a name="connect-to-the-hbase-cluster-using-hbase-java-rpc-apis"></a>連線到 HBase 叢集使用 HBase Java RPC Api

1.  建立相同的 Azure 虛擬網路和相同的子網路基礎結構服務 (IaaS) 虛擬機器。 如需建立新的 IaaS 虛擬機器的指示，請參閱[建立虛擬機器執行 Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md)。 當追蹤的文件中的步驟，您必須使用網路設定下列︰

    - __虛擬網路__︰&lt;叢集名稱 >-vnet
    - __子網路__︰ 預設

    > [AZURE.IMPORTANT] 取代&lt;叢集名稱 > 與上一個步驟中建立 HDInsight 叢集時，您使用的名稱。

    使用這些值將會設定為 HDInsight 叢集使用相同的虛擬網路和子網路虛擬機器。 這可讓它們彼此直接通訊。

2.  遠端連線到 HBase 使用 Java 應用程式，您必須使用完整的網域名稱 (FQDN)。 若要判斷一來，您必須取得 HBase 叢集連線特定 DNS 名稱。 若要執行這項作業，您可以使用下列方法之一︰

    * 若要進行 Ambari 通話使用網頁瀏覽器︰
    
        瀏覽至 https://&lt;ClusterName >.azurehdinsight.net/api/v1/clusters/&lt;ClusterName > / hosts?minimal_response = true。 結果 DNS 尾碼 JSON 檔案。

    * 使用 Ambari 網站︰

        1. 瀏覽至 https://&lt;ClusterName >。 azurehdinsight.net。
        2. 從上方的功能表中按一下 [**主辦城市**]。

    * 若要打電話其餘的使用捲曲︰

            curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

        在 [JavaScript 物件標記法 (JSON) 資料傳回，尋找 [」 host_name 」 項目]。 這將會包含在叢集節點的 FQDN。 例如︰

            ...
            "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
            ...

        與叢集名稱開頭的網域名稱的部分，是 DNS 尾碼。 例如，mycluster.b1.cloudapp.net。

    * 使用 PowerShell 的 Azure
    
        使用下列 PowerShell 的 Azure 指令碼註冊**取得 ClusterDetail**函數，可以用來傳回 DNS 後置字元︰

            function Get-ClusterDetail(
                [String]
                [Parameter( Position=0, Mandatory=$true )]
                $ClusterDnsName,
                [String]
                [Parameter( Position=1, Mandatory=$true )]
                $Username,
                [String]
                [Parameter( Position=2, Mandatory=$true )]
                $Password,
                [String]
                [Parameter( Position=3, Mandatory=$true )]
                $PropertyName
                )
            {
            <#
                .SYNOPSIS
                 Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
                .Description
                 This command shows the following 4 properties of an HDInsight cluster:
                 1. ZookeeperQuorum (supports only HBase type cluster)
                    Shows the value of HBase property "hbase.zookeeper.quorum".
                 2. ZookeeperClientPort (supports only HBase type cluster)
                    Shows the value of HBase property "hbase.zookeeper.property.clientPort".
                 3. HBaseRestServers (supports only HBase type cluster)
                    Shows a list of host FQDNs that run the HBase REST server.
                 4. FQDNSuffix (supports all cluster types)
                    Shows the FQDN suffix of hosts in the cluster.
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
                 This command shows the value of HBase property "hbase.zookeeper.quorum".
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
                 This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
                 This command shows a list of host FQDNs that run the HBase REST server.
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
                 This command shows the FQDN suffix of hosts in the cluster.
            #>

                $DnsSuffix = ".azurehdinsight.net"

                $ClusterFQDN = $ClusterDnsName + $DnsSuffix
                $webclient = new-object System.Net.WebClient
                $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

                if($PropertyName -eq "ZookeeperQuorum")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
                }
                if($PropertyName -eq "ZookeeperClientPort")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
                }
                if($PropertyName -eq "HBaseRestServers")
                {
                    $Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
                    $Response1 = $webclient.DownloadString($Url1)
                    $JsonObject1 = $Response1 | ConvertFrom-Json
                    $PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

                    $Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
                    $Response2 = $webclient.DownloadString($Url2)
                    $JsonObject2 = $Response2 | ConvertFrom-Json
                    foreach ($host_component in $JsonObject2.host_components)
                    {
                        $ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
                        Write-host $ConnectionString
                    }
                }
                if($PropertyName -eq "FQDNSuffix")
                {
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/YARN/components/RESOURCEMANAGER"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    $FQDN = $JsonObject.host_components[0].HostRoles.host_name
                    $pos = $FQDN.IndexOf(".")
                    $Suffix = $FQDN.Substring($pos + 1)
                    Write-host $Suffix
                }
            }

        執行後 Azure PowerShell 指令碼，使用下列命令以使用**取得 ClusterDetail**函數傳回的尾碼。 使用此命令時，請指定 [HDInsight HBase 叢集名稱、 系統管理員名稱和管理員密碼]。

            Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

        這會傳回的尾碼。 例如， **yourclustername.b4.internal.cloudapp.net**。

    * 使用 RDP
    
        您也可以使用遠端桌面連線至 HBase 叢集 （您會連線到主節點） 及執行**ipconfig**命令提示字元以取得的尾碼。 啟用遠端桌面通訊協定 (RDP) 與使用 RDP，連線到叢集指示請參閱[HDInsight 使用 Azure 入口網站中的管理 Hadoop 叢集][hdinsight-admin-portal]。
        
        ![hdinsight.hbase.dns.surffix][img-dns-surffix]


<!--
3.  Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

若要確認虛擬機器可以通訊與 HBase 叢集，請使用命令`ping headnode0.<dns suffix>`從虛擬機器。 例如，偵測 （ping) headnode0.mycluster.b1.cloudapp.net。

若要在 Java 應用程式中使用這項資訊，您可以遵循[使用 Maven 建立 Java 應用程式的使用 HBase HDInsight (Hadoop) 與](hdinsight-hbase-build-java-maven.md)建立應用程式中的步驟進行。 若要連線到遠端 HBase 伺服器的應用程式，請修改**hbase site.xml**檔案在此範例中用於動物園管理員的 FQDN。 例如︰

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [AZURE.NOTE] 如需有關 Azure 中的名稱解析虛擬網路，包括如何使用您自己的 DNS 伺服器，請參閱[名稱解析 (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。

##<a name="next-steps"></a>後續步驟

在此教學課程中，您學到如何建立 HBase 叢集的內容。 若要深入瞭解，請參閱︰

- [快速入門 HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [設定 HDInsight HBase 複寫](hdinsight-hbase-geo-replication.md)
- [建立 HDInsight Hadoop 叢集](hdinsight-provision-clusters.md)
- [使用 HBase Hadoop HDInsight 中使用快速入門](hdinsight-hbase-tutorial-get-started.md)
- [分析 HBase HDInsight 中使用 Twitter 舉動](hdinsight-hbase-analyze-twitter-sentiment.md)
- [虛擬網路概觀][vnet-overview]


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[vnet-overview]: ../virtual-network/virtual-networks-overview.md
[vm-create]: ../virtual-machines/virtual-machines-windows-hero-tutorial.md

[azure-portal]: https://portal.azure.com
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md#rdp

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: powershell-install-configure.md


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "新的 HBase 叢集佈建詳細資料"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "若要自訂 HBase 叢集使用指令碼的巨集指令"

[azure-preview-portal]: https://portal.azure.com
