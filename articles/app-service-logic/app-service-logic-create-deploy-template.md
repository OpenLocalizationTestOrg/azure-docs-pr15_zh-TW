<properties
   pageTitle="建立邏輯應用程式部署範本 |Microsoft Azure"
   description="瞭解如何建立邏輯應用程式部署範本，並使用此版本管理"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="create-a-logic-app-deployment-template"></a>建立邏輯應用程式部署範本

邏輯應用程式建立後，您可能要建立另存為 Azure 資源管理員範本。 如此一來，您可以輕鬆地部署邏輯應用程式的任何環境或資源群組，您可能需要在位置。 資源管理員範本簡介資訊，請務必取出[撰寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)及[部署資源透過 Azure 資源管理員範本](../resource-group-template-deploy.md)上的文件。

## <a name="logic-app-deployment-template"></a>邏輯應用程式部署範本

邏輯應用程式具有三個基本元件︰

* **邏輯應用程式資源**。 這項資源包含項目，例如價格計劃、 位置和工作流程定義的資訊。
* **工作流程定義**。 這是什麼所示程式碼檢視。 包含定義的流程及如何引擎應該執行的步驟。 這是`definition`邏輯應用程式資源的屬性。
* **連線**。 這些是不同的資源的安全地儲存任何連接器連接，例如連線字串和存取權杖相關的中繼資料。 您可以參照中的邏輯應用程式中的這些`parameters`邏輯應用程式資源的區段。

您可以檢視現有的邏輯應用程式的下列設備組件的所有使用如[Azure 資源檔案總管](http://resources.azure.com)] 工具。

若要讓邏輯應用程式與資源群組部署搭配使用的範本，必須先定義的資源，並視需要參數化。 例如，如果您部署至開發、 測試和生產環境，可能會想要每個環境中使用不同的連接字串到 SQL 資料庫。 或者，您可能會想要部署中不同的訂閱] 或 [資源群組。  

## <a name="create-a-logic-app-deployment-template"></a>建立邏輯應用程式部署範本

有有效的邏輯應用程式部署範本的最簡單方法是使用[Visual Studio 工具邏輯應用程式](./app-service-logic-deploy-from-vs.md)。  Visual Studio 工具產生有效的部署範本，可在任何訂閱或位置。

一些其他工具可協助您建立邏輯應用程式部署範本。 您可以撰寫以手動方式，也就是使用已討論視需要建立參數的資源。 另一個選項是使用[邏輯應用程式範本建立者](https://github.com/jeffhollan/LogicAppTemplateCreator)PowerShell 模組。 邏輯應用程式，並使用的，且然後會產生範本以供部署必要的參數的資源的連線，先評估此開啟來源模組。 例如，如果您有一邏輯應用程式，從 Azure 服務匯流排佇列中接收訊息，並將資料新增到 Azure SQL 資料庫，工具就會保留所有的協調流程邏輯並參數化 SQL 和服務匯流排連接字串，讓他們可以在部署設定。

>[AZURE.NOTE] 連線必須在同一個資源群組作為邏輯應用程式。

### <a name="install-the-logic-app-template-powershell-module"></a>安裝邏輯應用程式範本 PowerShell 模組

安裝模組的最簡單方法是透過[PowerShell 圖庫](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1)中，使用命令`Install-Module -Name LogicAppTemplate`。  

您也可以 PowerShell 模組手動安裝︰

1. 下載最新版本的[邏輯應用程式範本建立者](https://github.com/jeffhollan/LogicAppTemplateCreator/releases)。  
1. 擷取 PowerShell 模組] 資料夾中的資料夾 (通常`%UserProfile%\Documents\WindowsPowerShell\Modules`)。

使用任何租用戶和訂閱的存取模組權杖，我們建議您的[ARMClient](https://github.com/projectkudu/ARMClient)命令列工具使用。  此[部落格文章](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html)的討論 ARMClient 的更多詳細資料。

### <a name="generate-a-logic-app-template-by-using-powershell"></a>使用 PowerShell 產生邏輯應用程式範本

PowerShell 安裝之後，您可以使用下列命令來產生範本︰

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId`Azure 訂閱識別碼。 這一行第一次取得存取權杖透過 ARMClient，然後管道，透過 PowerShell 指令碼，然後建立 JSON 檔案中的 [範本。

## <a name="add-parameters-to-a-logic-app-template"></a>新增參數至邏輯應用程式範本

建立您的邏輯應用程式範本後，您可以繼續新增或修改您可能需要的參數。 例如，如果您定義包含 Azure 函數或巢狀您計劃部署單一部署中的工作流程的資源識別碼，您可以將更多資源新增至您的範本，並參數化識別碼，視需要。 相同適用於任何自訂 Api 或 Swagger 參照預期部署與每個資源群組的結束點。

## <a name="deploy-a-logic-app-template"></a>部署邏輯應用程式範本

您可以使用任何數字的工具，包括 PowerShell、 REST API、 Visual Studio 發行管理和 Azure 入口網站範本部署部署您的範本。 請參閱本文中相關的其他資訊的[部署使用 Azure 資源管理員範本的資源](../resource-group-template-deploy.md)。 此外，建議您建立[參數檔案](../resource-group-template-deploy.md#parameter-file)以儲存的參數值。

### <a name="authorize-oauth-connections"></a>授權 OAuth 連線

部署之後，邏輯應用程式的運作端對端有效的參數。 不過，OAuth 連線仍會需要授權產生的有效存取權杖。 您可以開啟在設計工具中的邏輯應用程式，然後授權連線。 或者，如果您想要自動化，您可以使用指令碼同意每一種 OAuth 連線。 有是 GitHub [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth)專案下一個範例指令碼。

## <a name="visual-studio-release-management"></a>Visual Studio 版本管理

常見的案例的部署及管理環境是邏輯應用程式部署範本搭配使用的工具，例如 Visual Studio 版本管理 」。 Visual Studio 小組服務包含[部署 Azure 資源群組](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup)工作，您可以新增至任何建置或放開管道的郵件。 您必須具備部署，授權[服務本金](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)，然後您可以產生發行定義。

1. 在發行管理] 中，若要建立新的定義，選取**空白**，啟動與空白的定義。

    ![建立新的空白定義][1]   

1. 選擇您需要的資源。 這可能會產生手動或建立程序的一部分邏輯應用程式範本。
1. 新增**Azure 資源群組部署**工作。
1. 設定與[主要的服務](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)，以及參照的範本與範本參數的檔案。
1. 繼續記錄其他環境、 自動化的測試]，或需要核准者發行程序中的步驟。

<!-- Image References -->
[1]: ./media/app-service-logic-create-deploy-template/emptyReleaseDefinition.PNG
