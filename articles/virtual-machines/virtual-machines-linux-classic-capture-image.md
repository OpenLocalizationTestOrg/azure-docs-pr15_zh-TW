<properties
    pageTitle="擷取圖像的 Linux VM |Microsoft Azure"
    description="瞭解如何將擷取的影像 Linux 型 Azure 虛擬機器 (VM) 建立傳統的部署模型。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="iainfou"/>


# <a name="how-to-capture-a-classic-linux-virtual-machine-as-an-image"></a>如何擷取圖像為傳統 Linux 虛擬機器

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]瞭解如何[執行這些步驟後，使用資源管理員模型](virtual-machines-linux-capture-image.md)。

本文將示範如何擷取圖像建立其他虛擬機器以執行 Linux 傳統 Azure 虛擬機器。 此圖像包括 OS 磁碟以及資料磁碟連接到虛擬機器。 因此您需要從圖像建立其他虛擬機器時設定的不包括網路設定]。

Azure 儲存的圖像下**圖像**，以及任何您已上傳的圖像。 如需圖像的詳細資訊，請參閱[關於虛擬機器中的圖像 Azure] []。

## <a name="before-you-begin"></a>開始之前

這些步驟是假設您已已經建立使用傳統部署模型 Azure 虛擬機器並設定作業系統，包括附加任何資料磁碟。 如果您需要建立 VM，瞭解[如何建立 Linux 虛擬機器] []。


## <a name="capture-the-virtual-machine"></a>擷取虛擬機器

1. 使用您所選擇的 SSH 用戶端[連線至虛擬機器](virtual-machines-linux-mac-create-ssh-keys.md)。

2. 在 [SSH] 視窗中，輸入下列命令。 從輸出`waagent`會有些許取決於此公用程式版本︰

    `sudo waagent -deprovision+user`

    清除系統，使其更適合用來將會嘗試此命令。 這項作業會執行下列工作︰

    - 移除 SSH 主索引鍵 （如果 Provisioning.RegenerateSshHostKeyPair 設定檔中的 「 y 」）
    - 清除 /etc/resolv.conf 中的名稱伺服器設定
    - 移除`root`使用者密碼/等或陰影 （如果 Provisioning.DeleteRootPassword 是設定檔中的 「 y 」）
    - 移除快取 DHCP 用戶端租用
    - 重設 localhost.localdomain 主機名稱
    - 刪除的最後一個提供的使用者帳戶 （取自 /var/lib/waagent）**及相關聯的資料**。

    >[AZURE.NOTE] 取消刪除檔案和 「 一般化 」 圖像的資料。 只有在您想要擷取另存為新的圖像範本的虛擬電腦上執行此命令。 它不保證圖像會清除所有的機密資訊，或適用於發佈給第三方。


3. 輸入**y**繼續進行。 您可以新增`-force`參數，若要避免此確認步驟。

4. 輸入 [**結束**] 以關閉 SSH 用戶端。

    >[AZURE.NOTE] 其餘的步驟是假設您已[安裝 Azure CLI](../xplat-cli-install.md)用戶端電腦上。 [Azure 傳統入口網站] []也能完成下列所有步驟。

5. 從您的用戶端電腦開啟 Azure CLI 並登入您的 Azure 訂閱。 如需詳細資訊，請閱讀[從 Azure CLI Azure 訂閱的連線](../xplat-cli-connect.md)。

6. 請確定您是在服務管理模式︰

    `azure config mode asm`

7. 關閉虛擬機器中的已取消提供在上述步驟來︰

    `azure vm shutdown <your-virtual-machine-name>`

    >[AZURE.NOTE] 您可以找出在您的訂閱中使用建立的所有虛擬機器`azure vm list`

8. 停止虛擬機器時，擷取圖像的命令︰

    `azure vm capture -t <your-virtual-machine-name> <new-image-name>`

    輸入您想要_新增圖像名稱_取代圖像。 這個命令所建立的一般化的 OS 圖像。 `-t`指令會刪除原始的虛擬機器。

9.  在清單中的圖像，可以用來設定任何新的虛擬機器現在到新的圖像。 您可以檢視其命令︰

    `azure vm image list`

    在[Azure 傳統入口網站] []，它會出現在 [**影像**] 清單中。

    ![成功的圖像擷取](./media/virtual-machines-linux-classic-capture-image/VMCapturedImageAvailable.png)


## <a name="next-steps"></a>後續步驟
圖像可以用來建立虛擬機器。 您可以使用 Azure CLI 命令`azure vm create`，並提供您建立的圖像名稱。 如需詳細資訊] 命令，請參閱[使用 Azure CLI 傳統部署模型](../virtual-machines-command-line-tools.md)。 若要建立自訂的虛擬機器使用**從圖庫**的方法，並選取您所建立的圖像，或者，使用[Azure 傳統入口網站] []。 如需詳細資訊，請參閱[如何建立自訂的虛擬機器] []。

**另請參閱︰**[Azure Linux 代理程式使用者指南](virtual-machines-linux-agent-user-guide.md)

[Azure 傳統入口網站]: http://manage.windowsazure.com
[關於在 Azure 虛擬機器圖像]: virtual-machines-linux-classic-about-images.md
[如何建立自訂的虛擬機器]: virtual-machines-linux-classic-create-custom.md
[How to Attach a Data Disk to a Virtual Machine]: virtual-machines-windows-classic-attach-disk.md
[如何建立 Linux 虛擬機器]: virtual-machines-linux-classic-create-custom.md
