<properties
    pageTitle="自訂使用指令碼動作的 HDInsight 叢集 |Microsoft Azure"
    description="瞭解如何自訂 HDInsight 叢集使用指令碼動作。"
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="nitinme"/>

# <a name="customize-windows-based-hdinsight-clusters-using-script-action"></a>自訂 Windows 型 HDInsight 叢集使用指令碼的巨集指令

**指令碼動作**可用來在叢集上安裝其他軟體的叢集建立程序期間叫用[自訂指令碼](hdinsight-hadoop-script-actions.md)。

本文中的資訊是 Windows 型 HDInsight 叢集特定項目。 針對 Linux 型叢集，請參閱[使用指令碼的巨集指令的自訂 Linux 型 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)。 

HDInsight 叢集可以進行自訂，以不同的其他方法，例如包括其他 Azure 儲存體帳戶，請變更 Hadoop 設定檔案 （核心 site.xml、 登錄區 site.xml 等），或新增到叢集一般位置共用文件庫 （例如登錄區、 Oozie）。 透過 PowerShell 的 Azure、 Azure HDInsight.NET sdk，您可以或 Azure 入口網站，就可以完成這些自訂項目。 如需詳細資訊，請參閱[建立 Hadoop 叢集中 HDInsight][hdinsight-provision-cluster]。

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-cli-and-dotnet-sdk.md)]

## <a name="script-action-in-the-cluster-creation-process"></a>叢集建立程序中的指令碼動作

在建立叢集時，才會使用指令碼的巨集指令。 下圖說明指令碼的巨集指令執行期間建立程序時︰

![HDInsight 叢集自訂以及期間叢集建立階段][img-hdi-cluster-states]

