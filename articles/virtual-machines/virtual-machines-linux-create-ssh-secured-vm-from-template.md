<properties
    pageTitle="建立使用 Azure 範本 Linux VM |Microsoft Azure"
    description="在使用 Azure 資源管理員範本 Azure Linux VM。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/24/2016"
    ms.author="v-livech"/>

# <a name="create-a-linux-vm-using-an-azure-template"></a>建立 Linux VM 使用 Azure 的範本

本文將示範如何快速部署 Linux 上 Azure 虛擬機器使用 Azure 範本。  需要，請參閱︰

- Azure 帳戶 ([取得免費的試用版](https://azure.microsoft.com/pricing/free-trial/))。

- 登入的[Azure CLI](../xplat-cli-install.md) `azure login`。

- Azure CLI_必須使用_Azure 資源管理員模式`azure config mode arm`。

您可以使用[Azure 入口網站](virtual-machines-linux-quick-create-portal.md)，也快速部署 Linux VM 範本。

## <a name="quick-command-summary"></a>快速命令摘要

```bash
azure group create \
-n myResourceGroup \
-l westus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## <a name="detailed-walkthrough"></a>詳細逐步解說

範本可讓您建立具有您想要在啟動期間自訂的設定]、 [設定，例如使用者名稱和 hostname Vm Azure。 在本文中，我們會啟動利用網路安全性群組 (NSG) 以及 Ubuntu VM Azure 範本與連接埠 22 開啟 SSH。

Azure 資源管理員範本是 JSON 檔案，可以使用簡單的一次性工作，例如啟動 Ubuntu VM 為已完成此文件中。  Azure 範本可以用於建構複雜的 Azure 設定，例如測試、 開發或產品部署堆疊整個環境。

## <a name="create-the-linux-vm"></a>建立 Linux VM

下列範例會示範如何呼叫`azure group create`建立資源群組及部署 SSH 保護 Linux VM 同時使用[此 Azure 資源管理員範本](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json)。 請記得，在您要使用的是您的環境唯一的名稱的範例。 此範例使用`myResourceGroup`作為資源群組名稱，然後`myVM`作為 VM 名稱。

```bash
azure group create \
--name myResourceGroup \
--location westus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

輸出看起來應該像下列輸出區塊︰

```bash
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== myAdminUser@myVM
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

該範例部署 VM 使用`--template-uri`參數。  您也可以下載或建立本機範本及傳遞範本使用`--template-file`參數與做為引數的範本檔案的路徑。 Azure CLI 會提示您輸入所需範本參數。

## <a name="next-steps"></a>後續步驟

搜尋[範本藝廊](https://azure.microsoft.com/documentation/templates/)探索下一步部署哪些應用程式架構。
