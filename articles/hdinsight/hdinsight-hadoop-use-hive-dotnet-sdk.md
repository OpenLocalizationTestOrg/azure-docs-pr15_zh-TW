<properties
    pageTitle="執行使用 HDInsight.NET SDK 的登錄區查詢 |Microsoft Azure"
    description="瞭解如何將提交到 Azure HDInsight Hadoop 使用 HDInsight.NET SDK Hadoop 工作。"
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

# <a name="run-hive-queries-using-hdinsight-net-sdk"></a>執行使用 HDInsight.NET SDK 的登錄區查詢

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]


瞭解如何提交使用 HDInsight.NET SDK 的登錄區查詢。

> [AZURE.NOTE] 從 Windows 用戶端，必須執行本文中的步驟進行。 使用登錄區使用 Linux、 OS X 或 Unix 用戶端資訊，請使用上方的文件中顯示的定位點選取器。

##<a name="prerequisites"></a>必要條件

這份文件之前，您必須具備下列項目︰

- **Hadoop 叢集 HDInsight 中**。 請參閱[開始使用 Linux 為基礎的 Hadoop HDInsight 中](hdinsight-use-sqoop.md#create-cluster-and-sql-database)。
- **Visual Studio 2012/2013年/2015年**。

##<a name="submit-hive-queries-using-hdinsight-net-sdk"></a>送出使用 HDInsight.NET SDK 的登錄區查詢

HDInsight.NET SDK 提供.NET 用戶端文件庫，以便從.NET HDInsight 叢集與搭配使用。 

**送出工作**

1. 在 Visual Studio 建立 C# 主控台應用程式。
2. 從 [Nuget 封裝管理員主控台中，執行下列命令。

        Install-Package Microsoft.Azure.Management.HDInsight.Job

2. 使用下列程式碼︰

        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading;
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

                private const string DefaultStorageAccountName = "<Default Storage Account Name>";
                private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
                private const string DefaultStorageContainerName = "<Default Blob Container Name>";

                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");

                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                    SubmitHiveJob();

                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }

                private static void SubmitHiveJob()
                {
                    Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "ravi" }, { "hive.exec.reducers.max", "1" } };
                    List<string> args = new List<string> { { "argA" }, { "argB" } };
                    var parameters = new HiveJobSubmissionParameters
                    {
                        Query = "SHOW TABLES",
                        Defines = defines,
                        Arguments = args
                    };

                    System.Console.WriteLine("Submitting the Hive job to the cluster...");
                    var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
                    var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                    System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                    System.Console.WriteLine("JobId is " + jobId);

                    System.Console.WriteLine("Waiting for the job completion ...");

                    // Wait for job completion
                    var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    while (!jobDetail.Status.JobComplete)
                    {
                        Thread.Sleep(1000);
                        jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    }

                    // Get job output
                    var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                        DefaultStorageContainerName);
                    var output = (jobDetail.ExitValue == 0)
                        ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                        : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure

                    System.Console.WriteLine("Job output is: ");

                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
                }
            }
        }

5. 按**F5**執行應用程式。


## <a name="next-steps"></a>後續步驟

本文中，您已經學會多種方式可以建立 HDInsight 叢集。 若要深入瞭解，請參閱下列文章︰

* [開始使用 Azure HDInsight][hdinsight-get-started]
* [建立 HDInsight Hadoop 叢集][hdinsight-provision]
* [使用 [Azure 入口網站管理 Hadoop 叢集 HDInsight 中](hdinsight-administer-use-management-portal.md)
* [HDInsight.NET SDK 參考](https://msdn.microsoft.com/library/mt271028.aspx)
* [使用 HDInsight 的豬](hdinsight-use-pig.md)
* [使用 HDInsight Sqoop](hdinsight-use-sqoop-mac-linux.md)
* [建立非互動式驗證.NET HDInsight 應用程式](hdinsight-create-non-interactive-authentication-dotnet-applications.md)


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md