當正在執行指令碼時，叢集會輸入**ClusterCustomization**階段。 此階段指令碼之下系統管理員帳戶的平行叢集，所有指定節點上執行時，並提供 [節點中的 [完整的管理員權限。

> [AZURE.NOTE] 因為您已在叢集節點的管理員權限**ClusterCustomization**階段期間，您可以使用指令碼執行作業，例如，停止並啟動服務，包括 Hadoop 相關的服務。 因此，屬於指令碼，您必須確定 Ambari 服務及其他 Hadoop 相關服務會啟動並執行指令碼完成執行之前。 這些服務，才能成功查明健康狀況與叢集狀態時所建立。 如果您變更任何設定會影響這些服務叢集上的時，您必須使用所提供的協助函數。 如需協助函數的詳細資訊，請參閱[HDInsight 開發指令碼動作指令碼][hdinsight-write-script]。

輸出] 和 [錯誤記錄檔的指令碼會儲存在您所指定的叢集預設儲存帳戶。 含有名稱的表格中儲存記錄**u < \cluster-name-fragment >< \time-stamp > setuplog**。 這些是彙總的記錄中的所有節點 （標頭節點和工作者節點） 叢集上執行指令碼。

每個叢集可以接受多個所指定的順序叫用的指令碼動作。 在主節點、 工作者節點，或兩者，就可以執行指令碼。

HDInsight 提供數種指令碼 HDInsight 叢集上安裝下列元件︰

名稱 | 指令碼
----- | -----
**安裝火花** | https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1。 請參閱[安裝並使用激起 HDInsight 叢集上][hdinsight-install-spark]。
**安裝 R** | https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1。 請參閱[安裝並使用 R HDInsight 叢集上的][hdinsight-install-r]。
**安裝 Solr** | https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1。 請參閱[安裝並使用 Solr 上 HDInsight 叢集](hdinsight-hadoop-solr-install.md)。
- **安裝 Giraph** | https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1。 請參閱[安裝並使用 Giraph 上 HDInsight 叢集](hdinsight-hadoop-giraph-install.md)。
| **預先載入登錄區文件庫** | https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1。 請參閱[新增登錄區文件庫 HDInsight 叢集上](hdinsight-hadoop-add-hive-libraries.md) |


## <a name="call-scripts-using-the-azure-portal"></a>使用 [Azure 入口網站的通話指令碼

**從 Azure 入口網站**

1. 開始建立叢集所述，[建立 Hadoop 叢集 HDInsight 中](hdinsight-provision-clusters.md#portal)。
2. 選用的設定] 底下的**指令碼動作**刀中，按一下 [**新增指令碼動作**提供詳細資料的指令碼動作，如下所示︰

    ![若要自訂叢集使用指令碼動作](./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "若要自訂叢集使用指令碼動作")

    <table border='1'>
        <tr><th>屬性</th><th>值</th></tr>
        <tr><td>名稱</td>
            <td>指定指令碼動作的名稱。</td></tr>
        <tr><td>指令碼 URI</td>
            <td>指定自訂叢集叫用的指令碼的 URI。 s</td></tr>
        <tr><td>不對/工作者</td>
            <td>在上執行的自訂指令碼指定節點 （「**不對**」 或 「**工作**」）。</b>.
        <tr><td>參數</td>
            <td>如果所需的指令碼，請指定參數。</td></tr>
    </table>

    按下 ENTER，若要新增一個以上的指令碼動作，以叢集上安裝多個元件。

3. 按一下 [以儲存指令碼的巨集指令設定，並繼續進行叢集建立的 [**選取**]。

## <a name="call-scripts-using-azure-powershell"></a>使用 PowerShell 的 Azure 通話指令碼

此下列 PowerShell 指令碼示範如何根據 HDInsight 叢集 Windows 上安裝火花。  

    # Provide values for these variables
    $subscriptionID = "<Azure Suscription ID>" # After "Login-AzureRmAccount", use "Get-AzureRmSubscription" to list IDs.

    $nameToken = "<Enter A Name Token>"  # The token is use to create Azure service names.
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    
    $resourceGroupName = $namePrefix + "rg"
    $location = "EAST US 2" # used for creating resource group, storage account, and HDInsight cluster.
    
    $hdinsightClusterName = $namePrefix + "spark"
    $httpUserName = "admin"
    $httpPassword = "<Enter a Password>"
    
    $defaultStorageAccountName = "$namePrefix" + "store"
    $defaultBlobContainerName = $hdinsightClusterName
    
    #############################################################
    # Connect to Azure
    #############################################################
    
    Try{
        Get-AzureRmSubscription
    }
    Catch{
        Login-AzureRmAccount
    }
    Select-AzureRmSubscription -SubscriptionId $subscriptionID
    
    #############################################################
    # Prepare the dependent components
    #############################################################
    
    # Create resource group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    
    # Create storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageAccountContext
    
    #############################################################
    # Create cluster with ApacheSpark
    #############################################################
    
    # Specify the configuration options
    $config = New-AzureRmHDInsightClusterConfig `
                -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
                -DefaultStorageAccountKey $defaultStorageAccountKey 
                
    
    # Add a script action to the cluster configuration
    $config = Add-AzureRmHDInsightScriptAction `
                -Config $config `
                -Name "Install Spark" `
                -NodeType HeadNode `
                -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1 `
    
    # Start creating a cluster with Spark installed
    New-AzureRmHDInsightCluster `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $hdinsightClusterName `
            -Location $location `
            -ClusterSizeInNodes 2 `
            -ClusterType Hadoop `
            -OSType Windows `
            -DefaultStorageContainer $defaultBlobContainerName `
            -Config $config


若要安裝其他軟體，您將需要取代指令碼中的指令碼檔︰


出現提示時，請叢集輸入認證。 可能需要幾分鐘，才能建立叢集。

## <a name="call-scripts-using-net-sdk"></a>使用.NET SDK 的通話指令碼 

下列範例會示範如何根據 HDInsight 叢集 Windows 上安裝火花。 若要安裝其他軟體，您必須將指令碼的檔案中的程式碼。

**若要建立火花 HDInsight 叢集** 

1. 在 Visual Studio 建立 C# 主控台應用程式。
2. 從 [Nuget 封裝管理員主控台中，執行下列命令。

        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package Microsoft.Azure.Management.ResourceManager -Pre
        Install-Package Microsoft.Azure.Management.HDInsight

2. 使用下列使用 Program.cs 檔案中的陳述式︰

        using System;
        using System.Security;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Models;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;

3. 以下列類別中放置程式碼︰

        private static HDInsightManagementClient _hdiManagementClient;

        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId; 
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";
        private const string ResourceGroupName = "<ExistingAzureResourceGroupName>";
        private const string NewClusterName = "<NewAzureHDInsightClusterName>";
        private const int NewClusterNumWorkerNodes = 2;
        private const string NewClusterLocation = "East US";
        private const string NewClusterVersion = "3.2";
        private const string ExistingStorageName = "<ExistingAzureStorageAccountName>";
        private const string ExistingStorageKey = "<ExistingAzureStorageAccountKey>";
        private const string ExistingContainer = "<ExistingAzureBlobStorageContainer>";
        private const string NewClusterType = "Hadoop";
        private const OSType NewClusterOSType = OSType.Windows;
        private const string NewClusterUsername = "<HttpUserName>";
        private const string NewClusterPassword = "<HttpUserPassword>";

        static void Main(string[] args)
        {
            System.Console.WriteLine("Running");

            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            CreateCluster();
        }

        private static void CreateCluster()
        {
            var parameters = new ClusterCreateParameters
            {
                ClusterSizeInNodes = NewClusterNumWorkerNodes,
                Location = NewClusterLocation,
                ClusterType = NewClusterType,
                OSType = NewClusterOSType,
                Version = NewClusterVersion,

                DefaultStorageAccountName = ExistingStorageName,
                DefaultStorageAccountKey = ExistingStorageKey,
                DefaultStorageContainer = ExistingContainer,

                UserName = NewClusterUsername,
                Password = NewClusterPassword,
            };

            ScriptAction sparkScriptAction = new ScriptAction("Install Spark",
                new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
        }

        /// <summary>
        /// Authenticate to an Azure subscription and retrieve an authentication token
        /// </summary>
        /// <param name="TenantId">The AAD tenant ID</param>
        /// <param name="ClientId">The AAD client ID</param>
        /// <param name="SubscriptionId">The Azure subscription ID</param>
        /// <returns></returns>
        static TokenCloudCredentials Authenticate(string TenantId, string ClientId, string SubscriptionId)
        {
            var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + TenantId);
            var tokenAuthResult = authContext.AcquireToken("https://management.core.windows.net/", 
                ClientId, 
                new Uri("urn:ietf:wg:oauth:2.0:oob"), 
                PromptBehavior.Always, 
                UserIdentifier.AnyUser);
            return new TokenCloudCredentials(SubscriptionId, tokenAuthResult.AccessToken);
        }
        /// <summary>
        /// Marks your subscription as one that can use HDInsight, if it has not already been marked as such.
        /// </summary>
        /// <remarks>This is essentially a one-time action; if you have already done something with HDInsight
        /// on your subscription, then this isn't needed at all and will do nothing.</remarks>
        /// <param name="authToken">An authentication token for your Azure subscription</param>
        static void EnableHDInsight(TokenCloudCredentials authToken)
        {
            // Create a client for the Resource manager and set the subscription ID
            var resourceManagementClient = new ResourceManagementClient(new TokenCredentials(authToken.Token));
            resourceManagementClient.SubscriptionId = SubscriptionId;
            // Register the HDInsight provider
            var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");
        }


4. 按**F5**執行應用程式。


## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>HDInsight 叢集上所用的開啟來源軟體支援
Microsoft Azure HDInsight 服務是非常有彈性的平台可讓您使用的周圍 Hadoop 格式開啟來源技術生態建置在雲端的大型資料應用程式。 Microsoft Azure 提供一般等級的支援開啟來源技術<a href="http://azure.microsoft.com/support/faq/" target="_blank">支援常見問題集 Azure 網站</a>的 [**支援範圍**] 區段中所述。 HDInsight 服務會提供其他支援層級的部分元件，如下所述。

有兩種類型的開啟來源元件所提供的 HDInsight 服務︰

- **內建的元件**-HDInsight 叢集上已預先安裝下列元件，並提供叢集核心功能。 例如，YARN ResourceManager、 登錄區查詢語言 (HiveQL) 及 Mahout 文件庫隸屬於此類別中。 叢集元件的完整清單有[HDInsight 所提供之 Hadoop 叢集版本中的新功能？](hdinsight-component-versioning.md)</a>.
- **自訂元件**-，以在叢集，使用者可以安裝，或使用您的工作量中的任何可用的社群中或建立您的元件。

完全支援的內建的元件，並找出並解決這些元件的相關問題可協助 Microsoft 支援服務。

> [AZURE.WARNING] 完全支援元件提供 HDInsight 叢集，以及 Microsoft 支援服務會協助找出並解決這些元件的相關問題。
>
> 自訂元件收到合理的支援，以協助您進一步疑難排解問題。 這可能會導致解決問題，或詢問要加入找到該技術深專業知識的位置開啟來源技術可用的頻道。 例如，有許多都可以使用，這樣的社群網站︰[適用於 HDInsight MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight)、 [http://stackoverflow.com](http://stackoverflow.com)。 也 Apache 專案有[http://apache.org](http://apache.org)，在專案網站，例如︰ [Hadoop](http://hadoop.apache.org/)、[火花](http://spark.apache.org/)。

HDInsight 服務會提供數種方式可使用自訂的元件。 如何使用或元件安裝叢集上，無論適用於支援相同層級。 以下是最常見的方式，可以使用自訂的元件 HDInsight 叢集上的清單︰

1. 可以叢集送出工作送出 Hadoop 或其他類型的工作可執行或使用自訂的元件。
2. 叢集自訂-叢集建立期間，您可以指定其他設定] 和 [叢集節點將會安裝的自訂元件。
3. 範例-常用的自訂元件、 Microsoft 與其他人可能會提供如何使用這些元件，HDInsight 叢集上的範例。 下列範例會提供不支援。

## <a name="develop-script-action-scripts"></a>開發指令碼動作指令碼

請參閱[HDInsight 開發指令碼動作指令檔][hdinsight-write-script]。


## <a name="see-also"></a>另請參閱

- [建立 HDInsight Hadoop 叢集][hdinsight-provision-cluster]說明如何建立 HDInsight 叢集，您可以使用其他自訂選項。
- [開發 HDInsight 指令碼動作指令碼][hdinsight-write-script]
- [安裝並使用火花 HDInsight 叢集上][hdinsight-install-spark]
- [安裝並使用 R HDInsight 叢集上][hdinsight-install-r]
- [安裝並使用 Solr 上 HDInsight 叢集](hdinsight-hadoop-solr-install.md)。
- [安裝並使用 Giraph 上 HDInsight 叢集](hdinsight-hadoop-giraph-install.md)。

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: powershell-install-configure.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "在叢集建立期間的階段"
