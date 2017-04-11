<properties
    pageTitle="Azure 機器學習 Web 服務︰ 部署及消耗 |Microsoft Azure"
    description="部署及使用 web 服務的資源。"
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="raymondl"
    editor=""/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="v-donglo"/>

# <a name="azure-machine-learning-web-services-deployment-and-consumption"></a>Azure 機器學習 Web 服務︰ 部署及消耗

您可以使用 Azure 電腦學習部署電腦學習工作流程與模型為 web 服務。 這些 web 服務可用從應用程式撥打電腦學習模型，透過網際網路進行預測即時或批次模式。 因為 web 服務 RESTful，您可以從各種不同的程式設計語言與平台，例如.NET 和 Java，以及 Excel 等應用程式進行通話。

下一節中提供的逐步解說，程式碼和文件，以協助您開始的連結。

## <a name="deploy-a-web-service"></a>部署 web 服務

### <a name="with-azure-machine-learning-studio"></a>使用 Azure 機器學習 Studio

電腦學習 Studio 與 Microsoft Azure 機器學習 Web 服務入口網站，可協助您部署及管理 web 服務無須撰寫程式碼。

下列連結提供關於部署新的 web 服務的一般資訊︰

* 如需有關如何部署新的 web 服務為基礎的 Azure 資源管理員的概觀，請參閱[部署新的 web 服務](machine-learning-webservice-deploy-a-web-service.md)。
* 如需部署 web 服務的逐步解說，請參閱[部署 Azure 電腦學習 web 服務](machine-learning-publish-a-machine-learning-web-service.md)。
* 如需如何建立並部署 web 服務的完整逐步解說，請參閱[逐步解說步驟 1︰ 建立電腦學習工作區](machine-learning-walkthrough-1-create-ml-workspace.md)。
* 部署 web 服務的特定範例，請參閱︰

    * [逐步解說步驟 5︰ 部署 Azure 電腦學習 web 服務](machine-learning-walkthrough-5-publish-web-service.md)
    * [部署到多個區域的 web 服務的方式](machine-learning-how-to-deploy-to-multiple-regions.md)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Web 服務資源提供者 Api (Azure 資源管理員 Api)

Web 服務的 Azure 電腦學習資源提供者可讓部署及管理 web 服務的使用 REST API 呼叫。 如需詳細資訊，請參閱 MSDN 上的[其他電腦學習 Web 服務 （部分）](https://msdn.microsoft.com/library/azure/mt767538.aspx)參考。

### <a name="with-powershell-cmdlets"></a>使用 PowerShell cmdlet

Azure web 服務的電腦學習資源提供者可讓部署及管理 web 服務的使用 PowerShell cmdlet。

若要使用 cmdlet，您必須先登入您 Azure 帳戶從 PowerShell 環境中使用[新增 AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx)指令程式。 如果您不熟悉如何呼叫所依據的資源管理員的 PowerShell 命令，請參閱[使用 PowerShell 的 Azure 與 Azure 資源管理員](../powershell-azure-resource-manager.md#login-to-your-azure-account)。

若要匯出的預測實驗，使用[此範例程式碼](https://github.com/ritwik20/AzureML-WebServices)。 建立.exe 檔案的程式碼之後，您可以輸入︰

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

執行應用程式建立 web 服務 JSON 範本。 若要部署 web 服務使用的範本，您必須新增下列資訊︰

* 儲存體帳戶名稱和鍵

    從[Azure 入口網站](https://portal.azure.com/)或[Azure 傳統入口網站](http://manage.windowsazure.com/)，您可以取得儲存體帳戶名稱和鍵。
* 認可計劃識別碼

    您可以取得從[Azure 機器學習 Web 服務](https://services.azureml.net)入口網站的計劃識別碼登入，並按一下方案名稱。

將其新增至 JSON 範本為子項目*MachineLearningWorkspace*節點的同一層級的 [*屬性*] 節點。

以下是範例︰

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

請參閱下列文章和程式碼範例，如需詳細資料︰

* MSDN 上的[Azure 電腦學習 Cmdlet]( https://msdn.microsoft.com/library/azure/mt767952.aspx)參考
* GitHub 範例[逐步解說](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt)

## <a name="consume-the-web-services"></a>使用 web 服務

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>從 Azure 機器學習 Web 服務 （測試） 的使用者介面

您可以測試您的 web 服務從 Azure 機器學習 Web 服務入口網站。 包括測試要求回應服務 （給），以及批次執行服務 (BES) 介面。

* [部署新的 web 服務](machine-learning-webservice-deploy-a-web-service.md)
* [部署 Azure 電腦學習 web 服務](machine-learning-publish-a-machine-learning-web-service.md)
* [逐步解說步驟 5︰ 部署 Azure 電腦學習 web 服務](machine-learning-walkthrough-5-publish-web-service.md)

### <a name="from-excel"></a>從 Excel

您可以下載可使用 web 服務的 Excel 範本︰

* [使用 Excel 中的 Azure 電腦學習 web 服務](machine-learning-consuming-from-excel.md)
* [Excel 增益集 Azure 機器學習 Web 服務](machine-learning-excel-add-in-for-web-services.md)


### <a name="from-a-rest-based-client"></a>從其他型的用戶端

Azure 機器學習 Web 服務是 RESTful 的 Api。 您可以使用這些 Api 從各種不同的平台，例如.NET、 Python、 R，Java 等等。您在[Microsoft Azure 機器學習 Web 服務入口網站](https://services.azureml.net)上的 web 服務的**消耗**頁面有範例程式碼，可協助您開始使用。 如需詳細資訊，請參閱[如何使用的電腦，學習實驗從已部署 Azure 電腦學習 web 服務](machine-learning-consume-web-services.md)。

