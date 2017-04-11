<properties
   pageTitle="使用.NET 中 HDInsight Hadoop 豬 |Microsoft Azure"
   description="瞭解如何使用 Hadoop.NET SDK 送出上 HDInsight Hadoop 豬工作。"
   services="hdinsight"
   documentationCenter=".net"
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/17/2016"
   ms.author="larryfr"/>

#<a name="run-pig-jobs-using-the-net-sdk-for-hadoop-in-hdinsight"></a>執行 Hadoop HDInsight 中使用.NET SDK 的豬工作

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

這份文件提供使用 Hadoop.NET SDK 提交 HDInsight 叢集上 Hadoop 豬工作的範例。

HDInsight.NET SDK 提供.NET 用戶端文件庫，可讓您更輕鬆地使用.NET 從 HDInsight 叢集。 豬可讓您建立模型一系列資料轉換 MapReduce 作業。 您將學習如何使用基本的 C# 應用程式提交到 HDInsight 叢集豬工作。

## <a name="prerequisites"></a>必要條件

若要完成此文件中的步驟進行，您需要下列項目。

* Azure HDInsight (Hadoop 上 HDInsight) 叢集 ([Windows 或 Linux 為基礎)。
* Visual Studio 2012 或 2013年或 2015年。

## <a name="create-the-application"></a>建立應用程式

HDInsight.NET SDK 提供.NET 用戶端文件庫，以便從.NET HDInsight 叢集與搭配使用。 


1. 開啟 Visual Studio 2012 或 2013
2. 從 [**檔案**] 功能表中，選取 [**新增**]，然後選取**專案**。
3. 新的專案中，輸入或選取下列的值。

    <table>
    <tr>
    <th>屬性</th>
    <th>值</th>
    </tr>
    <tr>
    <th>類別</th>
    <th>範本/Visual C# / Windows</th>
    </tr>
    <tr>
    <th>範本</th>
    <th>主控台應用程式</th>
    </tr>
    <tr>
    <th>名稱</th>
    <th>SubmitPigJob</th>
    </tr>
    </table>
4. 按一下**[確定**] 以建立專案。
5. [**工具**] 功能表中，選取 [**文件庫封裝管理員**或**Nuget 封裝管理員**]，然後選取**封裝管理員主控台**。
6. 在安裝.NET SDK 封裝主控台執行下列命令。

        Install-Package Microsoft.Azure.Management.HDInsight.Job

7. 從方案總管中，按兩下 [ **Program.cs** ，將其開啟。 以下列取代現有的程式碼。

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

                    SubmitPigJob();

                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }

                private static void SubmitPigJob()
                {
                    var parameters = new PigJobSubmissionParameters
                    {
                        Query = @"LOGS = LOAD 'wasbs:///example/data/sample.log';
                                    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
                                    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
                                    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
                                    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
                                    RESULT = order FREQUENCIES by COUNT desc;
                                    DUMP RESULT;"
                    };

                    System.Console.WriteLine("Submitting the Pig job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }


7. 按**F5**以啟動應用程式。
8. 按下**ENTER**以結束應用程式]。

## <a name="summary"></a>摘要

如您所見，Hadoop.NET SDK 可讓您建立提交到 HDInsight 叢集，豬工作的.NET 應用程式，並監控工作狀態。

## <a name="next-steps"></a>後續步驟

如需豬 HDInsight 中的一般資訊。

* [Hadoop HDInsight 上搭配使用的豬](hdinsight-use-pig.md)

資訊的其他方法您可以使用 Hadoop HDInsight 上。

* [使用上 HDInsight Hadoop 登錄區](hdinsight-use-hive.md)

* [使用 MapReduce Hadoop HDInsight 上使用](hdinsight-use-mapreduce.md)[預覽] 入口網站]: https://portal.azure.com/
