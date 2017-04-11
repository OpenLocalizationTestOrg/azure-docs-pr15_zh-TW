<properties
   pageTitle="開發和測試環境 |Microsoft Azure"
   description="瞭解如何使用 Azure 資源管理員範本來快速且一致地建立及刪除開發及測試環境。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/22/2016"
   ms.author="tomfitz"/>

# <a name="development-and-test-environments-in-microsoft-azure"></a>Microsoft Azure 中的開發和測試環境

多個開發並測試環境之前生產部署，通常會部署自訂應用程式。 建立內部部署環境時, 計算資源的採購或配置給每個應用程式的每個環境。 環境通常包含多個實體或虛擬機器以手動方式部署的特定設定或複雜的自動化指令碼。 部署通常需要小時，並不一致的錯誤導致環境。

## <a name="scenario"></a>案例 ##

當您佈建開發和測試環境 Microsoft Azure 中的時，您只支付您使用的資源。  本文說明如何快速和一致的方式建立、 維護及刪除開發和測試使用 Azure 資源管理員範本和參數檔案的環境，如下所示。

![案例](./media/solution-dev-test-environments/scenario.png)

上方顯示三個開發和測試環境。  每一個有 web 應用程式和 SQL 資料庫資源，指定範本檔案中。  應用程式的每個環境中的資料庫名稱不同，而唯一的參數檔案，每個環境中指定。  

如果您不熟悉 Azure 資源管理員概念，建議您先閱讀本文，閱讀[Azure 資源管理員的概觀](azure-resource-manager/resource-group-overview.md)文件。

您可能會想要先進行本文中的步驟如下所未閱讀任何參考的文件，以快速取得使用 Azure 資源管理員範本的經驗。 您已經完成步驟一次之後，您就可以取得大部分的所引發您的第一個問題的解答時間透過即可得到進一步的步驟，並閱讀參考文件。

## <a name="plan-azure-resource-use"></a>規劃 Azure 資源的使用
一旦您有高層級的設計您的應用程式時，您可以定義︰

- Azure 資源會包含您的應用程式。 您可能會建立您的應用程式，並將其部署 Azure Web app 的 Azure SQL 資料庫。  您可能會建立使用 PHP 和 MySQL IIS 和 SQL Server 或其他元件的虛擬機器中的應用程式。 [Azure 應用程式服務、 雲端服務，以及虛擬機器比較]( app-service-web/choose-web-site-cloud-service-vm.md)文章可協助您決定哪些 Azure 的資源，您可能想要使用您的應用程式。
- 例如可用性、 安全性和符合您的應用程式的縮放比例哪些服務層級的需求。

## <a name="download-an-existing-template"></a>下載現有的範本
Azure 資源管理員範本定義所有應用程式使用 Azure 資源。 您可以直接在 Azure 入口網站中，組織中部署或下載、 修改，並儲存在來源控制系統應用程式碼，已存在於數個範本。  完成下列步驟以下載現有的範本。

