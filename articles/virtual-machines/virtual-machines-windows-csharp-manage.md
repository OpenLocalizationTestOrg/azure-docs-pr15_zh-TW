<properties
    pageTitle="管理 Vm 使用 Azure 資源管理員及 C# |Microsoft Azure"
    description="管理虛擬機器使用 Azure 資源管理員及 C#。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="manage-azure-virtual-machines-using-azure-resource-manager-and-c"></a>管理使用 Azure 資源管理員與 C Azure 虛擬機器#  

本文中的工作會顯示如何管理虛擬機器，例如啟動、 停止和更新。 虛擬機器必須存在於完成這份文件中的任務的資源群組。

若要完成此文件中的工作，您需要︰

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [驗證權杖](../resource-group-authenticate-service-principal.md)

## <a name="create-a-visual-studio-project-and-install-packages"></a>建立 Visual Studio 專案，並安裝套件

NuGet 套件是安裝您需要完成這份文件中的工作文件庫的位置，最簡單方法。 安裝此文件的文件庫的 Azure Active Directory 驗證庫和計算的資源提供者文件庫。 完成下列步驟將在 Visual Studio 中的文件庫︰

1. 按一下 [**檔案** > **新** > **專案**。

2. 在**範本中** > **Visual C#**，選取**主控台應用程式**的名稱和位置的專案中，輸入，再按一下**[確定]**。

3. 以滑鼠右鍵按一下 [方案總管] 中的專案名稱，然後按一下 [**管理 NuGet 套件**。

4. 在 [搜尋] 方塊中輸入*Active Directory* Active Directory 驗證庫套件中，按一下 [**安裝**，，然後依照指示進行安裝套件。

5. 在頁面頂端，選取 [**包含搶鮮版**]。 在 [搜尋] 方塊中輸入*Microsoft.Azure.Management.Compute*計算.NET 文件庫，按一下 [**安裝**，然後依照指示進行安裝套件。

現在您已準備好開始使用文件庫來管理您的虛擬機器。

## <a name="set-up-the-project"></a>設定專案

現在建立的應用程式已安裝的文件庫，您建立的權杖使用應用程式的資訊。 這用來驗證至 Azure 資源管理員的要求。

1. 開啟 Program.cs 檔案的您所建立的專案，然後新增這些使用陳述式以頂端的檔案︰

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Compute;
        using Microsoft.Azure.Management.Compute.Models;
        using Microsoft.Rest;
        
2. 新增變數程式類別來指定的 [資源] 群組中，名稱與名稱虛擬機器，以及您的訂閱識別碼的主要方法︰

        var groupName = "resource group name";
        var vmName = "virtual machine name";  
        var subscriptionId = "subsciption id";

    您可以藉由執行取得 AzureRmSubscription 找到訂閱識別碼。
    
3. 若要取得所需建立認證的權杖，將這個方法加入程式類別︰

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
    
4. 若要建立的認證，將此程式碼加入主要方法 Program.cs 中︰

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);

5. 儲存 Program.cs 檔案。

## <a name="display-information-about-a-virtual-machine"></a>顯示虛擬機器中的資訊

1. 加入您先前建立的專案中的程式課程此方法︰

        public static async void GetVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Getting information about the virtual machine...");

          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var vmResult = await computeManagementClient.VirtualMachines.GetAsync(
            groupName, 
            vmName, 
            InstanceViewTypes.InstanceView);

          Console.WriteLine("hardwareProfile");
          Console.WriteLine("   vmSize: " + vmResult.HardwareProfile.VmSize);

          Console.WriteLine("\nstorageProfile");
          Console.WriteLine("  imageReference");
          Console.WriteLine("    publisher: " + vmResult.StorageProfile.ImageReference.Publisher);
          Console.WriteLine("    offer: " + vmResult.StorageProfile.ImageReference.Offer);
          Console.WriteLine("    sku: " + vmResult.StorageProfile.ImageReference.Sku);
          Console.WriteLine("    version: " + vmResult.StorageProfile.ImageReference.Version);
          Console.WriteLine("  osDisk");
          Console.WriteLine("    osType: " + vmResult.StorageProfile.OsDisk.OsType);
          Console.WriteLine("    name: " + vmResult.StorageProfile.OsDisk.Name);
          Console.WriteLine("    createOption: " + vmResult.StorageProfile.OsDisk.CreateOption);
          Console.WriteLine("    uri: " + vmResult.StorageProfile.OsDisk.Vhd.Uri);
          Console.WriteLine("    caching: " + vmResult.StorageProfile.OsDisk.Caching);

          Console.WriteLine("\nosProfile");
          Console.WriteLine("  computerName: " + vmResult.OsProfile.ComputerName);
          Console.WriteLine("  adminUsername: " + vmResult.OsProfile.AdminUsername);
          Console.WriteLine("  provisionVMAgent: " + vmResult.OsProfile.WindowsConfiguration.ProvisionVMAgent.Value);
          Console.WriteLine("  enableAutomaticUpdates: " + vmResult.OsProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);

          Console.WriteLine("\nnetworkProfile");
          foreach (NetworkInterfaceReference nic in vmResult.NetworkProfile.NetworkInterfaces)
          {
            Console.WriteLine("  networkInterface id: " + nic.Id);
          }

          Console.WriteLine("\nvmAgent");
          Console.WriteLine("  vmAgentVersion" + vmResult.InstanceView.VmAgent.VmAgentVersion);
          Console.WriteLine("    statuses");
          foreach (InstanceViewStatus stat in vmResult.InstanceView.VmAgent.Statuses)
          {
            Console.WriteLine("    code: " + stat.Code);
            Console.WriteLine("    level: " + stat.Level);
            Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
            Console.WriteLine("    message: " + stat.Message);
            Console.WriteLine("    time: " + stat.Time);
          }

          Console.WriteLine("\ndisks");
          foreach (DiskInstanceView idisk in vmResult.InstanceView.Disks)
          {
            Console.WriteLine("  name: " + idisk.Name);
            Console.WriteLine("  statuses");
            foreach (InstanceViewStatus istat in idisk.Statuses)
            {
              Console.WriteLine("    code: " + istat.Code);
              Console.WriteLine("    level: " + istat.Level);
              Console.WriteLine("    displayStatus: " + istat.DisplayStatus);
              Console.WriteLine("    time: " + istat.Time);
            }
          }

          Console.WriteLine("\nVM general status");
          Console.WriteLine("  provisioningStatus: " + vmResult.ProvisioningState);
          Console.WriteLine("  id: " + vmResult.Id);
          Console.WriteLine("  name: " + vmResult.Name);
          Console.WriteLine("  type: " + vmResult.Type);
          Console.WriteLine("  location: " + vmResult.Location);
          Console.WriteLine("\nVM instance status");
          foreach (InstanceViewStatus istat in vmResult.InstanceView.Statuses)
          {
            Console.WriteLine("\n  code: " + istat.Code);
            Console.WriteLine("  level: " + istat.Level);
            Console.WriteLine("  displayStatus: " + istat.DisplayStatus);
          }
          
        }

2. 若要打電話給您剛才新增的方法，此將程式碼新增至主要的方法︰

        GetVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
    
3. 儲存 Program.cs 檔案。

4. 按一下 [在 Visual Studio 中，[**開始**，然後登入 Azure AD 使用相同的使用者名稱和密碼，您可以使用您的訂閱。

    當您執行此方法時，您應該會看到類似此範例中︰
    
        Getting information about the virtual machine...
        hardwareProfile
          vmSize: Standard_A0

        storageProfile
          imageReference
            publisher: MicrosoftWindowsServer
            offer: WindowsServer
            sku: 2012-R2-Datacenter
            version: latest
          osDisk
            osType: Windows
            name: myosdisk
            createOption: FromImage
            uri: http://store1.blob.core.windows.net/vhds/myosdisk.vhd
            caching: ReadWrite

          osProfile
            computerName: vm1
            adminUsername: account1
            provisionVMAgent: True
            enableAutomaticUpdates: True

          networkProfile
            networkInterface 
              id: /subscriptions/{subscription-id}
                /resourceGroups/rg1/providers/Microsoft.Network/networkInterfaces/nc1

          vmAgent
            vmAgentVersion2.7.1198.766
            statuses
            code: ProvisioningState/succeeded
            level: Info
            displayStatus: Ready
            message: GuestAgent is running and accepting new configurations.
            time: 4/13/2016 8:35:32 PM

          disks
            name: myosdisk
            statuses
              code: ProvisioningState/succeeded
              level: Info
              displayStatus: Provisioning succeeded
              time: 4/13/2016 8:04:36 PM

          VM general status
            provisioningStatus: Succeeded
            id: /subscriptions/{subscription-id}
              /resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1
            name: vm1
            type: Microsoft.Compute/virtualMachines
            location: centralus

          VM instance status
            code: ProvisioningState/succeeded
              level: Info
              displayStatus: Provisioning succeeded
            code: PowerState/running
              level: Info
              displayStatus: VM running

## <a name="stop-a-virtual-machine"></a>停止虛擬機器

您可以停止虛擬機器中的兩種方法。 您可以停止虛擬機器並將其設定，但繼續會針對該扣或您可以停止虛擬機器和解除它。 當指標的配置虛擬機器時，與其相關聯的所有資源也都會解除配置及帳單，結束。

1. 註解的任何程式碼，您先前已新增至主要的方法，以外的程式碼，以取得認證。

2. 加入程式課程此方法︰

        public static async void StopVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Stopping the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.PowerOffAsync(groupName, vmName);
        }

    如果您想要解除虛擬機器，變更關掉電源呼叫此程式碼︰

        computeManagementClient.VirtualMachines.Deallocate(groupName, vmName);

3. 若要打電話給您剛才新增的方法，此將程式碼新增至主要的方法︰

        StopVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. 儲存 Program.cs 檔案。

5. 按一下 [在 Visual Studio 中，[**開始**，然後登入 Azure AD 使用相同的使用者名稱和密碼，您可以使用您的訂閱。

    您應該會看到停止的虛擬機器變更的狀態。 如果您執行的方法呼叫解除配置狀態已停止 （解除配置）。

## <a name="start-a-virtual-machine"></a>啟動虛擬機器

1. 註解的任何程式碼，您先前已新增至主要的方法，以外的程式碼，以取得認證。

2. 加入程式課程此方法︰

        public static async void StartVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Starting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.StartAsync(groupName, vmName);
        }

3. 若要打電話給您剛才新增的方法，此將程式碼新增至主要的方法︰

        StartVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. 儲存 Program.cs 檔案。

5. 按一下 [在 Visual Studio 中，[**開始**，然後登入 Azure AD 使用相同的使用者名稱和密碼，您可以使用您的訂閱。

    您應該會看到虛擬機器的狀態變更為執行。

## <a name="restart-a-running-virtual-machine"></a>重新啟動虛擬機器

1. 註解的任何程式碼，您先前已新增至主要的方法，以外的程式碼，以取得認證。

2. 加入程式課程此方法︰

        public static async void RestartVirtualMachineAsync(
          TokenCredentials credential,
          string groupName,
          string vmName,
          string subscriptionId)
        {
          Console.WriteLine("Restarting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.RestartAsync(groupName, vmName);
        }

3. 若要打電話給您剛才新增的方法，此將程式碼新增至主要的方法︰

        RestartVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. 儲存 Program.cs 檔案。

5. 按一下 [在 Visual Studio 中，[**開始**，然後登入 Azure AD 使用相同的使用者名稱和密碼，您可以使用您的訂閱。

## <a name="resize-a-virtual-machine"></a>調整大小虛擬機器

此範例為您示範如何變更虛擬機器的大小。

1. 註解的任何程式碼，您先前已新增至主要的方法，以外的程式碼，以取得認證。

2. 加入程式課程此方法︰

        public static async void UpdateVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Updating the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
          vmResult.HardwareProfile.VmSize = "Standard_A1";
          await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
        }

3. 若要打電話給您剛才新增的方法，此將程式碼新增至主要的方法︰

        UpdateVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. 儲存 Program.cs 檔案。

5. 按一下 [在 Visual Studio 中，[**開始**，然後登入 Azure AD 使用相同的使用者名稱和密碼，您可以使用您的訂閱。

    您應該會看到 Standard_A1 的虛擬機器變更的大小。

## <a name="add-a-data-disk-to-a-virtual-machine"></a>加入虛擬機器中的資料磁碟

此範例為您示範如何將執行的虛擬機器中的資料磁碟。

1. 註解的任何程式碼，您先前已新增至主要的方法，以外的程式碼，以取得認證。

2. 加入程式課程此方法︰

        public static async void AddDataDiskAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Adding the disk to the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
          vmResult.StorageProfile.DataDisks.Add(
            new DataDisk
              {
                Lun = 0,
                Name = "mydatadisk1",
                Vhd = new VirtualHardDisk
                  {
                    Uri = "https://mystorage1.blob.core.windows.net/vhds/mydatadisk1.vhd"
                  },
                CreateOption = DiskCreateOptionTypes.Empty,
                DiskSizeGB = 2,
                Caching = CachingTypes.ReadWrite
              });
          await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
        }

3. 若要打電話給您剛才新增的方法，此將程式碼新增至主要的方法︰

        AddDataDiskAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. 儲存 Program.cs 檔案。

5. 按一下 [在 Visual Studio 中，[**開始**，然後登入 Azure AD 使用相同的使用者名稱和密碼，您可以使用您的訂閱。

## <a name="delete-a-virtual-machine"></a>刪除虛擬機器

1. 註解的任何程式碼，您先前已新增至主要的方法，以外的程式碼，以取得認證。

2. 加入程式課程此方法︰

        public static async void DeleteVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Deleting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.DeleteAsync(groupName, vmName);
        }

3. 若要打電話給您剛才新增的方法，此將程式碼新增至主要的方法︰

        DeleteVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. 儲存 Program.cs 檔案。

5. 按一下 [在 Visual Studio 中，[**開始**，然後登入 Azure AD 使用相同的使用者名稱和密碼，您可以使用您的訂閱。

## <a name="next-steps"></a>後續步驟

如果有問題的部署，您可能會看看[Azure 入口網站與疑難排解資源群組部署](../resource-manager-troubleshoot-deployments-portal.md)
