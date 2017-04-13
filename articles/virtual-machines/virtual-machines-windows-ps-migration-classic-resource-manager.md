<properties
    pageTitle="移轉至資源管理員使用 PowerShell |Microsoft Azure"
    description="本文將引導 IaaS 資源的平台支援移轉從傳統至 Azure 資源管理員使用 PowerShell 的 Azure] 命令"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="cynthn"/>

# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>IaaS 資源從移轉傳統至 Azure 資源管理員使用 PowerShell 的 Azure

這些步驟會說明如何使用 PowerShell 的 Azure 命令，為傳統部署模型至 Azure 資源管理員部署模型服務 (IaaS) 資源移轉基礎結構。 

如果您想，那麼您也可以使用[Azure 命令列介面 (Azure CLI)](virtual-machines-linux-cli-migration-classic-resource-manager.md)移轉資源。

- 支援的移轉案例的 [背景]，請參閱[支援的平台的移轉 IaaS 傳統至 Azure 資源管理員資源](virtual-machines-windows-migration-classic-resource-manager.md)。 
- 詳細的指導方針和移轉逐步解說，請參閱[上從傳統至 Azure 資源管理員的支援平台的移轉技術深度剖析](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)。

## <a name="step-1-plan-for-migration"></a>步驟 1︰ 規劃移轉

以下是我們建議您為評估移轉 IaaS 資源傳統到資源管理員的一些最佳作法︰

- 閱讀的[支援與不支援的功能和設定](virtual-machines-windows-migration-classic-resource-manager.md)。 如果您有使用不支援的設定或功能的虛擬機器，我們建議您等待設定/功能的支援即將宣告。 或者，如果其符合您的需求，移除這項功能，或移出 [的設定，若要啟用移轉。
-   如果您有自動化今天部署基礎結構和應用程式的指令碼，請嘗試使用這些指令碼移轉建立類似的測試設定。 或者，您可以設定範例環境使用 Azure 入口網站。

>[AZURE.IMPORTANT]虛擬網路閘道器 （-網站，Azure ExpressRoute，應用程式閘道、 點為網站） 目前不支援從傳統的移轉到資源管理員。 若要移轉閘道器的傳統虛擬網路，請執行認可作業移動 （但不刪除閘道器可以執行準備步驟） 網路之前，先移除閘道器。 完成移轉後，重新連接的閘道器 Azure 資源管理員中的商務連絡人]。

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>步驟 2︰ 安裝最新版的 PowerShell 的 Azure

有兩個主要的選項，安裝 PowerShell 的 Azure: [PowerShell 圖庫](https://www.powershellgallery.com/profiles/azure-sdk/)或[網頁的平台安裝程式 (WebPI)](http://aka.ms/webpi-azps)。 WebPI 收到每月的更新。 PowerShell 庫接收持續更新。 本文根據 PowerShell 的 Azure 版本 2.1.0。

如需安裝指示，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。

<br>
## <a name="step-3-set-your-subscription-and-sign-up-for-migration"></a>步驟 3︰ 設定您的訂閱，並註冊移轉

首先，請啟動 PowerShell 提示。 移轉，必須先設定您的兩個傳統的環境及資源管理員。

登入您的帳戶資源管理員模型。

```powershell
    Login-AzureRmAccount
```

使用下列命令，以取得可用的訂閱︰

```powershell
    Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName
```

設定目前的工作階段 Azure 訂閱。 此範例**我 Azure**訂閱設定預設的訂閱名稱。 使用您自己取代範例訂閱名稱。 

```powershell
    Select-AzureRmSubscription –SubscriptionName "My Azure Subscription"
```

>[AZURE.NOTE] 註冊一次性的步驟，但您必須執行一次嘗試之前，先移轉。 不要註冊，您會看到下列錯誤訊息︰ 

>   *BadRequest︰ 移轉未註冊訂閱。* 

使用下列命令登錄移轉資源提供者︰

```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

請稍候五分鐘，完成註冊。 您可以使用下列命令，以檢查核准狀態︰

```powershell
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

請務必 RegistrationState`Registered`在繼續之前。 

立即登入您的帳戶的傳統模式。

```powershell
    Add-AzureAccount
```

使用下列命令，以取得可用的訂閱︰

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

設定目前的工作階段 Azure 訂閱。 此範例中設定預設訂閱**我 Azure**訂閱。 使用您自己取代範例訂閱名稱。 

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-4-make-sure-you-have-enough-azure-resource-manager-virtual-machine-cores-in-the-azure-region-of-your-current-deployment-or-vnet"></a>步驟 4︰ 請確定您目前的部署或 VNET 的 Azure 區域中有足夠的資源管理員 Azure 虛擬機器核心

若要檢查目前的數字的核心有 Azure 資源管理員] 中，您可以使用下列 PowerShell 命令。 若要進一步瞭解核心配額，請參閱[限制和 Azure 資源管理員](../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager)。 

此範例會檢查**西美國**地區的可用性。 使用您自己取代範例地區名稱。 

