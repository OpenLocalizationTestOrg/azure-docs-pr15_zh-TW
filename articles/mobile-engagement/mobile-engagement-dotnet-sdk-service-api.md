<properties 
    pageTitle="使用.NET SDK 存取 Azure 行動互動服務 Api" 
    description="說明如何使用行動互動.NET SDK 存取 Azure 行動互動服務 Api"        
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="erikre" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-multiple" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="using-net-sdk-to-access-azure-mobile-engagement-service-apis"></a>使用.NET SDK 存取 Azure 行動互動服務 Api

Azure 行動互動公開 Api，以管理裝置，一組達到/推入行銷活動等。若要互動這些 Api，我們也提供您[Swagger 檔案](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-mobileengagement/2014-12-01/swagger/mobile-engagement.json)可以使用工具來產生 Sdk 慣用的語言。 我們建議使用[AutoRest](https://github.com/Azure/AutoRest)工具來產生 SDK 從我們的 Swagger 檔案。 

我們以類似的方式可讓您使用這些 Api 使用 C# 包裝紙建立.NET SDK，您不需要執行驗證的權杖交涉並重新整理您自己。  

此範例會經歷的步驟，以便使用.NET SDK 設定︰

1. 首先，您需要設定您的 api 使用為所述[以下](mobile-engagement-api-authentication.md#authentication)的 Azure Active Directory 驗證。 結尾的步驟，您應具備有效**SubscriptionId**、 **TenantId**、 **ApplicationId**和**密碼**。 

2. 我們會使用簡單的 Windows 主控台應用程式來說明如何使用.NET SDK 建立公告行銷活動的案例。 因此，開啟 Visual Studio 並建立**主控台應用程式**。   

3. 接下來您需要下載.NET SDK 為 Nuget 圖庫中的**Microsoft Azure 互動管理文件庫**中可使用[以下](https://www.nuget.org/packages/Microsoft.Azure.Management.Engagement/)。
如果您從 Visual Studio 安裝 Nuget，您需要確認您已核取標記**包含搶鮮版**選項搜尋套件時︰

    ![][1]

4. 在 [`Program.cs`檔案中加入下列命名空間︰

        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.Engagement;
        using Microsoft.Azure.Management.Engagement.Models;

5. 接下來，您必須定義下列常數，我們將會用於驗證並與其互動行動互動應用程式中建立宣告行銷活動︰

        // For authentication
        const string TENANT_ID = "<Your TenantId>";
        const string CLIENT_ID = "<Your Application Id>";
        const string CLIENT_SECRET = "<Your Secret>";
        const string SUBSCRIPTION_ID = "<Your Subscription Id>";

        // This is the Azure Resource group concept for grouping together resources 
        //  see here: https://azure.microsoft.com/en-us/documentation/articles/resource-group-portal/
        const string RESOURCE_GROUP = "";

        // For Mobile Engagement operations
        // App Collection Name 
        const string APP_COLLECTION_NAME = "";
        // Application Resource Name - make sure you are using the one as specified in the Azure portal (NOT the App Name)
        const string APP_RESOURCE_NAME = "";

6. 定義`EngagementManagementClient`變數，我們將用來呼叫行動互動 SDK 方法︰

        static EngagementManagementClient engagementClient; 

7. 新增下列您`Main`方法︰

        try
            {
                // Initialize the Engagement SDK to call out APIs. 
                InitEngagementClient().Wait();

                // Create a Reach campaign
                CreateCampaign().Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.InnerException.Message);
                throw ex;
            }

8. 定義負責初始化的下列方法`EngagementManagementClient`第一次驗證，然後將本身關聯的計劃建立公告行銷活動的互動 Mobile 應用程式︰

        private static async Task InitEngagementClient()
        {
            var credentials = await ApplicationTokenProvider.LoginSilentAsync(TENANT_ID, CLIENT_ID, CLIENT_SECRET);
            engagementClient = new EngagementManagementClient(credentials) { SubscriptionId = SUBSCRIPTION_ID };
            
            // This is the Azure concept of ResourceGroup
            engagementClient.ResourceGroupName = RESOURCE_GROUP;

            // This is your Mobile Engagement App Collection & App Resource Name in which you create the campaign
            engagementClient.AppCollection = APP_COLLECTION_NAME;
            engagementClient.AppName = APP_RESOURCE_NAME;
        }

    > [AZURE.IMPORTANT] 請注意，您需要使用**應用程式資源名稱**Azure 管理入口網站應用程式名稱參數中所定義。 

9. 最後，定義會負責建立簡單**隨時**使用先前初始化的 EngagementClient CreateCampaign 方法 & **專用通知**使用的標題和郵件行銷活動︰ 

        private async static Task CreateCampaign()
        {
            //  Refer to the Announcement Campaign format from here - 
            //      https://msdn.microsoft.com/en-us/library/azure/mt683751.aspx
            // Make sure you are passing all the non-optional parameters
            Campaign parameters = new Campaign(
                name:"WelcomeCampaign",
                notificationTitle: "Welcome", 
                notificationMessage: "Thank you for installing the app!",
                type:"only_notif",
                deliveryTime:"any"
                );

            // Refer to the Campaign Kinds from here - https://msdn.microsoft.com/en-us/library/azure/mt683742.aspx
            CampaignStateResult result = 
                await engagementClient.Campaigns.CreateAsync(CampaignKinds.Announcements, parameters);
            Console.WriteLine("Campaign Id '{0}' was created successfully and it is in '{1}' state", result.Id, result.State);
        }

10. 執行主控台應用程式，您會看到下列成功建立的行銷活動︰

    **已成功建立行銷活動識別碼 」 159 」，它是在 「 草稿 」 狀態**

<!-- Images. -->

[1]: ./media/mobile-engagement-dotnet-sdk-service-api/include-prerelease.png
