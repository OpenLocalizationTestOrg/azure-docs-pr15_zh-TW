<properties
   pageTitle="修復服務地窖常見問題集 |Microsoft Azure"
   description="這個版本的常見問題集支援 Azure 備份 service 的公用預覽版本。 常見問題備份代理程式、 備份和保留、 復原、 安全性和 Azure 備份解決方案其他常見問題的解答。"
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="備份的方案。備份服務"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.date="10/21/2016"
     ms.author="trinadhk; markgal; jimpark;"/>

# <a name="recovery-services-vault---faq"></a>復原 Services 保存庫-常見問題集


本文提供有關修復服務保存庫，其補充[Azure 備份常見問題集](backup-azure-backup-faq.md)。 Azure 備份常見問題集提供完整的問與答備份 Azure 服務。  

您可以瞭解這份文件或相關的文件的 [Disqus] 區段中的 Azure 備份提出問題。 您也可以張貼[討論區](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)中的 Azure 備份服務的相關問題。

## <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-classic-mode-still-supported-br"></a>復原服務保存庫是依據資源管理員。 備份保存庫 （傳統模式） 仍然支援？ <br/>
是的仍然支援備份保存庫。 在 [[傳統入口網站](https://manage.windowsazure.com)中建立備份保存庫。 [Azure 入口網站](https://portal.azure.com)中建立修復服務保存庫。 不過，我們強烈建議您為所有未來的增強功能建立復原服務保存庫會只適用於修復服務保存庫。

## <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>我是否可以將備份保存庫移轉到修復服務保存庫？ <br/>
很抱歉否，此時您無法移轉備份保存庫的內容來修復服務保存庫。 我們正在新增這項功能，但無法使用公用預覽的一部分。

## <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>執行修復服務保存庫支援傳統 Vm 或資源管理員 Vm 嗎？ <br/>
復原服務保存庫支援兩種模型。  您可以備份在 [傳統] 入口網站 （也就是傳統模式 Vm） 中建立或 VM 建立 Azure 入口網站 （也就是依據資源管理員） 中修復服務保存庫。

## <a name="i-have-backed-up-my-classic-vms-in-backup-vault-now-i-want-to-migrate-my-vms-from-classic-mode-to-resource-manager-mode--how-can-i-backup-them-in-recovery-services-vault"></a>我已經備份備份保存庫在我傳統 Vm。 現在我要移轉到資源管理員模式我 Vm 傳統模式。  我如何可以備份其中復原服務保存庫？
當您從傳統移轉 Vm 資源管理員模式時，備份的傳統 Vm 備份保存庫中不會自動移轉到復原服務保存庫。 請遵循下列步驟來 VM 備份移轉︰

1. 在備份保存庫，請移至 [**受保護的項目**] 索引標籤並選取 VM。 按一下 [[停止保護](backup-azure-manage-vms-classic.md#stop-protecting-virtual-machines)]。 不要*刪除相關聯的備份資料*] 選項**未核取**。
2. 資源管理員模式傳統模式移轉虛擬機器。 請確定儲存和網路對應至虛擬機器也移轉到資源管理員模式。
3. 建立復原服務保存庫及設定移轉的虛擬機器使用**備份**動作上方保存庫儀表板上的備份。 深入瞭解如何[啟用復原服務保存庫中的備份](backup-azure-vms-first-look-arm.md)
