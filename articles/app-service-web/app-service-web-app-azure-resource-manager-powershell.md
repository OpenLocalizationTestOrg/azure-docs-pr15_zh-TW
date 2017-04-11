<properties
    pageTitle="Azure 資源管理員為基礎的 PowerShell 命令 Azure Web 應用程式 |Microsoft Azure"
    description="瞭解如何使用新的 Azure 資源管理員為基礎的 PowerShell 命令，管理您的 Azure Web 應用程式。"
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

# <a name="using-azure-resource-manager-based-powershell-to-manage-azure-web-apps"></a>使用 Azure 資源管理員型 PowerShell 來管理 Azure Web 應用程式#

> [AZURE.SELECTOR]
- [Azure CLI](app-service-web-app-azure-resource-manager-xplat-cli.md)
- [Azure PowerShell](app-service-web-app-azure-resource-manager-powershell.md)

使用 Microsoft Azure PowerShell 版本 1.0.0 新命令已加入，，讓使用者能夠使用 Azure 資源管理員為基礎的 PowerShell 命令，管理 Web 應用程式。

若要瞭解管理資源群組，請參閱[使用 PowerShell 的 Azure 與 Azure 資源管理員](../powershell-azure-resource-manager.md)。 

若要瞭解的參數和選項的 PowerShell 指令程式的完整清單，請參閱[Web 應用程式 Azure 資源管理員為基礎的 PowerShell 指令程式的完整 Cmdlet 參考](https://msdn.microsoft.com/library/mt619237.aspx)

## <a name="managing-app-service-plans"></a>管理應用程式服務方案 ##

### <a name="create-an-app-service-plan"></a>建立應用程式服務計劃 ###
若要建立的應用程式服務方案，請使用**新增 AzureRmAppServicePlan**指令程式。

以下是不同的參數的說明︰

-   **名稱**︰ 應用程式服務計劃的名稱。
-   **位置**︰ 服務方案的位置。
-   **ResourceGroupName**︰ 資源群組的新建立的應用程式服務方案。
-   **層**︰ 在您要的價格層 （預設為免費，其他選項包括 [共用、 基本、 標準和進階版）。
-   **WorkerSize**︰ 大小的同事 （預設值為小型如果層參數指定為基本、 標準、 或進階版。 其他選項包括中型企業版和大。）
-   **NumberofWorkers**︰ 同事的應用程式中的服務方案 （預設值為 1）。 

若要使用這個 cmdlet 的範例︰

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -Tier Premium -WorkerSize Large -NumberofWorkers 10

### <a name="create-an-app-service-plan-in-an-app-service-environment"></a>在應用程式服務的環境中建立的應用程式服務方案 ###
若要建立的應用程式服務環境的應用程式服務方案中使用相同的命令**新增 AzureRmAppServicePlan**命令額外的參數，指定 ASE 的名稱，以及 ASE 的資源群組名稱。

若要使用這個 cmdlet 的範例︰

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -AseName constosoASE -AseResourceGroupName contosoASERG -Tier Premium -WorkerSize Large -NumberofWorkers 10

若要深入瞭解應用程式服務環境，核取[應用程式服務環境簡介](app-service-app-service-environment-intro.md)

### <a name="list-existing-app-service-plans"></a>清單中現有的應用程式服務方案 ###

若要] 清單中的現有的應用程式服務方案，使用**取得 AzureRmAppServicePlan**指令程式。

若要列出所有應用程式服務方案，在您的訂閱，請使用︰ 

    Get-AzureRmAppServicePlan

若要在清單中特定的資源群組] 底下的所有應用程式服務方案，使用︰

    Get-AzureRmAppServicePlan -ResourceGroupname ContosoAzureResourceGroup

若要取得特定應用程式服務方案，請使用︰

    Get-AzureRmAppServicePlan -Name ContosoAppServicePlan


### <a name="configure-an-existing-app-service-plan"></a>設定現有的應用程式服務方案 ###

