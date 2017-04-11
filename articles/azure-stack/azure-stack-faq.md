<properties
    pageTitle="常見問題集的 Azure 堆疊 |Microsoft Azure"
    description="Azure 堆疊常見問題集。"
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="helaw"/>

# <a name="frequently-asked-questions-for-azure-stack"></a>Azure 堆疊的常見問題

## <a name="deployment"></a>部署

### <a name="do-i-need-to-format-my-data-disks-before-starting-or-restarting-an-installation"></a>我需要我的資料磁碟格式化之前啟動或重新啟動安裝嗎？

磁碟應該是以原始格式。 如果您重新安裝作業系統，您可能需要要檢查是否仍然有舊的儲存集區並刪除使用下列步驟︰

1. 開啟 [伺服器管理員。
2. 選取儲存集區。
3. 請參閱是否列出儲存集區。
4. 如果列出和啟用讀 / 寫，以滑鼠右鍵按一下**儲存集區**。
5. 以滑鼠右鍵按一下**虛擬硬碟**（左下角），選取 [刪除]。
6. 以滑鼠右鍵按一下 [**儲存集區**，然後按一下 [刪除]。
7. 再次啟動 Azure 堆疊指令碼，並確認磁碟驗證傳遞。

或者，您可以使用下列指令碼︰

```PowerShell
$pools = Get-StoragePool -IsPrimordial $False -ErrorVariable Err -ErrorAction SilentlyContinue
if ($pools -ne $null) {
  $pools| Set-StoragePool -IsReadOnly $False -ErrorVariable Err -ErrorAction SilentlyContinue
  $pools = Get-StoragePool -IsPrimordial $False -ErrorVariable Err -ErrorAction SilentlyContinue
  $pools | Get-VirtualDisk | Remove-VirtualDisk -Confirm:$False
  $pools | Remove-StoragePool -Confirm:$False
}
```

### <a name="can-i-use-all-ssd-disks-for-the-storage-pool-in-the-poc-installation"></a>可以使用所有 SSD 磁碟 POC 安裝中的儲存集區嗎？

在這個版本中不支援這項設定。  如需詳細資訊，請參閱[需求指南](azure-stack-deploy.md)如需詳細資訊。

### <a name="can-i-use-nvme-data-disks-for-the-microsoft-azure-stack-poc"></a>我可以使用 Microsoft Azure 堆疊 POC NVMe 資料磁碟嗎？

時儲存空格直接支援 NVMe 磁碟，Azure 堆疊只支援可能的磁碟機類型和可能的組合的子集直接儲存空間。 

### <a name="how-can-i-reinstall-azure-stack"></a>如何重新安裝 Azure 堆疊？
您可以遵循[重新部署指南](azure-stack-redeploy.md)中的步驟進行。  

## <a name="tenant"></a>租用戶

### <a name="can-i-deploy-my-own-images-as-a-tenant"></a>可以部署我自己的圖像作為租用戶嗎？

是的就像在 Azure 租用戶可以上傳圖像 Azure 堆疊中的除了使用的服務管理員圖像。 如需概觀，請參閱[新增 VM 圖像](azure-stack-add-vm-image.md)。 

## <a name="testing"></a>測試

### <a name="can-i-use-nested-virtualization-to-test-the-microsoft-azure-stack-poc"></a>可以使用巢狀虛擬化測試 Microsoft Azure 堆疊 POC 嗎？

巢狀虛擬化不受支援或測試 Azure 堆疊技術預覽 2。

## <a name="virtual-machines"></a>虛擬機器

### <a name="does-azure-stack-support-dynamic-disks-for-virtual-machines"></a>Azure 堆疊支援動態磁碟的虛擬機器嗎？

Azure 堆疊不支援動態磁碟。

## <a name="next-steps"></a>後續步驟

[疑難排解](azure-stack-troubleshooting.md)
