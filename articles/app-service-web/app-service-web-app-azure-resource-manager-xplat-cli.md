<properties
    pageTitle="Azure azure 資源管理員型跨平台命令列工具的 Web 應用程式 |Microsoft Azure"
    description="瞭解如何使用新 Azure 資源管理員型跨平台命令列工具來管理您的 Azure Web 應用程式。"
    services="app-service\web"
    documentationCenter=""
    authors="ahmedelnably"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="aelnably"/>

# <a name="using-azure-resource-manager-based-xplat-cli-for-azure-web-app"></a>使用 Azure 資源管理員型 XPlat CLI Azure Web 應用程式#

> [AZURE.SELECTOR]
- [Azure CLI](app-service-web-app-azure-resource-manager-xplat-cli.md)
- [Azure PowerShell](app-service-web-app-azure-resource-manager-powershell.md)

Microsoft Azure 跨平台命令列工具版本 0.10.5 版本已新增新的命令。 這些命令讓使用者能夠使用 Azure 資源管理員為基礎的 PowerShell 命令，管理 Web 應用程式。

若要瞭解管理資源群組，請參閱[使用 Azure CLI 管理 Azure 資源與資源群組](../xplat-cli-azure-resource-manager.md)。 


## <a name="managing-app-service-plans"></a>管理應用程式服務方案 ##

### <a name="create-an-app-service-plan"></a>建立應用程式服務計劃 ###
若要建立的應用程式服務方案，使用**azure appserviceplan 建立**命令。

以下是不同的參數的說明︰

-   **-資源群組**︰ 資源群組的新建立的應用程式服務方案。
-   **-名稱**︰ 應用程式服務計劃的名稱。
-   **-位置**︰ 應用程式服務方案的位置。
-   **-層**︰ 想要的價格 sku (的選項︰ F1 （免費）。 D1 （共用）。 B1 （基本小型企業版），B2 （基本中），和 B3 （基本大型）。 S1 （標準小型企業版），S2 （標準中），及 S3 （標準大型）。 P1 （Premium 小型企業版）、 P2 （進階版中），及第 3 頁 （大型進階版）。)
-   **-執行個體**︰ 同事的應用程式中的服務方案 （預設值為 1）。

若要使用這個 cmdlet 的範例︰

    azure appserviceplan create --name ContosoAppServicePlan --location "South Central US" --resource-group ContosoAzureResourceGroup --sku P1 --instances 10

### <a name="list-existing-app-service-plans"></a>清單中現有的應用程式服務方案 ###

若要] 清單中的現有的應用程式服務方案，使用**azure appserviceplan 清單**] 命令。

若要在清單中特定的資源群組] 底下的所有應用程式服務方案，使用︰

    azure appserviceplan list --resource-group ContosoAzureResourceGroup

若要取得特定應用程式服務方案，請使用**azure appserviceplan 顯示**命令︰

    azure appserviceplan show --name ContosoAppServicePlan --resource-group southeastasia

### <a name="configure-an-existing-app-service-plan"></a>設定現有的應用程式服務方案 ###

若要變更現有的應用程式服務方案的設定，請使用**azure appserviceplan config** ] 命令。 您可以變更的 sku，與同事的數目 

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1 --instances 9

#### <a name="scaling-an-app-service-plan"></a>按比例縮放的應用程式服務方案 ####

若要縮放現有的應用程式服務方案，請使用︰

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --instances 9

#### <a name="changing-the-sku-of-an-app-service-plan"></a>變更應用程式服務計劃的 SKU ####

若要變更的現有的應用程式服務方案的 sku，請使用︰

    azure appserviceplan config --nameContosoAppServicePlan --resource-group ContosoAzureResourceGroup --sku S1


### <a name="delete-an-existing-app-service-plan"></a>刪除現有的應用程式服務方案 ###