若要變更現有的應用程式服務方案的設定，請使用**設定 AzureRmAppServicePlan**指令程式。 您可以變更層、 工作者大小] 及 [同事的數目 

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard -WorkerSize Medium -NumberofWorkers 9

#### <a name="scaling-an-app-service-plan"></a>按比例縮放的應用程式服務方案 ####

若要縮放現有的應用程式服務方案，請使用︰

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -NumberofWorkers 9

#### <a name="changing-the-worker-size-of-an-app-service-plan"></a>變更應用程式服務計劃 」 的工作者大小 ####

若要變更的同事在現有的應用程式服務方案大小，請使用︰

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -WorkerSize Medium

#### <a name="changing-the-tier-of-an-app-service-plan"></a>變更應用程式服務計劃層 ####

若要變更的層的現有的應用程式服務方案，請使用︰

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard

### <a name="delete-an-existing-app-service-plan"></a>刪除現有的應用程式服務方案 ###

若要刪除現有的應用程式服務計劃，所有分派的 web 應用程式需要能移動或刪除 [第一次。 然後使用您可以刪除應用程式服務方案**移除 AzureRmAppServicePlan**指令程式。

    Remove-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup

## <a name="managing-app-service-web-apps"></a>管理應用程式服務 Web 應用程式 ##

### <a name="create-a-web-app"></a>建立 Web 應用程式 ###

若要建立 web 應用程式，使用 [**新增 AzureRmWebApp**指令程式。

以下是不同的參數的說明︰

- **名稱**︰ web 應用程式的名稱。
- **AppServicePlan**︰ 用來管理 web 應用程式的服務方案的名稱。
- **ResourceGroupName**︰ 主控的應用程式服務方案的資源群組。
- **位置**︰ web 應用程式的位置。

若要使用這個 cmdlet 的範例︰

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"

### <a name="create-a-web-app-in-an-app-service-environment"></a>建立 Web 應用程式中的應用程式服務環境 ###

若要建立 web 應用程式中的 [應用程式服務環境 (ASE)。 使用額外的參數的相同的 [**新增 AzureRmWebApp** ] 命令來指定 ASE 名稱與 ASE 所屬的資源群組名稱。

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"  -ASEName ContosoASEName -ASEResourceGroupName ContosoASEResourceGroupName

若要深入瞭解應用程式服務環境，核取[應用程式服務環境簡介](app-service-app-service-environment-intro.md)

### <a name="delete-an-existing-web-app"></a>刪除現有的 Web 應用程式 ###

若要刪除現有的 web 應用程式您可以使用**移除 AzureRmWebApp** cmdlet，您必須指定 web 應用程式的名稱和資源群組名稱。

    Remove-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup


### <a name="list-existing-web-apps"></a>清單中現有的 Web 應用程式 ###

若要現有的 web 應用程式] 清單中，使用**取得 AzureRmWebApp**指令程式。

若要在您的訂閱的所有 web 應用程式] 都清單中，使用︰

    Get-AzureRmWebApp

若要在清單中特定的資源群組] 底下的所有 web 應用程式，使用︰

    Get-AzureRmWebApp -ResourceGroupname ContosoAzureResourceGroup

若要在特定的 web 應用程式，請使用︰

    Get-AzureRmWebApp -Name ContosoWebApp

### <a name="configure-an-existing-web-app"></a>設定現有的 Web 應用程式 ###

若要變更 [設定] 和 [現有的 web 應用程式的設定，請使用**設定 AzureRmWebApp**指令程式。 參數的完整清單，請[Cmdlet 參照連結](https://msdn.microsoft.com/library/mt652487.aspx)

範例 (1): 使用這個 cmdlet 來變更連線字串

    $connectionstrings = @{ ContosoConn1 = @{ Type = “MySql”; Value = “MySqlConn”}; ContosoConn2 = @{ Type = “SQLAzure”; Value = “SQLAzureConn”} }
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -ConnectionStrings $connectionstrings

範例 (2): 新增或變更應用程式設定

    $appsettings = @{appsetting1 = "appsetting1value"; appsetting2 = "appsetting2value"}
    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -AppSettings $appsettings


範例 (3): 設定 web 應用程式在 64 位元模式中執行

    Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -Use32BitWorkerProcess $False

### <a name="change-the-state-of-an-existing-web-app"></a>變更現有的 Web 應用程式的狀態 ###

#### <a name="restart-a-web-app"></a>重新啟動 web 應用程式 ####

若要重新啟動 web 應用程式，您必須指定的 web 應用程式的 [名稱與資源] 群組。

    Restart-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="stop-a-web-app"></a>停止在 web 應用程式 ####

若要停止在 web 應用程式，您必須指定的 web 應用程式的 [名稱與資源] 群組。

    Stop-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### <a name="start-a-web-app"></a>啟動 web 應用程式 ####

若要開始在 web 應用程式，您必須指定的 web 應用程式的 [名稱與資源] 群組。

    Start-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-publishing-profiles"></a>管理 Web 應用程式發佈的設定檔 ###

每個 web 應用程式的發佈的設定檔，可以用來發佈您的應用程式，可以執行發佈功能設定檔的多個作業。

#### <a name="get-publishing-profile"></a>取得發佈的設定檔 ####

若要發佈的設定檔之 web 應用程式，請使用︰

    Get-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -OutputFile .\publishingprofile.txt

這個命令回音發佈的設定檔，以命令列以良好輸出發佈的設定檔，以文字檔案。

#### <a name="reset-publishing-profile"></a>重設發佈的設定檔 ####

若要重設兩 FTP 和 web 發佈密碼部署 web 應用程式，使用︰

    Reset-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### <a name="manage-web-app-certificates"></a>管理 Web 應用程式的憑證 ###

若要進一步瞭解如何管理 web 應用程式的憑證，請參閱[使用 PowerShell 的 SSL 憑證繫結](app-service-web-app-powershell-ssl-binding.md)


### <a name="next-steps"></a>後續步驟 ###
- 若要進一步瞭解資源管理員 PowerShell 的 Azure 支援，請參閱[使用 PowerShell 的 Azure 與 Azure 資源管理員。](../powershell-azure-resource-manager.md)
- 若要進一步瞭解應用程式服務環境，請參閱[應用程式服務環境簡介。](app-service-app-service-environment-intro.md)
- 若要瞭解如何管理應用程式服務 SSL 憑證使用 PowerShell，請參閱[SSL 憑證繫結使用 PowerShell。](app-service-web-app-powershell-ssl-binding.md)
- 若要瞭解 Azure Web 應用程式的 Azure 資源管理員為基礎的 PowerShell 指令程式的完整清單，請參閱[Azure Cmdlet 參考的 Web 應用程式 Azure 資源管理員 PowerShell Cmdlet。](https://msdn.microsoft.com/library/mt619237.aspx)
- - 若要瞭解如何管理應用程式服務使用 CLI，請參閱[Using Azure Resource Manager-Based XPlat CLI Azure Web 應用程式的](app-service-web-app-azure-resource-manager-xplat-cli.md)
