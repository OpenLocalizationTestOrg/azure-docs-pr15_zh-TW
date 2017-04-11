<properties
    pageTitle="IaaS 資源從移轉傳統至 Azure 資源管理員使用 Azure CLI |Microsoft Azure"
    description="本文將引導平台支援資源的移轉，從傳統至 Azure 資源管理員使用 Azure CLI"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/19/2016"
    ms.author="cynthn"/>

# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>IaaS 資源從移轉傳統至 Azure 資源管理員使用 Azure CLI

這些步驟會說明如何使用 Azure 命令列介面 (CLI) 命令，為傳統部署模型至 Azure 資源管理員部署模型服務 (IaaS) 資源移轉基礎結構。 文件需要[Azure CLI](../xplat-cli-install.md)。

>[AZURE.NOTE] 此處所述的所有運算都的冪。 如果您有不支援的功能或設定錯誤的問題，我們建議您重試準備，中止] 或 [確認作業。 在平台會再試一次。

## <a name="step-1-prepare-for-migration"></a>步驟 1︰ 準備移轉

以下是我們建議您為評估移轉 IaaS 資源傳統到資源管理員的一些最佳作法︰

- 讀取[清單不支援的設定或功能](virtual-machines-windows-migration-classic-resource-manager.md)。 如果您有使用不支援的設定或功能的虛擬機器，我們建議您等待功能/設定支援即將宣告。 或者，您可以移除這項功能，或移出 [的設定，若要啟用移轉，如果其符合您需求。
-   如果您有自動化今天部署基礎結構和應用程式的指令碼，請嘗試使用這些指令碼移轉建立類似的測試設定。 或者，您可以設定範例環境使用 Azure 入口網站。

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>步驟 2︰ 設定您的訂閱，並登錄提供者

移轉情況下，必須先設定您的兩個傳統的環境及資源管理員。 [安裝 Azure CLI](../xplat-cli-install.md) ，然後[選取您的訂閱](../xplat-cli-connect.md)。

登入您的帳戶。
    
    azure login

使用下列命令，以選取 Azure 訂閱。

    azure account set "<azure-subscription-name>"

>[AZURE.NOTE] 註冊是一次步驟，但它必須完成一次嘗試之前，先移轉。 不要註冊，您會看到下列錯誤訊息 

>   *BadRequest︰ 移轉未註冊訂閱。* 

使用下列命令登錄移轉資源提供者。 請注意，在某些情況下，此命令時發生逾時。 不過，您將會成功註冊。

    azure provider register Microsoft.ClassicInfrastructureMigrate

請稍候五分鐘，完成註冊。 您可以使用下列命令，以檢查核准的狀態。 請務必 RegistrationState`Registered`在繼續之前。

    azure provider show Microsoft.ClassicInfrastructureMigrate

現在切換 CLI`asm`模式。

    azure config mode asm

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-cores-in-the-azure-region-of-your-current-deployment-or-vnet"></a>步驟 3︰ 請確定您目前的部署或 VNET 的 Azure 區域中有足夠的資源管理員 Azure 虛擬機器核心

此步驟，您將需要切換至`arm`模式。 使用下列命令執行此動作。

```
azure config mode arm
```

您可以使用下列 CLI 命令來檢查的核心有 Azure 資源管理員] 中的目前量。 若要進一步瞭解核心配額，請參閱[限制和 Azure 資源管理員](../articles/azure-subscription-service-limits.md#limits-and-the-azure-resource-manager)

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

一旦您完成驗證此步驟中，您可以切換回`asm`模式。

    azure config mode asm


## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>步驟 4︰ 選項 1-移轉雲端服務中的虛擬機器 

使用下列命令，取得雲端服務的清單，然後挑選您想要移轉的雲端服務。 請注意，如果在雲端服務中的 Vm 處於虛擬網路，或是否有該子網站/工作者角色，您會收到錯誤訊息。

    azure service list

執行下列命令以取得雲端服務的部署名稱的詳細資訊的輸出。 在大部分情況下，部署名稱是雲端服務名稱相同。

    azure service show <serviceName> -vv

準備移轉雲端服務中的虛擬機器。 您有兩個選項可供選擇。

如果您想要移轉 Vm 平台建立虛擬網路，請使用下列命令。

    azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

如果您想要在資源管理員部署模型中現有的虛擬網路移轉時，請使用下列命令。

    azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> subnetName <vnetName>

準備作業是成功之後，您可以查看詳細資訊的輸出取得 Vm 的移轉狀態，並確保其位於`Prepared`狀態。

    azure vm show <vmName> -vv

使用 CLI 或 Azure 入口網站，以檢查準備資源的設定。 如果您不是準備好進行移轉，且您想要回到舊的狀態，請使用下列命令。

    azure service deployment abort-migration <serviceName> <deploymentName>

如果準備的設定看起來不錯，您可以向前移動，並使用下列命令確認資源。

    azure service deployment commit-migration <serviceName> <deploymentName>


    
## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>步驟 4︰ 選項 2-移轉虛擬網路中的虛擬機器

挑選您想要移轉的虛擬網路。 請注意，是否虛擬網路中包含網頁/工作者角色或 Vm 與不支援的設定，您會看到驗證錯誤訊息。

使用下列命令，在訂閱中取得所有虛擬的網路。

    azure network vnet list
    
輸出看起來會像這樣︰

![命令列以醒目提示的整個虛擬網路名稱的螢幕擷取畫面。](./media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

在上述範例中， **virtualNetworkName**是整個名稱**」 群組 classicubuntu16 classicubuntu16 」**。

準備移轉的虛擬您所選擇的網路使用下列命令。

    azure network vnet prepare-migration <virtualNetworkName>

使用 CLI 或 Azure 入口網站，以檢查準備的虛擬機器的設定。 如果您不是準備好進行移轉，且您想要回到舊的狀態，請使用下列命令。

    azure network vnet abort-migration <virtualNetworkName>

如果準備的設定看起來不錯，您可以向前移動，並使用下列命令確認資源。

    azure network vnet commit-migration <virtualNetworkName>

## <a name="step-5-migrate-a-storage-account"></a>步驟 5︰ 移轉儲存帳戶

一旦您完成移轉虛擬機器，我們建議您移轉儲存帳戶。

準備移轉中的儲存空間帳戶，使用下列命令

    azure storage account prepare-migration <storageAccountName>

使用 CLI 或 Azure 入口網站，以檢查準備的儲存帳戶的設定。 如果您不是準備好進行移轉，且您想要回到舊的狀態，請使用下列命令。

    azure storage account abort-migration <storageAccountName>

如果準備的設定看起來不錯，您可以向前移動，並使用下列命令確認資源。

    azure storage account commit-migration <storageAccountName>

## <a name="next-steps"></a>後續步驟

- [支援的平台的資源的移轉 IaaS 從傳統到資源管理員](virtual-machines-windows-migration-classic-resource-manager.md)
- [技術深度剖析上支援的平台的移轉從傳統到資源管理員](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
