<properties
   pageTitle="如何調整大小 Linux VM |Microsoft Azure"
   description="如何放大或縮小 Linux 虛擬機器，藉由變更虛擬記憶體大小。"
   services="virtual-machines-linux"
   documentationCenter="na"
   authors="mikewasson"
   manager="timlt"
   editor=""
   tags=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="mikewasson"/>


# <a name="how-to-resize-a-linux-vm"></a>如何調整大小 Linux VM

## <a name="overview"></a>概觀 

您佈建虛擬機器 (VM) 之後，您可以調整 VM 向上或向下變更[虛擬記憶體大小][vm-sizes]。 在某些情況下，您必須先解除配置 VM。 如果硬體叢集裝載 VM 上沒有新的大小，也可能會發生。

本文將示範如何調整大小使用[Azure CLI]Linux VM[azure-cli]。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]傳統的部署模型。


## <a name="resize-a-linux-vm"></a>調整大小 Linux VM 

若要調整大小 VM，請執行下列步驟。

1. 執行下列 CLI 命令。 這個命令會列出可用的硬體叢集裝載 VM VM 大小。

    ```
    azure vm sizes -g <resource-group> --vm-name <vm-name>
    ```

2. 如果列出所要的大小，請執行下列命令以調整大小 VM。

    ```
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    ```

    VM 會重新啟動此程序期間。 重新啟動之後，就會對應您現有的作業系統與資料磁碟。 暫時磁碟上的任何項目將會遺失。

    使用`--enable-boot-diagnostics`選項可讓[開機診斷][boot-diagnostics]、 登入任何有關啟動的錯誤。

3. 否則，如果沒有列出所要的大小，請執行下列命令解除 VM 調整，然後重新啟動 VM。

    ```
    azure vm deallocate -g <resource-group> <vm-name>
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    azure vm start -g <resource-group> <vm-name>
    ```

   > [AZURE.WARNING] 解除 VM 也釋放指派給 VM 任何動態 IP 位址。 不受影響的作業系統與資料磁碟。
   
## <a name="next-steps"></a>後續步驟

其他延展性執行多個 VM 執行個體，延展。 如需詳細資訊，請參閱[自動在虛擬機器縮放比例設定的 Linux 電腦不按比例縮放][scale-set]。 

<!-- links -->
   
[azure-cli]: ../xplat-cli-install.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]: virtual-machines-linux-sizes.md