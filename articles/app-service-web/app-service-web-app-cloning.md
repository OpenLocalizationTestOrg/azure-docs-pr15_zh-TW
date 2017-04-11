<properties
    pageTitle="使用 PowerShell web 應用程式複製"
    description="瞭解如何複製到新的 Web 應用程式使用 PowerShell Web 應用程式。"
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
    ms.date="01/13/2016"
    ms.author="ahmedelnably"/>

# <a name="azure-app-service-app-cloning-using-powershell"></a>請使用 PowerShell 的 azure 應用程式服務應用程式#

使用 Microsoft Azure PowerShell 1.1.0 版的版本新選項已新增至想要讓使用者能夠複製至新建立的應用程式在不同區域中，或在同一個區域中現有的 Web 應用程式的新 AzureRMWebApp。 這樣會啟用部署跨不同區域的數字的應用程式，快速且輕鬆地與客戶。

應用程式複製目前是只支援進階版層應用程式服務方案。 新功能使用相同的限制為 Web 應用程式備份的功能，請參閱[備份 Azure 應用程式服務中的 web 應用程式](web-sites-backup.md)。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

若要深入瞭解使用 Azure 資源管理員基礎 Azure PowerShell cmdlet 來管理您的 Web 應用程式會檢查[Azure 資源管理員基礎 Azure Web 應用程式的 PowerShell 命令](app-service-web-app-azure-resource-manager-powershell.md)

## <a name="cloning-an-existing-app"></a>複製現有的應用程式 ##

案例︰ 現有 web 應用程式] 在 [美國中部南部地區中的，使用者想要複製到新的 web 應用程式，在 [美國中部北美地區的內容。 這可以透過-SourceWebApp 選項以建立新 web 應用程式中使用 Azure 資源管理員版本的 PowerShell 指令程式。

了解包含來源 web 應用程式的資源群組名稱，我們可以使用下列 PowerShell 命令以取得 （在本例中名為來源 webapp） 的來源 web 應用程式的資訊︰

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

若要建立新的應用程式服務方案，我們可以使用 [新增 AzureRmAppServicePlan] 命令，如下列範例所示

    New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium

使用 [新增 AzureRmWebApp] 命令，我們可以在 [美國中部北美地區中，建立新 web 應用程式，並將它連接至現有的進階版層應用程式服務方案再者我們可以使用相同的資源群組作為來源 web 應用程式，或定義新的資源群組、 以下示範︰

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp

若要複製現有的 web 應用程式包括所有相關聯的部署位置，使用者必須使用 IncludeSourceWebAppSlots 參數，下列 PowerShell 命令會示範如何使用該參數新增 AzureRmWebApp 命令︰

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots $true

若要複製現有的 web 應用程式在同一個區域中，使用者需要建立新的資源群組和新的應用程式服務方案在同一個區域中，，然後使用下列 PowerShell 命令複製 web 應用程式

    $destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>複製現有的應用程式到應用程式服務環境 ##

案例︰ 現有 web 應用程式] 在 [美國中部南部地區中的，使用者想要複製到新的 web 應用程式至現有應用程式服務環境 (ASE) 的內容。

了解包含來源 web 應用程式的資源群組名稱，我們可以使用下列 PowerShell 命令以取得 （在本例中名為來源 webapp） 的來源 web 應用程式的資訊︰

    $srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp

了解 ASE 的名稱，然後 ASE 所屬的資源群組名稱，使用者可以使用 [新增 AzureRmWebApp] 命令，現有 ASE 中建立新 web 應用程式，以下示範︰

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp

位置參數是舊版的原因，因為必要，但若是在 ASE 建立應用程式，也會忽略。 

## <a name="cloning-an-existing-app-slot"></a>複製現有的應用程式位置 ##

案例︰ 使用者想要複製的 [新 Web 應用程式至現有 Web 應用程式位置或新的 Web 應用程式位置。 新的 Web 應用程式可在與原始的 Web 應用程式位置的同一個區域或在不同區域。

了解包含來源 web 應用程式的資源群組名稱，我們可以使用下列 PowerShell 命令的來源 web 應用程式位置資訊 （在本例中名為來源 webappslot） 連結至 Web App 來源 webapp:

    $srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot

以下示範如何建立新的 web 應用程式的來源 web 應用程式複本︰

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot

## <a name="configuring-traffic-manager-while-cloning-a-app"></a>設定流量管理員，請在應用程式 ##

建立多區域 web 應用程式設定 Azure 流量管理員，若要將流量路由傳送至所有這些 web 應用程式，即可 n 重要的情況，以確保的客戶可使用應用程式高度，當複製現有的 web 應用程式時您必須選擇連接至新的資料傳輸管理員設定檔或現有的兩個 web 應用程式-請注意，只支援版本的流量 Manager Azure 資源管理員。

### <a name="creating-a-new-traffic-manager-profile-while-cloning-a-app"></a>建立新的資料傳輸管理員設定檔，請在應用程式 ###

案例︰ 想要設定包含兩個 web 應用程式的 Azure 資源管理員流量管理員設定檔複製到另一個區域，web 應用程式的使用者。 以下示範如何建立新的 web 應用程式的來源 web 應用程式複製時設定新的資料傳輸管理員設定檔︰

    $destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile

### <a name="adding-new-cloned-web-app-to-an-existing-traffic-manager-profile"></a>新增至現有的資料傳輸管理員設定檔複製 Web 應用程式 ###

案例︰ 使用者已有他想要將兩個 web 應用程式新增為端點 Azure 資源管理員流量管理員設定檔。 若要這樣做，我們必須集結的現有的資料傳輸管理員設定檔識別碼中，我們需要訂閱識別碼資源群組名稱與現有的資料傳輸管理員設定檔名稱。

    $TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"

後的流量管理員識別碼，下列示範如何建立新的 web 應用程式的來源 web 應用程式複本時將其新增至現有的資料傳輸管理員設定檔︰

    $destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID

## <a name="current-restrictions"></a>目前的限制 ##

此功能是目前預覽中，我們正在新增一段時間的新功能，以下清單是目前版本的應用程式複製的已知的限制︰

- 不會複製自動縮放設定
- 備份排程設定不會複製
- 不會複製 VNET 設定
- 應用程式的深入見解會無法自動設定目的地 web app
- 不會複製輕鬆驗證設定
- 不會複製 kudu 副檔名
- 秘訣規則不會複製
- 不會複製資料庫內容


### <a name="references"></a>參照 ###
- [Azure 資源管理員基礎 Azure Web 應用程式的 PowerShell 命令](app-service-web-app-azure-resource-manager-powershell.md)
- [Web 應用程式複製使用 Azure 入口網站](app-service-web-app-cloning-portal.md)
- [備份 Azure 應用程式服務中的 web 應用程式](web-sites-backup.md)
- [Azure 流量管理員預覽 azure 資源管理員的支援](../../articles/traffic-manager/traffic-manager-powershell-arm.md)
- [應用程式服務環境簡介](app-service-app-service-environment-intro.md)
- [使用 Azure PowerShell 的 Azure 資源管理員](../powershell-azure-resource-manager.md)
