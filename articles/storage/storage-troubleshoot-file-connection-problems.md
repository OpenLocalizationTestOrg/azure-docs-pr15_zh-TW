<properties
    pageTitle="Azure 檔案儲存空間問題的疑難排解 |Microsoft Azure"
    description="Azure 檔案儲存空間問題的疑難排解"
    services="storage"
    documentationCenter=""
    authors="genlin"
    manager="felixwu"
    editor="na"
    tags="storage"/>

<tags
    ms.service="storage"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="genli"/>

# <a name="troubleshooting-azure-file-storage-problems"></a>Azure 檔案儲存空間問題的疑難排解

本文將列出當您從 Windows 和 Linux 的用戶端連線至 Microsoft Azure 檔案儲存空間相關聯的常見問題。 它也會提供可能的原因與解決方案有下列問題。

**（Windows 和 Linux 的用戶端的發生） 的一般問題**

- [嘗試開啟檔案時，會產生配額錯誤。](#quotaerror)

- [當您從 Windows 或 Linux 存取 Azure 檔案儲存空間的效能變慢](#slowboth)

**Windows 用戶端問題**

- [當您從 Windows 8.1 或 Windows Server 2012 R2 存取 Azure 檔案儲存空間的效能變慢](#windowsslow)

- [錯誤 53 嘗試裝載 Azure 檔案共用](#error53)

- [網路使用已順利完成，但卻沒看到 Azure 共用裝載 Windows 檔案總管中的檔案](#netuse)

- [儲存我的帳戶包含 「 / 」，請使用命令失敗](#slashfails)

- [我的應用程式或服務無法存取裝載的 Azure 檔案的磁碟機]。](#accessfiledrive)

- [若要最佳化效能的其他建議](#additional)

**Linux 用戶端問題**

- [「 您要複製檔案到不支援加密的目的地 」 的錯誤上傳/複製 Azure 檔案的檔案時](#encryption)

- [共用現有的檔案上的 「 主機已關閉 「 錯誤，或命令介面時執行的連接點上的命令清單](#errorhold)

- [當您嘗試 Linux VM 上裝載 Azure 檔案時，裝載錯誤 115](#error15)

- [Linux VM 發生隨機延遲命令，例如 「 1 ！ 」](#delayproblem)

## <a name="general-problems"></a>一般問題
<a id="quotaerror"></a>
### <a name="quota-error-when-trying-to-open-a-file"></a>嘗試開啟檔案時，會產生配額錯誤。

在 Windows 中，您會收到錯誤訊息，如下所示︰

**1816 ERROR_NOT_ENOUGH_QUOTA <>-0xc0000044**

**STATUS_QUOTA_EXCEEDED**

**沒有足夠的配額是處理此命令**

**無效的控點值時發生︰ 53**

在 Linux，您會收到錯誤訊息，如下所示︰

**<filename>[拒絕]**

**超出磁碟配額**

#### <a name="cause"></a>原因

因為您已同時開啟所允許的檔案控點的上限，就會發生的問題。

#### <a name="solution"></a>解決方案

關閉部分控點，以減少同時開啟控點的數目，然後再試一次。 如需詳細資訊，請參閱[Microsoft Azure 儲存體效能與延展性檢查清單](storage-performance-checklist.md)。

<a id="slowboth"></a>
### <a name="slow-performance-when-accessing-file-storage-from-windows-or-linux"></a>從 Windows 或 Linux 存取檔案儲存空間時的效能變慢

- 如果您沒有安裝特定的最小 I/O 大小需求，我們建議您使用 1 MB I/O 大小的最佳效能。

- 如果您知道最後的可延伸寫入，使用檔案的大小，您的軟體沒有相容性問題時不尚未撰寫尾端上包含零的檔案，然後設定事先，而不是每次寫入的檔案大小的擴充寫入。

## <a name="windows-client-problems"></a>Windows 用戶端問題
<a id="windowsslow"></a>
### <a name="slow-performance-when-accessing-the-file-storage-from-windows-81-or-windows-server-2012-r2"></a>從 Windows 8.1 或 Windows Server 2012 R2 存取檔案儲存空間時的效能變慢

正在執行 Windows 8.1 或 Windows Server 2012 R2 用戶端，請確定已安裝 hotfix [KB3114025](https://support.microsoft.com/kb/3114025) 。 此 hotfix 皆可改善建立，並關閉控點的效能。

您可以執行下列指令碼，檢查是否已安裝 hotfix 上︰

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

如果安裝 hotfix 後，會顯示下列輸出︰

**HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies**

**{96c345ef-3cac-477b-8fcd-bea1a564241c} 呼叫完成 0x1**

> [AZURE.NOTE]  Azure Marketplace 中的 Windows Server 2012 R2 圖像有 hotfix KB3114025 依預設在 2015 年 12 月中開始安裝。

<a id="additional"></a>
### <a name="additional-recommendations-to-optimize-performance"></a>若要最佳化效能的其他建議

永遠不會建立或寫入存取權，但無法讀取權限要求的快取 i/o 開啟檔案。 也就是說，當您呼叫**Createfile**、 永遠不會指定只**GENERIC_WRITE**，但一定要指定**GENERIC_READ |GENERIC_WRITE**。 唯控點無法快取本機，小型寫入，即使是只開啟檔案控點。 在小型寫入以上效能會嚴重。 請注意 CRT **fopen()** 」 」 模式開啟唯控點。

<a id="error53"></a>
### <a name="error-53-when-you-try-to-mount-or-unmount-an-azure-file-share"></a>「 錯誤 53 」 當您嘗試裝載或卸載 Azure 檔案共用

下列情況可能造成此問題︰

#### <a name="cause-1"></a>原因 1

「 系統 53 發生錯誤。 存取。 」 基於安全性理由，如果未加密的通訊通道，而不嘗試連線從 Azure 檔案共用所在的相同資料中心會遭到封鎖連線至 Azure 檔案共用。 如果使用者的用戶端作業系統不支援 SMB 加密未提供的通訊通道加密。 表示 「 系統 53 發生錯誤。 拒絕存取 」 錯誤訊息，當使用者嘗試從內部部署或不同的資料中心裝載檔案共用。 Windows 8、 Windows Server 2012 及更新版本，每個交涉要求包含中小企業 3.0 便支援加密。

#### <a name="solution-for-cause-1"></a>原因 1 的解決方案

從用戶端的符合需求的 Windows 8、 Windows Server 2012 或更新版本，或從虛擬機器上相同的資料中心，做為 Azure 儲存體帳戶所使用的連線連線 Azure 檔案共用。

#### <a name="cause-2"></a>原因 2

「 系統錯誤 53 」 當您裝載如果 Azure 檔案資料中心的連接埠 445 輸出通訊遭到封鎖，可能會發生的 Azure 檔案共用。 按一下[這裡](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx)即可允許或不允許存取的連接埠 445 的 Isp 摘要。

Comcast 及部分 IT 組織封鎖此連接埠。 若要瞭解是否前的 「 系統錯誤 53 」 訊息的原因，您可以使用 Portqry 查詢 TCP:445 結束點。 如果篩選為顯示 TCP:445 結束點，則會被封鎖的 TCP 連接埠。 以下是查詢範例︰

`g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

如果 TCP 445 被封鎖的網路路徑規則，您會看到下列輸出︰

**TCP 連接埠 445 （microsoft ds 服務）︰ 篩選**

如需有關使用 Portqry 的詳細資訊，請參閱[Portqry.exe 命令列公用程式的說明](https://support.microsoft.com/kb/310099)。

#### <a name="solution-for-cause-2"></a>原因 2 解決方案

使用您的 IT 組織開啟連接埠 445 輸出至[Azure IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)。

#### <a name="cause-3"></a>原因 3

如果用戶端上已啟用 NTLMv1 通訊也能接收的 「 系統錯誤 53 」。 有啟用 NTLMv1 建立較不安全的用戶端。 因此，通訊會遭到封鎖 Azure 檔案。 若要確認是否錯誤的原因，請確認下列登錄子機碼設為 3 的值︰

HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel。

如需詳細資訊，請參閱 TechNet 上的[LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx)主題。

#### <a name="solution-for-cause-3"></a>原因 3 解決方案

若要解決此問題，還原為 HKLM\SYSTEM\CurrentControlSet\Control\Lsa 登錄機碼 3 的預設值的 LmCompatibilityLevel 值。

Azure 檔案支援只 NTLMv2 驗證。 請確定群組原則會套用在用戶端。 這會防止發生此錯誤。 這也會視為安全性的最佳作法。 如需詳細資訊，請參閱[如何設定用戶端使用 NTLMv2 使用群組原則](https://technet.microsoft.com/library/jj852207(v=ws.11).aspx)

建議的原則設定是**只傳送 NTLMv2 回應**。 這會對應到登錄值為 3。 用戶端使用只 NTLMv2 驗證]，然後他們使用 NTLMv2 工作階段的安全性如果伺服器支援。 網域控制站接受 LM、 NTLM，以及 NTLMv2 驗證。

<a id="netuse"></a>
### <a name="net-use-was-successful-but-dont-see-the-azure-file-share-mounted-in-windows-explorer"></a>網路使用已順利完成，但無法看到共用裝載 Windows 檔案總管中的 Azure 檔案

#### <a name="cause"></a>原因

根據預設，不會以管理員身分執行 Windows 檔案總管]。 如果您執行**網路使用**系統管理員的命令提示字元時，您會對應網路磁碟機 」 以系統管理員身分。 」 因為使用者中心對應的磁碟機，登入的使用者帳戶不會顯示磁碟機如果他們裝載在不同的使用者帳戶。

#### <a name="solution"></a>解決方案

裝載非系統管理員的命令列的共用]。 或者，您可以依照[本 TechNet 主題](https://technet.microsoft.com/library/ee844140.aspx) **EnableLinkedConnections**登錄值設定。

<a id="slashfails"></a>
### <a name="my-storage-account-contains--and-the-net-use-command-fails"></a>儲存我的帳戶包含 「 / 」，請使用命令失敗

#### <a name="cause"></a>原因

在 [命令提示字元 (cmd.exe) 底下，執行**網路使用**命令時，它會剖析藉由新增 「 / 」 作為命令列選項。 這會導致失敗的磁碟機對應。

#### <a name="solution"></a>解決方案

您可以使用下列步驟其中一項來解決問題︰

• 請使用下列 PowerShell 命令︰

`New-SmbMapping -LocalPath y: -RemotePath \\server\share  -UserName acountName -Password "password can contain / and \ etc"`

從批次檔案這可以為

`Echo new-smbMapping ... | powershell -command –`

• 將雙引號括住要解決此問題的索引鍵，除非 「 / 」 為第一個字元。 如果是，請使用互動的模式，分別輸入您的密碼，或是重新產生金鑰以取得開頭沒有使用斜線 （/） 字元金鑰。

<a id="accessfiledrive"></a>
### <a name="my-applicationservice-cannot-access-mounted-azure-files-drive"></a>我的應用程式或服務無法存取裝載的 Azure 檔案的磁碟機

#### <a name="cause"></a>原因

磁碟機會裝載每個使用者。 如果您的應用程式或服務正在執行的不同的使用者帳戶，使用者就不會看到磁碟機。

#### <a name="solution"></a>解決方案

從相同的使用者帳戶的應用程式是要連接的磁碟機。 這可以使用工具，例如 psexec。

或者，您可以建立具有相同的權限的網路服務或系統帳戶的新使用者，然後再執行 [ **cmdkey**和**網路使用**該帳戶。 使用者名稱應該儲存體帳戶名稱，而密碼應該儲存帳戶金鑰]。 **網路使用**的另一個選項是將儲存體帳戶名稱和**網路使用**命令的使用者名稱和密碼參數中鍵。

請遵循這些指示之後，您可能會收到下列錯誤訊息: 「 系統 1312年發生錯誤。 不存在指定的工作階段。 可能已被終止 」 當您執行系統或網路服務帳戶的**網路使用**。 如果這種情況，請確認傳遞至**網路使用**的使用者名稱包含網域資訊 (例如: 「 [儲存體帳戶名稱]。 file.core.windows.net 」)。

## <a name="linux-client-problems"></a>Linux 用戶端問題

<a id="encryption"></a>
### <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>「 您要複製檔案到不支援加密的目的地 」 的錯誤

#### <a name="cause"></a>原因

Bitlocker 加密可以將檔案複製到 Azure 檔案。 不過，檔案儲存空間不支援 NTFS EFS。 因此，您可能會在此情況下使用 EFS。 如果您有透過 EFS 加密的檔案，除非解密複製的檔案的 [複製] 命令，可能會失敗檔案儲存空間的複製作業。

#### <a name="workaround"></a>因應措施

若要將檔案複製到檔案儲存空間，您必須先解密。 您可以使用下列方法之一︰

• 使用**複製 /d**。

• 設定以下登錄機碼︰

- 路徑 = HKLM\Software\Policies\Microsoft\Windows\System
- 值類型 = DWORD
- 名稱 = CopyFileAllowDecryptedRemoteDestination
- 值 = 1

不過，請注意，設定登錄機碼會影響所有網路共用複製作業。

<a id="errorhold"></a>
### <a name="host-is-down-error-on-existing-file-shares-or-the-shell-hangs-when-you-run-list-commands-on-the-mount-point"></a>共用現有的檔案上的 「 主機已關閉 「 錯誤，或當您執行的連接點的命令清單時，當機命令介面

#### <a name="cause"></a>原因

當用戶端閒置一段時間 Linux 用戶端上會發生此錯誤。 發生此錯誤時，用戶端中斷連線，然後用戶端連線逾時。

#### <a name="solution"></a>解決方案

做為[變更設定](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)，請將 Linux 分配擱置 backport 的一部分是 Linux 核心現在修正這個問題。

若要解決此問題的運作正常，維持連線並避免陷入閒置狀態，將檔案保留在您的寫入定期 Azure 檔案共用。 這必須是寫入作業，例如重新建立/修改日期寫入檔案。 否則，您可能會收到快取的結果，而您的作業可能不會觸發連線。

<a id="error15"></a>
### <a name="mount-error-115-when-you-try-to-mount-azure-files-on-the-linux-vm"></a>「 裝載錯誤 115 」 當您嘗試裝載 Azure Linux VM 上的檔案

#### <a name="cause"></a>原因

Linux 散佈還不支援加密功能中小企業 3.0。 在某些散佈，使用者可能會收到 「 115 」 錯誤訊息，在嘗試使用中小企業 3.0 因遺失的功能而裝載 Azure 檔案。

#### <a name="solution"></a>解決方案

如果用 Linux SMB 用戶端不支援加密，裝載 Azure 檔案上相同的資料中心的檔案儲存空間帳戶使用從 Linux VM 中小企業 2.1。

<a id="delayproblem"></a>
### <a name="linux-vm-experiencing-random-delays-in-commands-like-ls"></a>Linux VM 發生隨機延遲命令，例如 「 1 ！ 」

#### <a name="cause"></a>原因

這會裝載命令不包含**serverino**選項。 不**serverino**，1 命令會執行**狀態**，在每個檔案上。

#### <a name="solution"></a>解決方案

檢查**serverino** 」 / 等/fstab 」 輸入項目的︰

在/常用/sampledir 類型 cifs azureuser.file.core.windows.net/wms/comer (rw，nodev，relatime，vers = 2.1，sec = ntlmssp，快取 = 嚴格，使用者名稱 = len，網域 = X，file_mode = 0755，dir_mode = 0755 serverino，rsize = 65536，wsize = 65536，actimeo = 1)

如果**serverino**選項不存在，卸載，並由選取**serverino**選項裝載再次 Azure 檔案。

## <a name="learn-more"></a>深入瞭解

- [開始使用 windows Azure 檔案儲存空間](storage-dotnet-how-to-use-files.md)

- [快速入門 linux Azure 檔案儲存空間](storage-how-to-use-files-linux.md)
