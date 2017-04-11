<properties 
    pageTitle="教學課程︰ 建立含使用.NET API 複製活動的管線 |Microsoft Azure" 
    description="在本教學課程中，您建立 Azure 資料工廠管線複製活動與使用.NET API。" 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="10/27/2016" 
    ms.author="spelluru"/>

# <a name="tutorial-create-a-pipeline-with-copy-activity-using-net-api"></a>教學課程︰ 建立含使用.NET API 複製活動的管線
> [AZURE.SELECTOR]
- [概觀與先決條件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [複製精靈](data-factory-copy-data-wizard-tutorial.md)
- [Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Azure 資源管理員範本](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)


本教學課程教您如何建立和監視使用.NET API Azure 資料工廠。 資料工廠管線使用複製活動，將資料複製到 Azure SQL 資料庫 Azure Blob 儲存體。

複製活動 Azure 資料工廠執行資料移動。 全域可用服務，可以將各種資料存放區中的安全性、 可靠性，且可調整方式之間的資料複製為可活動。 如需複製活動詳細資訊，請參閱[資料移動活動](data-factory-data-movement-activities.md)文章。   

> [AZURE.NOTE] 
> 本文並未涵蓋所有資料工廠.NET API。 如需詳細資料工廠.NET SDK 資訊，請參閱[資料工廠.NET API 參考](https://msdn.microsoft.com/library/mt415893.aspx)。 

## <a name="prerequisites"></a>必要條件
- 可幫助[教學課程概觀與必要條件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)教學課程的概觀，完成的**必要**步驟。 
- Visual Studio 2012 或 2013年 2015
- 下載並安裝[Azure.NET SDK](http://azure.microsoft.com/downloads/)
- Azure PowerShell。 依照您的電腦上安裝 PowerShell 的 Azure[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)文件中的指示進行。 您可以使用 PowerShell 的 Azure 建立的 Azure Active Directory 應用程式。

### <a name="create-an-application-in-azure-active-directory"></a>Azure Active Directory 中建立的應用程式
建立 Azure Active Directory 應用程式、 建立服務主體應用程式，並將它指派給該**資料工廠參與者**的角色。  

1. 啟動**PowerShell**。 
1. 執行下列命令，然後輸入使用者名稱和密碼，您用於登入 Azure 入口網站。
    
        Login-AzureRmAccount   
2. 執行下列命令以檢視此帳戶的所有訂閱。

        Get-AzureRmSubscription 
3. 執行下列命令以選取您想要使用的訂閱。 取代**&lt;NameOfAzureSubscription** &gt; Azure 訂閱的名稱。 

        Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext

    > [AZURE.IMPORTANT] 請注意下**SubscriptionId**和**TenantId**從這個命令的輸出。 
4. 建立具名**ADFTutorialResourceGroup** PowerShell 中執行下列命令以 Azure 資源群組。  

        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    如果已經存在的資源群組，您可以指定是否要更新其 (Y) 或將其保留為 (N)。 

    如果您使用不同的資源群組，您需要在此教學課程中使用 ADFTutorialResourceGroup 取代資源群組的名稱。
5. 建立的 Azure Active Directory 應用程式。 

        $azureAdApplication = New-AzureRmADApplication -DisplayName "ADFCopyTutotiralApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfcopytutorialapp.org/example" -Password "Pass@word1"

    如果您收到下列錯誤，請指定不同的 URL，然後再次執行命令。 

        Another object with the same value for property identifierUris already exists.

6. 建立 AD 服務主要。 

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

7. 新增服務主要**資料工廠參與者**的角色。 

        New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

8. 取得應用程式識別碼。

        $azureAdApplication

    請注意下應用程式識別碼 (從輸出**applicationID** )。

您應該有下列四個值的步驟執行︰ 

- 租用戶識別碼
- 訂閱識別碼
- 應用程式識別碼 
- （指定的第一個命令） 的密碼   

## <a name="walkthrough"></a>逐步解說
1. 使用 Visual Studio 2012/2013年/2015年建立 C#.NET 主控台應用程式。
    1. 啟動 [ **Visual Studio** 2012/2013年/2015年。
    2. 按一下 [**檔案]**、 指向 [**新增**]，並按一下 [**專案**]。
    3. 展開**範本**，並選取**Visual C#**。 在此逐步解說，使用 C#，但是您可以使用任何.NET 語言。
    4. 從右側的 [專案類型清單中選取**主控台應用程式**。
    5. 您可以輸入**DataFactoryAPITestApp**的名稱。
    6. 您可以選取 [ **C:\ADFGetStarted**的位置。
    7. 按一下**[確定**] 以建立專案。
2. 按一下 [**工具]**，指向**Nuget 封裝管理員**]，按一下 [**封裝管理員主控台**。
3.  在**封裝管理員主控台**中，執行下列步驟︰ 
    1.  執行下列命令以安裝資料工廠套件︰`Install-Package Microsoft.Azure.Management.DataFactories`       
    2.  執行下列命令以安裝 Azure Active Directory 套件 （使用 Active Directory API 中的程式碼）︰`Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
6. 將下列**appSetttings** **App.config**檔案。 會使用這些設定，以協助程式方法︰ **GetAuthorizationHeader**。 

    取代值**&lt;應用程式識別碼&gt;**，**&lt;密碼&gt;**，**&lt;訂閱識別碼&gt;**，及**&lt;租用戶識別碼&gt;**使用您自己的值。 

        <?xml version="1.0" encoding="utf-8" ?>
        <configuration>
            <startup> 
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
            </startup>
            <appSettings>
                <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
                <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
                <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

                <add key="ApplicationId" value="your application ID" />
                <add key="Password" value="Password you used while creating the AAD application" />
                <add key="SubscriptionId" value= "Subscription ID" />
                <add key="ActiveDirectoryTenantId" value="Tenant ID" />
            </appSettings>
        </configuration>
6. 新增來源檔案 (Program.cs) 專案中**使用**下列陳述式。

        using System.Threading;
        using System.Configuration;
        using System.Collections.ObjectModel;
        
        using Microsoft.Azure.Management.DataFactories;
        using Microsoft.Azure.Management.DataFactories.Models;
        using Microsoft.Azure.Management.DataFactories.Common.Models;
        
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure;
6. 新增下列建立**主要**方法**DataPipelineManagementClient**類別的執行個體的程式碼。 您可以使用這個物件，建立資料工廠、 連結的服務、 輸入與輸出資料集，以及管線。 您也可以使用這個物件監控在執行階段資料集的扇形區。    

            // create data factory management client
            string resourceGroupName = "ADFTutorialResourceGroup";
            string dataFactoryName = "APITutorialFactory";

            TokenCloudCredentials aadTokenCredentials =
                new TokenCloudCredentials(
                    ConfigurationManager.AppSettings["SubscriptionId"],
                    GetAuthorizationHeader());

            Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

            DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

    > [AZURE.IMPORTANT] 
    > **ResourceGroupName**的值取代 Azure 資源群組的名稱。 
    > 
    > 更新資料工廠 (**dataFactoryName**) 是唯一的名稱。 資料 factory 名稱必須是全域唯一的。 請參閱[資料工廠-命名規則](data-factory-naming-rules.md)命名規則資料工廠成品主題。 

7. 新增下列程式碼會建立**資料工廠****主要**方法。

            // create a data factory
            Console.WriteLine("Creating a data factory");
            client.DataFactories.CreateOrUpdate(resourceGroupName,
                new DataFactoryCreateOrUpdateParameters()
                {
                    DataFactory = new DataFactory()
                    {
                        Name = dataFactoryName,
                        Location = "westus",
                        Properties = new DataFactoryProperties() { }
                    }
                }
            );

8. 新增下列程式碼會建立**Azure 儲存體連結服務****主要**方法。 

    > [AZURE.IMPORTANT] 取代**storageaccountname**和**accountkey**名稱與您的 Azure 儲存體帳戶金鑰。 

            // create a linked service for input data store: Azure Storage
            Console.WriteLine("Creating Azure Storage linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureStorageLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                        )
                    }
                }
            );
9. 新增下列程式碼會建立**Azure SQL 連結服務****主要**方法。
 
    > [AZURE.IMPORTANT] 取代您 Azure SQL server、 資料庫、 使用者和密碼的名稱**伺服器名稱**、**資料庫名稱**、**使用者名稱**和**密碼**。  

            // create a linked service for output data store: Azure SQL Database
            Console.WriteLine("Creating Azure SQL Database linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureSqlLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            new AzureSqlDatabaseLinkedService("Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30")
                        )
                    }
                }
            );
10. 新增 [建立**主要**方法**輸入與輸出資料集**的下列程式碼。 

            // create input and output datasets
            Console.WriteLine("Creating input and output datasets");
            string Dataset_Source = "DatasetBlobSource";
            string Dataset_Destination = "DatasetAzureSqlDestination";

            Console.WriteLine("Creating input dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,


            new DatasetCreateOrUpdateParameters()
            {
                Dataset = new Dataset()
                {
                    Name = Dataset_Source,
                    Properties = new DatasetProperties()
                    {
                        Structure = new List<DataElement>()
                        {
                            new DataElement() { Name = "FirstName", Type = "String" },
                            new DataElement() { Name = "LastName", Type = "String" }
                        },
                        LinkedServiceName = "AzureStorageLinkedService",
                        TypeProperties = new AzureBlobDataset()
                        {
                            FolderPath = "adftutorial/",
                            FileName = "emp.txt"
                        },
                        External = true,
                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },

                        Policy = new Policy()
                        {
                            Validation = new ValidationPolicy()
                            {
                                MinimumRows = 1
                            }
                        }
                    }
                }
            });


            Console.WriteLine("Creating output dataset of type: Azure SQL");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Destination,
                        Properties = new DatasetProperties()
                        {
                            Structure = new List<DataElement>()
                            {
                                new DataElement() { Name = "FirstName", Type = "String" },
                                new DataElement() { Name = "LastName", Type = "String" }
                            },
                            LinkedServiceName = "AzureSqlLinkedService",
                            TypeProperties = new AzureSqlTableDataset()
                            {
                                TableName = "emp"
                            },

                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },
                        }
                    }
                });

11. 新增下列程式碼，**會建立和啟動管線****主要**方法。 此管線有接收採用**BlobSource**做為來源**CopyActivity**和**BlobSink** 。

            // create a pipeline
            Console.WriteLine("Creating a pipeline");
            DateTime PipelineActivePeriodStartTime = new DateTime(2016, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
            DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
            string PipelineName = "ADFTutorialPipeline";

            client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new PipelineCreateOrUpdateParameters()
                {
                    Pipeline = new Pipeline()
                    {
                        Name = PipelineName,
                        Properties = new PipelineProperties()
                        {
                            Description = "Demo Pipeline for data transfer between blobs",

                    // Initial value for pipeline's active period. With this, you won't need to set slice status
                    Start = PipelineActivePeriodStartTime,
                            End = PipelineActivePeriodEndTime,

                            Activities = new List<Activity>()
                            {
                                new Activity()
                                {
                                    Name = "BlobToAzureSql",
                                    Inputs = new List<ActivityInput>()
                                    {
                                        new ActivityInput() {
                                            Name = Dataset_Source
                                        }
                                    },
                                    Outputs = new List<ActivityOutput>()
                                    {
                                        new ActivityOutput()
                                        {
                                            Name = Dataset_Destination
                                        }
                                    },
                                    TypeProperties = new CopyActivity()
                                    {
                                        Source = new BlobSource(),
                                        Sink = new BlobSink()
                                        {
                                            WriteBatchSize = 10000,
                                            WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                        }
                                    }
                                }
                            },
                        }
                    }
                }); 

12. 將下列程式碼新增至**主要**方法，以取得資料的扇形區輸出資料集的狀態。 有只在這個範例中預期的扇形區。   
 
            // Pulling status within a timeout threshold
            DateTime start = DateTime.Now;
            bool done = false;

            while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
            {
                Console.WriteLine("Pulling the slice status");
                // wait before the next status check
                Thread.Sleep(1000 * 12);

                var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
                    new DataSliceListParameters()
                    {
                        DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                        DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
                    });

                foreach (DataSlice slice in datalistResponse.DataSlices)
                {
                    if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
                    {
                        Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                        done = true;
                        break;
                    }
                    else
                    {
                        Console.WriteLine("Slice status is: {0}", slice.State);
                    }
                }
            }

14. 新增下列程式碼，取得執行**主要**方法的詳細資料，資料扇形區。

            Console.WriteLine("Getting run details of a data slice");

            // give it a few minutes for the output slice to be ready
            Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
            Console.ReadKey();

            var datasliceRunListResponse = client.DataSliceRuns.List(
                    resourceGroupName,
                    dataFactoryName,
                    Dataset_Destination,
                    new DataSliceRunListParameters()
                    {
                        DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
                    }
                );

            foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
            {
                Console.WriteLine("Status: \t\t{0}", run.Status);
                Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
                Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
                Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
                Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
                Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
                Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
            }

            Console.WriteLine("\nPress any key to exit.");
            Console.ReadKey();

13. 新增下列協助方法所使用的**程式**類別的**主要**方式。  
 
        public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    ClientCredential credential = new ClientCredential(ConfigurationManager.AppSettings["ApplicationId"], ConfigurationManager.AppSettings["Password"]);
                    result = context.AcquireToken(resource: ConfigurationManager.AppSettings["WindowsManagementUri"], clientCredential: credential);
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


15. 在方案總管中，展開專案 (**DataFactoryAPITestApp**)**參照**，以滑鼠右鍵按一下，然後按一下**新增的參照**。 選取 「**System.Configuration**」 組件] 核取方塊，然後按一下**[確定**]。 
16. 建立主控台應用程式。 按一下功能表上的 [**建立**，然後按一下 [**建立的解決方案**。 
16. 確認 [至少有一個檔案中有**adftutorial**容器中將 Azure blob 儲存體]。 如果不是，在記事本中使用下列內容建立**Emp.txt**檔案，然後上傳至 adftutorial 容器。

        John, Doe
        Jane, Doe
     
17. 按一下**偵錯**來執行樣本 -> 功能表上的 [**啟動偵錯**。 當您看到**快速執行資料扇形區的詳細資訊**時，請等候幾分鐘，然後按**ENTER**。 
18. 若要確認的資料工廠**APITutorialFactory**會建立包含下列成品中使用 Azure 入口網站︰ 
    - 連結服務︰ **LinkedService_AzureStorage** 
    - 資料集︰ **DatasetBlobSource**和**DatasetBlobDestination**。
    - 管線︰ **PipelineBlobSample** 
18. 驗證在兩個員工記錄會建立 「**emp**」 資料表中指定的 Azure SQL 資料庫。

## <a name="next-steps"></a>後續步驟

- 閱讀[資料移動活動](data-factory-data-movement-activities.md)文章提供您在本教學課程中使用複製活動的相關詳細的資訊。
- 如需詳細資料工廠.NET SDK 資訊，請參閱[資料工廠.NET API 參考](https://msdn.microsoft.com/library/mt415893.aspx)。 本文並未涵蓋所有資料工廠.NET API。 

 
