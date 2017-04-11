<properties
    pageTitle="管理.NET SDK 串流分析 |Microsoft Azure"
    description="開始使用串流分析管理.NET SDK。 瞭解如何設定，並執行分析工作︰ 建立專案、 輸入、 輸出以及轉換。"
    keywords=".net sdk，您可以分析 API"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>管理.NET SDK︰ 設定，並執行分析工作使用.NET Azure 資料流分析 API

瞭解如何設定使用使用管理.NET SDK 的.NET 資料流分析 API 執行的分析工作。 設定專案、 建立輸入與輸出來源、 轉換，以及開始及停止工作。 對於您分析工作，您可以串流 Blob 儲存體或事件中樞的資料。

請參閱[管理.NET 資料流分析 API 的參考文件](https://msdn.microsoft.com/library/azure/dn889315.aspx)。

Azure 資料流分析是完全受管理的服務，提供低延遲可用性、 縮放、 複雜的事件處理透過串流雲端中的資料。 資料流分析可讓客戶設定來分析資料流串流工作，並讓他們附近即時分析磁碟機。  


## <a name="prerequisites"></a>必要條件
這份文件之前，您必須具備下列項目︰

- 安裝 Visual Studio 2012 或 2013年。
- 下載並安裝[Azure.NET SDK](https://azure.microsoft.com/downloads/)。
- 建立您的訂閱中的 Azure 資源群組。 以下是範例 Azure PowerShell 指令碼。 PowerShell 的 Azure 資訊，請參閱[安裝及設定 PowerShell 的 Azure](../powershell-install-configure.md);  


        # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group
        Select-AzureSubscription -SubscriptionName <subscription name>

            # If Stream Analytics has not been registered to the subscription, remove the remark symbol (#) to run the Register-AzureRMProvider cmdlet to register the provider namespace
            #Register-AzureRMProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>


-   將輸入的來源和目標輸出設定為使用。 無法進一步的指示請參閱[新增輸入](stream-analytics-add-inputs.md)設定範例輸入[新增輸出](stream-analytics-add-outputs.md)設定輸出範例。


## <a name="set-up-a-project"></a>設定專案

若要建立分析工作會使用資料流分析 API.net，先設定您的專案。

1. 建立 Visual Studio C#.NET 主控台應用程式。
2. 在封裝管理員主控台中，執行下列命令以安裝 NuGet 套件。 第一個是 Azure 資料流分析管理.NET SDK。 第二個是將會用於驗證的 Azure Active Directory 用戶端。

        Install-Package Microsoft.Azure.Management.StreamAnalytics
        Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

4. 將下列**和 appSettings** App.config 檔案︰

        <appSettings>
          <!--CSM Prod related values-->
          <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
          <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
          <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
          <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
          <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
          <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION" />
          <add key="ActiveDirectoryTenantId" value="YOU TENANT ID" />
        </appSettings>


    以取代值**SubscriptionId**和**ActiveDirectoryTenantId** Azure 訂閱，租用戶識別碼。 您可以執行下列 Azure PowerShell cmdlet，以取得這些值︰

        Get-AzureAccount

5. 將下列**使用**陳述式新增至專案中的來源檔案 (Program.cs):

        using System;
        using System.Configuration;
        using System.Threading;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

6.  新增驗證協助方法︰

        public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(
                        ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                        ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    result = context.AcquireToken(
                        resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                        clientId: ConfigurationManager.AppSettings["AsaClientId"],
                        redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                        promptBehavior: PromptBehavior.Always);
                }
                catch (Exception threadEx)
                {
                    Console.WriteLine(threadEx.Message);
                }
            });

            thread.SetApartmentState(ApartmentState.STA);
            thread.Name = "AcquireTokenThread";
            thread.Start();
            thread.Join();

            if (result != null)
            {
                return result.AccessToken;
            }

            throw new InvalidOperationException("Failed to acquire token");
        }  


## <a name="create-a-stream-analytics-management-client"></a>建立資料流分析管理用戶端

**StreamAnalyticsManagementClient**物件可讓您管理工作和工作元素，例如輸入與輸出轉換。

