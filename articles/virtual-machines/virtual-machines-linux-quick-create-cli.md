<properties
   pageTitle="建立使用 CLI Linux VM Azure |Microsoft Azure"
   description="使用 CLI Azure 上建立 Linux VM。"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="vlivech"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/27/2016"
   ms.author="v-livech"/>


# <a name="create-a-linux-vm-on-azure-by-using-the-cli"></a>建立使用 CLI Linux VM Azure

本文將示範如何使用快速部署 Azure Linux 虛擬機器 (VM) `azure vm quick-create` command Azure 命令列介面 (CLI)。 `quick-create`命令部署 VM 內基本、 安全的基礎，您可以使用原型或快速測試的概念。 需要，請參閱︰

- Azure 帳戶 ([取得免費的試用版](https://azure.microsoft.com/pricing/free-trial/))。

- 登入的[Azure CLI](../xplat-cli-install.md) `azure login`。

- Azure CLI_必須使用_Azure 資源管理員模式`azure config mode arm`。

您可以使用[Azure 入口網站](virtual-machines-linux-quick-create-portal.md)，也快速部署 Linux VM。

## <a name="quick-commands"></a>快速命令

下列範例會示範如何部署 CoreOS VM 然後附加您 （您的引數可能不同） 的安全命令介面 (SSH) 金鑰︰

```bash
azure vm quick-create -M ~/.ssh/id_rsa.pub -Q CoreOS
```

## <a name="detailed-walkthrough"></a>詳細逐步解說

下列逐步解說有正在部署、 逐步解說，使用的每一個步驟執行說明 UbuntuLTS VM。

## <a name="vm-quick-create-aliases"></a>VM 快速建立別名

選擇分配快速的方法是使用對應到最常見的 OS 散佈 Azure CLI 別名。 下表列出的別名 （如 Azure CLI 版本 0.10)。 使用的所有部署`quick-create`預設值，以 solid-state 磁碟機 (SSD) 儲存空間，讓您更快速佈建和高效能磁碟存取備份的 Vm。 （這些別名代表 Azure 上可用的分配的小部分。 尋找更多的圖像中 Azure Marketplace[搜尋 PowerShell 中的圖像](virtual-machines-linux-cli-ps-findimage.md)，[網頁](https://azure.microsoft.com/marketplace/virtual-machines/)] 或 [[上傳您自己的自訂圖像](virtual-machines-linux-create-upload-generic.md)）。

| Alias （別名)     | Publisher | 優惠        | SKU         | 版本 |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS    | OpenLogic | CentOS       | 7.2         | 最新  |
| CoreOS    | CoreOS    | CoreOS       | 穩定      | 最新  |
| Debian    | credativ  | Debian       | 8           | 最新  |
| openSUSE  | SUSE      | openSUSE     | 13.2        | 最新  |
| RHEL      | 紅色的角色    | RHEL         | 7.2         | 最新  |
| UbuntuLTS | 標準 | Ubuntu 伺服器 | 14.04.4-LTS | 最新  |

下列區段使用`UbuntuLTS` **ImageURN**選項的別名 (`-Q`) 部署 Ubuntu 14.04.4 LTS 伺服器。

前一個`quick-create`範例只呼叫`-M`標幟] 來識別 SSH 公開金鑰上傳時停用 SSH 密碼，因此會提示您輸入下列引數︰

- 資源群組的名稱 （任何字串方式通常是您的第一個 Azure 資源群組）
- VM 名稱
- 位置 (`westus`或`westeurope`是很好的預設值)
- linux （若要讓知道您想要的 OS Azure）
- 使用者名稱

下列範例會指定所有的值，而不進一步加以需要。 只要您具備`~/.ssh/id_rsa.pub`ssh rsa 格式公開金鑰檔案，為其運作方式為︰

```bash
azure vm quick-create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--admin-username myAdminUser \
--ssh-public-file ~/.ssh/id_rsa.pub \
--image-urn UbuntuLTS
```

輸出看起來應該像下列輸出區塊︰

