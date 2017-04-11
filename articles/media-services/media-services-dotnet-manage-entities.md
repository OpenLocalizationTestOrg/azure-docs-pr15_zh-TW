
<properties 
    pageTitle="管理資產和相關的實體與媒體服務.NET SDK" 
    description="瞭解如何管理.NET 資產和媒體服務 SDK 與相關項目。" 
    authors="juliako" 
    manager="dwrede" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016"
    ms.author="juliako"/>


#<a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>管理資產和相關的實體與媒體服務.NET SDK


> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-manage-entities.md)
- [其餘](media-services-rest-manage-entities.md)


本主題說明如何完成下列媒體服務管理工作︰

- 取得的資產參照 
- 取得工作參考 
- 列出所有資產 
- 工作清單和資產 
- 列出所有存取原則 
- 列出所有定位器
- 列舉大量的項目
- 刪除資產 
- 刪除工作 
- 刪除存取原則 

##<a name="prerequisites"></a>必要條件 

請參閱[設定您的環境](media-services-set-up-computer.md)

##<a name="get-an-asset-reference"></a>取得資產參照

常用的工作是取得媒體服務中的現有資產的參考。 下列範例會顯示如何快速資產參照從伺服器上的資產集合內容物件，根據資產的識別碼。
下列範例會使用 Linq 查詢，取得現有 IAsset 物件的參考。

    static IAsset GetAsset(string assetId)
    {
        // Use a LINQ Select query to get an asset.
        var assetInstance =
            from a in _context.Assets
            where a.Id == assetId
            select a;
        // Reference the asset as an IAsset.
        IAsset asset = assetInstance.FirstOrDefault();
    
        return asset;
    }

##<a name="get-a-job-reference"></a>取得工作參考

當您使用處理媒體服務代碼中的工作時，您通常需要取得參照至現有的工作根據 id。 下列範例會示範如何取得工作收集 IJob 物件的參考。
WarningWarning 您可能需要啟動長編碼作業時，收到工作參照，以及要檢查執行緒上的工作狀態。 在這種情況下，當方法傳回往來，您需要擷取工作的重新整理的參照。

    static IJob GetJob(string jobId)
    {
        // Use a Linq select query to get an updated 
        // reference by Id. 
        var jobInstance =
            from j in _context.Jobs
            where j.Id == jobId
            select j;
        // Return the job reference as an Ijob. 
        IJob job = jobInstance.FirstOrDefault();
    
        return job;
    }

##<a name="list-all-assets"></a>列出所有的資產

當您在儲存空間中有的資產的數規模擴大時，還是有幫助您的資產的清單。 下列範例會示範如何逐一資產集合伺服器物件上。 使用每個資產，程式碼範例也會將其屬性值部分主控台。 例如，每個資產可以包含多個媒體檔案。 在程式碼範例寫出每個資產相關聯的所有檔案。



    static void ListAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);
    
        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();
    
        foreach (IAsset asset in _context.Assets)
        {
            // Display the collection of assets.
            builder.AppendLine("");
            builder.AppendLine("******ASSET******");
            builder.AppendLine("Asset ID: " + asset.Id);
            builder.AppendLine("Name: " + asset.Name);
            builder.AppendLine("==============");
            builder.AppendLine("******ASSET FILES******");
    
            // Display the files associated with each asset. 
            foreach (IAssetFile fileItem in asset.AssetFiles)
            {
                builder.AppendLine("Name: " + fileItem.Name);
                builder.AppendLine("Size: " + fileItem.ContentFileSize);
                builder.AppendLine("==============");
            }
        }
    
        // Display output in console.
        Console.Write(builder.ToString());
    }

##<a name="list-jobs-and-assets"></a>工作清單與資產

重要的相關的任務是與他們相關聯的工作中媒體服務清單資產。 下列範例會示範如何] 清單中的每個 IJob 物件，然後每個工作，會顯示工作的相關內容、 所有相關的工作，所有輸入資產，以及所有輸出資產。 在此範例中的程式碼會有許多其他任務的幫助的。 例如，如果您想要的輸出資產，從一或多個您先前已執行的編碼工作] 清單中，將此程式碼會示範如何存取輸出資產。 如果您有輸出資產的參照，然後以下載，或提供 Url 發表內容至其他的使用者或應用程式。 

