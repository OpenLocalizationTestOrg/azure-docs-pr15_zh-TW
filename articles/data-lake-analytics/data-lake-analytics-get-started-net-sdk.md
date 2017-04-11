<properties 
   pageTitle="開始使用 Azure 資料湖分析使用.NET SDK |Azure" 
   description="瞭解如何使用.NET SDK 建立資料湖存放帳戶，請建立資料湖分析工作，並提交 U SQL 所撰寫的工作。 " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/26/2016"
   ms.author="edmaca"/>

# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-net-sdk"></a>教學課程︰ 開始使用.NET SDK Azure 資料湖分析

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


瞭解如何使用 Azure.NET SDK 提交寫入[U](data-lake-analytics-u-sql-get-started.md) sql 資料湖分析資料的工作。 如需有關資料湖分析的詳細資訊，請參閱[Azure 資料湖分析概觀](data-lake-analytics-overview.md)。

在本教學課程中，您會開發 C# 主控台應用程式提交 U SQL 作業，讀取] 索引標籤的分隔值 (TSV) 檔案，並將其轉換成逗點分隔的值 (CSV) 檔案。 若要移到同一個教學課程中使用其他支援的工具，請按一下 [這份文件頂端的索引標籤。

##<a name="prerequisites"></a>必要條件

本教學課程之前，您必須具備下列項目︰

