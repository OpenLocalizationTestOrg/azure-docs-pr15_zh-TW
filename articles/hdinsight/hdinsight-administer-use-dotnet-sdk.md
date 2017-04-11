<properties
    pageTitle="管理中使用.NET SDK HDInsight Hadoop 叢集 |Microsoft Azure"
    description="瞭解如何使用 HDInsight.NET SDK 的 HDInsight 中 Hadoop 叢集執行系統管理工作。"
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    tags="azure-portal"
    authors="mumian"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="jgao"/>

# <a name="manage-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>使用.NET SDK 管理 Hadoop 叢集 HDInsight 中

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

瞭解如何管理 HDInsight 叢集使用[HDInsight.NET SDK](https://msdn.microsoft.com/library/mt271028.aspx)。


**必要條件**

這份文件之前，您必須具備下列項目︰

- **Azure 訂閱**。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。


##<a name="connect-to-azure-hdinsight"></a>連線至 Azure HDInsight

您將需要下列 Nuget 套件︰

    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.HDInsight

下列程式碼範例會示範如何連線至 Azure，您可以管理 HDInsight 叢集下 Azure 訂閱之前。

    using System;
    using Microsoft.Azure;
    using Microsoft.Azure.Management.HDInsight;
    using Microsoft.Azure.Management.HDInsight.Models;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;

    namespace HDInsightManagement
    {
        class Program
        {
            private static HDInsightManagementClient _hdiManagementClient;
            // Replace with your AAD tenant ID if necessary
            private const string TenantId = UserTokenProvider.CommonTenantId; 
            private const string SubscriptionId = "<Your Azure Subscription ID>";
            // This is the GUID for the PowerShell client. Used for interactive logins in this example.
            private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";

            static void Main(string[] args)
            {
                // Authenticate and get a token
                var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
                // Flag subscription for HDInsight, if it isn't already.
                EnableHDInsight(authToken);
                // Get an HDInsight management client
                _hdiManagementClient = new HDInsightManagementClient(authToken);

                // insert code here

                System.Console.WriteLine("Press ENTER to continue");
                System.Console.ReadLine();
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
        }
    }

當您執行這個程式時，您應該看到的提示。  如果您不想看到提示時，請參閱[建立非互動式驗證.NET HDInsight 應用程式](hdinsight-create-non-interactive-authentication-dotnet-applications.md)。

##<a name="create-clusters"></a>建立叢集

請參閱[使用.NET SDK 的 HDInsight 中的 [建立 Linux 型叢集](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)

##<a name="list-clusters"></a>清單叢集

下列程式碼片段列出叢集與某些屬性︰

    var results = _hdiManagementClient.Clusters.List();
    foreach (var name in results.Clusters) {
        Console.WriteLine("Cluster Name: " + name.Name);
        Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
        Console.WriteLine("\t Cluster location: " + name.Location);
        Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
    }

##<a name="delete-clusters"></a>刪除叢集

若要刪除叢集同步或非同步使用下列的程式碼片段︰ 

    _hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
    _hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
            
##<a name="scale-clusters"></a>縮放比例叢集
叢集縮放功能可讓您變更正在執行中 Azure HDInsight，而不需要重新建立 [叢集叢集使用工作者節點數目。

>[AZURE.NOTE] 只使用 HDInsight 版本 3.1.3 叢集或更高支援。 如果您不確定您的叢集版本的您可以核取 [屬性] 頁面。  請參閱[清單] 和 [顯示叢集](hdinsight-administer-use-portal-linux.md#list-and-show-clusters)。

變更每種類型的支援 HDInsight 叢集資料節點數目的影響︰

- Hadoop

    您可以順暢完美地增加背景工作中的節點數目 Hadoop 叢集執行而不會影響任何擱置] 或 [執行的工作。 在進行作業時也能提交新的工作。 縮放比例作業失敗適當地處理以便叢集永遠左功能的狀態。

    當 Hadoop 叢集縮小來減少資料節點數目時，部分叢集服務會重新啟動。 這會使所有執行，並且擱置的工作，在 [縮放比例] 作業完成失敗。 您可以不過，重新送出工作一旦完成作業。

- HBase

    流暢地，您可以新增或移除節點] 在執行中時，HBase 叢集。 完成 [縮放比例] 作業的幾分鐘內自動對稱地區的伺服器。 不過，您可以登入的叢集 headnode，並從 [命令提示字元視窗中執行下列命令以手動平衡地區伺服器︰

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

- 大量

    流暢地，您可以新增或移除資料節點] 在執行中時，大量叢集。 但縮放作業成功完成之後，您將需要重新平衡拓撲。

    平衡可完成兩種方法︰

    * 大量網頁 UI
    * 命令列介面 (CLI) 工具

    請參閱[Apache 大量文件](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)更多。

    大量網頁 UI HDInsight 叢集上有︰

    ![hdinsight 大量比例 rebalance](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

    以下是如何使用 [CLI 命令來重新平衡大量拓撲範例︰

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors

        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

下列程式碼片段顯示如何調整叢集同步或非同步︰

    _hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
    _hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
    

##<a name="grantrevoke-access"></a>授與/撤銷存取

HDInsight 叢集有下列 HTTP web 服務 （所有這些服務需要 RESTful 的端點）︰

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


根據預設，這些服務會授與存取。 您可以撤銷/授與存取權。 撤銷︰

    var httpParams = new HttpSettingsParameters
    {
        HttpUserEnabled = false,
        HttpUsername = "admin",
        HttpPassword = "*******",
    };
    _hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);

若要授與︰

    var httpParams = new HttpSettingsParameters
    {
        HttpUserEnabled = enable,
        HttpUsername = "admin",
        HttpPassword = "*******",
    };
    _hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);


>[AZURE.NOTE] 授與/撤銷存取，您會重設叢集使用者名稱和密碼。

這也可以透過入口網站完成。 請參閱[使用 Azure 入口網站的管理 HDInsight][hdinsight-admin-portal]。

##<a name="update-http-user-credentials"></a>更新 HTTP 使用者認證

則為[HTTP/撤銷授與存取權](#grant/revoke-access)的相同程序。如果叢集已獲授予他人 HTTP 存取，您必須先撤銷。  然後授與存取權，以新的 HTTP 使用者認證。


##<a name="find-the-default-storage-account"></a>尋找預設儲存帳戶

下列程式碼片段示範如何取得預設儲存空間客戶名稱] 和 [叢集的預設儲存帳戶金鑰。

    var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
    foreach (var key in results.Configuration.Keys)
    {
        Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
    }


##<a name="submit-jobs"></a>送出工作

**提交 MapReduce 工作**

請參閱[在 HDInsight 執行 Hadoop MapReduce 範例](hdinsight-hadoop-run-samples-linux.md)。

**提交登錄區工作** 

請參閱[使用.NET SDK 執行登錄區查詢](hdinsight-hadoop-use-hive-dotnet-sdk.md)。

**送出的豬工作**

請參閱[使用.NET SDK 執行豬工作](hdinsight-hadoop-use-pig-dotnet-sdk.md)。

**提交 Sqoop 工作**

請參閱[使用 Sqoop 與 HDInsight](hdinsight-hadoop-use-sqoop-dotnet-sdk.md)。

**提交 Oozie 工作**

請參閱[使用 Oozie 與 Hadoop 來定義並執行 HDInsight 中的工作流程](hdinsight-use-oozie-linux-mac.md)。

##<a name="upload-data-to-azure-blob-storage"></a>將資料上傳至 Azure Blob 儲存體
請參閱[上傳至 HDInsight 的資料][hdinsight-upload-data]。


## <a name="see-also"></a>另請參閱
* [HDInsight.NET SDK 參照文件](https://msdn.microsoft.com/library/mt271028.aspx)
* [藉由使用 [Azure 入口網站管理 HDInsight][hdinsight-admin-portal]
* [管理 HDInsight 使用命令列介面][hdinsight-admin-cli]
* [建立 HDInsight 叢集][hdinsight-provision]
* [上傳至 HDInsight 的資料][hdinsight-upload-data]
* [開始使用 Azure HDInsight][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-portal-linux.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md