更多選項可將資產的詳細資訊，請參閱[使用.net 媒體服務 SDK 發表的資產](media-services-deliver-streaming-content.md)。

    // List all jobs on the server, and for each job, also list 
    // all tasks, all input assets, all output assets.

    static void ListJobsAndAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);
    
        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();
    
        foreach (IJob job in _context.Jobs)
        {
            // Display the collection of jobs on the server.
            builder.AppendLine("");
            builder.AppendLine("******JOB*******");
            builder.AppendLine("Job ID: " + job.Id);
            builder.AppendLine("Name: " + job.Name);
            builder.AppendLine("State: " + job.State);
            builder.AppendLine("Order: " + job.Priority);
            builder.AppendLine("==============");
    
    
            // For each job, display the associated tasks (a job  
            // has one or more tasks). 
            builder.AppendLine("******TASKS*******");
            foreach (ITask task in job.Tasks)
            {
                builder.AppendLine("Task Id: " + task.Id);
                builder.AppendLine("Name: " + task.Name);
                builder.AppendLine("Progress: " + task.Progress);
                builder.AppendLine("Configuration: " + task.Configuration);
                if (task.ErrorDetails != null)
                {
                    builder.AppendLine("Error: " + task.ErrorDetails);
                }
                builder.AppendLine("==============");
            }
    
            // For each job, display the list of input media assets.
            builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
            foreach (IAsset inputAsset in job.InputMediaAssets)
            {
    
                if (inputAsset != null)
                {
                    builder.AppendLine("Input Asset Id: " + inputAsset.Id);
                    builder.AppendLine("Name: " + inputAsset.Name);
                    builder.AppendLine("==============");
                }
            }
    
            // For each job, display the list of output media assets.
            builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
            foreach (IAsset theAsset in job.OutputMediaAssets)
            {
                if (theAsset != null)
                {
                    builder.AppendLine("Output Asset Id: " + theAsset.Id);
                    builder.AppendLine("Name: " + theAsset.Name);
                    builder.AppendLine("==============");
                }
            }
    
        }
    
        // Display output in console.
        Console.Write(builder.ToString());
    }

##<a name="list-all-access-policies"></a>列出所有存取原則

在媒體服務中，您可以定義存取原則資產或其檔案。 存取原則可定義資產 （何種類型的存取權，並持續時間） 或檔案的權限。 在媒體服務代碼中，您通常定義存取原則所建立的 IAccessPolicy 物件，然後將它關聯現有的資產。 然後，您會建立 ILocator 物件，讓您直接存取提供媒體服務中的資產。 Visual Studio 專案隨附本文件系列中包含多個程式碼範例，顯示如何建立及指定存取原則和定位器資產。

下列範例會示範如何在伺服器上的所有存取原則] 都清單，並顯示的每個相關聯的權限類型。 實用的另一種方法檢視存取原則是清單在伺服器上的所有 ILocator 物件，然後的每一個定位，您可以列出其相關聯的存取原則使用其 AccessPolicy 屬性。

    static void ListAllPolicies()
    {
        foreach (IAccessPolicy policy in _context.AccessPolicies)
        {
            Console.WriteLine("");
            Console.WriteLine("Name:  " + policy.Name);
            Console.WriteLine("ID:  " + policy.Id);
            Console.WriteLine("Permissions: " + policy.Permissions);
            Console.WriteLine("==============");
    
        }
    }

##<a name="list-all-locators"></a>列出所有定位器

定位器是提供存取資產，以及資產的權限，定義搜尋的相關聯的存取原則直接路徑的 URL。 每個資產可以有其定位器屬性與其相關聯的 ILocator 物件的集合。 伺服器內容也有包含所有定位器定位器集合。

下列範例會列出所有定位器在伺服器上。 針對每個定位，其會顯示相關的資產和存取原則的識別碼。 也會資產顯示類型的權限、 到期日和完整路徑。