- **Visual Studio 2015、 Visual Studio 2013 更新 4 或使用 Visual c + + 安裝的 Visual Studio 2012**。
- **Microsoft Azure SDK.net 2.5 版或上方**。  安裝並使用[Web 平台安裝程式](http://www.microsoft.com/web/downloads/platform.aspx)。
- **Azure 資料湖分析帳戶**。 請參閱[管理資料湖分析使用 Azure.NET SDK](data-lake-analytics-manage-use-dotnet-sdk.md)。

##<a name="create-console-application"></a>建立主控台應用程式

在本教學課程中，您可以處理某些搜尋記錄檔。  搜尋記錄可以儲存在資料湖市集或 Azure Blob 儲存體。 

範例搜尋記錄檔找公用 Azure Blob 容器中。 應用程式中，您會將檔案下載到您的工作站，然後將檔案上傳至您的資料湖 Analytics 帳戶的預設資料湖存放區帳戶。

**若要建立 U SQL 指令碼**

資料湖分析工作語言撰寫的 U SQL。 若要進一步瞭解 U SQL，請參閱[開始使用 U SQL 語言](data-lake-analytics-u-sql-get-started.md)和[U SQL 語言參考](http://go.microsoft.com/fwlink/?LinkId=691348)。

下列 U SQL 指令碼，建立**SampleUSQLScript.txt**檔案，並將檔案中的 **C:\temp\**路徑。  在下一個程序中建立的.NET 應用程式中的硬式編碼時的路徑。  

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();
    
    OUTPUT @searchlog   
        TO "/Output/SearchLog-from-Data-Lake.csv"
    USING Outputters.Csv();

這個 U SQL 指令碼會讀取使用**Extractors.Tsv()**的來源資料檔案，，然後建立使用**Outputters.Csv()**csv 檔案。 

C# 程式中，您需要準備**/Samples/Data/SearchLog.tsv**檔案和**/Output/**資料夾。    

會更容易使用的檔案儲存在預設資料湖帳戶的相對路徑。 您也可以使用絕對的路徑。  例如 

    adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
    
若要存取連結的儲存帳戶檔案，您必須使用絕對的路徑。  在連結的 Azure 儲存體帳戶儲存的檔案的語法是︰

    wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

>[AZURE.NOTE] 目前有 Azure 資料湖服務的已知的問題。  如果 [範例] 應用程式會中斷，或發生錯誤，您可能需要手動刪除資料湖存放與資料湖分析的指令碼會建立帳戶。  如果您不熟悉 Azure 入口網站，[管理 Azure 資料湖分析使用 Azure 入口網站](data-lake-analytics-manage-use-portal.md)本指南可帶您入門。       

**若要建立的應用程式**

1. 開啟 Visual Studio。
2. 建立 C# 主控台應用程式。
3. 開啟 NuGet 套件管理主控台，然後執行下列命令︰

        Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
        Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
        Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package WindowsAzure.Storage


       
5. 在 Program.cs，貼上下列的程式碼︰

        using System;
        using System.IO;
        using System.Collections.Generic;
        using System.Threading;
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.StoreUploader;
        using Microsoft.Azure.Management.DataLake.Analytics;
        using Microsoft.Azure.Management.DataLake.Analytics.Models;
        using Microsoft.WindowsAzure.Storage.Blob;

        namespace SdkSample
        {
          class Program
          {
            private const string SUBSCRIPTIONID = "<Enter Your Azure Subscription ID>";
            private const string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
            private const string DOMAINNAME = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.

            private static string _adlaAccountName = "<Enter an Existing Data Lake Analytics Account Name>";
            private static string _adlsAccountName = "<Enter the default Data Lake Store Account Name>";

            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
            private static DataLakeAnalyticsJobManagementClient _adlaJobClient;
        
            private static void Main(string[] args)
            {
                string localFolderPath = @"c:\temp\";

                // Connect to Azure
                var creds = AuthenticateAzure(DOMAINNAME, CLIENTID);

                SetupClients(creds, SUBSCRIPTIONID);

                // Transfer the source file from a public Azure Blob container to Data Lake Store.
                CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://adltutorials.blob.core.windows.net/adls-sample-data/SearchLog.tsv"));
                blob.DownloadToFile(localFolderPath + "SearchLog.tsv", FileMode.Create); // from WASB
                UploadFile(localFolderPath + "SearchLog.tsv", "/Samples/Data/SearchLog.tsv"); // to ADLS
                WaitForNewline("Source data file prepared.", "Submitting a job.");


                // Submit the job
                Guid jobId = SubmitJobByPath(localFolderPath + "SampleUSQLScript.txt", "My First ADLA Job");
                WaitForNewline("Job submitted.", "Waiting for job completion.");

                // Wait for job completion
                WaitForJob(jobId);
                WaitForNewline("Job completed.", "Downloading job output.");

                // Download job output
                DownloadFile(@"/Output/SearchLog-from-Data-Lake.csv", localFolderPath + "SearchLog-from-Data-Lake.csv");
        
                WaitForNewline("Job output downloaded. You can now exit.");
            }
        
            public static ServiceClientCredentials AuthenticateAzure(
                string domainName,
                string nativeClientAppCLIENTID)
            {
                // User login via interactive popup
                SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
                // Use the client ID of an existing AAD "Native Client" application.
                var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
                return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
            }

            public static void SetupClients(ServiceClientCredentials tokenCreds, string subscriptionId)
            {
                _adlaClient = new DataLakeAnalyticsAccountManagementClient(tokenCreds);
                _adlaClient.SubscriptionId = subscriptionId;

                _adlaJobClient = new DataLakeAnalyticsJobManagementClient(tokenCreds);

                _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(tokenCreds);
            }

            public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
            {
                var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
                var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
                var uploader = new DataLakeStoreUploader(parameters, frontend);
                uploader.Execute();
            }

            public static void DownloadFile(string srcPath, string destPath)
            {
                var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
                var fileStream = new FileStream(destPath, FileMode.Create);

                stream.CopyTo(fileStream);
                fileStream.Close();
                stream.Close();
            }

            // Helper function to show status and wait for user input
            public static void WaitForNewline(string reason, string nextAction = "")
            {
                Console.WriteLine(reason + "\r\nPress ENTER to continue...");

                Console.ReadLine();

                if (!String.IsNullOrWhiteSpace(nextAction))
                    Console.WriteLine(nextAction);
            }


            // List all Data Lake Analytics accounts within the subscription
            public static List<DataLakeAnalyticsAccount> ListADLAAccounts()
            {
                var response = _adlaClient.Account.List();
                var accounts = new List<DataLakeAnalyticsAccount>(response);

                while (response.NextPageLink != null)
                {
                    response = _adlaClient.Account.ListNext(response.NextPageLink);
                    accounts.AddRange(response);
                }

                Console.WriteLine("You have %i Data Lake Analytics account(s).", accounts.Count);
                for (int i = 0; i < accounts.Count; i++)
                {
                    Console.WriteLine(accounts[i].Name);
                }

                return accounts;
            }
            public static Guid SubmitJobByPath(string scriptPath, string jobName)
            {
                var script = File.ReadAllText(scriptPath);

                var jobId = Guid.NewGuid();
                var properties = new USqlJobProperties(script);
                var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
                var jobInfo = _adlaJobClient.Job.Create(_adlaAccountName, jobId, parameters);

                return jobId;
            }

            public static JobResult WaitForJob(Guid jobId)
            {
                var jobInfo = _adlaJobClient.Job.Get(_adlaAccountName, jobId);
                while (jobInfo.State != JobState.Ended)
                {
                    jobInfo = _adlaJobClient.Job.Get(_adlaAccountName, jobId);
                }
                return jobInfo.Result.Value;
            }
          }
        }

6. 按**F5**執行應用程式。 輸出就像︰

    ![Azure 資料湖分析工作 U SQL.NET SDK 輸出](./media/data-lake-analytics-get-started-net-sdk/data-lake-analytics-dotnet-job-output.png)

7. 核取 [輸出檔案。  預設路徑和檔名為 c:\Temp\SearchLog-from-Data-Lake.csv。

## <a name="see-also"></a>另請參閱

- 若要查看同一個教學課程中使用其他工具，請按一下 [上方的頁面] 索引標籤選擇器]。
- 若要查看較複雜的查詢，請參閱[使用 Azure 資料湖分析分析網站記錄](data-lake-analytics-analyze-weblogs.md)。
- 若要開始開發 U SQL 應用程式，請參閱[使用資料湖工具的 Visual Studio 開發的 U SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)。
- 若要瞭解 U SQL，請參閱[開始使用 Azure 資料湖分析 U SQL 語言](data-lake-analytics-u-sql-get-started.md)和[U SQL 語言參考](http://go.microsoft.com/fwlink/?LinkId=691348)。
- 管理工作，請參閱[管理 Azure 資料湖分析使用 Azure 入口網站](data-lake-analytics-manage-use-portal.md)。
- 若要取得資料湖分析的概觀，請參閱[Azure 資料湖分析概觀](data-lake-analytics-overview.md)。
