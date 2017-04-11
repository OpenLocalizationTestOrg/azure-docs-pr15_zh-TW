<properties 
   pageTitle="StorSimple 部署問題進行疑難排解 |Microsoft Azure"
   description="說明如何診斷和修復當您第一次部署 StorSimple 時，發生錯誤。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/18/2016"
   ms.author="alkohli" />

# <a name="troubleshoot-storsimple-device-deployment-issues"></a>StorSimple 裝置部署問題進行疑難排解

## <a name="overview"></a>概觀

本文提供您的 Microsoft Azure StorSimple 部署很有幫助疑難排解指南。 其說明常見的問題可能的原因與建議的步驟可協助您解決設定 StorSimple 時，您可能會遇到的問題。 這項資訊適用於 StorSimple 內部部署的實體裝置和 StorSimple 虛擬裝置。

> [AZURE.NOTE] 當您第一次，部署裝置或就可能會導致之後，裝置可運作時，就可能發生裝置設定相關問題，您可能要面對。 本文著重於第一次部署問題的疑難排解。 若要疑難排解操作的裝置，移至[疑難排解操作裝置問題](storsimple-troubleshoot-operational-device.md)。

本文也說明疑難排解 StorSimple 部署的工具，並提供的逐步說明疑難排解範例。

## <a name="first-time-deployment-issues"></a>第一次部署問題

如果您有問題時執行第一次部署您的裝置，請，考慮下列動作︰

- 如果您正在進行疑難排解實體裝置，請確定硬體的已安裝並設定為[您 StorSimple 8100 裝置上安裝](storsimple-8100-hardware-installation.md)或[安裝 StorSimple 從 8600 裝置](storsimple-8600-hardware-installation.md)所述。
- 核取 [以供部署的先決條件。 請確定您已[部署設定檢查清單](storsimple-deployment-walkthrough.md#deployment-configuration-checklist)所述的所有資訊。
- 檢閱 StorSimple 發行記事] 可查看問題是否描述。 版本資訊中包括已知的安裝問題的因應措施。 

裝置部署時最常用問題美元發生該使用者時執行設定精靈，當他們註冊的裝置，透過 Windows PowerShell StorSimple。 （您使用 Windows PowerShell 的 StorSimple 登錄及設定 StorSimple 裝置。 如需有關裝置註冊的詳細資訊，請參閱[步驟 3︰ 設定和註冊您的裝置，透過 Windows PowerShell StorSimple](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple))。

下列各節可以協助您解決您的第一次設定 StorSimple 裝置時遇到的問題。

## <a name="first-time-setup-wizard-process"></a>第一次設定精靈程序

下列步驟摘要設定精靈程序。 設定的詳細的資訊，請參閱[部署您的內部部署 StorSimple 裝置](storsimple-deployment-walkthrough.md)。

