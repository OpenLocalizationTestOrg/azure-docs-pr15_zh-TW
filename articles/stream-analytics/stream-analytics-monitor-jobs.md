<properties
    pageTitle="以程式設計方式監控上串流分析工作 |Microsoft Azure"
    description="瞭解如何以程式設計方式監視透過 REST Api、 Azure sdk，您可以或 Powershell 所建立的資料流分析作業。"
    keywords=".net 監視器，作業監視器，監視應用程式"
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


# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>以程式設計方式建立串流分析作業監視器
 本文將示範如何啟用監控串流分析工作。 資料流分析工作建立透過 REST Api、 Azure sdk，您可以或 Powershell 執行有監控預設不啟用。  您可以手動啟用此 Azure 入口網站中，瀏覽至工作的 [監視器] 頁面，按一下 [啟用] 按鈕，或您可以遵循本文中的連線，自動執行此程序。 監視資料會顯示在 [串流分析工作 Azure 入口網站中的 「 監控 」 索引標籤。

![作業監視器工作] 索引標籤](./media/stream-analytics-monitor-jobs/stream-analytics-monitor-jobs-tab.png)

## <a name="prerequisites"></a>必要條件
這份文件之前，您必須具備下列項目︰

- Visual Studio 2012 或 2013年。
- 下載並安裝[Azure.NET SDK](https://azure.microsoft.com/downloads/)。
- 現有的資料流分析工作所需監控啟用。

## <a name="setup-a-project"></a>設定專案

1.  建立 Visual Studio C#.Net 主控台應用程式。
2.  在封裝管理員主控台中，執行下列命令以安裝 NuGet 套件。 第一個是 Azure 資料流分析管理.NET SDK。 第二個是 Azure 監視器 SDK 會用來啟用監視。 最後一個是將會用於驗證的 Azure Active Directory 用戶端。

    ```
    Install-Package Microsoft.Azure.Management.StreamAnalytics
    Install-Package Microsoft.Azure.Insights -Pre
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

3.  將下列和 appSettings App.config 檔案。

    ```
    <appSettings>
        <!--CSM Prod related values-->
        <add key="ResourceGroupName" value="RESOURCE GROUP NAME" />
        <add key="JobName" value="YOUR JOB NAME" />
        <add key="StorageAccountName" value="YOUR STORAGE ACCOUNT"/>
        <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
        <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
        <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
        <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
        <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
        <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION ID" />
        <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
    </appSettings>
    ```
以取代值*SubscriptionId*和*ActiveDirectoryTenantId* Azure 訂閱，租用戶識別碼。 您可以執行下列 PowerShell cmdlet，以取得這些值︰

    ```
    Get-AzureAccount
    ```
4.  將下列文字新增至專案中的來源檔案 (Program.cs) 使用陳述式。

    ```
        using System;
        using System.Configuration;
        using System.Threading;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.Insights;
        using Microsoft.Azure.Management.Insights.Models;
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```
5.  新增驗證協助方法。

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

## <a name="create-management-clients"></a>建立管理用戶端
下列程式碼會設定的必要的變數和管理用戶端。

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>啟用監視現有的資料流分析工作

下列程式碼會啟用監視**現有**的資料流分析工作。 程式碼的第一個部分會執行 GET 要求資料流分析服務所擷取特定的資料流分析工作的相關資訊。 它會使用 「 識別碼 」 屬性 （從 GET 要求擷取） 做為參數放入方法在第二個傳送放入的程式碼的下半部要求獲得深入見解服務啟用串流分析工作監控。

> [AZURE.WARNING]
> 如果您先前已啟用監控為不同的資料流分析工作，可以透過 Azure 入口網站或以程式設計方式透過以下程式碼，**建議您提供相同的儲存體帳戶名稱時您先前已啟用監控。**
>
> 儲存帳戶已連結至您建立您的資料流分析工作中的區域，而不是本身的工作。
>
> 所有的資料流分析工作 （及所有其他 Azure 資源） 的同一個區域中共用此儲存監視資料的儲存空間帳戶。 如果您提供不同的儲存帳戶時，它可能會導致非預期的側邊效果的其他資料流分析工作及/或其他 Azure 資源監控。
>
> 用來取代儲存體帳戶名稱```“<YOUR STORAGE ACCOUNT NAME>”```以下應該位於資料流分析工作您同一份訂閱的儲存空間帳戶啟用監視。

    // Get an existing Stream Analytics job
    JobGetParameters jobGetParameters = new JobGetParameters()
    {
        PropertiesToExpand = "inputs,transformation,outputs"
    };
    JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);

    // Enable monitoring
    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
    {
            Properties = new ServiceDiagnosticSettings()
            {
                StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
            }
    };
    insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);



## <a name="get-support"></a>取得支援
進一步協助，請嘗試我們[Azure 資料流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)。


## <a name="next-steps"></a>後續步驟

- [Azure 資料流分析簡介](stream-analytics-introduction.md)
- [開始使用 Azure 資料流狀況分析](stream-analytics-get-started.md)
- [不按比例縮放 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure 資料流分析查詢語言參照](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 資料流分析管理 REST API 參照](https://msdn.microsoft.com/library/azure/dn835031.aspx)