請注意，資產定位器路徑基底 URL 資產。 若要建立的使用者或應用程式瀏覽到的個別檔案直接路徑，您的程式碼必須定位器路徑中新增的特定檔案路徑。 如需有關如何進行此動作的詳細資訊，請參閱主題[與媒體服務 SDK.net 發表的資產](media-services-deliver-streaming-content.md)。

    static void ListAllLocators()
    {
        foreach (ILocator locator in _context.Locators)
        {
            Console.WriteLine("***********");
            Console.WriteLine("Locator Id: " + locator.Id);
            Console.WriteLine("Locator asset Id: " + locator.AssetId);
            Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
            Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
            Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
            // The locator path is the base or parent path (with included permissions) to access  
            // the media content of an asset. To create a full URL to a specific media file, take 
            // the locator path and then append a file name and info as needed.  
            Console.WriteLine("Locator base path: " + locator.Path);
            Console.WriteLine("");
        }
    }

## <a name="enumerating-through-large-collections-of-entities"></a>列舉大量的項目

當查詢項目，就會傳回一次，因為公用其餘 v2 限制 1000年結果的查詢結果的實體 1000年的限制。 您需要時列舉大量的實體，使用 [略過和記錄。 
    
下列函數迴圈中提供的媒體服務帳戶的所有工作。 媒體服務會傳回 1000年工作的工作集合中。 此函數會使用 [略過和記錄，以確保所有工作會列舉 （如果您的帳戶中有 1000 個以上的工作）。
    
    static void ProcessJobs()
    {
        try
        {
    
            int skipSize = 0;
            int batchSize = 1000;
            int currentBatch = 0;
    
            while (true)
            {
                // Loop through all Jobs (1000 at a time) in the Media Services account
                IQueryable _jobsCollectionQuery = _context.Jobs.Skip(skipSize).Take(batchSize);
                foreach (IJob job in _jobsCollectionQuery)
                {
                    currentBatch++;
                    Console.WriteLine("Processing Job Id:" + job.Id);
                }
    
                if (currentBatch == batchSize)
                {
                    skipSize += batchSize;
                    currentBatch = 0;
                }
                else
                {
                    break;
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }

##<a name="delete-an-asset"></a>刪除資產

下列範例會刪除資產。

    static void DeleteAsset( IAsset asset)
    {
        // delete the asset
        asset.Delete();
    
        // Verify asset deletion
        if (GetAsset(asset.Id) == null)
            Console.WriteLine("Deleted the Asset");
    
    }

##<a name="delete-a-job"></a>刪除工作

若要刪除的工作，您必須檢查工作狀態，[狀態] 屬性中的指示。 可以刪除已完成或已取消的工作，而在特定其他狀態，例如佇列、 排程，或處理的工作，必須先取消，然後刪除。
下列範例會顯示檢查工作狀態，然後刪除時狀態已完成或取消刪除工作的方法。 將此程式碼取決於前一節中的工作的參考本主題︰ 取得工作的參考。

    static void DeleteJob(string jobId)
    {
        bool jobDeleted = false;
    
        while (!jobDeleted)
        {
            // Get an updated job reference.  
            IJob job = GetJob(jobId);
    
            // Check and handle various possible job states. You can 
            // only delete a job whose state is Finished, Error, or Canceled.   
            // You can cancel jobs that are Queued, Scheduled, or Processing,  
            // and then delete after they are canceled.
            switch (job.State)
            {
                case JobState.Finished:
                case JobState.Canceled:
                case JobState.Error:
                    // Job errors should already be logged by polling or event 
                    // handling methods such as CheckJobProgress or StateChanged.
                    // You can also call job.DeleteAsync to do async deletes.
                    job.Delete();
                    Console.WriteLine("Job has been deleted.");
                    jobDeleted = true;
                    break;
                case JobState.Canceling:
                    Console.WriteLine("Job is cancelling and will be deleted "
                        + "when finished.");
                    Console.WriteLine("Wait while job finishes canceling...");
                    Thread.Sleep(5000);
                    break;
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    job.Cancel();
                    Console.WriteLine("Job is scheduled or processing and will "
                        + "be deleted.");
                    break;
                default:
                    break;
            }
    
        }
    }


##<a name="delete-an-access-policy"></a>刪除存取原則

下列範例會示範如何取得根據原則 Id，存取原則的參照，然後刪除原則。

    static void DeleteAccessPolicy(string existingPolicyId)
    {
        // To delete a specific access policy, get a reference to the policy.  
        // based on the policy Id passed to the method.
        var policyInstance =
                from p in _context.AccessPolicies
                where p.Id == existingPolicyId
                select p;
        IAccessPolicy policy = policyInstance.FirstOrDefault();
    
        policy.Delete();
    
    }
    


##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