若要刪除現有的應用程式服務計劃，所有分派的 web 應用程式需要能移動或刪除 [第一次。 然後使用 [您可以刪除應用程式服務方案**azure webapp 刪除**] 命令。

    azure appserviceplan delete --name ContosoAppServicePlan --resource-group southeastasia

## <a name="managing-app-service-web-apps"></a>管理應用程式服務 Web 應用程式 ##

### <a name="create-a-web-app"></a>建立 Web 應用程式 ###

若要建立 web 應用程式，使用**azure webapp 建立**命令。

以下是不同的參數的說明︰

- **-名稱**: web 應用程式的名稱。
- **-計劃**︰ 服務計劃用來管理 web 應用程式的名稱。
- **-資源群組**︰ 主控的應用程式服務方案的資源群組。
- **-位置**: web 應用程式的位置。

若要使用這個 cmdlet 的範例︰

    azure webapp create --name ContosoWebApp --resource-group ContosoAzureResourceGroup --plan ContosoAppServicePlan --location "South Central US"

### <a name="delete-an-existing-web-app"></a>刪除現有的 Web 應用程式 ###

若要刪除現有的 web 應用程式您可以使用**azure webapp 刪除**命令，您必須指定 web 應用程式的名稱和資源群組名稱。

    azure webapp delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="list-existing-web-apps"></a>清單中現有的 Web 應用程式 ###

若要現有的 web 應用程式] 清單中，使用 [ **azure webapp 清單**] 命令。

若要在清單中特定的資源群組] 底下的所有 web 應用程式，使用︰

    azure webapp list --resource-group ContosoAzureResourceGroup

若要在特定的 web 應用程式，請使用**azure webapp 顯示**命令。

    azure webapp show --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="configure-an-existing-web-app"></a>設定現有的 Web 應用程式 ###

若要變更 [設定] 和 [現有的 web 應用程式的設定，請使用 [ **azure webapp 組態設定**] 命令。

範例 (1): 變更 web 應用程式的 php 版本 

    azure webapp config set --name ContosoWebApp --resource-group ContosoAzureResourceGroup --phpversion 5.6

範例 (2): 新增或變更應用程式設定

    webapp config appsettings set --name ContosoWebApp --resource-group ContosoAzureResourceGroup appsetting1=appsetting1value,appsetting2=appsetting2value

若要怎麼知道其他設定，可能是變更，請使用 [ **azure webapp 組態設定-h** ] 命令。

### <a name="change-the-state-of-an-existing-web-app"></a>變更現有的 Web 應用程式的狀態 ###

#### <a name="restart-a-web-app"></a>重新啟動 web 應用程式 ####

若要重新啟動 web 應用程式，您必須指定的 web 應用程式的 [名稱與資源] 群組。

    azure webapp restart --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="stop-a-web-app"></a>停止在 web 應用程式 ####

若要停止在 web 應用程式，您必須指定的 web 應用程式的 [名稱與資源] 群組。

    azure webapp stop --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="start-a-web-app"></a>啟動 web 應用程式 ####

若要開始在 web 應用程式，您必須指定的 web 應用程式的 [名稱與資源] 群組。

    azure webapp start --name ContosoWebApp --resource-group ContosoAzureResourceGroup

### <a name="manage-web-app-publishing-profiles"></a>管理 Web 應用程式發佈的設定檔 ###

每個 web 應用程式有可以用來發佈您的應用程式中的發佈設定檔。

#### <a name="get-publishing-profile"></a>取得發佈的設定檔 ####

若要發佈的設定檔之 web 應用程式，請使用︰

    azure webapp publishingprofile --name ContosoWebApp --resource-group ContosoAzureResourceGroup

這個命令回音發佈的設定檔使用者名稱和密碼] 命令。

### <a name="manage-web-app-hostnames"></a>管理 Web 應用程式主機名稱 ###

若要管理 hostname 繫結 web 應用程式，請使用 [ **azure webapp 設定主機名稱**] 命令  

#### <a name="list-hostname-bindings"></a>清單 hostname 繫結 ####

若要取得目前的主機名稱繫結 web 應用程式，請使用︰

    azure webapp config hostnames list --name ContosoWebApp --resource-group ContosoAzureResourceGroup

#### <a name="add-hostname-bindings"></a>主機名稱繫結加入 ####

若要新增 hostname 繫結至 web 應用程式，請使用︰

    azure webapp config hostnames add --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

#### <a name="delete-hostname-bindings"></a>刪除 hostname 繫結 ####

若要刪除 hostname 繫結，請使用︰

    azure webapp config hostnames delete --name ContosoWebApp --resource-group ContosoAzureResourceGroup --hostname www.contoso.com

### <a name="next-steps"></a>後續步驟 ###
- 若要進一步瞭解 Azure 資源管理員 CLI 支援，請參閱[管理 Azure 資源與資源群組使用 Azure CLI。](../xplat-cli-azure-resource-manager.md)
- 若要瞭解如何管理應用程式服務使用 PowerShell，請參閱[Using Azure Resource Manager-Based PowerShell 管理 Azure Web 應用程式。](app-service-web-app-azure-resource-manager-powershell.md)
