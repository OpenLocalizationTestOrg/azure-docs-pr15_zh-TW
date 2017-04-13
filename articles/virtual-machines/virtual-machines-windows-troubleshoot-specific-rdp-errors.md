<properties
    pageTitle="Azure Vm 的特定 RDP 錯誤訊息 |Microsoft Azure"
    description="瞭解特定的錯誤訊息，您可能會收到時，嘗試使用遠端桌面連線到 Windows 虛擬機器中 Azure"
    keywords="遠端桌面錯誤、 遠端桌面連線錯誤，無法連線至 VM 遠端桌面疑難排解"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="10/14/2016"
    ms.author="iainfou"/>

# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>疑難排解 Windows vm Azure 中的特定 RDP 錯誤訊息
使用遠端桌面連線到 Windows 虛擬機器 (VM) Azure 中時，您可能會收到特定的錯誤訊息。 本文詳細說明一些常見的錯誤訊息進行疑難排解，以及疑難排解步驟解決方法。 如果您有使用 RDP 您 vm 連線問題，但不是會發生此特定錯誤訊息，請參閱[疑難排解指南遠端桌面](virtual-machines-windows-troubleshoot-rdp-connection.md)。

在特定的錯誤訊息的資訊，請參閱下列各項︰

- [遠端工作階段中斷，因為沒有遠端桌面授權伺服器提供授權](#rdplicense)。
- [遠端桌面找不到"名稱"的電腦](#rdpname)。
- [驗證錯誤。無法連線到本機安全性授權](#rdpauth)。
- [Windows 安全性錯誤︰ 無法解決您的認證](#wincred)。
- [[這部電腦無法連線到遠端電腦](#rdpconnect)]。

<a id="rdplicense"></a>
## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>遠端工作階段中斷連線，因為沒有遠端桌面授權伺服器提供授權。

原因︰ 遠端桌面伺服器角色 120 天授權限期已過期，您必須安裝授權。

因應措施，儲存 RDP 檔案的本機複本從入口網站及執行此命令連線的 PowerShell 命令提示字元。 此步驟會停用授權只的連線︰

        mstsc <File name>.RDP /admin

如果您不是需要兩個以上的 vm 的同時遠端桌面連線，您可以使用伺服器管理員，若要移除的遠端桌面伺服器角色。

如需詳細資訊，請參閱的部落格文章[Azure VM 失敗，並顯示 「 無可用的遠端桌面授權伺服器 」](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/)。

<a id="rdpname"></a>
## <a name="remote-desktop-cant-find-the-computer-name"></a>遠端桌面找不到"名稱"的電腦。

原因︰ 遠端桌面用戶端電腦上的無法解決設定 RDP 檔案中電腦的名稱。

可能的方案︰

- 如果您是組織的網路，請確定您的電腦有權存取的 proxy 伺服器，並可以傳送給該 HTTPS 流量。

- 如果您使用的儲存在本機的 RDP 檔案，請嘗試使用項目所產生的入口網站。 此步驟可確保您有虛擬機器，或雲端服務和 VM 的端點連接埠的正確 DNS 名稱。 以下是產生入口網站的範例 RDP 檔案︰

        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

具有此 RDP 檔案的地址部分︰
- 包含 VM (「 tailspin-azdatatier.cloudapp.net 「 在此範例中) 的雲端服務的完整的網域名稱。

- 遠端桌面流量 (55919) 的端點外部 TCP 連接埠。

<a id="rdpauth"></a>
## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>驗證錯誤。 本機安全性授權無法連絡。

原因︰ 目標 VM 無法找出您的認證的使用者名稱部分中的安全性授權。

您的使用者名稱時在表單中*SecurityAuthority*\\*使用者名稱*(範例︰ CORP\User1)， *SecurityAuthority*部分為 VM 的電腦名稱 （適用於本機安全性授權） 或 Active Directory 網域名稱。

可能的方案︰

- 如果是本機的 vm 帳戶，請確定 VM 名稱正確。

- 如果帳戶是在 Active Directory 網域，請檢查拼字的網域名稱。

- 如果是 Active Directory 網域帳戶，且的網域名稱的拼字正確，請確認此網域控制站可在該網域。 是包含網域控制站是無法使用，因為它尚未啟動的網域控制站的 Azure 虛擬網路中的一般問題。 解決方法您可以使用本機系統管理員帳戶，而不是網域帳戶。

<a id="wincred"></a>
## <a name="windows-security-error-your-credentials-did-not-work"></a>Windows 安全性錯誤︰ 無法解決您的認證。

原因︰ 目標 VM 無法驗證您的帳戶名稱和密碼。

Windows 的電腦可以驗證本機帳戶或的網域帳戶的認證。

- 本機帳戶，請使用*電腦名稱*\\*使用者名稱*的語法 (範例︰ SQL1\Admin4798)。
- 網域帳戶使用*網域名稱*\\*使用者名稱*的語法 (範例︰ CONTOSO\peterodman)。

如果您已升級您的網域控制站在新的 Active Directory 樹系 VM，您登入本機系統管理員帳戶會轉換為相同的密碼的新樹系和網域中的對等帳戶中。 然後會刪除本機帳戶。

例如，如果您使用本機帳戶 DC1\DCAdmin，登入，然後升階為新的樹系 corp.contoso.com 網域的網域控制站的虛擬機器，DC1\DCAdmin 本機帳戶會刪除與相同的密碼與建立新的網域帳戶 (CORP\DCAdmin)。

請確定您的帳戶名稱會以正確的帳戶，可以驗證的虛擬機器的名稱和密碼正確。

如果您需要變更本機系統管理員帳戶的密碼，請參閱[如何重設密碼或遠端桌面服務的 Windows 虛擬機器](virtual-machines-windows-reset-rdp.md)。

<a id="rdpconnect"></a>
## <a name="this-computer-cant-connect-to-the-remote-computer"></a>這台電腦無法連線到遠端電腦。

原因︰ 用來連線的帳戶沒有遠端桌面登入權限。

每個 Windows 電腦有一個遠端桌面使用者本機群組，其中包含的帳戶和遠端可以登入它的群組。 本機系統管理員群組的成員也有存取權，即使這些帳戶不會列出遠端桌面使用者的 [本機] 群組中。 網域的電腦，本機管理員群組，也包含該網域的網域管理員。

請確定您用來與連線的帳戶有遠端桌面登入權限。 解決方法使用網域或本機系統管理員帳戶透過遠端桌面連線。 若要新增的遠端桌面使用者本機群組所要的帳戶，請使用 Microsoft 管理主控台嵌入式管理單元 (**系統工具] > 本機使用者和群組] > 群組 > 遠端桌面使用者**)。


## <a name="next-steps"></a>後續步驟
如果沒有任何這些錯誤發生，您有使用 RDP 連線未知的問題，請參閱[疑難排解指南遠端桌面](virtual-machines-windows-troubleshoot-rdp-connection.md)。

- [Azure IaaS (Windows) 診斷程式套件](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)
- 如需疑難排解存取 VM 上執行應用程式中的步驟，請參閱[疑難排解存取 Azure VM 上執行應用程式](virtual-machines-linux-troubleshoot-app-connection.md)。
- 如果您有使用安全命令介面 (SSH) 連線到 Linux VM Azure 中的問題，請參閱[疑難排解 SSH 連線 Linux VM Azure 中](virtual-machines-linux-troubleshoot-ssh-connection.md)。