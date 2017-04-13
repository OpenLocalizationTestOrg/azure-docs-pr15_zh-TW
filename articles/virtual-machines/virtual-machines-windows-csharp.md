<properties
    pageTitle="部署使用 C# Azure 資源 |Microsoft Azure"
    description="瞭解如何使用 C# 和 Azure 資源管理員建立 Microsoft Azure 資源。"
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
    ms.date="10/06/2016"
    ms.author="davidmu"/>

# <a name="deploy-azure-resources-using-c"></a>部署使用 C Azure 資源# 

本文將示範如何建立使用 C# Azure 資源。

您必須先確定您已完成下列工作︰

- 安裝[Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- 驗證[Windows 管理 Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595)或[Windows 管理架構 4.0](http://www.microsoft.com/download/details.aspx?id=40855)的安裝
- 取得[驗證的權杖](../resource-group-authenticate-service-principal.md)

執行這些步驟需要大約 30 分鐘。

## <a name="step-1-create-a-visual-studio-project-and-install-the-libraries"></a>步驟 1︰ 建立 Visual Studio 專案，並安裝文件庫

NuGet 套件是最簡單的方法安裝您需要完成本教學課程中的文件庫。 若要取得您需要在 Visual Studio 中的文件庫，請執行下列步驟︰

1. 按一下 [**檔案** > **新** > **專案**。

2. 在**範本中** > **Visual C#**，選取**主控台應用程式**的名稱和位置的專案中，輸入，再按一下**[確定]**。

3. 以滑鼠右鍵按一下 [方案總管] 中的專案名稱，然後按一下 [**管理 NuGet 套件**。

4. 在 [搜尋] 方塊中輸入*Active Directory* Active Directory 驗證庫套件中，按一下 [**安裝**，，然後依照指示進行安裝套件。

5. 在頁面頂端，選取 [**包含搶鮮版**]。 在 [搜尋] 方塊中輸入*Microsoft.Azure.Management.Compute*計算.NET 文件庫，按一下 [**安裝**，然後依照指示進行安裝套件。

6. 在 [搜尋] 方塊中輸入*Microsoft.Azure.Management.Network*網路.NET 文件庫中，按一下 [**安裝**，然後依照指示進行安裝套件。

7. 在 [搜尋] 方塊中輸入*Microsoft.Azure.Management.Storage*儲存.NET 文件庫中，按一下 [**安裝**，然後依照指示進行安裝套件。

8. 在 [搜尋] 方塊中輸入*Microsoft.Azure.Management.ResourceManager* ，按一下 [資源管理文件庫的 [**安裝**]。

現在您準備好要開始建立您的應用程式中使用文件庫。

## <a name="step-2-create-the-credentials-that-are-used-to-authenticate-requests"></a>步驟 2︰ 建立用於驗證要求的認證

現在您可以格式化應用程式，您先前所建立的資訊將會用於驗證要求至 Azure 資源管理員的認證。

1. 開啟 Program.cs 檔案的您所建立的專案，然後新增這些使用陳述式以頂端的檔案︰

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Azure.Management.Storage;
        using Microsoft.Azure.Management.Storage.Models;
        using Microsoft.Azure.Management.Network;
        using Microsoft.Azure.Management.Network.Models;
        using Microsoft.Azure.Management.Compute;
        using Microsoft.Azure.Management.Compute.Models;
        using Microsoft.Rest;

2. 若要建立所需的權杖，將這個方法加入程式類別︰

        private static async Task<AuthenticationResult> GetAccessTokenAsync()
        {
          var cc = new ClientCredential("{client-id}", "{client-secret}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (token == null)
          {
            throw new InvalidOperationException("Could not get the token");
          }
          return token;
        }

    {用戶端識別碼} 取代的 Azure Active Directory 識別項應用程式中，{用戶端-私人} 與 AD 應用程式，與 {租用戶識別碼} 便捷鍵與您的訂閱的租用戶識別碼。 您可以藉由執行取得 AzureRmSubscription 找到的租用戶識別碼。 您可以使用 [Azure 入口網站中尋找便捷鍵。

3. 若要打電話給您先前已新增的方法，將此程式碼加入主要方法 Program.cs 檔案中︰

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);

4. 儲存 Program.cs 檔案。

## <a name="step-3-register-the-resource-providers-and-create-the-resources"></a>步驟 3︰ 註冊資源提供者，並建立資源

### <a name="register-the-providers-and-create-a-resource-group"></a>註冊提供者，並建立資源群組

資源群組中，必須包含所有資源。 您可以將資源新增至群組之前，必須與資源提供者註冊您的訂閱。

1. 若要指定您想要使用的資源名稱的程式課程主要方法加入變數︰

        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var location = "location name";
        var storageName = "storage account name";
        var ipName = "public ip name";
        var subnetName = "subnet name";
        var vnetName = "virtual network name";
        var nicName = "network interface name";
        var avSetName = "availability set name";
        var vmName = "virtual machine name";  
        var adminName = "administrator account name";
        var adminPassword = "administrator account password";
        
    所有變數的值取代的名稱及您想要使用的識別碼。 您可以藉由執行取得 AzureRmSubscription 找到訂閱識別碼。

2. 若要建立資源群組，並登錄提供者，請將這個方法加入程式類別︰

        public static async Task<ResourceGroup> CreateResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location)
        {
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
            
          Console.WriteLine("Registering the providers...");
          var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
          Console.WriteLine(rpResult.RegistrationState);
          
          Console.WriteLine("Creating the resource group...");
          var resourceGroup = new ResourceGroup { Location = location };
          return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
        }

3. 若要打電話給您先前已新增的方法，此將程式碼新增至主要的方法︰

        var rgResult = CreateResourceGroupAsync(
          credential,
          groupName,
          subscriptionId,
          location);
        Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

### <a name="create-a-storage-account"></a>建立儲存的帳戶

[儲存帳戶](../storage/storage-create-storage-account.md)需要儲存虛擬硬碟檔案所建立的虛擬機器。

1. 若要建立的儲存空間帳戶，將這個方法加入程式類別︰

        public static async Task<StorageAccount> CreateStorageAccountAsync(
          TokenCredentials credential,       
          string groupName,
          string subscriptionId,
          string location,
          string storageName)
        {
          Console.WriteLine("Creating the storage account...");
          var storageManagementClient = new StorageManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await storageManagementClient.StorageAccounts.CreateAsync(
            groupName,
            storageName,
            new StorageAccountCreateParameters()
            {
              Sku = new Microsoft.Azure.Management.Storage.Models.Sku() 
                { Name = SkuName.StandardLRS},
              Kind = Kind.Storage,
              Location = location
            }
          );
        }

2. 若要打電話給您先前已新增的方法，加入此程式碼程式類別的主要方法︰

        var stResult = CreateStorageAccountAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          storageName);
        Console.WriteLine(stResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-the-public-ip-address"></a>建立的公用 IP 位址

進行通訊的虛擬機器需要的公用 IP 位址。

1. 若要建立虛擬機器中的公用 IP 位址，請將程式類別中的這個方法︰

        public static async Task<PublicIPAddress> CreatePublicIPAddressAsync(
          TokenCredentials credential,  
          string groupName,
          string subscriptionId,
          string location,
          string ipName)
        {
          Console.WriteLine("Creating the public ip...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await networkManagementClient.PublicIPAddresses.CreateOrUpdateAsync(
            groupName,
            ipName,
            new PublicIPAddress
            {
              Location = location,
              PublicIPAllocationMethod = "Dynamic"
            }
          );
        }

2. 若要打電話給您先前已新增的方法，加入此程式碼程式類別的主要方法︰

        var ipResult = CreatePublicIPAddressAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          ipName);
        Console.WriteLine(ipResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-the-virtual-network"></a>建立虛擬網路

使用資源管理員部署模型建立虛擬機器必須在虛擬網路。

1. 若要建立子網路和虛擬網路時，將程式類別中的這個方法︰

        public static async Task<VirtualNetwork> CreateVirtualNetworkAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string vnetName,
          string subnetName)
        {
          Console.WriteLine("Creating the virtual network...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          
          var subnet = new Subnet
          {
            Name = subnetName,
            AddressPrefix = "10.0.0.0/24"
          };
          
          var address = new AddressSpace {
            AddressPrefixes = new List<string> { "10.0.0.0/16" }
          };
          
          return await networkManagementClient.VirtualNetworks.CreateOrUpdateAsync(
            groupName,
            vnetName,
            new VirtualNetwork
            {
              Location = location,
              AddressSpace = address,
              Subnets = new List<Subnet> { subnet }
            }
          );
        }
        
2. 若要打電話給您先前已新增的方法，加入此程式碼程式類別的主要方法︰

        var vnResult = CreateVirtualNetworkAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          vnetName,
          subnetName);
        Console.WriteLine(vnResult.Result.ProvisioningState);  
        Console.ReadLine();
        
### <a name="create-the-network-interface"></a>建立網路介面

虛擬機器必須通訊虛擬網路上的網路介面。

1. 若要建立網路介面，將這個方法加入程式類別︰

        public static async Task<NetworkInterface> CreateNetworkInterfaceAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string subnetName,
          string vnetName,
          string ipName,
          string nicName)
        {
          Console.WriteLine("Creating the network interface...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var subnetResponse = await networkManagementClient.Subnets.GetAsync(
            groupName,
            vnetName,
            subnetName
          );
          var pubipResponse = await networkManagementClient.PublicIPAddresses.GetAsync(groupName, ipName);

          return await networkManagementClient.NetworkInterfaces.CreateOrUpdateAsync(
            groupName,
            nicName,
            new NetworkInterface
            {
              Location = location,
              IpConfigurations = new List<NetworkInterfaceIPConfiguration>
              {
                new NetworkInterfaceIPConfiguration
                {
                  Name = nicName,
                  PublicIPAddress = pubipResponse,
                  Subnet = subnetResponse
                }
              }
            }
          );
        }

2. 若要打電話給您先前已新增的方法，加入此程式碼程式類別的主要方法︰

        var ncResult = CreateNetworkInterfaceAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          subnetName,
          vnetName,
          ipName,
          nicName);
        Console.WriteLine(ncResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-an-availability-set"></a>建立可用性設定

可用性組，方便您管理應用程式所使用的虛擬機器進行的維修作業。

1. 若要建立可用性設定，將這個方法加入程式類別︰

        public static async Task<AvailabilitySet> CreateAvailabilitySetAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string avsetName)
        {
          Console.WriteLine("Creating the availability set...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await computeManagementClient.AvailabilitySets.CreateOrUpdateAsync(
            groupName,
            avsetName,
            new AvailabilitySet()
            {
              Location = location
            }
          );
        }

2. 若要打電話給您先前已新增的方法，加入此程式碼程式類別的主要方法︰

        var avResult = CreateAvailabilitySetAsync(
          credential,  
          groupName,
          subscriptionId,
          location,
          avSetName);
        Console.ReadLine();

### <a name="create-a-virtual-machine"></a>建立虛擬機器

現在，您建立的所有支援的資源，您可以建立虛擬機器。

1. 若要建立虛擬機器，將這個方法加入程式類別︰

        public static async Task<VirtualMachine> CreateVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName,
          string subscriptionId,
          string location,
          string nicName,
          string avsetName,
          string storageName,
          string adminName,
          string adminPassword,
          string vmName)
        {
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var nic = networkManagementClient.NetworkInterfaces.Get(groupName, nicName);

          var computeManagementClient = new ComputeManagementClient(credential);
          computeManagementClient.SubscriptionId = subscriptionId;
          var avSet = computeManagementClient.AvailabilitySets.Get(groupName, avsetName);

          Console.WriteLine("Creating the virtual machine...");
          return await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(
            groupName,
            vmName,
            new VirtualMachine
            {
              Location = location,
              AvailabilitySet = new Microsoft.Azure.Management.Compute.Models.SubResource
              {
                Id = avSet.Id
              },
              HardwareProfile = new HardwareProfile
              {
                VmSize = "Standard_A0"
              },
              OsProfile = new OSProfile
              {
                AdminUsername = adminName,
                AdminPassword = adminPassword,
                ComputerName = vmName,
                WindowsConfiguration = new WindowsConfiguration
                {
                  ProvisionVMAgent = true
                }
              },
              NetworkProfile = new NetworkProfile
              {
                NetworkInterfaces = new List<NetworkInterfaceReference>
                {
                  new NetworkInterfaceReference { Id = nic.Id }
                }
              },
              StorageProfile = new StorageProfile
              {
                ImageReference = new ImageReference
                {
                  Publisher = "MicrosoftWindowsServer",
                  Offer = "WindowsServer",
                  Sku = "2012-R2-Datacenter",
                  Version = "latest"
                },
                OsDisk = new OSDisk
                {
                  Name = "mytestod1",
                  CreateOption = DiskCreateOptionTypes.FromImage,
                  Vhd = new VirtualHardDisk
                  {
                    Uri = "http://" + storageName + ".blob.core.windows.net/vhds/mytestod1.vhd"
                  }
                }
              }
            }
          );
        }

    >[AZURE.NOTE] 本教學課程中建立虛擬機器執行 Windows 伺服器作業系統版本。 若要進一步瞭解選取其他圖像，請參閱[瀏覽並使用 Windows PowerShell 和 Azure CLI 選取 Azure 虛擬機器圖像](virtual-machines-linux-cli-ps-findimage.md)。

2. 若要打電話給您先前已新增的方法，此將程式碼新增至主要的方法︰

        var vmResult = CreateVirtualMachineAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          nicName,
          avSetName,
          storageName,
          adminName,
          adminPassword,
          vmName);
        Console.WriteLine(vmResult.Result.ProvisioningState);
        Console.ReadLine();

##<a name="step-4-delete-the-resources"></a>步驟 4︰ 刪除資源

您會使用 Azure 中的資源，因為永遠是好的做法，若要刪除不再需要的資源。 如果您想要刪除虛擬機器及所有支援的資源，您只需要為刪除資源群組。

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

2.  若要打電話給您先前已新增的方法，此將程式碼新增至主要的方法︰

        DeleteResourceGroupAsync(
          credential,
          groupName,
          subscriptionId);
        Console.ReadLine();

## <a name="step-5-run-the-console-application"></a>步驟 5︰ 執行主控台應用程式

1. 若要執行主控台應用程式，按一下 [**開始**在 Visual Studio 中，，然後登入 Azure AD 使用相同的使用者名稱和密碼，您可以使用您的訂閱。

2. 若要建立的每個資源傳回每個狀態碼後，請按下**Enter** 。 虛擬機器會建立之後，請執行下一個步驟，然後再按 Enter，若要刪除的所有資源。

    應該以完成需要關於五分鐘，完全從一開始執行此主控台應用程式。 按 enter 鍵啟動刪除資源之前，您可能需要幾分鐘的時間刪除之前，請確認 Azure 入口網站中的資源建立。

3. 若要查看資源的狀態，瀏覽至 Azure 入口網站中的稽核記錄︰

    ![瀏覽 Azure 入口網站中的稽核記錄](./media/virtual-machines-windows-csharp/crpportal.png)
    
## <a name="next-steps"></a>後續步驟

- 利用使用範本來建立虛擬機器中[部署 Azure 虛擬機器使用 C# 和資源管理員範本](virtual-machines-windows-csharp-template.md)中使用的資訊。
- 瞭解如何管理您檢閱[使用 Azure 資源管理員與 PowerShell 管理虛擬機器](virtual-machines-windows-csharp-manage.md)建立虛擬機器。
