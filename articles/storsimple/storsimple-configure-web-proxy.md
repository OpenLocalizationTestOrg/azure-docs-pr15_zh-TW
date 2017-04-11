<properties 
   pageTitle="設定網頁 proxy StorSimple 裝置 |Microsoft Azure"
   description="瞭解如何使用 Windows PowerShell 的 StorSimple 設定 StorSimple 裝置的網頁 proxy 設定。"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="configure-web-proxy-for-your-storsimple-device"></a>設定網頁 proxy StorSimple 裝置

## <a name="overview"></a>概觀

本教學課程說明如何使用 Windows PowerShell 的 StorSimple 來設定和檢視 StorSimple 裝置的網頁 proxy 設定。 StorSimple 裝置與雲端通訊時，會使用 web proxy 設定。 Web proxy 伺服器用來新增另一個圖層的安全性，篩選內容，以便於頻寬需求或甚至說明分析的快取。

網頁 proxy 是選擇性的設定 StorSimple 裝置。 您可以設定網頁 proxy，只是透過 Windows PowerShell 的 StorSimple。 設定所執行的程序如下所示︰

1. 您第一次設定透過設定精靈或 Windows PowerShell 的 StorSimple cmdlet 的 web proxy 設定。

2. 然後，您會啟用 StorSimple cmdlet 透過 Windows PowerShell 的設定的網頁 proxy 設定。

將網頁的 proxy 設定完成之後，您可以檢視設定的網頁 proxy 設定的 Microsoft Azure StorSimple 管理員服務與 Windows PowerShell 中 StorSimple。 

閱讀本教學課程之後，您將可以︰

- 使用設定精靈和 cmdlet 設定網頁 proxy
- 使用 cmdlet 來啟用網頁 proxy
- Azure 傳統入口網站中檢視 web proxy 設定
- 疑難排解在 web proxy 設定時的錯誤


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>設定網頁 proxy 透過 Windows PowerShell 的 StorSimple

您可以使用下列其中一項設定網頁 proxy 設定︰

- 設定精靈引導您完成設定步驟。

- 在 StorSimple 的 Windows PowerShell Cmdlet。

下列各節討論這些方法。

## <a name="configure-web-proxy-via-the-setup-wizard"></a>設定網頁 proxy 透過設定精靈

您可以使用設定精靈引導您完成 web proxy 設定的步驟。 執行下列步驟，設定您的裝置上的網頁 proxy。

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>若要設定網頁 proxy 透過設定精靈

1. 在循序主控台] 功能表中，選擇 [**登入的完整功能**的選項 1，並提供**裝置系統管理員的密碼**。 輸入下列命令以啟動設定精靈工作階段︰

    `Invoke-HcsSetupWizard`

2. 如果這是您的裝置註冊使用設定精靈的第一次，您必須設定所有必要的網路，直到您到達 web proxy 設定。 如果您的裝置已登錄，您可以接受所有設定的網路設定，直到您到達 web proxy 設定。 在安裝精靈中，提示您設定網頁 proxy 設定，請輸入**[是]**。

3. **Proxy 網址**，請指定的 IP 位址或完整的網域名稱 (FQDN)，您的 web proxy 伺服器與您想要與雲端通訊時，使用您裝置的 TCP 連接埠號碼。 使用下列格式︰

    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`

    根據預設，指定 TCP 連接埠號碼 8080。

4. 選擇 [驗證類型為**NTLM**、**基本**、 或 [**無**]。 基本是最不安全的驗證的 proxy 伺服器設定。 NT 區域網路管理員 (NTLM) 是使用三種方式的郵件系統 （有時四需要額外的完整性時） 的高度安全和複雜驗證通訊協定驗證使用者。 預設驗證是 NTLM。 如需詳細資訊，請參閱[基本](http://hc.apache.org/httpclient-3.x/authentication.html)和[NTLM 驗證](http://hc.apache.org/httpclient-3.x/authentication.html)。 

    > [AZURE.IMPORTANT] **在 StorSimple 管理員服務，裝置監控圖表無法運作時是基本或裝置的 proxy 伺服器設定中啟用 NTLM 驗證。搭配使用監控圖表中，您必須確定驗證設為 [無]。**

5. 如果您使用的 「 驗證 」，提供**Web Proxy 使用者名稱**和**Web Proxy 的密碼**。 您也必須確認密碼。

    ![設定網頁 Proxy StorSimple Device1](./media/storsimple-configure-web-proxy/IC751830.png)

如果您第一次註冊您的裝置，繼續進行註冊。 如果您的裝置已註冊，精靈會結束。 將儲存設定的設定值。

網頁 proxy 將現在也會啟用。 您可以略過[啟用網頁 proxy](#enable-web-proxy)的步驟，並直接移至[Azure 傳統入口網站中的檢視 web proxy 設定](#view-web-proxy-settings-in-the-azure-classic-portal)。


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>設定網頁 proxy 透過 Windows PowerShell StorSimple cmdlet

設定網頁 proxy 設定替代方法是透過 Windows PowerShell 的 StorSimple cmdlet。 執行下列步驟，以設定網頁 proxy。

#### <a name="to-configure-web-proxy-via-cmdlets"></a>若要設定網頁 proxy 透過 cmdlet

1. 在循序主控台] 功能表中，選擇 [**登入的完整功能**的選項 1，]。 出現提示時，提供**裝置系統管理員的密碼**。 預設密碼`Password1`。

2. 在命令提示字元中，輸入︰

    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`

    輸入並確認密碼出現提示時，如下所示。

    ![設定網頁 Proxy StorSimple Device3](./media/storsimple-configure-web-proxy/IC751831.png)

網頁 proxy 現在設定，必須啟用。

## <a name="enable-web-proxy"></a>啟用網頁 proxy

預設為停用網頁 proxy。 StorSimple 裝置上設定 web proxy 設定之後，您需要使用 Windows PowerShell 的 StorSimple 啟用 web proxy 設定。

> [AZURE.NOTE] **如果您使用設定精靈來設定網頁 proxy，便無法需要此步驟。依預設設定精靈工作階段後自動啟用網頁 proxy。**

若要啟用您的裝置上的網頁 proxy StorSimple 的 Windows PowerShell 中執行下列步驟︰

#### <a name="to-enable-web-proxy"></a>若要啟用網頁 proxy

1. 在循序主控台] 功能表中，選擇 [**登入的完整功能**的選項 1，]。 出現提示時，提供**裝置系統管理員的密碼**。 預設密碼`Password1`。

