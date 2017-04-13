<properties
    pageTitle="常見問題集 Linux Vm |Microsoft Azure"
    description="提供幾項資源管理員模型建立 Linux 虛擬機器常見問題的解答。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="cynthn"/>

# <a name="frequently-asked-question-about-linux-virtual-machines"></a>常見問題集的問題有關 Linux 虛擬機器

本文中的一些常見問題 Linux 虛擬機器 Azure 使用資源管理員部署模型中所建立。 本主題的 Windows 版本，請參閱[常見問題集問題有關 Windows 虛擬機器](virtual-machines-windows-faq.md)

## <a name="what-can-i-run-on-an-azure-vm"></a>我可以在 Azure VM 來執行什麼？

所有訂閱者可以執行伺服器軟體 Azure 虛擬機器上。 如需詳細資訊，請參閱[在 Azure-Endorsed 散佈 Linux](virtual-machines-linux-endorsed-distros.md)


## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>我可以搭配虛擬機器使用多少儲存空間？

每個資料磁碟可以是 1 TB。 您可以使用的資料磁碟數目而定的虛擬機器大小。 如需詳細資訊，請參閱[虛擬機器的大小](virtual-machines-linux-sizes.md)。

Azure 儲存體帳戶提供作業系統磁碟及任何資料磁碟的儲存空間。 每個磁碟是儲存為網頁 blob 的.vhd 檔案。 定價詳細資料，請參閱[儲存定價詳細資料](https://azure.microsoft.com/pricing/details/storage/)。


## <a name="how-can-i-access-my-virtual-machine"></a>如何可以存取我的虛擬機器？

遠端連接到登入虛擬機器中，使用安全命令介面 (SSH)。 請參閱如何連線[從 Windows](virtual-machines-linux-ssh-from-windows.md)或[Linux 和 Mac](virtual-machines-linux-mac-create-ssh-keys.md)的指示。 根據預設，SSH 可讓最大值 10 同時連線。 您可以編輯設定檔，以提高這個數字。


如果您遇到問題，請參閱[疑難排解安全命令介面 (SSH) 連線](virtual-machines-linux-troubleshoot-ssh-connection.md)。


## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>可以使用暫存的磁碟 (/ 開發/sdb1) 以儲存資料嗎？

不使用暫存的磁碟 (/ 開發/sdb1) 來儲存資料。 就只會有暫存的儲存空間。 您可能會遺失無法復原的資料。


## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>我可以複製或複製現有的 Azure VM 嗎？

[是]。 如需相關指示，請參閱[如何建立 Linux 虛擬機器中的資源管理員部署模型的複本](virtual-machines-linux-copy-vm.md)。


## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>為什麼我看不到加拿大中央和加拿大東亞區域透過 Azure 資源管理員？

兩個新區域加拿大中央和加拿大東亞不會自動註冊的虛擬機器建立現有 Azure 訂閱。 虛擬機器部署透過 Azure 的入口網站，以使用 Azure 資源管理員的其他區域時，會自動完成此登錄。 虛擬機器部署至任何其他 Azure 區域之後，後續的虛擬機器應該可使用新的區域。


## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>我可以新增 NIC 我 vm 建立後嗎？

[否]。 新增 NIC 才可建立時間。


## <a name="are-there-any-computer-name-requirements"></a>是否有任何電腦名稱需求？

[是]。 電腦名稱可以是 64 個字元的長度的最大值。 如需詳細資訊，解決命名您的資源，請參閱[基礎結構命名方針](virtual-machines-linux-infrastructure-naming-guidelines.md)。


## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>建立 VM 時有哪些使用者名稱需求？

使用者名稱必須是 1-64 個字元的長度。

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

密碼 6 72 字元的長度，並符合下列 4 複雜度需求登出 3:

- 有較低的字元
- 有上方的字元
- 有數字
- 具有特殊字元 （Regex 符合 [\W_]）

不允許下列密碼︰

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">Pa$ $ word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">密碼 ！</td>
        <td style="text-align:center">密碼 1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">iloveyou ！</td>
    </tr>
</table>