```bash
info:    Executing command vm quick-create
+ Listing virtual machine sizes available in the location "westus"
+ Looking up the VM "myVM"
info:    Verifying the public key SSH file: /Users/ahmet/.ssh/id_rsa.pub
info:    Using the VM Size "Standard_DS1"
info:    The [OS, Data] Disk or image configuration requires storage account
+ Looking up the storage account cli16330708391032639673
+ Looking up the NIC "examp-westu-1633070839-nic"
info:    An nic with given name "examp-westu-1633070839-nic" not found, creating a new one
+ Looking up the virtual network "examp-westu-1633070839-vnet"
info:    Preparing to create new virtual network and subnet
/ Creating a new virtual network "examp-westu-1633070839-vnet" [address prefix: "10.0.0.0/16"] with subnet "examp-westu-1633070839-snet" [address prefix: "10.+.1.0/24"]
+ Looking up the virtual network "examp-westu-1633070839-vnet"
+ Looking up the subnet "examp-westu-1633070839-snet" under the virtual network "examp-westu-1633070839-vnet"
info:    Found public ip parameters, trying to setup PublicIP profile
+ Looking up the public ip "examp-westu-1633070839-pip"
info:    PublicIP with given name "examp-westu-1633070839-pip" not found, creating a new one
+ Creating public ip "examp-westu-1633070839-pip"
+ Looking up the public ip "examp-westu-1633070839-pip"
+ Creating NIC "examp-westu-1633070839-nic"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the storage account clisto1710997031examplev
+ Creating VM "myVM"
+ Looking up the VM "myVM"
+ Looking up the NIC "examp-westu-1633070839-nic"
+ Looking up the public ip "examp-westu-1633070839-pip"
data:    Id                              :/subscriptions/2<--snip-->d/resourceGroups/exampleResourceGroup/providers/Microsoft.Compute/virtualMachines/exampleVMName
data:    ProvisioningState               :Succeeded
data:    Name                            :exampleVMName
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_DS1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :Canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :clic7fadb847357e9cf-os-1473374894359
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://cli16330708391032639673.blob.core.windows.net/vhds/clic7fadb847357e9cf-os-1473374894359.vhd
data:
data:    OS Profile:
data:      Computer Name                 :myVM
data:      User Name                     :myAdminUser
data:      Linux Configuration:
data:        Disable Password Auth       :true
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-42-FB
data:          Provisioning State        :Succeeded
data:          Name                      :examp-westu-1633070839-nic
data:          Location                  :westus
data:            Public IP address       :138.91.247.29
data:            FQDN                    :examp-westu-1633070839-pip.westus.cloudapp.azure.com
data:
data:    Diagnostics Profile:
data:      BootDiagnostics Enabled       :true
data:      BootDiagnostics StorageUri    :https://clisto1710997031examplev.blob.core.windows.net/
data:
data:      Diagnostics Instance View:
info:    vm quick-create command OK
```

## <a name="log-in-to-the-new-vm"></a>登入新 VM

登入您 vm 使用輸出中所列的公用 IP 位址。 您也可以使用所列的完整的網域名稱 (FQDN):

```bash
ssh -i ~/.ssh/id_rsa.pub ahmet@138.91.247.29
```

登入程序看起來應該像下列輸出區塊︰

```bash
Warning: Permanently added '138.91.247.29' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-65-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Thu Sep  8 22:50:57 UTC 2016

  System load: 0.63              Memory usage: 2%   Processes:       81
  Usage of /:  39.6% of 1.94GB   Swap usage:   0%   Users logged in: 0

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

myAdminUser@myVM:~$
```

## <a name="next-steps"></a>後續步驟

`azure vm quick-create`命令是快速部署 VM，因此可以登入艦隊命令介面，並開始工作的方法。 不過，使用`vm quick-create`並不會提供更多的控制，也不會讓您建立更複雜的環境。  若要部署 Linux VM 自訂基礎結構，您可以依照這些文件︰

- [若要建立特定的部署使用 Azure 資源管理員範本](virtual-machines-linux-cli-deploy-templates.md)
- [建立您自己的自訂環境 Linux vm 直接使用 Azure CLI] 命令](virtual-machines-linux-create-cli-complete.md)
- [使用範本的 Azure 上建立 SSH 保護 Linux VM](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

您也可以[使用`docker-machine`Azure 驅動程式的各種命令，迅速建立為 docker 主機 Linux VM](virtual-machines-linux-docker-machine.md)。
