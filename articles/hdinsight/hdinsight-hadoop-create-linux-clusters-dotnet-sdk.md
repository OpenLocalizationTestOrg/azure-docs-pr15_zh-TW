<properties
    pageTitle="在使用 HDInsight.NET SDK 的 HDInsight Linux 上建立 Hadoop、 HBase、 或火花叢集 |Microsoft Azure"
    description="瞭解如何建立使用 HDInsight.NET SDK 的 HDInsight linux Hadoop、 HBase、 或火花叢集。"
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/02/2016"
    ms.author="jgao"/>

#<a name="create-linux-based-clusters-in-hdinsight-using-the-net-sdk"></a>使用.NET SDK 的 HDInsight 中建立 Linux 型叢集

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

HDInsight.NET SDK 提供.NET 用戶端文件庫，可讓您更容易使用 HDInsight.NET Framework 應用程式。 這份文件會示範如何建立使用.NET SDK Linux 型 HDInsight 叢集。

> [AZURE.IMPORTANT] 這份文件中的步驟，建立一個工作者節點叢集。 如果您打算 32 個以上的工作者節點，在叢集建立，或藉由縮放叢集之後建立，您就必須選取至少 8 核心和 14 GB ram 主節點大小。
>
> 如需有關節點大小和相關聯的成本的詳細資訊，請參閱[HDInsight 價格](https://azure.microsoft.com/pricing/details/hdinsight/)。

##<a name="prerequisites"></a>必要條件

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

- **Azure 訂閱**。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
- __Visual Studio 2013 或 2015__

### <a name="access-control-requirements"></a>存取控制需求

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>建立叢集

1. 開啟 Visual Studio 2013 或 2015年。

2. 使用下列設定值建立新的 Visual Studio 專案

  	|屬性|值|
  	|--------|-----|
  	|範本|範本/Visual C# / Windows/主控台應用程式|
  	|名稱|CreateHDICluster|

5. 從 [**工具**] 功能表中，按一下 [ **Nuget 封裝管理員**]，然後按一下**封裝管理員主控台**。

6. 在安裝套件主控台執行下列命令︰

        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package Microsoft.Azure.Management.ResourceManager -Pre
        Install-Package Microsoft.Azure.Management.HDInsight

    這些命令新增至目前的 Visual Studio 專案的.NET 文件庫和它們的參考。

6. 從方案總管中，按兩下 [ **Program.cs**以開啟它，貼上下列的程式碼，並提供變數中的值︰

        using System;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Models;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Net.Http;
        using Newtonsoft.Json;
        using System.Collections.Generic;

        namespace CreateHDInsightCluster
        {
            class Program
            {
                private static HDInsightManagementClient _hdiManagementClient;

                // Replace with your AAD tenant ID if necessary
                private const string TenantId = UserTokenProvider.CommonTenantId; 
                private const string SubscriptionId = "<Your Azure Subscription ID>";
                // This is the GUID for the PowerShell client. Used for interactive logins in this example.
                private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";

                private static string SubscriptionId = "<Enter Your Subscription ID>";
                private const string ExistingResourceGroupName = "<Enter Resource Group Name>";
                private const string ExistingStorageName = "<Enter Default Storage Account Name>.blob.core.windows.net";
                private const string ExistingStorageKey = "<Enter Default Storage Account Key>";
                private const string ExistingBlobContainer = "<Enter Default Bob Container Name>";
                private const string NewClusterName = "<Enter HDInsight Cluster Name>";
                private const int NewClusterNumNodes = 2;
                private const string NewClusterLocation = "EAST US 2";     // Must be the same as the default Storage account
                private const OSType NewClusterOSType = OSType.Linux;
                private const string NewClusterType = "Hadoop";
                private const string NewClusterVersion = "3.2";
                private const string NewClusterUsername = "admin";
                private const string NewClusterPassword = "<Enter HTTP User Password>";
                private const string NewClusterSshUserName = "sshuser";
                private const string NewClusterSshPublicKey = @"---- BEGIN SSH2 PUBLIC KEY ----
                    Comment: ""rsa-key-20150731""
                    AAAAB3NzaC1yc2EAAAABJQAAAQEA4QiCRLqT7fnmUA5OhYWZNlZo6lLaY1c+IRsp
                    gmPCsJVGQLu6O1wqcxRqiKk7keYq8bP5s30v6bIljsLZYTnyReNUa5LtFw7eauGr
                    yVt3Pve6ejfWELhbVpi0iq8uJNFA9VvRkz8IP1JmjC5jsdnJhzQZtgkIrdn3w0e6
                    WVfu15kKyY8YAiynVbdV51EB0SZaSLdMZkZQ81xi4DDtCZD7qvdtWEFwLa+EHdkd
                    pzO36Mtev5XvseLQqzXzZ6aVBdlXoppGHXkoGHAMNOtEWRXpAUtEccjpATsaZhQR
                    zZdZlzHduhM10ofS4YOYBADt9JohporbQVHM5w6qUhIgyiPo7w==
                    ---- END SSH2 PUBLIC KEY ----"; //replace the public key with your own

                static void Main(string[] args)
                {
                    System.Console.WriteLine("Creating a cluster.  The process takes 10 to 20 minutes ...");

                    // Authenticate and get a token
                    var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
                    // Flag subscription for HDInsight, if it isn't already.
                    EnableHDInsight(authToken);
                    // Get an HDInsight management client
                    _hdiManagementClient = new HDInsightManagementClient(authToken);

                    // Set parameters for the new cluster
                    var parameters = new ClusterCreateParameters
                    {
                        ClusterSizeInNodes = NewClusterNumNodes,
                        UserName = NewClusterUsername,
                        ClusterType = NewClusterType,
                        OSType = NewClusterOSType,
                        Version = NewClusterVersion,

                        DefaultStorageAccountName = ExistingStorageName,
                        DefaultStorageAccountKey = ExistingStorageKey,
                        DefaultStorageContainer = ExistingBlobContainer,

                        Password = NewClusterPassword,
                        Location = NewClusterLocation,

                        SshUserName = NewClusterSshUserName,
                        SshPublicKey = NewClusterSshPublicKey
                    };
                    // Create the cluster
                    _hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, parameters);

                    System.Console.WriteLine("The cluster has been created. Press ENTER to continue ...");
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
    
10. 類別成員值取代。

7. 按**F5**執行應用程式。 主控台視窗應該開啟，並顯示應用程式的狀態。 您也會提示您 Azure 帳戶認證。 它可能需要幾分鐘，若要建立 HDInsight 叢集，通常約 15。

## <a name="use-bootstrap"></a>使用啟動安裝程式

如需詳細資訊，請參閱[自訂 HDInsight 叢集使用啟動安裝程式](hdinsight-hadoop-customize-cluster-bootstrap.md)。

修改[建立叢集](#create-clusters)設定群組的範例︰

    static void Main(string[] args)
    {
        System.Console.WriteLine("Creating a cluster.  The process takes 10 to 20 minutes ...");

        // Authenticate and get a token
        var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
        // Flag subscription for HDInsight, if it isn't already.
        EnableHDInsight(authToken);
        // Get an HDInsight management client
        _hdiManagementClient = new HDInsightManagementClient(authToken);

        // Set parameters for the new cluster
        var extendedParameters = new ClusterCreateParametersExtended
        {
            Location = NewClusterLocation,
            Properties = new ClusterCreateProperties
            {
                ClusterDefinition = new ClusterDefinition
                {
                    ClusterType = NewClusterType.ToString()
                },
                ClusterVersion = NewClusterVersion,
                OperatingSystemType = NewClusterOSType
            }
        };

        var coreConfigs = new Dictionary<string, string>
        {
            {"fs.defaultFS", string.Format("wasbs://{0}@{1}", ExistingBlobContainer, ExistingStorageName)},
            {
                string.Format("fs.azure.account.key.{0}", ExistingStorageName),
                ExistingStorageKey
            }
        };

        // bootstrap
        var hiveConfigs = new Dictionary<string, string>
        {
            { "hive.metastore.client.socket.timeout", "90"}
        };

        var gatewayConfigs = new Dictionary<string, string>
        {
            {"restAuthCredential.isEnabled", "true"},
            {"restAuthCredential.username", NewClusterUsername},
            {"restAuthCredential.password", NewClusterPassword}
        };

        var configurations = new Dictionary<string, Dictionary<string, string>>
        {
            {"core-site", coreConfigs},
            {"gateway", gatewayConfigs},
            {"hive-site", hiveConfigs}
        };

        var serializedConfig = JsonConvert.SerializeObject(configurations);
        extendedParameters.Properties.ClusterDefinition.Configurations = serializedConfig;

        var sshPublicKeys = new List<SshPublicKey>();
        var sshPublicKey = new SshPublicKey
        {
            CertificateData =
                string.Format("ssh-rsa {0}", NewClusterSshPublicKey)
        };
        sshPublicKeys.Add(sshPublicKey);

        var headNode = new Role
        {
            Name = "headnode",
            TargetInstanceCount = 2,
            HardwareProfile = new HardwareProfile
            {
                VmSize = "Large"
            },
            OsProfile = new OsProfile
            {
                LinuxOperatingSystemProfile = new LinuxOperatingSystemProfile
                {
                    UserName = NewClusterSshUserName,
                    Password = NewClusterSshPassword //,
                    // When use a SSH pulbic key, make sure to remove comments, headers and trailers, and concatenate the key into one line 
                    //SshProfile = new SshProfile
                    //{
                    //    SshPublicKeys = sshPublicKeys
                    //}
                }
            }
        };

        var workerNode = new Role
        {
            Name = "workernode",
            TargetInstanceCount = NewClusterNumNodes,
            HardwareProfile = new HardwareProfile
            {
                VmSize = "Large"
            },
            OsProfile = new OsProfile
            {
                LinuxOperatingSystemProfile = new LinuxOperatingSystemProfile
                {
                    UserName = NewClusterSshUserName,
                    Password = NewClusterSshPassword //,
                    //SshProfile = new SshProfile
                    //{
                    //    SshPublicKeys = sshPublicKeys
                    //}
                }
            }
        };

        extendedParameters.Properties.ComputeProfile = new ComputeProfile();
        extendedParameters.Properties.ComputeProfile.Roles.Add(headNode);
        extendedParameters.Properties.ComputeProfile.Roles.Add(workerNode);

        _hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, extendedParameters);

        System.Console.WriteLine("The cluster has been created. Press ENTER to continue ...");
        System.Console.ReadLine();
    }


## <a name="use-script-action"></a>使用指令碼的巨集指令

更多 inforamtion，請參閱[自訂 Linux 型 HDInsight 叢集使用指令碼動作](hdinsight-hadoop-customize-cluster-linux.md)。

修改要撥打的指令碼動作，以安裝︰[建立叢集](#create-clusters)的範例

    static void Main(string[] args)
    {
        System.Console.WriteLine("Creating a cluster.  The process takes 10 to 20 minutes ...");

        // Authenticate and get a token
        var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
        // Flag subscription for HDInsight, if it isn't already.
        EnableHDInsight(authToken);
        // Get an HDInsight management client
        _hdiManagementClient = new HDInsightManagementClient(authToken);

        // Set parameters for the new cluster
        var parameters = new ClusterCreateParameters
        {
            ClusterSizeInNodes = NewClusterNumNodes,
            Location = NewClusterLocation,
            ClusterType = NewClusterType,
            OSType = NewClusterOSType,
            Version = NewClusterVersion,

            DefaultStorageAccountName = ExistingStorageName,
            DefaultStorageAccountKey = ExistingStorageKey,
            DefaultStorageContainer = ExistingBlobContainer,

            UserName = NewClusterUsername,
            Password = NewClusterPassword,
            SshUserName = NewClusterSshUserName,
            SshPublicKey = NewClusterSshPublicKey
        };

        ScriptAction rScriptAction = new ScriptAction("Install R",
            new Uri("https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh"), "");

        parameters.ScriptActions.Add(ClusterNodeType.HeadNode,new System.Collections.Generic.List<ScriptAction> { rScriptAction});
        parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { rScriptAction });
        
        _hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, parameters);

        System.Console.WriteLine("The cluster has been created. Press ENTER to continue ...");
        System.Console.ReadLine();
    }

##<a name="next-steps"></a>後續步驟

現在您已成功建立 HDInsight 叢集，使用下列若要瞭解如何使用叢集。 

###<a name="hadoop-clusters"></a>Hadoop 叢集

* [使用 HDInsight 的登錄區](hdinsight-use-hive.md)
* [使用 HDInsight 的豬](hdinsight-use-pig.md)
* [使用 HDInsight MapReduce](hdinsight-use-mapreduce.md)

###<a name="hbase-clusters"></a>HBase 叢集

* [在 HDInsight HBase 快速入門](hdinsight-hbase-tutorial-get-started-linux.md)
* [Java 為開發應用程式上 HDInsight HBase](hdinsight-hbase-build-java-maven-linux.md)

###<a name="storm-clusters"></a>大量叢集

* [開發上 HDInsight 大量 Java 拓撲](hdinsight-storm-develop-java-topology.md)
* [使用大量上 HDInsight Python 元件](hdinsight-storm-develop-python-topology.md)
* [部署，並監控與上 HDInsight 大量的拓撲](hdinsight-storm-deploy-monitor-topology-linux.md)

###<a name="spark-clusters"></a>火花叢集

* [建立使用 Scala 獨立應用程式](hdinsight-apache-spark-create-standalone-application.md)
* [在使用晚總火花叢集從遠端執行工作](hdinsight-apache-spark-livy-rest-interface.md)
* [使用 BI 火花︰ 執行火花 HDInsight 中使用的 BI 工具的互動式的資料分析](hdinsight-apache-spark-use-bi-tools.md)
* [與電腦學習火花︰ 使用火花 HDInsight 預測食物檢查結果中](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [火花串流︰ 使用火花 HDInsight 建置即時串流應用程式中](hdinsight-apache-spark-eventhub-streaming.md)

### <a name="run-jobs"></a>執行的工作

- [使用.NET SDK 的 HDInsight 中執行的工作區](hdinsight-hadoop-use-hive-dotnet-sdk.md)
- [使用.NET SDK 的 HDInsight 中執行的豬工作](hdinsight-hadoop-use-pig-dotnet-sdk.md)
- [使用.NET SDK 的 HDInsight 中執行 Sqoop 工作](hdinsight-hadoop-use-sqoop-dotnet-sdk.md)
- [HDInsight 中執行 Oozie 工作](hdinsight-use-oozie.md)
