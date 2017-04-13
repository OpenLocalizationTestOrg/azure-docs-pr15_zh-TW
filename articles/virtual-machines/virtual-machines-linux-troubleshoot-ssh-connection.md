<properties
    pageTitle="疑難排解與 VM SSH 連線問題 |Microsoft Azure"
    description="如何執行 Linux Azure VM 的疑難排解問題，例如 「 SSH 連線失敗 」 或 「 SSH 連線被拒 」。"
    keywords="ssh 拒絕連線，ssh 錯誤、 azure ssh，SSH 連線失敗"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="iainfou"/>

# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>疑難排解 SSH 連線失敗，請錯誤，或遭到拒絕 Azure Linux VM
有各種原因發生安全命令介面 (SSH) 錯誤，SSH 連線失敗，或 SSH 被拒當您嘗試連線到 Linux 虛擬機器 (VM)。 本文可協助您尋找並修正問題。 若要疑難排解並解決連線問題，您可以使用 Azure 入口網站，Azure CLI 或 Linux VM 存取副檔名。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

如果您需要更多協助，這份文件中的任一點時，您也可以連絡[MSDN Azure](http://azure.microsoft.com/support/forums/)和堆疊溢位論壇 Azure 專家。 或者，您可以檔案 Azure 支援事件。 移至[Azure 支援網站](http://azure.microsoft.com/support/options/)，然後選取 [**取得支援**。 瞭解如何使用 Azure 支援的資訊，請參閱[Microsoft Azure 支援常見問題集](http://azure.microsoft.com/support/faq/)。


## <a name="quick-troubleshooting-steps"></a>快速的疑難排解步驟
每個疑難排解步驟之後，請嘗試重新連線到 VM。

1. 重設 SSH 設定。
2. 重設使用者的認證。
3. 請確認[網路安全性群組](../virtual-network/virtual-networks-nsg.md)規則允許 SSH 流量。
    - 請確定網路安全性群組規則存在 （依預設，TCP 連接埠 22） 允許 SSH 流量。
    - 您無法使用連接埠重新導向 / 對應不使用 Azure 負載平衡器。
4. 核取[VM 資源健康狀況](../resource-health/resource-health-overview.md)。 
    - 請確定 VM 報表所健全。
    - 如果您有啟用開機診斷程式，請確認 VM 不報告開機錯誤記錄檔中。
5. 重新啟動 VM。
6. 重新部署 VM。

繼續閱讀更詳細的疑難排解步驟與說明。


## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>提供疑難排解 SSH 連線問題的方法

您可以重設認證] 或 [SSH 設定使用下列方法之一︰

- [Azure 入口網站](#using-the-azure-portal)的絕佳若要快速重設 SSH 設定或 SSH 索引鍵和您沒有安裝 Azure 工具。
- [Azure CLI 命令](#using-the-azure-cli)-如果您已在命令列中，快速或重設 SSH 設定認證。
- [Azure VMAccessForLinux 副檔名](#using-the-vmaccess-extension)-建立，並重複使用 json 定義檔案重設 SSH 設定] 或 [使用者認證。

每個疑難排解步驟之後，再次嘗試連線到您 VM。 如果您仍然無法連線，請嘗試下一個步驟。


## <a name="using-the-azure-portal"></a>使用 [Azure 入口網站
Azure 入口網站提供快速的方法來重設 SSH 設定] 或 [使用者認證，但不在您的本機電腦上安裝任何工具。

選取您 VM Azure 入口網站中。 捲動至 [**支援 + 疑難排解**] 區段，然後選取 [如下列範例所示的 [**重設密碼**︰

![重設 SSH 設定] 或 [Azure 入口網站中的認證](./media/virtual-machines-linux-troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a>重設 SSH 設定
為第一個步驟中，選取 [`Reset SSH configuration only`從**模式**下拉式功能表與先前的螢幕擷取畫面，然後按一下 [**重設**] 按鈕。 完成此動作後，請嘗試再次存取您 VM。

### <a name="reset-ssh-credentials-for-a-user"></a>重設使用者的 SSH 認證
若要重設現有使用者的認證，請選取 [`Reset SSH public key`或`Reset password`從**模式**下拉式功能表如上述的螢幕擷取畫面所示。 指定使用者名稱和 SSH 鍵或新密碼，然後按一下 [**重設**] 按鈕。

您也可以建立 sudo VM 從這個功能表上的權限的使用者。 輸入新的使用者名稱和相關聯的密碼或 SSH 鍵，然後按一下 [**重設**] 按鈕。


## <a name="using-the-azure-cli"></a>使用 Azure CLI
如果您未，[安裝 Azure CLI 並連線到您 Azure 的訂閱](../xplat-cli-install.md)。 請確定您使用資源管理員模式，如下所示︰

```
azure config mode arm
```

如果您已建立並上傳自訂 Linux 磁碟映像，確定[Microsoft Azure Linux 代理程式](virtual-machines-linux-agent-user-guide.md)版本 2.0.5，或已安裝的更新版本。 建立使用庫圖像 Vm 的此存取延伸已經安裝和設定為您。

### <a name="reset-ssh-configuration"></a>重設 SSH 設定
設定錯誤 SSHD 設定本身或服務發生錯誤。 您可以重設 SSHD 本身 SSH 設定正確。 重設 SSHD 應該是您的第一個疑難排解步驟。

下列範例會重設在名為 VM SSHD `myVM` [資源] 群組中名為`myResourceGroup`。 使用您自己 VM 及資源群組名稱，如下所示︰

```bash
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>重設使用者的 SSH 認證
如果 SSHD 正常運作，您可以重設酬謝使用者的密碼。 下列範例會重設認證`myUsername`中指定的值`myPassword`，名為 VM 上`myVM`中`myResourceGroup`。 使用您自己的值如下所示︰

```bash
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

如果使用 SSH 金鑰驗證，您可以重設特定使用者的 SSH 索引鍵。 下列範例會更新中儲存的 SSH 鍵`~/.ssh/azure_id_rsa.pub`名為使用者`myUsername`，名為 VM 上`myVM`中`myResourceGroup`。 使用您自己的值如下所示︰

```bash
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-file ~/.ssh/azure_id_rsa.pub
```


## <a name="using-the-vmaccess-extension"></a>使用 VMAccess 副檔名
Linux 副檔名為 VM 存取讀取 json 檔案，以定義要執行的動作。 這些動作會包括重設 SSHD、 重設 SSH 鍵，或新增使用者。 您仍可使用 Azure CLI 撥打 VMAccess 分機]，但是如有需要，您可以會重複使用 json 檔案跨多個 Vm。 這種方法可讓您建立的 json 檔案，然後呼叫給定的案例存放庫。

### <a name="reset-sshd"></a>重設 SSHD
建立一個名為檔案`PrivateConf.json`下列內容︰

```bash
{  
    "reset_ssh":"True"
}
```

使用 Azure CLI，然後呼叫`VMAccessForLinux`重設您 SSHD 連線指定 json 檔案的副檔名。 下列範例會重設命名 VM 上的 SSHD`myVM`中`myResourceGroup`。 使用您自己的值如下所示︰

```bash
azure vm extension set myResourceGroup myVM \
    VMAccessForLinux Microsoft.OSTCExtensions "1.2" \
    --private-config-path PrivateConf.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>重設使用者的 SSH 認證
如果 SSHD 正常運作，您可以重設酬謝使用者的認證。 若要重設使用者的密碼，請建立一個名為檔案`PrivateConf.json`。 下列範例會重設認證`myUsername`中指定的值`myPassword`。 輸入下列幾行至您`PrivateConf.json`檔案時，使用您自己的值︰

```bash
{
    "username":"myUsername", "password":"myPassword"
}
```

若要重設使用者的 SSH 金鑰，請先建立一個名為檔案或者`PrivateConf.json`。 下列範例會重設認證`myUsername`中指定的值`myPassword`，名為 VM 上`myVM`中`myResourceGroup`。 輸入下列幾行至您`PrivateConf.json`檔案時，使用您自己的值︰

```bash
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

建立您 json 檔案後，使用呼叫 Azure CLI`VMAccessForLinux`指定 json 檔案重設 SSH 使用者認證的分機號碼。 下列範例會重設認證，名為 VM 上的`myVM`中`myResourceGroup`。 使用您自己的值如下所示︰

```
azure vm extension set myResourceGroup myVM \
    VMAccessForLinux Microsoft.OSTCExtensions "1.2" \
    --private-config-path PrivateConf.json
```


## <a name="restart-a-vm"></a>重新啟動 VM
如果您有重設 SSH 設定及使用者認證，或發生錯誤如此一來，您可以嘗試重新啟動 VM 基礎計算問題的地址。

### <a name="azure-portal"></a>Azure 入口網站
若要重新啟動 VM 使用 Azure 入口網站，請選取您 VM，按一下 [***重新啟動**] 按鈕，如下列範例所示︰

![重新啟動 VM Azure 入口網站](./media/virtual-machines-linux-troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
下列範例會重新命名 VM `myVM` [資源] 群組中名為`myResourceGroup`。 使用您自己的值如下所示︰

```bash
azure vm restart --resource-group myResourceGroup --name myVM
```


## <a name="redeploy-a-vm"></a>重新部署 VM
您可以重新部署 VM 到另一個節點中 Azure 可能可以修正任何基礎網路問題。 重新部署 VM 的相關資訊，請參閱[重新部署至新的 Azure 節點的虛擬機器](virtual-machines-windows-redeploy-to-new-node.md)。

> [AZURE.NOTE] 這項作業完成後，請暫時磁碟資料將會遺失，就會更新動態虛擬機器與相關聯的 IP 位址。

### <a name="azure-portal"></a>Azure 入口網站
若要重新部署 VM 使用 Azure 入口網站，選取您的 VM 和向下**支援 + 疑難排解**區段捲動。 按一下 [**部署**] 按鈕，如下列範例所示︰

![重新部署 VM Azure 入口網站中](./media/virtual-machines-linux-troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
下列範例會重新命名的 VM `myVM` [資源] 群組中名為`myResourceGroup`。 使用您自己的值如下所示︰

```bash
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>建立使用傳統部署模型 Vm

請嘗試這些步驟來解決 Vm 建立使用傳統的部署模型的最常見的 SSH 連線失敗。 每個步驟之後，請嘗試重新連線到 VM。

- 重設從[Azure 入口網站](https://portal.azure.com)的遠端存取。 在 Azure 入口網站中，選取您 VM，按一下 [**重設遠端...** ] 按鈕。

- 重新啟動 VM。 在[Azure 入口網站](https://portal.azure.com)中，選取您 VM，按一下 [**重新啟動**] 按鈕。

    -或-

    在 [ [Azure 傳統入口網站](https://manage.windowsazure.com)中，選取 [**虛擬機器** > **執行個體** > **重新啟動**。

- 重新部署至新的 Azure 節點 VM。 如需如何重新部署 VM 的資訊，請參閱[重新部署至新的 Azure 節點的虛擬機器](virtual-machines-windows-redeploy-to-new-node.md)。

    這項作業完成後，請暫時磁碟資料將會遺失，就會更新動態虛擬機器與相關聯的 IP 位址。

- [如何重設密碼或 SSH Linux 型虛擬機器](virtual-machines-linux-classic-reset-access.md)中的指示，請依照下列︰
    - 重設密碼或 SSH 鍵。
    - 建立_sudo_使用者帳戶。
    - 重設 SSH 設定。

- 核取任何平台問題 VM 的資源健康狀況。<br>
  選取您的 VM 和**設定**中向下的捲動 > **檢查健康狀況**。


## <a name="additional-resources"></a>其他資源

- 如果您仍然無法 SSH 您 vm 之後步驟之後，請參閱[更詳細的疑難排解步驟](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md)來檢閱額外的步驟來解決您的問題。

- 如需有關如何疑難排解應用程式存取的詳細資訊，請參閱[疑難排解存取 Azure 虛擬機器上執行的應用程式](virtual-machines-linux-troubleshoot-app-connection.md)

- 如需更多疑難排解虛擬機器建立使用傳統的部署模型的詳細資訊，請參閱[如何重設密碼或 Linux 型虛擬機器 SSH](virtual-machines-linux-classic-reset-access.md)。