將下列程式碼新增至**主要**方法開頭中︰

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";
    string streamAnalyticsInputName = "<YOUR JOB INPUT NAME>";
    string streamAnalyticsOutputName = "<YOUR JOB OUTPUT NAME>";
    string streamAnalyticsTransformationName = "<YOUR JOB TRANSFORMATION NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    // Create Stream Analytics management client
    StreamAnalyticsManagementClient client = new StreamAnalyticsManagementClient(aadTokenCredentials);

**ResourceGroupName**變數的值應該是您建立，或挑選中的必要步驟中的 [資源] 群組的名稱相同。

若要自動建立工作的認證簡報外觀，請參閱[驗證服務主要與 Azure 資源管理員](../resource-group-authenticate-service-principal.md)。

這篇文章的其他章節假設此程式碼是在**主要**方法的開頭。

## <a name="create-a-stream-analytics-job"></a>建立資料流分析工作

下列程式碼會建立在您所定義的 [資源] 群組的資料流分析工作。 您會將輸入、 輸出和轉換加入工作更新版本。

    // Create a Stream Analytics job
    JobCreateOrUpdateParameters jobCreateParameters = new JobCreateOrUpdateParameters()
    {
        Job = new Job()
        {
            Name = streamAnalyticsJobName,
            Location = "<LOCATION>",
            Properties = new JobProperties()
            {
                EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Adjust,
                Sku = new Sku()
                {
                    Name = "Standard"
                }
            }
        }
    };

    JobCreateOrUpdateResponse jobCreateResponse = client.StreamingJobs.CreateOrUpdate(resourceGroupName, jobCreateParameters);


## <a name="create-a-stream-analytics-input-source"></a>建立資料流分析輸入的來源

下列程式碼會建立 blob 輸入的來源類型與 CSV 序列化串流分析輸入的來源。 若要建立的事件中心輸入的來源，請使用**EventHubStreamInputDataSource** ，而不是**BlobStreamInputDataSource**。 同樣地，您可以自訂 [輸入來源序列化的類型。

    // Create a Stream Analytics input source
    InputCreateOrUpdateParameters jobInputCreateParameters = new InputCreateOrUpdateParameters()
    {
        Input = new Input()
        {
            Name = streamAnalyticsInputName,
            Properties = new StreamInputProperties()
            {
                Serialization = new CsvSerialization
                {
                    Properties = new CsvSerializationProperties
                    {
                        Encoding = "UTF8",
                        FieldDelimiter = ","
                    }
                },
                DataSource = new BlobStreamInputDataSource
                {
                    Properties = new BlobStreamInputDataSourceProperties
                    {
                        StorageAccounts = new StorageAccount[]
                        {
                            new StorageAccount()
                            {
                                AccountName = "<YOUR STORAGE ACCOUNT NAME>",
                                AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
                            }
                        },
                        Container = "samples",
                        PathPattern = ""
                    }
                }
            }
        }
    };

    InputCreateOrUpdateResponse inputCreateResponse =
        client.Inputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobInputCreateParameters);

輸入的來源，從 Blob 儲存體或 [事件] 中心內，會與指定的工作。 若要使用相同的輸入的來源為不同的工作，您必須再次呼叫的方法，並指定不同的名稱。


## <a name="test-a-stream-analytics-input-source"></a>測試資料流分析的輸入的來源

**TestConnection**方法測試資料流分析作業是否能夠連線到輸入的來源，以及其他特定輸入的來源類型。 例如，在您在先前的步驟建立 blob 輸入的來源，方法將核取儲存體帳戶名稱和索引鍵組合可以用來連線到儲存的帳戶，以及檢查存在指定的容器。

    // Test input source connection
    DataSourceTestConnectionResponse inputTestResponse =
        client.Inputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsInputName);

## <a name="create-a-stream-analytics-output-target"></a>建立資料流分析輸出目標

若要建立資料流分析輸入的來源非常類似，建立輸出目標。 輸入的來源，例如輸出目標會與指定的工作。 若要使用的相同的輸出目標不同的工作，您必須再次呼叫的方法，並指定不同的名稱。

