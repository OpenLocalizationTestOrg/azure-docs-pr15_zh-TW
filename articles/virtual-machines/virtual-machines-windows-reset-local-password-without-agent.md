<properties
   pageTitle="密碼本機 Windows Azure 來賓代理程式未安裝時 |Microsoft Azure"
   description="如何重設本機的 Windows 使用者帳戶的密碼，當 Azure 來賓代理程式未安裝或 VM 上運作"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/05/2016"
   ms.author="iainfou"/>

# <a name="how-to-reset-local-windows-password-for-azure-vm"></a>如何重設的 Azure VM 本機的 Windows 密碼
您可以重設 VM Azure 使用[Azure 入口網站或 PowerShell 的 Azure](virtual-machines-windows-reset-rdp.md)提供安裝 Azure 來賓代理程式中的本機 Windows 密碼。 這個方法是為 Azure VM 重設密碼的主要方式。 如果您沒有回應、 Azure 來賓代理程式發生問題或上傳的自訂圖像之後安裝失敗，您可以手動重設 Windows 密碼。 本文詳細說明如何藉由將來源 OS 虛擬磁碟附加至另一個 VM 重設本機帳戶的密碼。 

> [AZURE.WARNING] 僅使用此程序的最後一個步驟。 請務必嘗試重設密碼，使用[Azure 入口網站或 PowerShell 的 Azure](virtual-machines-windows-reset-rdp.md)第一次。


## <a name="overview-of-the-process"></a>程序概觀
執行本機 Azure 來賓代理程式沒有存取時，Windows vm Azure 中重設密碼的核心步驟如下所示︰

- 刪除來源 VM。 虛擬磁碟會保留。
- Azure 訂閱中的另一個 vm 附加來源 VM OS 磁碟。 此 VM 稱為 「 疑難排解 VM 」。
- 使用疑難排解 VM 建立來源 VM OS 磁碟上的某些設定檔。
- 中斷連結疑難排解 VM VM OS 的磁碟。
- 使用資源管理員範本來建立 VM，使用原始的虛擬磁碟。
- 新的 VM 開機時，您建立設定檔更新所需的使用者的密碼。


## <a name="detailed-steps"></a>詳細的步驟
請務必嘗試重設密碼，使用[Azure 入口網站或 PowerShell 的 Azure](virtual-machines-windows-reset-rdp.md)之前先嘗試以下步驟。 請確定您擁有您 VM 備份，在您開始之前。 

1. 刪除受影響的 VM Azure 入口網站中。 刪除 VM 只會刪除中繼資料，VM Azure 中的參照。 刪除 VM 時，仍會保留虛擬磁碟︰

    - 選取 VM Azure 入口網站中，按一下 [*刪除*]:

    ![刪除現有 VM](./media/virtual-machines-windows-reset-local-password-without-guest-agent/delete_vm.png)

