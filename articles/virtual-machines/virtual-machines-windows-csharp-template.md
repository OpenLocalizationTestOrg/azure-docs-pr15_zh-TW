<properties
    pageTitle="部署 VM 使用 C# 和資源管理員範本 |Microsoft Azure"
    description="瞭解如何使用 C# 和資源管理員範本部署 Azure VM。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="davidmu"/>

# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>部署使用 C# 和資源管理員範本 Azure 虛擬機器

使用資源群組及範本，您可以管理的所有資源在一起支援您的應用程式。 本文將示範如何使用 Visual Studio 及 C# 設定驗證，請建立範本，然後再部署 [Azure 資源使用您所建立的範本。

您必須先確定您已完成設定步驟執行︰

- 安裝[Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- 驗證[Windows 管理 Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595)或[Windows 管理架構 4.0](http://www.microsoft.com/download/details.aspx?id=40855)的安裝
- 取得[驗證的權杖](../resource-group-authenticate-service-principal.md)
- 建立使用[PowerShell 的 Azure](../resource-group-template-deploy.md)、 [Azure CLI](../resource-group-template-deploy-cli.md)或[Azure 入口網站](../resource-group-template-deploy-portal.md)的資源群組。

執行這些步驟需要大約 30 分鐘。
    
## <a name="step-1-create-the-visual-studio-project-the-template-file-and-the-parameters-file"></a>步驟 1︰ 建立 Visual Studio 專案、 範本檔案和參數檔案

### <a name="create-the-template-file"></a>建立範本檔案

Azure 資源管理員範本可讓您的部署及管理 Azure 資源放在一起。 範本是 JSON 描述的資源和相關聯的部署參數值。

在 Visual Studio 中，執行下列步驟︰

1. 按一下 [**檔案** > **新** > **專案**。

2. 在**範本中** > **Visual C#**，選取**主控台應用程式**的名稱和位置的專案中，輸入，再按一下**[確定]**。

3. 以滑鼠右鍵按一下專案名稱，在方案總管中的，按一下 [**新增** > **新項目**。

4. 按一下 [Web] 選取 JSON 檔案、 *VirtualMachineTemplate.json*輸入名稱，再按一下 [**新增**。

5. 在開啟和 VirtualMachineTemplate.json 檔案的右括弧括住，新增所需的結構描述元素和必要的 contentVersion 項目︰

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
        }

6. [參數](../resource-group-authoring-templates.md#parameters)並非必要，但它們提供範本部署時，請輸入值。 新增參數的項目和子項目 contentVersion 項目之後︰

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
        }

7. [變數](../resource-group-authoring-templates.md#variables)可以用於範本，若要指定可能會經常變更的值或需要建立的參數值組合的值。 在 [參數] 區段後新增變數項目︰

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"  
          },
        }

8. 範本中下一步定義[資源](../resource-group-authoring-templates.md#resources)，例如虛擬機器、 虛擬網路及儲存帳戶。 新增 [資源] 區段後的變數區段︰

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": { "type": "string" },
            "adminPassword": { "type": "securestring" }
          },
          "variables": {
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks','myvn1')]",
            "subnetRef": "[concat(variables('vnetID'),'/subnets/mysn1')]"
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "mystorage1",
              "apiVersion": "2015-06-15",
              "location": "[resourceGroup().location]",
              "properties": { "accountType": "Standard_LRS" }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/publicIPAddresses",
              "name": "myip1",
              "location": "[resourceGroup().location]",
              "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": { "domainNameLabel": "mydns1" }
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/virtualNetworks",
              "name": "myvnet1",
              "location": "[resourceGroup().location]",
              "properties": {
                "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
                "subnets": [ {
                  "name": "mysn1",
                  "properties": { "addressPrefix": "10.0.0.0/24" }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Network/networkInterfaces",
              "name": "mync1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/publicIPAddresses/myip1",
                "Microsoft.Network/virtualNetworks/myvn1"
              ],
              "properties": {
                "ipConfigurations": [ {
                  "name": "ipconfig1",
                  "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                      "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'myip1')]"
                    },
                    "subnet": { "id": "[variables('subnetRef')]" }
                  }
                } ]
              }
            },
            {
              "apiVersion": "2016-03-30",
              "type": "Microsoft.Compute/virtualMachines",
              "name": "myvm1",
              "location": "[resourceGroup().location]",
              "dependsOn": [
                "Microsoft.Network/networkInterfaces/mync1",
                "Microsoft.Storage/storageAccounts/mystorage1"
              ],
              "properties": {
                "hardwareProfile": { "vmSize": "Standard_A1" },
                "osProfile": {
                  "computerName": "myvm1",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                  "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version" : "latest"
                  },
                  "osDisk": {
                    "name": "myosdisk1",
                    "vhd": {
                      "uri": "https://mystorage1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    },
                    "caching": "ReadWrite",
                    "createOption": "FromImage"
                  }
                },
                "networkProfile": {
                  "networkInterfaces" : [ {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces','mync1')]"
                  } ]
                }
              }
            } ]
          }
      
