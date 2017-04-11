<properties
    pageTitle="應用程式服務 API 應用程式的中繼資料的 API 搜索與程式碼產生 |Microsoft Azure"
    description="了解如何 Azure 應用程式服務中的 API 應用程式會使用 Swagger 中繼資料為了產生的 API 探索和程式碼。"
    services="app-service\api"
    documentationCenter=".net"
    authors="tdykstra"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-api"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="rachelap"/>

# <a name="app-service-api-apps-metadata-for-api-discovery-and-code-generation"></a>產生的 API 探索和程式碼的應用程式服務 API 應用程式中繼資料 

支援[Swagger 2.0](http://swagger.io/) API 中繼資料是內建應用程式服務 API 應用程式。 您沒有使用 Swagger，但如果您使用它，您可以利用更輕鬆地探索及耗用的 API 應用程式功能。   

## <a name="swagger-endpoint"></a>Swagger 端點

您可以指定的 API 中的應用程式的 API 應用程式的屬性會提供 Swagger 2.0 JSON 中繼資料端點。 端點可相對於基底 API 應用程式的 URL 或絕對 URL。 絕對 Url 可以指向外部 API 應用程式]。 

許多下游用戶端 （適用於範例、 Visual Studio 程式碼的產生及 PowerApps 」 新增 API 」 流程） URL 必須公開存取 （不是由使用者或服務驗證保護）。 這表示，如果您使用應用程式服務驗證，想要在您的應用程式本身的 API 定義的方式公開，您需要使用允許匿名流量連絡您的 API 的驗證選項。 如需詳細資訊，請參閱[驗證與授權的應用程式服務 API 應用程式](app-service-api-authentication.md)。

### <a name="portal-blade"></a>入口網站刀

[Azure 入口網站](https://portal.azure.com/)中可以看見及**API 定義**刀上變更端點 URL。

![](./media/app-service-api-metadata/apidefblade.png)

### <a name="azure-resource-manager-property"></a>Azure 資源管理員屬性

您也可以利用[資源檔案總管](https://resources.azure.com/)或[Azure 資源管理員範本](../resource-group-authoring-templates.md)中的命令列工具，例如[PowerShell 的 Azure](../powershell-install-configure.md)和[Azure CLI](../xplat-cli-install.md)設定 API 應用程式的 API 定義 URL。 

在 [**資源檔案總管**] 中，移至 [**訂閱 > {您的訂閱} > resourceGroups > {資源群組} > 提供者 > Microsoft.Web > 網站 > {網站} > 設定 > web**，，您會看到`apiDefinition`屬性︰

        "apiDefinition": {
          "url": "https://contactslistapi.azurewebsites.net/swagger/docs/v1"
        }

如需設定 Azure 資源管理員範本的範例`apiDefinition`] 屬性中，開啟[azuredeploy.json 待辦事項清單範例應用程式中的檔案](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json)。 尋找看起來像 JSON 樣本如上所示的範本] 區段。

### <a name="default-value"></a>預設值

當您使用 Visual Studio 建立 API 應用程式時，API 定義端點會自動設定為基礎的 URL 加上的 API 應用程式`/swagger/docs/v1`。 這是預設的 URL， [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) NuGet 套件用來做為 ASP.NET Web API 專案的動態產生的 Swagger 中繼資料。 

## <a name="code-generation"></a>產生程式碼

其中一個優點 Swagger 整合 Azure API 應用程式會自動產生程式碼。 產生的用戶端類別讓您更容易撰寫程式碼，通話 API 應用程式。

使用 Visual Studio 或從命令列，您可以產生 API 應用程式的用戶端程式碼。 如需如何在 Visual Studio 中的用戶端類別產生 ASP.NET Web API 專案的相關資訊，請參閱[開始使用 API 應用程式與 ASP.NET](app-service-api-dotnet-get-started.md#codegen)。 如需如何將其從命令列所有的支援語言的相關資訊，請參閱[Azure/autorest](https://github.com/azure/autorest)存放庫的讀我檔案上 GitHub.com。
 
## <a name="next-steps"></a>後續步驟

引導您建立的逐步教學課程，部署及分成 API 應用程式]，請參閱[Azure 應用程式服務中的 API 應用程式快速入門](app-service-api-dotnet-get-started.md)。

如果您使用 Azure API 管理的 API 應用程式時，您就可以將 API 管理匯入您的 API 使用 Swagger 中繼資料。 如需詳細資訊，請參閱[如何匯入作業 Azure API 管理的 API 的定義](../api-management/api-management-howto-import-api.md)。 
