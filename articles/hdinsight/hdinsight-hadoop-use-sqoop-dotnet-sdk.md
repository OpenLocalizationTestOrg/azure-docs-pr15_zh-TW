<properties
    pageTitle="HDInsight 中使用 Hadoop Sqoop |Microsoft Azure"
    description="瞭解如何使用 HDInsight.NET SDK 執行 Sqoop 匯入及匯出 Hadoop 叢集和 Azure SQL 資料庫之間。"
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
   ms.date="09/14/2016"
    ms.author="jgao"/>

#<a name="run-sqoop-jobs-using-net-sdk-for-hadoop-in-hdinsight"></a>執行 Hadoop HDInsight 中使用.NET SDK Sqoop 工作

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

瞭解如何使用 HDInsight.NET SDK 中匯入及匯出 HDInsight 叢集和 Azure SQL 資料庫或 SQL Server 資料庫之間的 HDInsight 執行 Sqoop 工作。

> [AZURE.NOTE] 本文中的步驟，可以使用 [Windows 或 Linux HDInsight 叢集;不過，這些步驟只會從 Windows 用戶端運作。 若要選擇其他方法使用上方的本文定位點選取器。

###<a name="prerequisites"></a>必要條件

本教學課程之前，您必須具備下列項目︰

- **Hadoop 叢集 HDInsight 中**。 請參閱[建立叢集和 SQL 資料庫](hdinsight-use-sqoop.md#create-cluster-and-sql-database)。

## <a name="run-sqoop-using-net-sdk"></a>執行 Sqoop 使用.NET SDK

HDInsight.NET SDK 提供.NET 用戶端文件庫，以便從.NET HDInsight 叢集與搭配使用。 在此區段中，您將建立 C# 主控台應用程式，將 hivesampletable 匯出至您之前在本教學課程中所建立的 SQL 資料庫資料表。

**提交 Sqoop 工作**

1. 在 Visual Studio 建立 C# 主控台應用程式。
2. 從 Visual Studio 封裝管理員主控台中，執行下列 Nuget 命令以匯入套件。

        Install-Package Microsoft.Azure.Management.HDInsight.Job
        
3. 使用下列程式碼 Program.cs 檔案中︰

        using System.Collections.Generic;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;
        
        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;
        
                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";
        
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
        
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
        
                    SubmitSqoopJob();
        
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
        
                private static void SubmitSqoopJob()
                {
                    var sqlDatabaseServerName = "<SQLDatabaseServerName>";
                    var sqlDatabaseLogin = "<SQLDatabaseLogin>";
                    var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                    var sqlDatabaseDatabaseName = "<DatabaseName>";
        
                    var tableName = "<TableName>";
                    var exportDir = "/tutorials/usesqoop/data";
        
                    // Connection string for using Azure SQL Database.
                    // Comment if using SQL Server
                    var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                    // Connection string for using SQL Server.
                    // Uncomment if using SQL Server
                    //var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
        
                    var parameters = new SqoopJobSubmissionParameters
                    {
                        Files = new List<string> { "/user/oozie/share/lib/sqoop/sqljdbc41.jar" }, // This line is required for Linux-based cluster.
                        Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
                    };
        
                    System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }
        
4. 若要執行程式按**F5** 。 

##<a name="limitations"></a>限制

* 大量匯出-使用 Linux 型 HDInsight、 用來將資料匯出至 Microsoft SQL Server Azure SQL 資料庫或 Sqoop 連接器目前不支援大量插入。

* 批次處理-Linux 型 HDInsight，與使用時`-batch`切換執行插入時，Sqoop 會執行，而不是批次處理插入作業的多個插入。

##<a name="next-steps"></a>後續步驟

現在您已經學會如何使用 Sqoop。 若要深入瞭解，請參閱︰

- [使用 Oozie 與 HDInsight](hdinsight-use-oozie.md): Oozie 工作流程中的使用 Sqoop 動作。
- [分析航班延遲情況資料增添使用 HDInsight](hdinsight-analyze-flight-delay-data.md)︰ 使用登錄區來分析航班延遲資料，，然後使用 Sqoop 將資料匯出至 Azure SQL 資料庫。
- [上傳至 HDInsight 的資料](hdinsight-upload-data.md)︰ 尋找上傳至 HDInsight/Azure Blob 儲存體的資料的其他方法。