```powershell
Get-AzureRmVMUsage -Location "West US"
```

## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>步驟 5︰ 執行移轉 IaaS 資源的命令

>[AZURE.NOTE] 此處所述的所有運算都的冪。 如果您有不支援的功能或設定錯誤的問題，我們建議您重試準備，中止] 或 [確認作業。 在平台然後嘗試再次的動作。

### <a name="migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>移轉雲端服務 （不在虛擬網路） 中的虛擬機器

使用下列命令，取得雲端服務的清單，然後挑選您想要移轉的雲端服務。 如果在雲端服務中的 Vm 位於虛擬網路或有網頁或背景工作的角色，命令會傳回錯誤訊息。

```powershell
    Get-AzureService | ft Servicename
```

取得雲端服務的部署名稱。 在此範例中，服務名稱是**我的服務**。 範例服務名稱換成您自己的服務名稱。 

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

準備移轉雲端服務中的虛擬機器。 您有兩個選項可供選擇。

* **選項 1。移轉 Vm 平台建立虛擬網路**

    如果您可以將移轉雲端服務，使用下列命令，請先驗證︰

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    之前的命令會顯示任何警告和封鎖移轉的錯誤。 如果驗證成功，然後您可以移到**準備**步驟︰

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```

* **選項 2。資源管理員部署模型中現有的虛擬網路移轉**

    此範例中設定**myResourceGroup** **myVirtualNetwork**虛擬網路名稱與**mySubNet**子網路名稱的資源群組名稱。 在此範例中的名稱換成您自己的資源的名稱。

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    如果您可以將移轉雲端服務，使用下列命令，請先驗證︰

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    之前的命令會顯示任何警告和封鎖移轉的錯誤。 如果驗證成功，您可以繼續進行下列準備步驟︰

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```
    

使用其中一項上述選項的準備作業成功之後，查詢 Vm 移轉狀態。 確保其在`Prepared`狀態。

此範例會將**myVM**VM 名稱。 範例名稱換成您自己 VM 的名稱。

    ```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
    ```

使用 PowerShell 或 Azure 入口網站檢查準備資源的設定。 如果您不是準備好進行移轉，且您想要回到舊的狀態，請使用下列命令︰

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

如果準備的設定看起來不錯，您可以向前移動，並使用下列命令確認資源︰

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="migrate-virtual-machines-in-a-virtual-network"></a>移轉虛擬網路中的虛擬機器

若要移轉虛擬機器中虛擬網路，您要移轉的網路。 虛擬機器會自動移轉的網路。 挑選您想要移轉的虛擬網路。 

此範例中設定的虛擬網路名稱**myVnet**。 使用您自己取代範例虛擬網路名稱。 

```powershell
    $vnetName = "myVnet"
```

>[AZURE.NOTE] 如果虛擬網路包含網頁或工作者角色或 Vm 與不支援的設定，您會收到驗證錯誤訊息。

如果您可以使用下列命令移轉虛擬網路，請先驗證︰

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

之前的命令會顯示任何警告和封鎖移轉的錯誤。 如果驗證成功，您可以繼續進行下列準備步驟︰

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

使用 PowerShell 的 Azure 或 Azure 入口網站，以檢查準備的虛擬機器的設定。 如果您不是準備好進行移轉，且您想要回到舊的狀態，請使用下列命令︰

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

如果準備的設定看起來不錯，您可以向前移動，並使用下列命令確認資源︰

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="migrate-a-storage-account"></a>移轉儲存帳戶

一旦您完成移轉虛擬機器，我們建議您移轉儲存帳戶。

準備移轉的儲存空間的每個帳戶使用下列命令。 在此範例中，儲存體帳戶名稱會是**myStorageAccount**。 範例名稱換成您自己的儲存空間帳戶的名稱。 

```powershell
    $storageAccountName = "myStorageAccount"
    Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
```

使用 PowerShell 的 Azure 或 Azure 入口網站，以檢查準備的儲存帳戶的設定。 如果您不是準備好進行移轉，且您想要回到舊的狀態，請使用下列命令︰

```powershell
    Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
```

如果準備的設定看起來不錯，您可以向前移動，並使用下列命令確認資源︰

```powershell
    Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
```

## <a name="next-steps"></a>後續步驟

- 如需移轉的詳細資訊，請參閱[支援的平台的移轉 IaaS 傳統至 Azure 資源管理員資源](virtual-machines-windows-migration-classic-resource-manager.md)。
- 若要移轉的其他網路資源至資源管理員使用 PowerShell 的 Azure，使用類似的步驟以[移動 AzureNetworkSecurityGroup](https://msdn.microsoft.com/library/mt786729.aspx)、[移動 AzureReservedIP](https://msdn.microsoft.com/library/mt786752.aspx)及[移動 AzureRouteTable](https://msdn.microsoft.com/library/mt786718.aspx)。
- 您可以使用從傳統到資源管理員移轉 Azure 資源開啟來源指令碼，請參閱[社群移轉到 Azure 資源管理員移轉工具](virtual-machines-windows-migration-scripts.md)