1. 瀏覽[Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates/)GitHub 存放庫中的現有範本。 在清單中，您會看到 「[201-web-應用程式-sql-資料庫](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database)」 資料夾。 多個自訂應用程式包含的 web 應用程式和 SQL 資料庫，因為此範本作為本文的其餘部分的範例，協助您瞭解如何使用範本。 並不完整說明此範本建立，並設定，所有本文的範圍，但如果您打算使用它來建立您組織中的 [實際的環境，您會想要瞭解其閱讀[佈建與 SQL 資料庫的 web 應用程式](app-service-web/app-service-web-arm-with-sql-database-provision.md)文件。
附註︰ 2015 年 12 月版[201-web-應用程式-sql-資料庫](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database)範本的撰寫這份文件。 下面連結指向範本，參數檔案範本的版本。
2. 按一下[azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.json) 201-web-應用程式-sql-資料庫] 資料夾中檔案以檢視其內容。 這是 Azure 資源管理員範本檔案。 
3. 在檢視模式中，按一下 」[Raw](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.json)] 按鈕。 
4. 使用滑鼠，請選取 [此檔案的內容，並將其儲存到您的電腦中，為檔案命名為 「 TestApp1 Template.json 」。 
5. 檢查範本內容，請注意下列動作︰
 - **資源**一節︰ 本節定義的 Azure 此範本所建立的資源類型。 其他資源類型，此範本建立[Azure Web 應用程式](app-service-web/app-service-web-overview.md)和[Azure SQL 資料庫](sql-database/sql-database-technical-overview.md)的資源。 如果您想要執行及管理網站和虛擬機器中的 SQL server，您可以使用 「[iis 2vm-sql 1vm](https://github.com/Azure/azure-quickstart-templates/tree/master/iis-2vm-sql-1vm)」 或 「[燈應用程式](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app)」 範本，但這份文件中的指示進行根據[201-web-應用程式-sql-資料庫](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database)範本。
 - **參數**區段︰ 本節定義的參數，可以設定的每個資源。 某些範本中指定參數有 「 預設值] 屬性，而其他人沒有。 在部署 Azure 資源的範本，您必須提供值必須在範本中指定的預設值] 屬性的所有參數。  如果您不提供值參數皆使用預設值] 屬性，則會使用指定的範本中的 [預設值參數值。

範本可讓您定義的 Azure 資源所建立並參數的每個資源可以設定的。 您可以進一步瞭解範本，以及如何設計您自己的讀取[設計 Azure 資源管理員範本的最佳作法](best-practices-resource-manager-design-templates.md)一文。

## <a name="download-and-customize-an-existing-parameter-file"></a>下載並自訂現有的參數檔案

雖然您可能會想*相同*Azure 在每個環境中建立的資源，您可能想設定的*其他*每個環境中的資源。  這是來源參數檔案的位置。 建立參數檔案包含每個環境中的唯一值的完成下列步驟。   

1. [201-web-應用程式-sql-資料庫] 資料夾中檢視[azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/tree/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.parameters.json)檔案的內容。 這是您儲存在上一節中的範本檔案的參數檔案。 
2. 在檢視模式中，按一下 」[Raw](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/3f24f7b7e1e377538d1d548eaa6eab2851a21810/201-web-app-sql-database/azuredeploy.parameters.json)] 按鈕。 
3. 使用滑鼠，選取 [此檔案的內容，並將它們儲存在三個不同的檔案在電腦上使用下列名稱︰
 - TestApp1-參數-Development.json
 - TestApp1-參數-Test.json
 - TestApp1 參數 e-Production.json

3. 使用任何文字或 JSON 編輯器，編輯您在步驟 3 中，取代右邊的**參數**下方所列的*值*右邊的檔案中的參數值所列的值建立的開發環境參數檔案︰ 
 - **站台名稱**︰ *TestApp1DevApp*
 - **hostingPlanName**: *TestApp1DevPlan*
 - **siteLocation**︰*美國中部*
 - **伺服器名稱**︰ *testapp1devsrv*
 - **serverLocation**︰*美國中部*
 - **administratorLogin**: *testapp1Admin*
 - **administratorLoginPassword**︰*取代您的密碼*
 - **資料庫**︰ *testapp1devdb*

4. 使用任何文字或 JSON 編輯器] 中，編輯您在步驟 3，取代建立測試環境參數檔案右邊的**參數**以下所列的右側的 [*值*與檔案中的參數值所列的值︰
 - **站台名稱**︰ *TestApp1TestApp*
 - **hostingPlanName**: *TestApp1TestPlan*
 - **siteLocation**︰*美國中部*
 - **伺服器名稱**︰ *testapp1testsrv*
 - **serverLocation**︰*美國中部*
 - **administratorLogin**: *testapp1Admin*
 - **administratorLoginPassword**︰*取代您的密碼*
 - **資料庫**︰ *testapp1testdb*

5. 使用任何文字或 JSON 編輯器，編輯您在步驟 3 中建立生產前參數檔案。  什麼是下方取代整個檔案的內容︰

        {
          "$schema" : "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
          "contentVersion" : "1.0.0.0",
          "parameters" : {
        "administratorLogin" : {
          "value" : "testApp1Admin"
        },
        "administratorLoginPassword" : {
          "value" : "replace with your password"
        },
        "databaseName" : {
          "value" : "testapp1preproddb"
        },
        "hostingPlanName" : {
          "value" : "TestApp1PreProdPlan"
        },
        "serverLocation" : {
          "value" : "Central US"
        },
        "serverName" : {
          "value" : "testapp1preprodsrv"
        },
        "siteLocation" : {
          "value" : "Central US"
        },
        "siteName" : {
          "value" : "TestApp1PreProdApp"
        },
        "sku" : {
          "value" : "Standard"
        },
            "requestedServiceObjectiveName" : {
              "value" : "S1"
        }
          }
        }

在上述生產前參數檔案中， **sku**和**requestedServiceObjectiveName**參數已*加入*，而這些自行加入開發和測試參數檔案中。 這是因為下列參數在範本中，指定的預設值，在開發和測試環境中，使用預設值，但測試生產環境中使用這些參數的環境非預設值。

下列參數測試生產環境中使用非預設值是以測試您可能會想要的生產環境，也可以進行測試，讓這些參數的值。  所有這些參數與 Azure [Web 應用程式的主機服務方案](https://azure.microsoft.com/pricing/details/app-service/)，或**sku**和 Azure [SQL 資料庫](https://azure.microsoft.com/pricing/details/sql-database/)或**requestedServiceObjectiveName**所使用的應用程式。  不同的 sku 和服務目標名稱有不同的成本及功能及支援不同的服務等級指標。

下表列出範本和使用，而非生產前參數檔案中的預設值的值中指定下列參數的預設值。

| 參數 | 預設值 | 參數檔案值 |
|---|---|---|
| **sku** | 免費 | 標準 |
| **requestedServiceObjectiveName** | S0 | S1 |

## <a name="create-environments"></a>建立環境
[Azure [資源] 群組](azure-resource-manager/resource-group-overview.md)中，必須先建立所有 Azure 資源。 資源群組可讓您在群組 Azure 的資源，讓他們可以共同管理。  資源群組可以指派[權限](./active-directory/role-based-access-built-in-roles.md)，使組織中的特定人員可以建立、 修改、 刪除或檢視這些及其中的資源。  [Azure 入口網站](https://portal.azure.com)中可以檢視和帳單資訊的 [資源] 群組中的資源。 資源群組會建立 Azure[區域](https://azure.microsoft.com/regions/)中。  在本文中的中央的美國地區中建立的所有資源。 當您開始建立實際的環境時，您可以選擇最符合您需求的地區。 

建立使用下列方法之一環境的資源群組。  所有方法都達成相同的結果。

###<a name="azure-command-line-interface-cli"></a>Azure 的命令列介面 (CLI)

請確定您有 CLI[安裝](xplat-cli-install.md)於 Windows OS X 或 Linux 電腦，且您已[連線](xplat-cli-connect.md) [Azure AD 帳戶](./active-directory/active-directory-how-subscriptions-associated-directory.md)（也稱為工作或學校帳戶） Azure 訂閱。 從 CLI 命令列中，輸入下列命令以建立的開發環境的 [資源] 群組。

    azure group create "TestApp1-Development" "Central US"

如果成功，將會傳回下列命令︰

    info:    Executing command group create
    + Getting resource group TestApp1-Development
    + Creating resource group TestApp1-Development
    info:    Created resource group TestApp1-Development
    data:    Id:                  /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development
    data:    Name:                TestApp1-Development
    data:    Location:            centralus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

若要建立測試環境的 [資源] 群組中，輸入下列命令︰

    azure group create "TestApp1-Test" "Central US"

若要建立測試生產環境的 [資源] 群組中，輸入下列命令︰

    azure group create "TestApp1-Pre-Production" "Central US"

###<a name="powershell"></a>PowerShell

確定您有 Azure PowerShell 第 1.01 或更高的 Windows 電腦上安裝及已連線至您的訂閱，詳述[如何安裝和設定 PowerShell 的 Azure](powershell-install-configure.md)文件中的[Azure AD 帳戶](./active-directory/active-directory-how-subscriptions-associated-directory.md)（也稱為工作或學校帳戶）。 在 PowerShell 命令提示字元中，輸入下列命令以建立的開發環境的 [資源] 群組。

    New-AzureRmResourceGroup -Name TestApp1-Development -Location "Central US"

如果成功，將會傳回下列命令︰

    ResourceGroupName : TestApp1-Development
    Location          : centralus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/uuuuuuuu-vvvv-wwww-xxxx-yyyy-zzzzzzzzzzzz/resourceGroups/TestApp1-Development

若要建立測試環境的 [資源] 群組中，輸入下列命令︰

    New-AzureRmResourceGroup -Name TestApp1-Test -Location "Central US"

若要建立測試生產環境的 [資源] 群組中，輸入下列命令︰

    New-AzureRmResourceGroup -Name TestApp1-Pre-Production -Location "Central US"

###<a name="azure-portal"></a>Azure 入口網站

1. [Azure AD](./active-directory/active-directory-how-subscriptions-associated-directory.md) （也稱為工作或學校） 帳戶[Azure 入口網站](https://portal.azure.com)登入。 按一下 [新增--> 管理--> [資源群組和 [資源群組名稱] 方塊中輸入 「 TestApp1 開發 「 您的訂閱，然後選取下圖所示，在 [資源群組位置] 方塊中選取 「 美國中部 」。
   ![入口網站](./media/solution-dev-test-environments/rgcreate.png)
2. 按一下 [建立] 按鈕，建立資源群組]。
3. 按一下 [瀏覽]，向下捲動至 [資源群組清單，按一下上的 [資源群組，如下所示。
   ![入口網站](./media/solution-dev-test-environments/rgbrowse.png) 
4. 按一下 [資源群組後就會看到 [資源群組刀使用新的資源群組。
   ![入口網站](./media/solution-dev-test-environments/rgview.png)
5. 建立 TestApp1 測試並 TestApp1 預先生產資源群組建立上述 TestApp1 開發資源群組的方式相同。

##<a name="deploy-resources-to-environments"></a>部署資源環境

使用範本檔案解決方案的和參數檔案使用下列方法之一每個環境的每個環境的資源群組部署 Azure 資源。  這兩種方法達成相同的結果。

###<a name="azure-command-line-interface-cli"></a>Azure 的命令列介面 (CLI)

從 CLI 命令列中，輸入命令下列資源群組部署資源您建立的開發環境中，使用您儲存在上一個步驟中的檔案路徑取代 [路徑]。

    azure group deployment create -g TestApp1-Development -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Development.json 

幾分鐘，看到 「 等待部署完成 」 的訊息之後,] 命令將會傳回下列如果成功︰

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "Deployment1"
    + Waiting for deployment to complete
    data:    DeploymentName     : Deployment1
    data:    ResourceGroupName  : TestApp1-Development
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : XXXX-XX-XXT20:20:23.5202316Z
    data:    Mode               : Incremental
    data:    Name                           Type          Value
    data:    -----------------------------  ------------  ----------------------------
    data:    siteName                       String        TestApp1DevApp
    data:    hostingPlanName                String        TestApp1DevPlan
    data:    siteLocation                   String        Central US
    data:    sku                            String        Free
    data:    workerSize                     String        0
    data:    serverName                     String        testapp1devsrv
    data:    serverLocation                 String        Central US
    data:    administratorLogin             String        testapp1Admin
    data:    administratorLoginPassword     SecureString  undefined
    data:    databaseName                   String        testapp1devdb
    data:    collation                      String        SQL_Latin1_General_CP1_CI_AS
    data:    edition                        String        Standard
    data:    maxSizeBytes                   String        1073741824
    data:    requestedServiceObjectiveName  String        S0
    info:    group deployment create command OKx

如果未成功] 命令，解決任何錯誤訊息，然後再試一次。  常見問題的解決辦法使用不符合 Azure 資源命名限制的參數值。 [Azure 中的疑難排解資源群組部署](./resource-manager-troubleshoot-deployments-cli.md)文件中，可以找到其他疑難排解秘訣。

從 CLI 命令列中，輸入命令下列資源群組部署資源您建立測試環境中，使用您儲存在上一個步驟中的檔案路徑取代 [路徑]。

    azure group deployment create -g TestApp1-Test -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Test.json

從 CLI 命令列中，輸入命令下列資源群組部署資源您建立測試生產環境中，使用您儲存在上一個步驟中的檔案路徑取代 [路徑]。

    azure group deployment create -g TestApp1-Pre-Production -n Deployment1 -f [path]TestApp1-Template.json -e [path]TestApp1-Parameters-Pre-Production.json
  
###<a name="powershell"></a>PowerShell

PowerShell 的 Azure （1.01 或更新版本） 的命令提示字元，輸入命令下列資源群組部署資源您建立的開發環境中，使用您儲存在上一個步驟中的檔案路徑取代 [路徑]。

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Development -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Development.json -Name Deployment1 

後看到閃爍游標幾分鐘，[] 命令將會傳回下列如果成功︰

    DeploymentName    : Deployment1
    ResourceGroupName : TestApp1-Development
    ProvisioningState : Succeeded
    Timestamp         : XX/XX/XXXX 2:44:48 PM
    Mode              : Incremental
    TemplateLink      : 
    Parameters        : 
                        Name             Type                       Value     
                        ===============  =========================  ==========
                        siteName         String                     TestApp1DevApp
                        hostingPlanName  String                     TestApp1DevPlan
                        siteLocation     String                     Central US
                        sku              String                     Free      
                        workerSize       String                     0         
                        serverName       String                     testapp1devsrv
                        serverLocation   String                     Central US
                        administratorLogin  String                     testapp1Admin
                        administratorLoginPassword  SecureString                         
                        databaseName     String                     testapp1devdb
                        collation        String                     SQL_Latin1_General_CP1_CI_AS
                        edition          String                     Standard  
                        maxSizeBytes     String                     1073741824
                        requestedServiceObjectiveName  String                     S0        
                        
    Outputs           :

  如果未成功] 命令，解決任何錯誤訊息，然後再試一次。  常見問題的解決辦法使用不符合 Azure 資源命名限制的參數值。 [Azure 中的疑難排解資源群組部署](./resource-manager-troubleshoot-deployments-powershell.md)文件中，可以找到其他疑難排解秘訣。

  在 PowerShell 命令提示字元中，輸入命令下列資源群組部署資源您建立測試環境中，使用您儲存在上一個步驟中的檔案路徑取代 [路徑]。

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Test -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Test.json -Name Deployment1

  在 PowerShell 命令提示字元中，輸入命令下列資源群組部署資源您建立測試生產環境中，使用您儲存在上一個步驟中的檔案路徑取代 [路徑]。

    New-AzureRmResourceGroupDeployment -ResourceGroupName TestApp1-Pre-Production -TemplateFile [path]TestApp1-Template.json -TemplateParameterFile [path]TestApp1-Parameters-Pre-Production.json -Name Deployment1

在來源控制系統可以建立版本，維護您的應用程式碼的範本和參數檔案。  您也無法將指令碼檔案，並儲存您的程式碼以及上述的命令。

> [AZURE.NOTE] 您可以將部署範本 Azure 直接 」 部署 Azure] 按鈕即可[佈建與 SQL 資料庫的 Web App](https://azure.microsoft.com/documentation/templates/201-web-app-sql-database/)文件上。  您會發現此還是有幫助瞭解範本，但是這麼做會不讓您編輯，版本，並儲存您的範本和參數值應用程式碼，本文並未涵蓋進一步瞭解這個方法的詳細資訊，讓。

## <a name="maintain-environments"></a>維護環境
整個開發、 在不同環境中的 Azure 資源可能不一致變更設定有意或無意間。  這可能會導致不必要的疑難排解並解決問題，在應用程式開發週期。

1. 開啟[Azure 入口網站](https://portal.azure.com)，以變更環境。 
2. 登入，您完成上述步驟所用的同一個帳戶。 
3. 為所示的圖片下方，按一下 [瀏覽-資源群組 （您可能需要向下捲動以查看資源群組）。
   ![入口網站](./media/solution-dev-test-environments/rgbrowse.png)
4. 按一下上方的圖片中的資源群組上後，您會看到資源群組刀和您在先前的步驟如下圖所示的三個資源群組。 按一下 [TestApp1 開發資源群組，然後就會列出您在先前的步驟完成 TestApp1 開發資源群組部署中的範本所建立的資源刀。  刪除 TestApp1DevApp Web 應用程式資源中的 [TestApp1DevApp TestApp1 開發資源群組刀，並在 TestApp1DevApp Web 應用程式刀，然後按一下 [刪除。
   ![入口網站](./media/solution-dev-test-environments/portal2.png)
5. 按一下 [是] 時入口網站會提示您是否您確定您想要刪除資源。 關閉 TestApp1 開發資源群組刀再重新開啟它會立即顯示，而不您剛刪除的 Web 應用程式。  資源群組的內容現在應該與不同。 您可以進一步嘗試刪除多個資源群組的多個資源，或甚至變更的部分資源組態設定。 而不是使用 Azure 入口網站，若要刪除資源群組中的資源，您可以使用 PowerShell[移除 AzureResource](https://msdn.microsoft.com/library/azure/dn757676.aspx)命令或或 「 azure 資源刪除 」 命令從 CLI 完成相同的工作。
6. 若要取得的所有資源和應該會回到狀態他們應該位於 [資源] 群組中的設定，重新部署環境中[部署資源環境](#deploy-resources-to-environments)] 區段中，使用您用的同一個命令的資源群組，但取代 「 Deployment1 」 與 「 Deployment2 」。
7.  在步驟 4 中所顯示的圖片中 TestApp1 開發刀摘要區段所示，您會看到的一次您在上一個步驟的入口網站中刪除的 Web 應用程式已存在，會執行任何其他資源為您可能已經刪除。 若要變更的任何資源的設定，您也可以驗證，他們已被設定回為的值參數檔案中太。 部署 Azure 資源管理員範本與您環境的優點之一是您可以輕鬆地重新部署到已知的狀態環境隨時。
8. 如果您按一下下圖中的 「 上次部署 」 下的文字，您會看到顯示 [資源] 群組的部署記錄刀。  既然您使用的第一個部署和 「 Deployment2 」 的名稱為 「 Deployment1 「 第二個部署，您會有兩個項目。  按一下在部署會顯示刀顯示每個部署的結果。
  ![入口網站](./media/solution-dev-test-environments/portal3.png)



## <a name="delete-environments"></a>刪除環境
一旦您完成使用環境，您會想要將其刪除，因此您不會造成您無法再使用的 Azure 資源的使用費用。  刪除環境很容易比建立它們。  在先前的步驟，每個環境建立個別的 Azure 資源群組，然後將 [資源群組部署資源。 

刪除環境中使用下列方法之一。  所有方法都達成相同的結果。

### <a name="azure-cli"></a>Azure CLI

從 CLI 提示中，輸入以下資料︰

    azure group delete "TestApp1-Development"

出現提示時，輸入 y，然後按下 enter 若要移除的開發環境和其所有資源。 請稍候幾分鐘] 命令會傳回下列動作︰

    info:    group delete command OK

從 CLI 提示中，輸入下列命令以刪除剩餘的環境︰

    azure group delete "TestApp1-Test"
    azure group delete "TestApp1-Pre-Production"
  
### <a name="powershell"></a>PowerShell

PowerShell 的 Azure （1.01 或更新版本） 的命令提示字元，輸入下列命令，以刪除資源群組及其所有內容。    

    Remove-AzureRmResourceGroup -Name TestApp1-Development

當系統提示您確認您要移除 [資源] 群組中，輸入 y，再按下 enter 鍵。

輸入下列命令以刪除剩餘的環境︰

    Remove-AzureRmResourceGroup -Name TestApp1-Test
    Remove-AzureRmResourceGroup -Name TestApp1-Pre-Production

### <a name="azure-portal"></a>Azure 入口網站

1. 在 Azure 入口網站中，瀏覽到資源群組如同在先前的步驟。 
2. 選取 TestApp1 開發資源群組，然後在 TestApp1 開發資源群組刀，然後按一下 [刪除]。 新的刀就會出現。 輸入資源的群組名稱，然後按一下 [刪除] 按鈕。
![入口網站](./media/solution-dev-test-environments/rgdelete.png)
3. 刪除 TestApp1 測試並 TestApp1 預先生產資源群組刪除 TestApp1 開發資源群組的方式相同。

無論您使用的方法，資源群組]，所有他們所包含的資源將不再存在，而且不再會造成資源的帳單的費用。  

最小化 Azure 應用程式開發，您可以使用[Azure 自動化](automation/automation-intro.md)排程期間會發生的資源使用量支出的工作︰

- 停止虛擬機器結尾的每一天，且在每一天的開頭重新啟動。
- 刪除整個環境結尾的每一天，在每一天開始重新建立。
 
既然您已遇到如何輕鬆建立、 維護及刪除開發和測試環境中，您可以進一步您只是進一步嘗試使用上述步驟，並閱讀本文中所包含的參照。

## <a name="next-steps"></a>後續步驟

- [委派的管理控制](./active-directory/role-based-access-control-configure.md)每個藉由將 Microsoft Azure AD 群組或使用者指派給能夠執行的作業 Azure 資源子集的特定角色的環境中的其他資源。
- [指派標籤](resource-group-using-tags.md)為每個環境及/或個別資源的資源群組]。 您可能會新增至您的資源群組 」 環境 」，並設定其值會對應到您的環境的名稱。 當您需要組織的帳單或管理資源，標籤就會特別有用。
- 監控和[Azure 入口網站](https://portal.azure.com)中的資源群組資源的帳單。