2. 在命令提示字元中，輸入︰

    `Enable-HcsWebProxy`

    您現在已啟用 StorSimple 裝置上的網頁 proxy 設定。

    ![設定網頁 Proxy StorSimple Device4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-classic-portal"></a>Azure 傳統入口網站中檢視 web proxy 設定

Web proxy 設定會透過 Windows PowerShell 介面設定，而且無法在 [傳統] 入口網站中變更。 您不過，可以在 [傳統] 入口網站中檢視這些設定的設定。 執行下列步驟，以檢視 web proxy。

#### <a name="to-view-web-proxy-settings"></a>若要檢視 web proxy 設定
1. 瀏覽至**StorSimple 管理員服務 > 裝置**。 選取並按一下 [裝置]，然後移至 [**設定**。
1. 向下捲動到**Web proxy 設定**] 區段的 [**設定**] 頁面上。 您可以設定的網頁 proxy 設定裝置上檢視您 StorSimple 如下所示。

    ![在管理入口網站中檢視 Web Proxy](./media/storsimple-configure-web-proxy/ViewWebProxyPortal_M.png)
 
## <a name="errors-during-web-proxy-configuration"></a>在 web proxy 設定時的錯誤

如果 web proxy 設定有設定不正確，就會在 Windows PowerShell 中的使用者顯示錯誤訊息，StorSimple 的。 下表說明這些錯誤訊息，可能原因和建議的動作。

|序列 [否]。|HRESULT 錯誤的程式碼|可能的原因|建議的動作|
|:---|:---|:---|:---|
|1。|0x80070001|從被動式控制器執行命令，無法與使用中的控制站通訊。|作用中的控制器上執行的命令。 若要從被動式控制器執行] 命令，您將需要修正從被動式連線至作用中的控制站。 您需要加入 Microsoft 支援服務中斷連線時。|
|2。|0x800710dd-作業識別不正確|StorSimple 虛擬裝置上不支援 proxy 設定。|StorSimple 虛擬裝置上不支援 proxy 設定。 這些只 StorSimple 實體裝置上設定。|
|3。|0x80070057-不正確的參數|其中一個參數提供 proxy 設定不正確。|URI 未提供正確的格式。 使用下列格式︰`http://<IP address or FQDN of the web proxy server>:<TCP port number>`|
|4。|0x800706ba-RPC 伺服器無法使用|根本原因是下列其中一項︰</br></br>叢集不是設定。</br></br>未執行資料路徑服務。</br></br>從被動式控制站執行命令，無法與使用中的控制站通訊。|請加入 Microsoft 技術支援，以確保叢集設定並執行資料路徑服務。</br></br>從作用中的控制站執行命令。 如果您想要從被動式控制器執行命令，您必須以確保被動式控制器可以與使用中控制器通訊。 您需要加入 Microsoft 支援服務中斷連線時。|
|5。|0x800706be-RPC 呼叫失敗|叢集已關閉。|請加入 Microsoft 技術支援，以確保叢集設定。|
|6。|0x8007138f-找不到叢集資源|找不到平台服務叢集資源。 這樣就會發生時安裝並未正確。|您可能需要執行工廠重設您的裝置上。 若要建立的平台的資源。 請連絡 Microsoft 支援服務的下一個步驟。|
|7。|0x8007138c-不在線上叢集資源|平台或資料路徑叢集資源不在線上。|請連絡 Microsoft 支援，以確保資料路徑與平台服務資源在線上。|

> [AZURE.NOTE] 
> 
> -  上述清單的錯誤訊息並不詳盡。 
> - 您 StorSimple 管理員服務的 Azure 傳統入口網站中會顯示有關 web proxy 設定錯誤。 裝置狀態如果完成設定之後，有網頁 proxy 發生問題，請將在 [傳統] 入口網站中變更為**離線**。 |

## <a name="next-steps"></a>後續步驟

- 如果您是部署您的裝置，或設定網頁 proxy 設定時遇到任何問題，請參閱[疑難排解 StorSimple 裝置部署](storsimple-troubleshoot-deployment.md)。

- 若要瞭解如何使用 StorSimple 管理員服務，請移至[使用 StorSimple 管理員服務，來管理您的 StorSimple 裝置](storsimple-manager-service-administration.md)。