1. 執行[叫用 HcsSetupWizard](https://technet.microsoft.com/library/dn688135.aspx) cmdlet 來啟動設定精靈會引導您執行其餘的步驟。 
2. 設定網路︰ 設定精靈可讓您網路設定資料 0 網路介面 StorSimple 裝置上。 這些設定包括下列各項︰
  - 虛擬 IP (VIP)、 子網路遮罩和閘道 –[設定 HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx)指令程式在背景中執行。 它 StorSimple 裝置上設定的 IP 位址、 子網路遮罩和資料 0 網路介面的閘道器。
  - 在背景中執行主要的 DNS 伺服器 –[設定 HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx)指令程式。 將其設定的 DNS 設定 StorSimple 解決方案。
  - 在背景中執行 NTP 伺服器 –[設定 HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx)指令程式。 將其設定 StorSimple 方案 NTP 伺服器設定。
  - 在背景中執行選擇性網頁 proxy –[設定 HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx)指令程式。 設定並可讓您 StorSimple 解決方案的 web proxy 設定。
3. 設定密碼︰ 下一步是為您設定裝置管理員及 StorSimple 快照管理員的密碼。 如果您正在執行更新 1，然後您就不需要設定 StorSimple 快照管理員密碼。
  - 裝置系統管理員的密碼用來登入您的裝置。 預設裝置密碼是**密碼 1**。
  - 當您設定的裝置使用 StorSimple 快照管理員需要 StorSimple 快照管理員密碼。 您必須先在安裝精靈中，設定密碼，然後您可以設定，並將它從 StorSimple 管理員服務變更。 此密碼驗證裝置使用 StorSimple 快照管理員。
 
    > [AZURE.IMPORTANT] 收集之前註冊，但套用您已成功註冊裝置之後，才密碼。 無法套用密碼時，系統會提示您提供的密碼，直到收集必要的密碼 （符合複雜度需求）。

4. 註冊裝置︰ 最後一個步驟是向 Microsoft Azure 中執行的 StorSimple 管理員服務註冊裝置。 註冊要求您從 Azure 傳統入口網站，以[取得服務登錄機碼](storsimple-manage-service.md#get-the-service-registration-key)，並在安裝精靈中提供。 成功註冊裝置之後，服務資料加密金鑰提供給您。 請務必在安全的位置保留此加密金鑰，因為它必須登錄後續的所有裝置的服務。

## <a name="common-errors-during-device-deployment"></a>在裝置部署期間的常見錯誤

下表列出常見的錯誤，您可能會遇到的時機您︰

- 設定必要的網路的設定。
- 設定網頁選擇性 proxy 設定。
- 為您設定裝置管理員及 StorSimple 快照管理員的密碼。 
- 註冊裝置。 

## <a name="errors-during-the-required-network-settings"></a>必要的網路設定時發生錯誤

| [否]。| 錯誤訊息 | 可能的原因 | 建議的動作 |
| ---| ------------- | --------------- | ------------------ |
| 1  | 呼叫 HcsSetupWizard︰ 這個命令只作用中的控制器上執行。 | 設定已被被動式控制器上執行。| 從作用中控制器執行此命令。 如需詳細資訊，請參閱[識別您的裝置上使用中控制器](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device)。|
| 2 | 呼叫 HcsSetupWizard︰ 裝置尚未準備。 | 有資料 0 的網路連線問題。| 檢查資料 0 實體的網路連線。|
| 3 | 呼叫 HcsSetupWizard︰ 有 IP 位址衝突的網路上其他系統 (從 HRESULT 的例外狀況︰ 0x80070263)。 | 提供給資料 0 IP 已在其他系統的使用中。 | 提供新的 IP 的不是使用。|
| 4 | 呼叫 HcsSetupWizard︰ 無法叢集資源。 (從 HRESULT 的例外狀況︰ 0x800713AE)。 | 複製 VIP。 提供的 IP 已在使用中。| 提供新的 IP 的不是使用。|
| 5 | 呼叫 HcsSetupWizard︰ 無效 IPv4 位址。 | 不正確的格式中提供的 IP 位址。| 檢查格式，然後再提供您的 IP 位址。 如需詳細資訊，請參閱[Ipv4 位址][1]。 |
| 6 | 呼叫 HcsSetupWizard︰ 無效的 IPv6 位址。 | 不正確的格式中提供的 IP 位址。| 檢查格式，然後再提供您的 IP 位址。 如需詳細資訊，請參閱[Ipv6 位址][2]。|
| 7 | 呼叫 HcsSetupWizard︰ 有可用的多個端點端點對應程式。 (從 HRESULT 的例外狀況︰ 0x800706D9) | 叢集功能無法運作。 | 後續步驟[連絡 Microsoft 支援人員](storsimple-contact-microsoft-support.md)。

## <a name="errors-during-the-optional-web-proxy-settings"></a>選用的 web proxy 設定時發生錯誤

| [否]。| 錯誤訊息 | 可能的原因 | 建議的動作 |
| ---| ------------- | --------------- | ------------------ |
| 1  | 呼叫 HcsSetupWizard︰ 不正確的參數 (從 HRESULT 的例外狀況︰ 0x80070057) | 其中一個參數提供 proxy 設定不正確。| URI 未提供正確的格式。 使用下列格式︰ http://*<IP address or FQDN of the web proxy server>*:*<TCP port number>* |
| 2 | 呼叫 HcsSetupWizard: RPC 伺服器無法使用 (從 HRESULT 的例外狀況︰ 0x800706ba) | 根本原因是下列其中一項︰<ol><li>叢集不是最。</li><li>被動式控制器無法與使用中的控制站，並從被動式控制器執行命令。</li></ol> | 根據根本原因︰<ol><li>[連絡 Microsoft 支援人員](storsimple-contact-microsoft-support.md)，請務必叢集設定。</li><li>從作用中的控制站執行命令。 如果您想要從被動式控制器執行命令，您必須以確保被動式控制器能以無聲的作用中的控制站。 您必須[連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)中斷連線時。</li></ol> |
| 3 | 呼叫 HcsSetupWizard: RPC 呼叫無法 (從 HRESULT 的例外狀況︰ 0x800706be) | 叢集已關閉。 | [連絡 Microsoft 支援人員](storsimple-contact-microsoft-support.md)，請務必叢集設定。|
| 4 | 呼叫 HcsSetupWizard︰ 叢集找不到的資源 (從 HRESULT 的例外狀況︰ 0x8007138f) | 找不到叢集資源。 這樣就會發生時安裝不正確。 | 您可能需要重設為原廠預設設定的裝置。 [連絡 Microsoft 支援人員](storsimple-contact-microsoft-support.md)，以建立叢集資源。|
| 5 | 呼叫 HcsSetupWizard︰ 叢集不在線上資源 (從 HRESULT 的例外狀況︰ 0x8007138c)| 叢集資源不在線上。 | 後續步驟[連絡 Microsoft 支援人員](storsimple-contact-microsoft-support.md)。|

## <a name="errors-related-to-device-administrator-and-storsimple-snapshot-manager-passwords"></a>錯誤與相關裝置管理員] 和 [StorSimple 快照管理員密碼

預設裝置系統管理員的密碼是**密碼 1**。 第一次登入; 之後的密碼到期因此，您必須使用設定精靈，變更。 當您第一次註冊裝置，您必須提供的新裝置系統管理員的密碼。 

如果您使用 Windows Server 主機上執行 StorSimple 快照管理員軟體管理裝置，然後您也必須提供 StorSimple 快照管理員密碼在第一次註冊時。 

請確定您的密碼符合下列需求︰

- 您的裝置管理員密碼應該之間 8 及 15 個字元。
- 您的 StorSimple 快照管理員密碼應該 14 或 15 個字元。
- 密碼應該包含下列的 4 個字元類型的 3︰ 小寫、 大寫、 數字及特殊。 
- 您的密碼無法最後 24 個密碼相同。

此外，請記住的密碼到期每年，且您順利註冊裝置之後，才可以變更。 如果基於任何原因，註冊失敗，不會變更密碼。 在 [裝置管理員和 StorSimple 快照管理員密碼的詳細資訊，請移至[使用 StorSimple 管理員服務，變更 StorSimple 密碼](storsimple-change-passwords.md)。

設定 [裝置管理員和 StorSimple 快照管理員密碼時，可能會遇到下列一或多個下列錯誤。

| [否]。| 錯誤訊息 | 建議的動作 |
| ---| ------------- | ------------------ | 
| 1  | 密碼超過最大的長度。 |使用符合這些需求的密碼︰<ul><li>您的裝置管理員密碼必須介於 8 及 15 個字元。</li><li>您的 StorSimple 快照管理員密碼必須 14 或 15 個字元。</li></ul> | 
| 2 | 密碼不符合所需的長度。 | 使用符合這些需求的密碼︰<ul><li>您的裝置管理員密碼必須介於 8 及 15 個字元。</li><li>您的 StorSimple 快照管理員密碼必須 14 或 15 個字元。</lu></ul> |
| 3 | 密碼只能包含小寫字母的字元。 | 密碼必須包含下列的 4 個字元類型的 3︰ 小寫、 大寫、 數字及特殊。 請確定您的密碼符合這些需求。 |
| 4 | 密碼只能包含數字的字元。 | 密碼必須包含下列的 4 個字元類型的 3︰ 小寫、 大寫、 數字及特殊。 請確定您的密碼符合這些需求。 |
| 5 | 密碼必須包含特殊字元。 | 密碼必須包含下列的 4 個字元類型的 3︰ 小寫、 大寫、 數字及特殊。 請確定您的密碼符合這些需求。 |
| 6 | 密碼必須包含下列的 4 個字元類型的 3︰ 大寫、 小寫字母、 數字及特殊。 | 您的密碼不包含需要的字元類型。 請確定您的密碼符合這些需求。 |
| 7 | 參數不符合確認。 | 請確定您的密碼符合所有的需求，您將它輸入正確。 |
| 8 | 您的密碼不符合預設值。 | 預設密碼是*密碼 1*。 您需要在第一次登入後，變更密碼。 |
| 9 | 您輸入的密碼不符合裝置密碼。 請重新輸入密碼。 | 檢查密碼，然後再次輸入密碼。 |

密碼會收集才能裝置註冊，但只有在成功的登錄之後，就會套用。 密碼復原工作流程需要註冊的裝置。 

> [AZURE.IMPORTANT] 一般而言，如果嘗試將密碼套用失敗，然後軟體重複嘗試收集密碼，直到找到為止。 少數情況下，不能套用密碼。 在此情況下，您可以註冊裝置，並繼續，但是不會變更密碼。 您會收到任何哪並未變更密碼的提示，裝置系統管理員或 StorSimple 快照管理員密碼。 如果發生這種情況，我們建議您變更兩個密碼。

您可以重設 StorSimple 管理員服務透過 Azure 傳統的入口網站中的密碼。 如需詳細資訊，請移至︰ 

- [變更裝置系統管理員的密碼](storsimple-change-passwords.md#change-the-device-administrator-password)。
- [變更 StorSimple 快照管理員的密碼](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password)。

## <a name="errors-during-device-registration"></a>在裝置註冊時的錯誤

您可以使用 Microsoft Azure 中執行的 StorSimple 管理員服務註冊裝置。 您無法在裝置註冊時發生一或多個下列問題。

| [否]。| 錯誤訊息 | 可能的原因 | 建議的動作 |
| ---| ------------- | --------------- | ------------------ |
| 1  | 錯誤 350027︰ 無法註冊裝置與 StorSimple 管理員。 |  | 等待幾分鐘，然後再試一次。 如果問題持續發生，請[連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)。|
| 2  | 錯誤 350013︰ 中註冊裝置發生錯誤。 這可能是因為不正確的服務登錄機碼。 | | 請一次以正確的服務登錄機碼註冊裝置。 如需詳細資訊，請參閱[存取服務登錄機碼。](storsimple-manage-service.md#get-the-service-registration-key) |
| 3 | 錯誤 350063: StorSimple 管理員服務傳遞，但註冊驗證失敗。 請再試一段時間後。 | 此錯誤表示驗證 ACS 已經過了，但是服務 register 呼叫失敗。 這可能是結果是月網路問題。 | 如果問題持續發生，請[連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)。 |
| 4 | 錯誤 350049︰ 無法在註冊時存取服務。 | 服務進行通話時，會收到 web 例外狀況。 在某些情況下，這可能會收到固定具備重試作業。 | 請檢查您的 IP 位址和 DNS 名稱，然後再試一次。 如果問題持續發生，[連絡 Microsoft 支援服務。](storsimple-contact-microsoft-support.md) | 
| 5 | 錯誤 350031︰ 已註冊的裝置。 | | 不需要任何動作。 |
| 6 | 錯誤 350016︰ 裝置註冊失敗。 | |請確定登錄機碼正確。 |
| 7 | 呼叫 HcsSetupWizard︰ 發生錯誤時註冊您的裝置。這可能是因為不正確的 IP 位址或 DNS 名稱。 請檢查您的網路設定，然後再試一次。 如果問題持續發生，請[連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)。 （錯誤 350050） | 請確定您的裝置可以偵測 （ping） 的外部網路。 如果您沒有連線到外部網路，此錯誤可能失敗註冊。 此錯誤可能的組合的一或多項動作︰<ul><li>不正確的 IP</li><li>不正確的子網路</li><li>不正確的閘道器</li><li>不正確的 DNS 設定</li></ul> | 請參閱[逐步說明疑難排解的範例](#step-by-step-storsimple-troubleshooting-example)中的步驟進行。 |
| 8 | 呼叫 HcsSetupWizard︰ 目前的作業失敗，因為內部服務錯誤 [0x1FBE2]。 請重試之後某個作業。 如果問題持續發生，請連絡 Microsoft 支援服務。 | 這是從服務或代理程式的所有使用者隱藏錯誤擲回一般錯誤。 最常見的原因可能是 ACS 驗證失敗。 可能失敗的原因是有 NTP server 組態問題，並在裝置上的時間設定不正確。 | 修正時間 （如果有問題），然後再試一次註冊。 如果您可以使用 [設定 HcsSystem 時區] 命令來調整時區、 時區 （例如 「 太平洋標準時間 」） 中的每個單字大寫。  如果此問題持續發生，請[連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)的下一個步驟。 |
| 9 | 警告︰ 無法啟動裝置。 尚未變更您的裝置系統管理員和 StorSimple 快照管理員密碼。 | 如果註冊失敗，請裝置管理員和 StorSimple 快照管理員密碼不會變更。 |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>疑難排解 StorSimple 部署工具

StorSimple 包含數種工具，可用來疑難排解 StorSimple 方案。 這些功能包括︰

- 支援封裝和裝置記錄檔 
- 專為疑難排解的 Cmdlet 

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>疑難排解支援套件和裝置記錄檔

支援套件包含所有相關記錄，可協助 Microsoft 技術支援小組裝置的問題進行疑難排解。 您可以使用 Windows PowerShell StorSimple 用來產生加密的支援套件，然後您可以與支援人員共用。

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>若要檢視記錄檔] 或 [支援封裝的內容

1. 使用 Windows PowerShell 的 StorSimple 產生支援套件中所述[建立及管理支援套件](storsimple-create-manage-support-package.md)。

2. 下載至本機在用戶端電腦上[解密指令碼](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65)。

3. 您可以使用此[逐步程序](storsimple-create-manage-support-package.md#edit-a-support-package)來開啟並解密支援套件。

4. 解密的支援封裝記錄檔為 etw/etvx 格式。 您可以執行下列步驟，以 Windows 事件檢視器中檢視這些檔案︰
  1. 在您的 Windows 用戶端上執行**「 eventvwr 」**的命令。 這會啟動 [事件檢視器]。
  2. 在 [**動作**] 窗格中，按一下 [**開啟儲存記錄檔**，指向 etvx/etw 格式 （支援封裝） 的記錄檔。 您現在可以檢視檔案。 您開啟檔案之後，您可以用滑鼠右鍵按一下，然後將檔案儲存為文字。
   
    > [AZURE.IMPORTANT] 您也可以使用 Windows PowerShell 中開啟這些檔案的**取得 WinEvent**指令程式。 如需詳細資訊，請參閱 Windows PowerShell cmdlet 參考文件中的[取得 WinEvent](https://technet.microsoft.com/library/hh849682.aspx) 。

5. 在 [事件檢視器中，開啟記錄，尋找含有裝置設定的相關問題的下列記錄檔︰

  - hcs_pfconfig 操作記錄
  - hcs_pfconfig/設定

6. 記錄檔]，在搜尋字串相關稱為安裝精靈的指令程式。 如需這些 cmdlet 的清單，請參閱[第一次設定精靈程序](#first-time-setup-wizard-process)。 

7. 如果您無法找出問題的原因，您可以[連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)的下一個步驟。 使用在[建立支援要求](storsimple-contact-microsoft-support.md#create-a-support-request)的步驟，當您連絡 Microsoft 支援服務取得協助。

## <a name="cmdlets-available-for-troubleshooting"></a>Cmdlet 可用的疑難排解

您可以使用下列 Windows PowerShell cmdlet 偵測連線錯誤。

- `Get-NetAdapter`︰ 使用這個 cmdlet 來偵測網路介面的狀況。 

- `Test-Connection`︰ 使用這個 cmdlet 來檢查內部和外部網路的網路連線。

- `Test-HcsmConnection`︰ 使用這個 cmdlet 來檢查成功註冊裝置的連線。

如果您執行的 StorSimple 裝置上更新 1，下列診斷 cmdlet，也會提供。

- `Sync-HcsTime`︰ 使用這個 cmdlet 來顯示裝置的時間，並強制時間與同步處理 NTP 伺服器。

- `Enable-HcsPing`與`Disable-HcsPing`︰ 使用這些 cmdlet 允許主機 StorSimple 裝置上的網路介面偵測 （ping）。 根據預設，StorSimple 網路介面未回應 ping 要求。

- `Trace-HcsRoute`︰ 使用這個 cmdlet 作為路由追蹤工具。 它封包的最終目的地的方式上的每個路由器一段時間，與然後會計算每個躍點傳回的封包所根據的結果。 由於`Trace-HcsRoute`會顯示在任何指定的路由器或連結，封包遺失的程度，您可以找出哪些路由器或連結可能會導致網路問題。 

- `Get-HcsRoutingTable`︰ 使用這個 cmdlet 來顯示 [本機 IP 路由表。

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>取得 NetAdapter cmdlet 與疑難排解

當您設定的第一次裝置部署的網路介面時，硬體狀態不適用於 StorSimple 管理員服務 UI 因為裝置還沒有註冊服務。 此外，[硬體狀態] 頁面可能不一定正確反映狀態的裝置，尤其是會影響服務同步處理問題。 在下列情況下，您可以使用`Get-NetAdapter`cmdlet 來決定健康狀況與您的網路介面的狀態。

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>若要檢視您的裝置上的所有網路介面卡清單

1. 啟動 Windows PowerShell StorSimple，，然後輸入`Get-NetAdapter`。 

2. 使用的輸出`Get-NetAdapter`cmdlet 和下列方針，若要瞭解您的網路介面的狀態。
  - 如果介面良好且已啟用， **ifIndex**狀態會顯示為**設定**。
  - 如果介面狀況良好，但實際未連線 （依網路纜線）， **ifIndex**會顯示為 [**已停用**。
  - 如果介面健全但未啟用， **ifIndex**狀態會顯示為**NotPresent**中。
  - 如果不存在的介面，此清單中就不會出現。 StorSimple 管理員服務 UI 仍會顯示這個介面失敗的狀態。

如需有關如何使用這個 cmdlet 的詳細資訊，請在 Windows PowerShell cmdlet 參考前往[GetNetAdapter](https://technet.microsoft.com/library/jj130867.aspx) 。 

下列各節中顯示的輸出`Get-NetAdapter`指令程式。 

 在下列範例中，控制器 0 是被動式控制器，並設定，如下所示︰

- 資料 0、 資料 1、 資料 2 和 3 資料網路介面存在在裝置。
- 資料 4 和 5 資料網路介面卡沒有出現。因此，就不會列在輸出。
- 已啟用資料 0。

控制站 1 是作用中的控制站，並設定，如下所示︰

- 資料 0、 資料 1、 資料 2、 3 的資料、 資料 4 及資料 5 網路介面存在在裝置。
- 已啟用資料 0。

**範例輸出 – 控制器 0**

以下是控制器 0 （被動式控制站） 的輸出。 未連線資料 1、 資料 2 和 3 的資料。 因為未出現在裝置上，將不會列出資料 4 和 5 的資料。 

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**範例輸出 – 控制站 1**

以下是從控制器 1 （作用中的控制站） 輸出。 只有資料 0 網路介面在裝置上的設定和使用。

     Controller1>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
     Ethernet 2           HCS VNIC                                    13       Up
     DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
     DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent

 
## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>疑難排解與測試連線 cmdlet

您可以使用`Test-Connection`cmdlet 來判斷您 StorSimple 的裝置是否可以連線到外部網路。 如果在安裝精靈中，所有網路的參數，包括 DNS，會設定正確，您可以使用`Test-Connection`cmdlet 來偵測 （ping） 外部網路，例如 outlook.com 已知的位址。 

您應該啟用偵測 （ping） 若要疑難排解這個指令程式的連線問題，如果偵測 （ping） 已停用。

請參閱下列範例的輸出`Test-Connection`指令程式。 

> [AZURE.NOTE] 在第一個範例中，裝置已設定不正確的 dns。 在第二個範例中，DNS 正確無誤。
 
**範例輸出 – 不正確的 DNS**

在下列範例中，有任何輸出 IPV4 和 IPV6 位址，亦即指出 DNS 仍無法解決。 這表示沒有連線到外部網路，而且必須提供正確的 DNS。 

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**範例輸出 – 正確 DNS**

在下列範例中，DNS 會傳回 IPV4 位址，表示已正確設定 DNS。 向您確認已連線至外部網路。 

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>測試 HcsmConnection cmdlet 與疑難排解

使用`Test-HcsmConnection`裝置已經連線到並註冊 StorSimple 管理員服務的相關的指令程式。 這個指令程式可協助您確認已註冊的裝置與相對應的 StorSimple 管理員服務之間的連線。 您可以在 Windows PowerShell 執行此命令的 StorSimple。 

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>若要執行此測試 HcsmConnection 指令程式

1. 請確定該裝置已註冊。

2. 核取裝置狀態。 如果裝置已停用，在進行的維修作業模式中，或離線，您可能會看到下列錯誤︰ 

   - ErrorCode.CiSDeviceDecommissioned – 指出裝置已停用。
   - ErrorCode.DeviceNotReady – 這表示裝置中進行的維修作業模式。
   - ErrorCode.DeviceNotReady – 指出裝置不在線上。

3. 確認 StorSimple 管理員服務正在執行 （使用[取得 ClusterResource](https://technet.microsoft.com/library/ee461004.aspx)指令程式）。 如果服務不在執行中，您可能會看到下列錯誤︰

   - ErrorCode.CiSApplianceAgentNotOnline
   - ErrorCode.CisPowershellScriptHcsError – 這表示時發生例外狀況執行取得 ClusterResource 時。

4. 核取的 Access 控制項服務 (ACS) token。 如果就會擲回 web 例外狀況，可能是閘道器問題、 遺失的 proxy 驗證、 不正確的 DNS 或驗證失敗的結果。 您可能會看到下列錯誤︰

   - ErrorCode.CiSApplianceGateway – 這表示 HttpStatusCode.BadGateway 例外狀況︰ 名稱解析程式服務無法解析主機名稱。 
   - 這表示 HttpStatusCode.ProxyAuthenticationRequired 例外狀況 （HTTP 狀態碼 407） ErrorCode.CiSApplianceProxy –: 在用戶端無法通過驗證的 proxy 伺服器。 
   - ErrorCode.CiSApplianceDNSError – 這表示 WebExceptionStatus.NameResolutionFailure 例外狀況︰ 名稱解析程式服務無法解析主機名稱。
   - ErrorCode.CiSApplianceACSError – 這表示服務傳回驗證錯誤，但沒有連線能力。
   
    如果它不會擲回 web 例外狀況，檢查 ErrorCode.CiSApplianceFailure。 這表示應用裝置失敗。

5. 核取雲端服務的連線。 如果服務擲回 web 例外狀況，您可能會看到下列錯誤︰

  - ErrorCode.CiSApplianceGateway – 這表示 HttpStatusCode.BadGateway 例外狀況︰ 中繼 proxy 伺服器收到錯誤的要求，從另一個 proxy 或原始的伺服器。
  - 這表示 HttpStatusCode.ProxyAuthenticationRequired 例外狀況 （HTTP 狀態碼 407） ErrorCode.CiSApplianceProxy –: 在用戶端無法通過驗證的 proxy 伺服器。 
  - ErrorCode.CiSApplianceDNSError – 這表示 WebExceptionStatus.NameResolutionFailure 例外狀況︰ 名稱解析程式服務無法解析主機名稱。
  - ErrorCode.CiSApplianceACSError – 這表示服務傳回驗證錯誤，但沒有連線能力。
  
    如果它不會擲回 web 例外狀況，檢查 ErrorCode.CiSApplianceSaasServiceError。 這表示 StorSimple 管理員服務問題。
 
6. 檢查 Azure 服務匯流排連線。 ErrorCode.CiSApplianceServiceBusError 表示裝置無法連線到服務匯流排。
 
記錄檔 CiSCommandletLog0Curr.errlog，而且 CiSAgentsvc0Curr.errlog 的詳細資訊，例如例外狀況的詳細資料。 

如需有關如何使用 cmdlet，請移至在 Windows PowerShell 中[測試 HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx)參照文件。

> [AZURE.IMPORTANT] 您可以執行使用中] 及 [被動控制器這個指令程式。 
 
請參閱下列範例的輸出`Test-HcsmConnection`指令程式。 

**範例輸出 – 成功註冊裝置執行 StorSimple 版本 （年 7 月 2014 年）**

第一個範例會從裝置已成功註冊 StorSimple 管理員服務，並有無連線問題。 

     Controller1>Test-HcsmConnection -verbose
     Checking device state  ... Success.
     Device registered successfully
     Checking device authentication.  ... This operation will take few minutes to complete....
     Checking device authentication.  ... Success.
     Checking connectivity from device to StorSimple Manager service.  ... This operation will take few minutes to complete....
     Checking connectivity from device to StorSimple Manager service.  ... Success.
     Checking connectivity from StorSimple Manager service to StorSimple device. .... Success.
     Controller1>

**範例輸出 – 成功註冊裝置執行 StorSimple 更新 1**

如果您正在執行更新 1 StorSimple 裝置上，您不需要執行搭配詳細資訊。

      Controller1>Test-HcsmConnection
       
      Checking device registration state  ... Success
      Device registered successfully
       
      Checking primary NTP server [time.windows.com] ... Success
       
      Checking web proxy  ... NOT SET
       
      Checking primary IPv4 DNS server [10.222.118.154] ... Success
      Checking primary IPv6 DNS server  ... NOT SET
      Checking secondary IPv4 DNS server [10.222.120.24] ... Success
      Checking secondary IPv6 DNS server  ... NOT SET
       
      Checking device online  ... Success
 
      Checking device authentication  ... This will take a few minutes.
      Checking device authentication  ... Success
       
      Checking connectivity from device to service  ... This will take a few minutes.
       
      Checking connectivity from device to service  ... Success
       
      Checking connectivity from service to device  ... Success
       
      Checking connectivity to Microsoft Update servers  ... Success
      Controller1>

**範例輸出 – 離線執行 StorSimple 發行的裝置 （年 7 月 2014 年）**

此範例會從 Azure 傳統入口網站中的 [**離線**狀態的裝置。

     Checking device state: Success 
     Device is registered successfully 
     Checking connectivity from device to SaaS.. Failure

裝置無法連線使用目前的 web proxy 設定。 這可能是 web proxy 設定] 或 [網路連線問題發生問題。 在此情況下，請確定您網站的 proxy 設定正確無誤，而且 web proxy 伺服器在線上，並且連線到。 

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>疑難排解同步處理 HcsTime cmdlet

使用這個 cmdlet，以顯示 [裝置] 時間。 如果裝置時間位移與 NTP 伺服器，您就可以強制-同步處理的時間與您 NTP 伺服器，然後使用這個指令程式。 如果 NTP 伺服器和裝置之間的位移大於 5 分鐘，您會看到一則警告。 如果位移超過 15 分鐘，然後裝置會離線。 您還是可以使用這個 cmdlet 強制時間同步處理。 不過，如果位移超過 15 小時，然後您會無法強制同步處理的時間和錯誤訊息就會顯示。

**範例輸出 – 使用同步處理 HcsTime 強制的時間同步處理**
 
     Controller0>Sync-HcsTime
     The current device time is 4/24/2015 4:05:40 PM UTC.
 
     Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
     [Y] Yes [N] No (Default is "Y"): Y
     Controller0>

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>疑難排解透過啟用 HcsPing 和停用 HcsPing cmdlet

使用這些 cmdlet，以確保您的裝置上的網路介面回應 ICMP ping 要求。 依預設 StorSimple 網路介面未回應 ping 要求。 使用這個指令程式是最簡單的方法，若要判斷您的裝置是否線上和可以連線。  

**範例輸出 – 啟用 HcsPing 和停用 HcsPing**

     Controller0>
     Controller0>Enable-HcsPing
     Successfully enabled ping.
     Controller0>
     Controller0>
     Controller0>Disable-HcsPing
     Successfully disabled ping.
     Controller0>

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>疑難排解與追蹤 HcsRoute cmdlet

使用這個 cmdlet 為路由追蹤工具。 它封包的最終目的地的方式上的每個路由器一段時間，與然後會計算每個躍點傳回的封包所根據的結果。 因為指令程式會顯示在任何指定的路由器或連結的封包遺失的程度，您可以找出哪些路由器或連結可能會導致網路問題。

**顯示如何追蹤路由與追蹤 HcsRoute 封包輸出的範例**

     Controller0>Trace-HcsRoute -Target 10.126.174.25
     
     Tracing route to contoso.com [10.126.174.25]
     over a maximum of 30 hops:
       0  HCSNode0 [10.126.173.90]
       1  contoso.com [10.126.174.25]
      
     Computing statistics for 25 seconds...
                 Source to Here   This Node/Link
     Hop  RTT    Lost/Sent = Pct  Lost/Sent = Pct  Address
       0                                           HCSNode0 [10.126.173.90]
                                     0/ 100 =  0%   |
       1    0ms     0/ 100 =  0%     0/ 100 =  0%  contoso.com
      [10.126.174.25]
      
     Trace complete.

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>取得 HcsRoutingTable cmdlet 與疑難排解

您可以使用這個 cmdlet 來檢視路由表 StorSimple 裝置。 路由是一組可以幫助您判斷資料封包在移動中透過網際網路通訊協定 (IP) 網路流量會導向的規則。 

路由表顯示介面和傳送資料到指定網路的閘道器。 同時也會提供這是移至連線到特定的目的地路徑的決策人員路由公制。 較低路由公制，較高的喜好設定。 

例如，如果您有 2 網路介面，資料 2 和 3 的資料連線至網際網路。 如果資料 2 和 3 的資料路由矩陣分別是 15 和 261，較低路由公制與資料 2 是連接網際網路時所使用的偏好的介面。

如果您正在執行更新 1 StorSimple 裝置上，您的資料 0 網路介面有雲端流量的最高喜好設定。 這表示，不論是否有其他雲端啟用介面，雲端流量會被瀏覽資料 0。 

如果您執行`Get-HcsRoutingTable`cmdlet cmdlet 會而不用指定任何參數 （如下列範例所示），輸出 IPv4 和 IPv6 路由的資料表。 或者，您可以指定`Get-HcsRoutingTable -IPv4`或`Get-HcsRoutingTable -IPv6`以取得相關路由表格。

      Controller0>
      Controller0>Get-HcsRoutingTable
      ===========================================================================
      Interface List
       14...00 50 cc 79 63 40 ......Intel(R) 82574L Gigabit Network Connection
       12...02 9a 0a 5b 98 1f ......Microsoft Failover Cluster Virtual Adapter
       13...28 18 78 bc 4b 85 ......HCS VNIC
        1...........................Software Loopback Interface 1
       21...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
       22...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
      ===========================================================================
       
      IPv4 Route Table
      ===========================================================================
      Active Routes:
      Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0  192.168.111.100  192.168.111.101     15
              127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
              127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
            169.254.0.0      255.255.0.0         On-link     169.254.1.235    261
          169.254.1.235  255.255.255.255         On-link     169.254.1.235    261
        169.254.255.255  255.255.255.255         On-link     169.254.1.235    261
          192.168.111.0    255.255.255.0         On-link   192.168.111.101    266
        192.168.111.101  255.255.255.255         On-link   192.168.111.101    266
        192.168.111.255  255.255.255.255         On-link   192.168.111.101    266
              224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
              224.0.0.0        240.0.0.0         On-link     169.254.1.235    261
              224.0.0.0        240.0.0.0         On-link   192.168.111.101    266
        255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        255.255.255.255  255.255.255.255         On-link     169.254.1.235    261
        255.255.255.255  255.255.255.255         On-link   192.168.111.101    266
      ===========================================================================
      Persistent Routes:
        Network Address          Netmask  Gateway Address  Metric
                0.0.0.0          0.0.0.0  192.168.111.100       5
      ===========================================================================
       
      IPv6 Route Table
      ===========================================================================
      Active Routes:
       If Metric Network Destination      Gateway
        1    306 ::1/128                  On-link
       13    276 fd99:4c5b:5525:d80b::/64 On-link
       13    276 fd99:4c5b:5525:d80b::1/128
                                          On-link
       13    276 fd99:4c5b:5525:d80b::3/128
                                          On-link
       13    276 fe80::/64                On-link
       12    261 fe80::/64                On-link
       13    276 fe80::17a:4eba:7c80:727f/128
                                          On-link
       12    261 fe80::fc97:1a53:e81a:3454/128
                                          On-link
        1    306 ff00::/8                 On-link
       13    276 ff00::/8                 On-link
       12    261 ff00::/8                 On-link
       14    266 ff00::/8                 On-link
      ===========================================================================
      Persistent Routes:
        None
       
      Controller0>
 
## <a name="step-by-step-storsimple-troubleshooting-example"></a>逐步 StorSimple 疑難排解範例

下列範例顯示逐步疑難排解 StorSimple 部署。 在範例案例中，裝置註冊失敗的錯誤訊息，指出網路設定] 或 [DNS 名稱不正確。

傳回的錯誤訊息為︰

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

錯誤可能會因下列任何一項︰

- 不正確的硬體安裝
- 區域網路介面
- 不正確的 IP 位址、 子網路遮罩、 閘道、 主要的 DNS 伺服器或 web proxy
- 不正確的登錄機碼
- 不正確的防火牆設定

### <a name="to-locate-and-fix-the-device-registration-problem"></a>若要找出並修正裝置註冊問題

1. 檢查您的裝置設定︰ 在作用中的控制站，執行`Invoke-HcsSetupWizard`。

     > [AZURE.NOTE] 作用中的控制站必須執行設定精靈。 若要確認您已連線到作用中的控制站，看看橫幅呈現循序主控台。 橫幅會指出您是否連線至控制器 0 或 1，控制器和控制器是作用中或被動式。 如需詳細資訊，請前往[識別您的裝置上使用中控制器](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device)。
 
2. 請確定該裝置已正確妥︰ 檢查網路纜線回平面裝置上。 纜線連接是特定的裝置型號的。 如需詳細資訊，移至[您 StorSimple 8100 裝置上安裝](storsimple-8100-hardware-installation.md)或[安裝 StorSimple 從 8600 裝置](storsimple-8600-hardware-installation.md)。

     > [AZURE.NOTE] 如果您使用 10 GbE 網路連接埠，您必須使用所提供的 QSFP SFP 介面卡和 SFP 纜線。 如需詳細資訊，請參閱[纜線、 切換參數及收發 Mellanox 連接埠的 OEM 供應商所建議的清單](http://www.mellanox.com/page/cables?mtag=cable_overview)。
 
3. 確認網路介面的狀況︰

   - 使用取得 NetAdapter cmdlet 資料 0 偵測網路介面的狀況。 
   - 如果連結無法運作， **ifindex**狀態會指出介面已關閉。 然後您需要檢查的應用裝置，並切換的連接埠的網路連線。 您也必須找出不正確的纜線。 
   - 如果您懷疑的資料 0 作用中的控制器上的連接埠失敗，您可以確認此連線到資料 0 控制器 1 上的連接埠。 若要確認此，中斷裝置的後，從控制器 0 的網路纜線、 纜線連接控制器 1，，然後重新取得 NetAdapter 指令程式。 
   如果資料 0 連接埠上控制器失敗，請[連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)的下一個步驟。 您可能需要將您的系統控制器。
 
4. 確認切換的連線︰
   - 請確定在控制器 0 與控制器 1 您主要的圍繞符號中的資料 0 網路介面位於相同的子網路。 
   - 檢查路由器的中心。 一般而言，您應該連接兩個控制站相同的中心或路由器。 
   - 請確定您使用的連線選項，有相同的虛擬區域網路中的兩個控制站資料 0。
   
5. 排除任何使用者錯誤︰

  - 執行設定精靈一次 (執行**叫用 HcsSetupWizard**)，然後輸入一次，請確定沒有任何錯誤值。 
  - 確認註冊使用金鑰。 相同的登錄機碼可以用於多個裝置連接到 StorSimple 管理員服務。 使用中[取得服務登錄機碼](storsimple-manage-service.md#get-the-service-registration-key)的程序，以確保您使用的正確的登錄機碼。

    > [AZURE.IMPORTANT] 如果您有多個服務執行時，您必須確保註冊裝置使用適當的服務的登錄機碼。 如果您有錯誤的 StorSimple 管理員服務註冊裝置，您必須[連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)的下一個步驟。 您可能必須執行工廠重設的裝置 （這可能會導致資料遺失），然後將其連線至預定的服務。

6. 您可以使用 [測試連線 cmdlet 來確認您已連線至外部網路。 如需詳細資訊，請前往[測試連線 cmdlet 與疑難排解](#troubleshoot-with-the-test-connection-cmdlet)。

7. 檢查防火牆干擾。 如果您已驗證的虛擬 IP (VIP)、 子網路、 閘道和 DNS 設定的所有無誤，您仍然可看見的連線發生問題，然後有可能是您的防火牆封鎖了您的裝置與外部網路之間的通訊。 您需要確認連接埠 80 和 443 可連外通訊 StorSimple 裝置上。 如需詳細資訊，請參閱[網路 StorSimple 裝置的需求](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device)。

8. 查看記錄檔。 移至[支援套件和裝置適用於疑難排解的記錄檔](#support-packages-and-device-logs-available-for-troubleshooting)。

9. 如果上述步驟執行無法解決問題，[請連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)取得協助。

## <a name="next-steps"></a>後續步驟
[瞭解如何疑難排解操作的裝置](storsimple-troubleshoot-operational-device.md)。

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 
