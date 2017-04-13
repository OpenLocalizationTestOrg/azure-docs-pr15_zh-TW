<properties
    pageTitle="若要建立 Linux VM 的不同方式 |Microsoft Azure"
    description="瞭解不同的方式，來建立 Linux 虛擬機器上 Azure，包括工具和每一種方法的教學課程的連結。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="09/27/2016"
    ms.author="iainfou"/>

# <a name="different-ways-to-create-a-linux-virtual-machine-in-azure"></a>若要建立 Linux 虛擬機器 Azure 中的不同方式

若要建立 Linux 虛擬機器 (VM) 使用的工具及舒適若您要的工作流程的 Azure 中有彈性。 這份文件摘要列出這些差異與建立您的 Linux Vm 的範例。


## <a name="azure-cli"></a>Azure CLI 

Azure CLI 位於透過 npm 套件、 distro 所提供的套件或 Docker 容器的平台上。 您可以閱讀更多關於[如何安裝和設定 Azure CLI](../xplat-cli-install.md)。 下列教學課程提供範例使用 Azure CLI。 閱讀更多詳細資料，顯示的 CLI 快速開始命令的每個文件︰

- [從 Azure CLI 開發和測試建立 Linux VM](virtual-machines-linux-quick-create-cli.md)
    - 下列範例會建立使用公開金鑰名為 CoreOS VM `azure_id_rsa.pub`:

    ```bash
    azure vm quick-create -ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
        --image-urn CoreOS
    ```

- [建立安全的 Linux VM 使用 Azure 的範本](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
    - 下列範例會建立 VM 使用儲存在 GitHub 範本︰

    ```bash
    azure group create --name TestRG --location WestUS 
        --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
    ```

- [建立使用 Azure CLI 完成 Linux 環境](virtual-machines-linux-create-cli-complete.md)
    - 包括顯示狀態組中建立負載平衡器與多個 Vm。

- [新增 Linux VM 磁碟](virtual-machines-linux-add-disk.md)
    - 下列範例會 5 Gb 磁碟名為現有 vm `TestVM`:

    ```bash
    azure vm disk attach-new --resource-group TestRG --vm-name TestVM \
        --size-in-GB 5
    ```

## <a name="azure-portal"></a>Azure 入口網站

[Azure 入口網站](https://portal.azure.com)可讓您快速建立 VM，因為沒有安裝在您的系統上的任何項目。 若要建立 VM 使用 Azure 入口網站︰

- [建立 Linux VM 使用 Azure 入口網站](virtual-machines-linux-quick-create-portal.md) 
- [附加磁碟，使用 [Azure 入口網站](virtual-machines-linux-attach-disk-portal.md)


## <a name="operating-system-and-image-choices"></a>作業系統及圖像選項
在建立 VM 時，您可以選擇根據您想要執行的作業系統圖像。 Azure 和其合作夥伴提供許多圖像，其中包含應用程式和已預先安裝的工具。 或者，您也可以上傳您自己的影像 （請參閱[下一節](#use-your-own-image)） 的其中一個。

### <a name="azure-images"></a>Azure 圖像
使用`azure vm image`CLI 命令，請參閱什麼是依發行者、 distro 發行和建置。

可用的發行者] 清單，如下所示︰

```bash
azure vm image list-publishers --location WestUS
```

清單提供的產品 （提供） 指定的 publisher，如下所示︰

```bash
azure vm image list-offers --location WestUS --publisher Canonical
```

] 清單使用的 Sku （distro 發行版本） 的指定項優惠，如下所示︰

```bash
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

列出所有可用的圖像的各的版本如下︰

```bash
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

如需瀏覽和使用可用的圖像的更多範例，請參閱[瀏覽並選取 Azure 虛擬機器 Azure CLI 的影像](virtual-machines-linux-cli-ps-findimage.md)。

`azure vm quick-create`和`azure vm create`命令有可用來快速存取 [常見 distros 和其最新版本的別名。 使用別名，通常會比指定 publisher、 優惠、 SKU，以及版的每當您建立 VM 更快︰

| Alias （別名)     | Publisher | 優惠        | SKU         | 版本 |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS    | OpenLogic | Centos       | 7.2         | 最新  |
| CoreOS    | CoreOS    | CoreOS       | 穩定      | 最新  |
| Debian    | credativ  | Debian       | 8           | 最新  |
| openSUSE  | SUSE      | openSUSE     | 13.2        | 最新  |
| RHEL      | Redhat    | RHEL         | 7.2         | 最新  |
| SLES      | SLES      | SLES         | 12 SP1      | 最新  |
| UbuntuLTS | 標準 | UbuntuServer | 14.04.4-LTS | 最新  |

### <a name="use-your-own-image"></a>使用您自己的圖像

如果您需要特定自訂項目，您可以使用圖像，根據現有的 Azure VM*擷取*該 VM。 您也可以上傳建立內部部署的影像。 如需支援的 distros，以及如何使用您自己的影像的詳細資訊，請參閱下列文章︰

- [Azure 背書散佈](virtual-machines-linux-endorsed-distros.md)

- [非背書散佈的資訊](virtual-machines-linux-create-upload-generic.md)

- [如何擷取 Linux 虛擬機器為資源管理員範本](virtual-machines-linux-capture-image.md)。
    - 快速入門擷取現有的 VM 範例命令︰

    ```bash
    azure vm deallocate --resource-group TestRG --vm-name TestVM
    azure vm generalize --resource-group TestRG --vm-name TestVM
    azure vm capture --resource-group TestRG --vm-name TestVM --vhd-name-prefix CapturedVM
    ```

## <a name="next-steps"></a>後續步驟

- 建立 Linux VM 從[入口網站](virtual-machines-linux-quick-create-portal.md)， [CLI](virtual-machines-linux-quick-create-cli.md)，或使用[Azure 資源管理員範本](virtual-machines-linux-cli-deploy-templates.md)。

- 之後建立 Linux VM，[新增資料磁碟](virtual-machines-linux-add-disk.md)。

- 快速步驟來[重設密碼或 SSH 鍵及管理使用者](virtual-machines-linux-using-vmaccess-extension.md)
