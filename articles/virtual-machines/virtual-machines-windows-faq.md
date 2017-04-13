<properties
    pageTitle="Windows Vm 的常見問題集 |Microsoft Azure"
    description="提供幾項資源管理員模型建立 Windows 虛擬機器常見問題的解答。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="cynthn"/>

# <a name="frequently-asked-question-about-windows-virtual-machines"></a>常見問題集的問題有關 Windows 虛擬機器 


本文中的 Windows Azure 使用資源管理員部署模型中建立的虛擬機器的一些常見問題。 本主題 Linux 版本，請參閱[常見問題集問題有關 Linux 虛擬機器](virtual-machines-linux-faq.md)

## <a name="what-can-i-run-on-an-azure-vm"></a>我可以在 Azure VM 來執行什麼？

所有訂閱者可以執行伺服器軟體 Azure 虛擬機器上。 執行 Microsoft 伺服器軟體 Azure 中的支援原則的相關資訊，請參閱[支援的 Azure 虛擬機器 Microsoft 伺服器軟體](https://support.microsoft.com/kb/2721672)

某些版本的 Windows 7 和 Windows 8.1 可 MSDN Azure 優勢訂閱者與 MSDN 開發和測試 Pay-As-You-Go 訂閱者，開發和測試工作。 如需詳細資訊，包括指示和限制，請參閱[MSDN 訂閱者的 Windows 用戶端圖像](http://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/)。 


## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>我可以搭配虛擬機器使用多少儲存空間？

每個資料磁碟可以是 1 TB。 您可以使用的資料磁碟數目而定的虛擬機器大小。 如需詳細資訊，請參閱[虛擬機器的大小](virtual-machines-windows-sizes.md)。

Azure 儲存體帳戶提供作業系統磁碟及任何資料磁碟的儲存空間。 每個磁碟是儲存為網頁 blob 的.vhd 檔案。 定價詳細資料，請參閱[儲存定價詳細資料](https://azure.microsoft.com/pricing/details/storage/)。


## <a name="how-can-i-access-my-virtual-machine"></a>如何可以存取我的虛擬機器？

連線遠端 Windows vm 使用遠端桌面連線 (RDP)。 如需相關指示，請參閱[如何連線並登入執行 Windows Azure 虛擬機器](virtual-machines-windows-connect-logon.md)。 支援的兩個同時連線最大值，除非將伺服器設定為遠端桌面服務的階段主持人。  


如果您有使用遠端桌面的問題，請參閱[疑難排解遠端桌面連線到 windows Azure 虛擬機器](virtual-machines-windows-troubleshoot-rdp-connection.md)。 

如果您熟悉 HYPER-V，您可能會尋找 VMConnect 類似的工具。 Azure 不會提供類似的工具，因為虛擬機器主控台存取不受支援。

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>可以使用暫存的磁碟 （依預設 d 磁碟機） 來儲存資料嗎？

不使用暫存的磁碟來儲存資料。 這不是暫存的儲存空間，讓您想要保留無法復原的資料的風險。 虛擬機器移至不同的主應用程式時，可能會發生資料遺失。 調整大小虛擬機器、 更新主應用程式或硬體失敗 」 主機上的移動虛擬機器可能的原因。

如果您需要使用 d 磁碟機字母的應用程式，可以暫時磁碟使用 d 以外的重新指派磁碟機字母。 如需相關指示，請參閱[變更 Windows 暫時磁碟機字母](virtual-machines-windows-classic-change-drive-letter.md)。

## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>如何變更暫存的磁碟機字母？

您可以變更的磁碟機字母移動頁面檔案，並重新指派磁碟機字母，但您需要請確定您執行的步驟，以特定順序。 如需相關指示，請參閱[變更 Windows 暫存磁碟機字母](virtual-machines-windows-classic-change-drive-letter.md)。

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>可以新增現有的 VM 可用性集嗎？

[否]。 如果您想要加入的可用性設定您 VM，必須先建立集中 VM。 目前沒有 VM 新增到可用性之後建立之後設定郵件的方式。

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>上載至 Azure 虛擬機器可以？

[是]。 如需相關指示，請參閱[上傳至 Azure 的 Windows VM 圖像](virtual-machines-windows-upload-image.md)

## <a name="can-i-resize-the-os-disk"></a>可以調整大小 OS 磁碟？

[是]。 如需相關指示，請參閱[如何展開的 [資源群組 Azure 虛擬機器 OS 磁碟機](virtual-machines-windows-expand-os-disk.md)。

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>我可以複製或複製現有的 Azure VM 嗎？

[是]。 如需相關指示，請參閱[如何建立 Windows 虛擬機器中的資源管理員部署模型的複本](virtual-machines-windows-vhd-copy.md)。

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>為什麼我看不到加拿大中央和加拿大東亞區域透過 Azure 資源管理員？

兩個新區域加拿大中央和加拿大東亞不會自動註冊的虛擬機器建立現有 Azure 訂閱。 虛擬機器部署透過 Azure 的入口網站，以使用 Azure 資源管理員的其他區域時，會自動完成此登錄。 虛擬機器部署至任何其他 Azure 區域之後，後續的虛擬機器應該可使用新的區域。

## <a name="does-azure-support-linux-vms"></a>Azure 是否支援 Linux Vm？

[是]。 若要快速建立 Linux VM 試試，請參閱[建立 Linux VM Azure 使用入口網站上](virtual-machines-linux-quick-create-portal.md)。

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>我可以新增 NIC 我 vm 建立後嗎？

[否]。 新增 NIC 才可建立時間。

## <a name="are-there-any-computer-name-requirements"></a>是否有任何電腦名稱需求？

[是]。 電腦名稱可以是 15 個字元的長度最大值。 如需詳細資訊，解決命名您的資源，請參閱[基礎結構命名方針](virtual-machines-windows-infrastructure-naming-guidelines.md)。

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>建立 VM 時有哪些使用者名稱需求？

使用者名稱可以 20 個字元的最大值，而且無法在一段結束 (「。 」)。 

不允許下列使用者名稱︰

<table>
    <tr>
        <td style="text-align:center">系統管理員 </td><td style="text-align:center"> 管理員 </td><td style="text-align:center"> 使用者 </td><td style="text-align:center"> user1</td>
    </tr>
    <tr>
        <td style="text-align:center">測試 </td><td style="text-align:center"> user2 </td><td style="text-align:center"> test1 </td><td style="text-align:center"> user3</td>
    </tr>
    <tr>
        <td style="text-align:center">admin1 </td><td style="text-align:center"> 1 </td><td style="text-align:center"> 123 </td><td style="text-align:center"> 在</td>
    </tr>
    <tr>
        <td style="text-align:center">actuser  </td><td style="text-align:center"> adm </td><td style="text-align:center"> admin2 </td><td style="text-align:center"> aspnet</td>
    </tr>
    <tr>
        <td style="text-align:center">備份 </td><td style="text-align:center"> 主控台 </td><td style="text-align:center"> david </td><td style="text-align:center"> 來賓</td>
    </tr>
    <tr>
        <td style="text-align:center">名字 </td><td style="text-align:center"> 擁有者 </td><td style="text-align:center"> 根 </td><td style="text-align:center"> 伺服器</td>
    </tr>
    <tr>
        <td style="text-align:center">sql </td><td style="text-align:center"> 支援 </td><td style="text-align:center"> support_388945a0 </td><td style="text-align:center"> 系統</td>
    </tr>
    <tr>
        <td style="text-align:center">test2 </td><td style="text-align:center"> test3 </td><td style="text-align:center"> user4 </td><td style="text-align:center"> user5</td>
    </tr>
</table>

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>建立 VM 時有哪些密碼要求？

密碼 8 123 字元的長度，並符合下列 4 複雜度需求登出 3:

- 有較低的字元
- 有上方的字元
- 有數字
- 具有特殊字元 （Regex 符合 [\W_]）

不允許下列密碼︰

<table>
    <tr>
        <td style="text-align:center">abc@123</td><td style="text-align:center">P@$$w0rd</td><td style="text-align:center">P@ssw0rd</td><td style="text-align:center">P@ssword123</td><td style="text-align:center">Pa$ $ word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td><td style="text-align:center">密碼 ！</td><td style="text-align:center">密碼 1</td><td style="text-align:center">Password22</td><td style="text-align:center">iloveyou ！</td>
    </tr>
</table>
