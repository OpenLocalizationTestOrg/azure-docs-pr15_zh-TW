<properties
    pageTitle="建立使用 Azure 入口網站 Linux VM |Microsoft Azure"
    description="建立 Linux VM 使用 Azure 入口網站。"
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
    ms.topic="hero-article"
    ms.date="10/25/2016"
    ms.author="v-livech"
/>

# <a name="create-a-linux-vm-on-azure-using-the-portal"></a>Azure 使用入口網站上建立 Linux VM


本文將示範如何建立 Linux 虛擬機器中使用[Azure 入口網站](https://portal.azure.com/)。

的需求如下︰

- [Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)

- [SSH 公開及私密金鑰索引鍵的檔案](virtual-machines-linux-mac-create-ssh-keys.md)


1. 登入您的 Azure 帳戶身分識別與 Azure 入口網站，請按一下 [ **+ 新增**在左上角中︰

    ![screen1](../media/virtual-machines-linux-quick-create-portal/screen1.png)

2. 然後**Ubuntu 伺服器 14.04 LTS**從**精選應用程式**的圖像，清單，請按一下 [**虛擬機器**中**Marketplace** ]。  確認底部部署模型`Resource Manager`，然後按一下 [**建立**]。

    ![screen2](../media/virtual-machines-linux-quick-create-portal/screen2.png)

3. 在 [**基本功能**] 頁面中，輸入︰
    - vm 名稱
    - 管理員使用者的使用者名稱
    - 驗證類型設定為**SSH 公開金鑰**
    - SSH 公開金鑰為字串 (從您`~/.ssh/`目錄)
    - 資源群組名稱，或選取現有的群組

    按一下**[確定**]，以繼續，然後選擇 [虛擬記憶體大小。外觀看起來如下所示︰

    ![screen3](../media/virtual-machines-linux-quick-create-portal/screen3.png)

4. 選擇**DS1**大小]，在進階版 SSD 安裝 Ubuntu]，然後按一下 [**選取**要設定的設定]。

    ![screen4](../media/virtual-machines-linux-quick-create-portal/screen4.png)

5. 在 [**設定**]，保留預設值的儲存空間和網路的值，然後按一下**[確定**]，檢視摘要]。  選擇 DS1 通知磁碟類型設定為 [進階 SSD、 **S** notates SSD。

    ![screen5](../media/virtual-machines-linux-quick-create-portal/screen5.png)

6. 確認新 Ubuntu VM 的設定，然後按一下**[確定]**。

    ![screen6](../media/virtual-machines-linux-quick-create-portal/screen6.png)

7. 開啟入口網站儀表板，然後選擇 [**網路介面**中的 [您 NIC

    ![screen7](../media/virtual-machines-linux-quick-create-portal/screen7.png)

8. 開啟 [NIC 設定] 下的公用 IP 位址功能表

    ![screen8](../media/virtual-machines-linux-quick-create-portal/screen8.png)

9. 將使用 SSH 公開金鑰的公用 IP SSH

```
ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## <a name="next-steps"></a>後續步驟

現在您已建立 Linux VM 快速使用於測試或示範用途。 若要建立 Linux VM 自訂基礎結構，您可以依照這些文件。

- [使用範本的 Azure 上建立 Linux VM](virtual-machines-linux-cli-deploy-templates.md)
- [使用範本的 Azure 上建立 SSH 保護 Linux VM](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [建立使用 Azure CLI Linux VM](virtual-machines-linux-create-cli-complete.md)
