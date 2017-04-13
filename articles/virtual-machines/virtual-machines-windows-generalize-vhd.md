<properties
    pageTitle="一般化 Windows VHD |Microsoft Azure"
    description="瞭解如何使用 Sysprep 一般化 Windows VM 與資源管理員部署模型搭配使用。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="cynthn"/>
    
    
    
    
# <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>一般化使用 Sysprep Windows 虛擬機器

本節說明如何一般化 Windows 虛擬機器作為圖像。 Sysprep 會移除所有的個人帳戶資訊，除此之外，，並準備要做為圖像的電腦。 如需 Sysprep 的詳細資訊，請參閱[如何使用 Sysprep: 簡介](http://technet.microsoft.com/library/bb457073.aspx)。

請確定電腦上執行的伺服器角色所支援的 Sysprep。 如需詳細資訊，請參閱[Sysprep 支援的伺服器角色](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

>[AZURE.IMPORTANT] 如果您正在執行 Sysprep 之前將您 VHD 上傳至 Azure 第一次，請確定您已[準備好您 VM](virtual-machines-windows-prepare-for-upload-vhd-image.md)之前執行 Sysprep。 

1. 登入 Windows 虛擬機器。

2. 以管理員身分開啟命令提示字元視窗。 將目錄變更**%windir%\system32\sysprep**，然後再執行`sysprep.exe`。

3. 在**系統準備工具**] 對話方塊中，選取 [**輸入系統的全新體驗 (OOBE)**，然後確定已選取 [**一般化**] 核取方塊。

4. 在 [**關閉選項**] 中，選取 [**關閉**]。

5. 按一下**[確定]**。

    ![開始 Sysprep](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)

6. Sysprep 完成後，請將其關閉虛擬機器中。 

## <a name="next-steps"></a>後續步驟

- 如果 VM 內部部署，您可以立即[上傳至 Azure VHD](virtual-machines-windows-upload-image.md)。
- 如果 VM 是 Azure 中，您可以[建立一般化 VM 中的圖像](virtual-machines-windows-capture-image.md)。