2. 疑難排解 vm 附加來源 VM OS 磁碟。 疑難排解 VM 必須在來源 VM OS 磁碟為相同的區域 (例如`West US`):

    - 您可以選取 [疑難排解 VM Azure 入口網站中。 按一下 [*磁碟* | *附加現有*︰

    ![將現有的磁碟連結](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_attach_existing.png)

    選取 [ *VHD 檔案*，然後選取 [包含您的來源 VM 儲存帳戶︰

    ![選取儲存的帳戶](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_storageaccount.PNG)

    選取來源容器。 來源容器通常是*vhd*:

    ![選取存放容器](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_container.png)

    選取要附加 OS vhd。 按一下 [完成程序的 [*選取*︰

    ![選取來源虛擬磁碟](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_source_vhd.png)

3. 使用遠端桌面版的疑難排解 vm 連線，並確保來源 VM OS 磁碟都可看到︰

    - 選取疑難排解 VM Azure 入口網站中，按一下 [*連線*]。
    - 開啟下載的 RDP 檔案。 輸入使用者名稱和密碼的疑難排解 VM。
    - 在檔案總管] 中，尋找您所附加的資料磁碟。 如果來源 VM 的 VHD 疑難排解 vm 附加唯一資料磁碟，它應該 f︰ 磁碟機︰

    ![檢視附加的資料磁碟](./media/virtual-machines-windows-reset-local-password-without-guest-agent/troubleshooting_vm_fileexplorer.png)

4. 建立`gpt.ini`中`\Windows\System32\GroupPolicy`來源 VM 的磁碟機上 （如果 gpt.ini 存在，重新命名為 [gpt.ini.bak):

    > [AZURE.WARNING] 請確定您在 c:\windows\tmp，疑難排解 vm OS 磁碟機中沒有不小心建立下列檔案。 在您的來源附加為資料磁碟的 VM OS 磁碟上建立下列檔案。

    - 新增至下列幾行`gpt.ini`您建立的檔案︰

    ```
    [General]
    gPCFunctionalityVersion=2
    gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
    Version=1
    ```

    ![建立 gpt.ini](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_gpt_ini.png)
 
5. 建立`scripts.ini`中`\Windows\System32\GroupPolicy\Machine\Scripts`。 請確定 [顯示隱藏的資料夾。 如有需要建立`Machine`或`Scripts`資料夾。

    - 新增下列幾行`scripts.ini`您建立的檔案︰

    ```
    [Startup]
    0CmdLine=C:\Windows\System32\FixAzureVM.cmd
    0Parameters=
    ```

    ![建立 scripts.ini](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_scripts_ini.png)
 
6. 建立`FixAzureVM.cmd`中`\Windows\System32`下列內容，取代`<username>`和`<newpassword>`使用您自己的值︰

    ```
    NET USER <username> <newpassword>
    ```

    ![建立 FixAzureVM.cmd](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_fixazure_cmd.png)

    定義新密碼時，您必須符合您 VM 設定的密碼複雜度需求。

7. 在 Azure 入口網站中卸離疑難排解 VM 從磁碟機︰

    - 選取疑難排解 VM Azure 入口網站中，按一下 [*磁碟*。
    - 選取資料磁碟附加在步驟 2 中，按一下 [*中斷連結*︰

    ![卸離磁碟](./media/virtual-machines-windows-reset-local-password-without-guest-agent/detach_disk.png)

8. 建立 VM 之前，取得您的來源 OS 磁碟 URI:

    - 選取的儲存空間帳戶 Azure 入口網站中，按一下 [*二進位大型物件*。
    - 選取 [容器]。 來源容器通常是*vhd*:

    ![選取 [儲存帳戶 blob](./media/virtual-machines-windows-reset-local-password-without-guest-agent/select_storage_details.png)

    選取您的來源 VM OS VHD，然後按一下 [ *URL*名稱旁的 [*複製*] 按鈕︰

    ![複製磁碟 URI](./media/virtual-machines-windows-reset-local-password-without-guest-agent/copy_source_vhd_uri.png)

9. 從來源 VM OS 磁碟建立 VM:

    - 使用[此 Azure 資源管理員範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd)來建立 VM 從特殊 VHD。 按一下 [ `Deploy to Azure` ] 按鈕，開啟 Azure 入口網站，為您填入樣板的詳細資料。
    - 如果您想要保留 vm 所有先前的設定，請選取 [*編輯範本*提供您現有的 VNet、 子網路、 網路介面卡或公用 IP]。
    - 在 [`OSDISKVHDURI`參數文字方塊中，貼上的來源 VHD URI 取得在先前的步驟︰

    ![從範本建立 VM](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_new_vm_from_template.png)

10. 正在執行新 VM 之後，連線到遠端桌面使用您在所指定的新密碼 VM`FixAzureVM.cmd`指令碼。

11. 從您的新 vm 遠端工作階段，移除清理環境下列檔案︰

    - 從 %windir%\System32
        - 移除 FixAzureVM.cmd
    - 從 %windir%\System32\GroupPolicy\Machine\
        - 移除 scripts.ini
    - 從 %windir%\System32\GroupPolicy
        - 移除 gpt.ini （如果 gpt.ini 存在於之前，而您重新命名該 gpt.ini.bak，重新命名.bak 檔案返回 gpt.ini）

## <a name="next-steps"></a>後續步驟
如果您仍然無法連線使用遠端桌面，請參閱[RDP 疑難排解指南](virtual-machines-windows-troubleshoot-rdp-connection.md)。 疑難排解方法，而不是特定的步驟查看[詳細的 RDP 疑難排解指南](virtual-machines-windows-detailed-troubleshoot-rdp.md)。 您也可以[開啟 Azure 支援要求](https://azure.microsoft.com/support/options/)的實際操作的協助。