下列程式碼會建立輸出目標 （Azure SQL 資料庫）。 您可以自訂的輸出目標的資料類型及/或序列化類型。

    // Create a Stream Analytics output target
    OutputCreateOrUpdateParameters jobOutputCreateParameters = new OutputCreateOrUpdateParameters()
    {
        Output = new Output()
        {
            Name = streamAnalyticsOutputName,
            Properties = new OutputProperties()
            {
                DataSource = new SqlAzureOutputDataSource()
                {
                    Properties = new SqlAzureOutputDataSourceProperties()
                    {
                        Server = "<YOUR DATABASE SERVER NAME>",
                        Database = "<YOUR DATABASE NAME>",
                        User = "<YOUR DATABASE LOGIN>",
                        Password = "<YOUR DATABASE LOGIN PASSWORD>",
                        Table = "<YOUR DATABASE TABLE NAME>"
                    }
                }
            }
        }
    };

    OutputCreateOrUpdateResponse outputCreateResponse =
        client.Outputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobOutputCreateParameters);

## <a name="test-a-stream-analytics-output-target"></a>測試資料流分析輸出目標

資料流分析輸出目標也有測試連線的**TestConnection**方法。

    // Test output target connection
    DataSourceTestConnectionResponse outputTestResponse =
        client.Outputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsOutputName);

## <a name="create-a-stream-analytics-transformation"></a>建立資料流分析的轉換

下列程式碼會建立與查詢的資料流分析轉換 「 選取 * 輸入 」，並指定一個串流單位配置串流分析作業。 如需有關如何調整串流單位的詳細資訊，請參閱[比例 Azure 資料流分析工作](stream-analytics-scale-jobs.md)。


    // Create a Stream Analytics transformation
    TransformationCreateOrUpdateParameters transformationCreateParameters = new TransformationCreateOrUpdateParameters()
    {
        Transformation = new Transformation()
        {
            Name = streamAnalyticsTransformationName,
            Properties = new TransformationProperties()
            {
                StreamingUnits = 1,
                Query = "select * from Input"
            }
        }
    };

    var transformationCreateResp =
        client.Transformations.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, transformationCreateParameters);

輸入與輸出，例如轉換連結到特定的資料流分析工作底下建立。

## <a name="start-a-stream-analytics-job"></a>啟動資料流分析作業
建立資料流分析工作、 input(s)、 輸出，以及轉換之後, 您可以開始工作呼叫**開始**的方法。

下列範例資料流分析工作自訂輸出開始時間 2012 年 12 月 12 日 12:12:12 設定的程式碼啟動 UTC:

    // Start a Stream Analytics job
    JobStartParameters jobStartParameters = new JobStartParameters
    {
        OutputStartMode = OutputStartMode.CustomTime,
        OutputStartTime = new DateTime(2012, 12, 12, 0, 0, 0, DateTimeKind.Utc)
    };

    LongRunningOperationResponse jobStartResponse = client.StreamingJobs.Start(resourceGroupName, streamAnalyticsJobName, jobStartParameters);



## <a name="stop-a-stream-analytics-job"></a>停止串流分析作業
您可以停止執行中的資料流分析工作呼叫**停止**的方法。

    // Stop a Stream Analytics job
    LongRunningOperationResponse jobStopResponse = client.StreamingJobs.Stop(resourceGroupName, streamAnalyticsJobName);

## <a name="delete-a-stream-analytics-job"></a>刪除資料流分析工作
工作，以及基礎子資源，包括 input(s)、 輸出和轉換的工作，將會刪除 [**刪除**] 方法。

    // Delete a Stream Analytics job
    LongRunningOperationResponse jobDeleteResponse = client.StreamingJobs.Delete(resourceGroupName, streamAnalyticsJobName);


## <a name="get-support"></a>取得支援
進一步協助，請嘗試我們[Azure 資料流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。


## <a name="next-steps"></a>後續步驟

您已學習使用.NET SDK 建立並執行分析工作的基本概念。 若要深入瞭解，請參閱下列各項︰

- [Azure 資料流分析簡介](stream-analytics-introduction.md)
- [開始使用 Azure 資料流狀況分析](stream-analytics-get-started.md)
- [不按比例縮放 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure 資料流分析管理.NET SDK](https://msdn.microsoft.com/library/azure/dn889315.aspx)。
- [Azure 資料流分析查詢語言參照](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 資料流分析管理 REST API 參照](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
