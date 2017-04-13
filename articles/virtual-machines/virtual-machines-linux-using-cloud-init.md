<properties
    pageTitle="若要在建立自訂 Linux VM 使用雲端初始化 |Microsoft Azure"
    description="若要在建立自訂 Linux VM 使用雲端初始化。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"
/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="v-livech"
/>

# <a name="using-cloud-init-to-customize-a-linux-vm-during-creation"></a>若要在建立自訂 Linux VM 使用雲端初始化

本文說明如何讓雲端初始化指令碼來設定 [主機名稱] 安裝的更新套件，及管理使用者帳戶。  從 Azure CLI VM 建立期間稱為 「 雲端初始化指令碼。  需要，請參閱︰

- Azure 帳戶 ([取得免費的試用版](https://azure.microsoft.com/pricing/free-trial/))。

- 登入的[Azure CLI](../xplat-cli-install.md) `azure login`。

- Azure CLI_必須使用_Azure 資源管理員模式`azure config mode arm`。

## <a name="quick-commands"></a>快速命令

建立一個設定主機名稱、 更新所有套件，並將 sudo 使用者加入 Linux 的雲端 init.txt 指令碼。

```bash
#cloud-config
hostname: myVMhostname
apt_upgrade: true
users:
  - name: myNewAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myVM
```
建立以啟動 Vm 到資源群組。

```bash
azure group create myResourceGroup westus
```

建立要設定讓它在開機期間使用雲端初始化 Linux VM。

```bash
azure vm create \
-g myResourceGroup \
-n myVM \
-l westus \
-y Linux \
-f myVMnic \
-F myVNet \
-P 10.0.0.0/22 \
-j mySubnet \
-k 10.0.0.0/24 \
-Q canonical:ubuntuserver:14.04.2-LTS:latest \
-M ~/.ssh/id_rsa.pub \
-u myAdminUser \
-C cloud-init.txt
```

## <a name="detailed-walkthrough"></a>詳細逐步解說

### <a name="introduction"></a>簡介

當您啟動新的 Linux VM 時，您所收到的標準 Linux VM 的任何自訂或準備好您的需求進行。 [雲端初始化](https://cloudinit.readthedocs.org)是標準的方式，用來插入到該 Linux VM 的指令碼] 或 [設定] 設定為其開機為第一次。

Azure，有三種不同方式變更到 Linux VM 正在部署或啟動。

- 插入使用雲端初始化指令碼。
- 插入使用 Azure [VMAccess 副檔名](virtual-machines-linux-using-vmaccess-extension.md)的指令碼。
- 使用雲端初始化 Azure 範本。
- 使用[CustomScriptExtention](virtual-machines-linux-extensions-customscript.md)Azure 範本。

若要隨時開機後插入指令碼︰

- 若要直接執行命令 SSH
- 插入使用 Azure [VMAccess 副檔名](virtual-machines-linux-using-vmaccess-extension.md)，強制或 Azure 的範本中的指令碼
- 設定管理工具，例如 Ansible、 Salt、 主廚和傀儡。

>[AZURE.NOTE]: VMAccess Extension executes a script as root in the same way using SSH can.  However, using the VM extension enables several features that Azure offers that can be useful depending upon your scenario.

## <a name="cloud-init-availability-on-azure-vm-quick-create-image-aliases"></a>Azure VM 雲端初始化可用性快速建立圖像別名︰

| Alias （別名)     | Publisher | 優惠        | SKU         | 版本 | 雲端初始化 |
|:----------|:----------|:-------------|:------------|:--------|:-----------|
| CentOS    | OpenLogic | Centos       | 7.2         | 最新  | 沒有         |
| CoreOS    | CoreOS    | CoreOS       | 穩定      | 最新  | [是]        |
| Debian    | credativ  | Debian       | 8           | 最新  | 沒有         |
| openSUSE  | SUSE      | openSUSE     | 13.2        | 最新  | 沒有         |
| RHEL      | Redhat    | RHEL         | 7.2         | 最新  | 沒有         |
| UbuntuLTS | 標準 | UbuntuServer | 14.04.4-LTS | 最新  | [是]        |

Microsoft 正在與合作夥伴可以取得雲端初始化包含，以及使用其提供給 Azure 的圖像。

## <a name="adding-a-cloud-init-script-to-the-vm-creation-with-the-azure-cli"></a>將雲端初始化指令碼新增至 Azure CLI VM 建立圖形

若要建立 VM Azure 中時，請啟動雲端初始化指令碼，指定 Azure CLI 雲端初始化檔案`--custom-data`切換。

建立以啟動 Vm 到資源群組。

```bash
azure group create myResourceGroup westus
```

建立要設定讓它在開機期間使用雲端初始化 Linux VM。

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubnet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud-init.txt
```

## <a name="creating-a-cloud-init-script-to-set-the-hostname-of-a-linux-vm"></a>建立雲端初始化指令碼設定的 Linux VM 主機名稱

其中一個最簡單且最重要的設定任何 Linux VM 應主機名稱。 我們可以輕鬆地將此雲端初始化使用這個指令碼。  

### <a name="example-cloud-init-script-named-cloudconfighostnametxt"></a>名為範例雲端初始化指令碼`cloud_config_hostname.txt`。

``` bash
#cloud-config
hostname: myservername
```

這個雲端初始化指令碼期間 VM 初始啟動時，若要設定主機名稱`myservername`。

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubNet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud_config_hostname.txt
```

登入，並確認的新 VM 主機名稱。

```bash
ssh myVM
hostname
myservername
```

## <a name="creating-a-cloud-init-script-to-update-linux"></a>建立更新 Linux 雲端初始化指令碼

安全性]，您想要更新的第一次開機您 Ubuntu VM。  使用雲端初始化我們可以執行的追蹤指令碼，根據您使用的 Linux 通訊群組。

### <a name="example-cloud-init-script-cloudconfigaptupgradetxt-for-the-debian-family"></a>範例雲端初始化指令碼`cloud_config_apt_upgrade.txt`Debian 家族

```bash
#cloud-config
apt_upgrade: true
```

透過 Linux 開機之後，更新所有已安裝的套件`apt-get`。

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubNet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud_config_apt_upgrade.txt
```

登入，並確認更新所有套件。

```bash
ssh myUbuntuVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="creating-a-cloud-init-script-to-add-a-user-to-linux"></a>建立雲端初始化指令碼，將使用者新增至 Linux

在任何新的 Linux VM 上的第一個工作的其中一個是新增您自己的使用者，或若要避免使用`root`。 SSH 索引鍵是針對安全性和合用性的最佳作法，即會新增到`~/.ssh/authorized_keys`這個雲端初始化指令碼的檔案。

### <a name="example-cloud-init-script-cloudconfigadduserstxt-for-debian-family"></a>範例雲端初始化指令碼`cloud_config_add_users.txt`Debian 家族

```bash
#cloud-config
users:
  - name: myCloudInitAddedAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myUbuntuVM
```

Linux 開機之後，所有列出的使用者建立和新增至 sudo 群組。

```bash
azure vm create \
--resource-group myResourceGroup \
--name myVM \
--location westus \
--os-type Linux \
--nic-name myVMnic \
--vnet-name myVNet \
--vnet-address-prefix 10.0.0.0/22 \
--vnet-subnet-name mySubNet \
--vnet-subnet-address-prefix 10.0.0.0/24 \
--image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--admin-username myAdminUser \
--custom-data cloud_config_add_users.txt
```

登入，並確認新建立的使用者。

```bash
ssh myVM
cat /etc/group
```

輸出

```bash
root:x:0:
<snip />
sudo:x:27:myCloudInitAddedAdminUser
<snip />
myCloudInitAddedAdminUser:x:1000:
```

## <a name="next-steps"></a>後續步驟

雲端初始化變得一標準的方式來修改您的 Linux VM 開機。 Azure 也有 VM 擴充功能，可讓您修改您 LinuxVM 開機，或在執行中時。 例如，您可以使用 Azure VMAccessExtension VM 正在執行時，重設 SSH 或使用者的資訊。 使用雲端初始化，您必須重新啟動電腦重設密碼。

[瞭解虛擬機器副檔名和功能](virtual-machines-linux-extensions-features.md)

[管理使用者與 SSH，核取或修復磁碟上 Azure Linux Vm 使用 VMAccess 副檔名](virtual-machines-linux-using-vmaccess-extension.md)