9. 儲存您所建立的範本檔案。

### <a name="create-the-parameters-file"></a>建立參數檔案

若要指定資源參數的範本中所定義的值，您可以建立參數檔案包含部署範本時所使用的值。 在 Visual Studio 中，執行下列步驟︰

1. 以滑鼠右鍵按一下專案名稱，在方案總管中的，按一下 [**新增** > **新項目**。

2. 按一下 [Web] 選取 JSON 檔案、 *Parameters.json*輸入名稱，再按一下 [**新增**。

3. 開啟 parameters.json 檔案，然後新增 [這個 JSON 內容︰

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" }
          }
        }

    >[AZURE.NOTE] 本文會建立虛擬機器執行 Windows 伺服器作業系統版本。 若要進一步瞭解選取其他圖像，請參閱[瀏覽並使用 Windows PowerShell 和 Azure CLI 選取 Azure 虛擬機器圖像](virtual-machines-linux-cli-ps-findimage.md)。

4. 儲存您所建立的參數檔案。

## <a name="step-2-install-the-libraries"></a>步驟 2︰ 安裝文件庫

NuGet 套件是最簡單的方法安裝您需要完成本教學課程中的文件庫。 您需要建立資源的 [Azure 資源管理文件庫和 Azure Active Directory 驗證庫。 若要取得這些 Visual Studio 中的文件庫，請執行下列步驟︰

1. 以滑鼠右鍵按一下專案名稱方案總管] 中的，按一下 [**管理 NuGet 套件**]，再按一下 [瀏覽]。

2. 在 [搜尋] 方塊中輸入*Active Directory* Active Directory 驗證庫套件中，按一下 [**安裝**，，然後依照指示進行安裝套件。

4. 在頁面頂端，選取 [**包含搶鮮版**]。 在 [搜尋] 方塊中輸入*Microsoft.Azure.Management.ResourceManager* Microsoft Azure 資源管理文件庫，按一下 [**安裝**，然後依照指示進行安裝套件。

現在您準備好要開始建立您的應用程式中使用文件庫。

## <a name="step-3-create-the-credentials-that-are-used-to-authenticate-requests"></a>步驟 3︰ 建立用於驗證要求的認證

建立 Azure Active Directory 應用程式，並且會在安裝驗證的文件庫。 現在您可以格式化應用程式的資訊將會用於驗證要求 Azure 資源管理員的認證。

1. 開啟 Program.cs 檔案的您所建立的專案，然後新增這些使用陳述式以頂端的檔案︰

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Rest;
        using System.IO;

2.  新增至程式類別，以取得所需建立認證的權杖的這個方法︰

        private static async Task<AuthenticationResult> GetAccessTokenAsync()
        {
          var cc = new ClientCredential("{client-id}", "{client-secret}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (token == null)
          {
            throw new InvalidOperationException("Could not get the token.");
          }
          return token;
        }

    {用戶端識別碼} 取代的 Azure Active Directory 識別項應用程式中，{用戶端-私人} 與 AD 應用程式，與 {租用戶識別碼} 便捷鍵與您的訂閱的租用戶識別碼。 您可以藉由執行取得 AzureRmSubscription 找到的租用戶識別碼。 您可以使用 [Azure 入口網站中尋找便捷鍵。

3. 若要建立的認證，將此程式碼加入主要方法 Program.cs 檔案中︰

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);

4. 儲存 Program.cs 檔案。

## <a name="step-4-deploy-the-template"></a>步驟 4︰ 部署範本

在此步驟中，使用您先前建立的 [資源] 群組，但您也可以建立使用[ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx)和[ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx)類別的資源群組。

1. 若要指定您先前建立的資源的名稱、 部署名稱，以及您的訂閱識別碼程式類別的主要方法加入變數︰

        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var deploymentName = "deployment name";

    群組名稱的值取代資源群組的名稱。 以您想要使用的部署名稱取代 deploymentName 的值。 您可以藉由執行取得 AzureRmSubscription 找到訂閱識別碼。

2. 將這個方法加入程式類別部署至 [資源] 群組的資源，使用您所定義的範本︰

        public static async Task<DeploymentExtended> CreateTemplateDeploymentAsync(
          TokenCredentials credential,
          string groupName,
          string deploymentName,
          string subscriptionId)
        {
          Console.WriteLine("Creating the template deployment...");
          var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            Template = File.ReadAllText("..\\..\\VirtualMachineTemplate.json"),
            Parameters = File.ReadAllText("..\\..\\Parameters.json")
          };
          var resourceManagementClient = new ResourceManagementClient(credential) 
            { SubscriptionId = subscriptionId };
          return await resourceManagementClient.Deployments.CreateOrUpdateAsync(
            groupName,
            deploymentName,
            deployment);
        }

    如果您想要部署儲存帳戶的範本，您可以與 TemplateLink 屬性來取代 [範本] 屬性。

3. 若要打電話給您剛才新增的方法，此將程式碼新增至主要的方法︰

        var dpResult = CreateTemplateDeploymentAsync(
          credential,
          groupName,
          deploymentName,
          subscriptionId);
        Console.WriteLine(dpResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

## <a name="step-5-delete-the-resources"></a>步驟 5︰ 刪除資源

您會使用 Azure 中的資源，因為永遠是好的做法，若要刪除不再需要的資源。 您不需要的每個資源個別刪除資源群組。 刪除資源群組和它的所有資源會自動被都刪除。

1.  若要刪除 [資源] 群組中，將這個方法加入程式類別︰

        public static async void DeleteResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId)
        {
          Console.WriteLine("Deleting resource group...");
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
        }

2.  若要打電話給您剛才新增的方法，此將程式碼新增至主要的方法︰

        DeleteResourceGroupAsync(
          credential,
          groupName,
          subscriptionId);
        Console.ReadLine();

##<a name="step-6-run-the-console-application"></a>步驟 6︰ 執行主控台應用程式

1.  若要執行主控台應用程式，按一下 [**開始**在 Visual Studio 中，，然後登入 Azure AD 使用相同的認證，您可以使用您的訂閱。

2.  接受狀態會出現後，請按下**Enter** 。

    應該以完成需要關於五分鐘，完全從一開始執行此主控台應用程式。 按 enter 鍵啟動刪除資源之前，您可能需要幾分鐘的時間刪除之前，請確認 Azure 入口網站中的資源建立。

3. 若要查看資源的狀態，瀏覽至 Azure 入口網站中的稽核記錄︰

    ![瀏覽 Azure 入口網站中的稽核記錄](./media/virtual-machines-windows-csharp-template/crpportal.png)

## <a name="next-steps"></a>後續步驟

- 如果有問題的部署，下一步是查看[Azure 入口網站與疑難排解資源群組部署](../resource-manager-troubleshoot-deployments-portal.md)。
- 瞭解如何管理您檢閱[使用 Azure 資源管理員與 PowerShell 管理虛擬機器](virtual-machines-windows-csharp-manage.md)建立虛擬機器。
