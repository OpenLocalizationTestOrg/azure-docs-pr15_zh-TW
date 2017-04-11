<properties 
    pageTitle="Azure 多重因素驗證伺服器升級 PhoneFactor 代理程式"
    description="這份文件將說明如何開始使用 Azure MFA 伺服器，以及如何從較舊的 phonefactor 代理程式升級。"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="upgrading-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>Azure 多重因素驗證伺服器升級 PhoneFactor 代理程式

升級從 PhoneFactor 代理程式 v5.x 或更舊版本 Azure 多重因素驗證伺服器需要 PhoneFactor 代理程式和相關的元件至多重因素驗證伺服器先解除安裝，可以安裝其相關的元件。

## <a name="to-upgrade-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>Azure 多重因素驗證伺服器升級 PhoneFactor 代理程式
<ol>
<li>首先，請先備份 PhoneFactor 資料檔案。 預設的安裝位置會是 C:\Program Files\PhoneFactor\Data\Phonefactor.pfdata。


<li>如果已安裝的使用者入口網站︰</li>
<ol>
<li>瀏覽至安裝資料夾，請先備份 web.config 檔案。 預設的安裝位置是 C:\inetpub\wwwroot\PhoneFactor。</li>


<li>如果您已新增自訂的佈景主題入口網站，請先備份您的自訂資料夾下方 C:\inetpub\wwwroot\PhoneFactor\App_Themes 目錄。</li>


<li>解除安裝 [使用者] 入口網站透過 PhoneFactor 代理程式 （只在如果 PhoneFactor 代理程式相同的伺服器上安裝使用），或透過 Windows 程式和功能。</li></ol>




<li>如果已安裝行動應用程式的 Web 服務︰
<ol>
<li>移至 [安裝] 資料夾，然後備份 web.config 檔案。 預設的安裝位置是 C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService。</li>
<li>解除安裝的行動應用程式 Web 服務透過 Windows 程式和功能。</li></ol>

<li>如果已安裝 Web 服務 SDK，將它解除安裝透過 PhoneFactor 代理程式，或透過 Windows 程式和功能。

<li>解除安裝 PhoneFactor 代理程式透過 Windows 程式和功能。

<li>多重因素驗證伺服器安裝。 請注意，安裝路徑挑選自前次 PhoneFactor 代理程式安裝登錄機碼，它應該安裝的同一個位置 (例如 C:\Program Files\PhoneFactor)。 新的安裝會有不同的預設安裝路徑 （例如 C:\Program Files\Multi 雙因素驗證伺服器）。 應在安裝期間，升級左先前 PhoneFactor 代理程式的資料檔案，因此您的使用者及設定仍應為有安裝新的多重因素驗證伺服器後。

<li>如果出現提示，請啟動多重因素驗證伺服器，並確保會被指派到正確的複寫群組。

<li>如果之前已安裝 Web 服務 SDK，安裝新的 Web 服務 SDK 透過多重因素驗證 Server 使用者介面。 請注意，現在預設虛擬目錄名稱 「 MultiFactorAuthWebServiceSdk 」，而不是 「 PhoneFactorWebServiceSdk 」。 如果您想要使用先前的名稱，您必須在安裝期間變更虛擬目錄的名稱。 否則，如果您允許安裝]，以使用新的預設名稱，您必須變更參考 Web 服務 SDK，例如使用者入口網站和行動應用程式 Web 服務為指向的正確位置的任何應用程式中的 URL。

<li>如果先前 PhoneFactor 代理程式伺服器上安裝使用者入口網站，安裝新多重因素驗證使用者的入口網站透過多重因素驗證 Server 使用者介面。 請注意，現在預設虛擬目錄名稱 「 MultiFactorAuth 」，而不是 「 PhoneFactor 」。 如果您想要使用先前的名稱，您必須在安裝期間變更虛擬目錄的名稱。 否則，如果您允許安裝]，以使用新的預設名稱，請按一下 [使用者入口網站] 圖示，在多重因素驗證伺服器並更新使用者入口網站 URL，在 [設定] 索引標籤上。

<li>如果使用者入口網站及/或行動應用程式 Web 服務之前已安裝在不同 PhoneFactor 代理程式的伺服器上︰
<ol>
<li>移至 [安裝位置 (例如 C:\Program Files\PhoneFactor)，然後將適當的 installer(s) 複製到另一個伺服器。 有使用者入口網站和行動應用程式 Web 服務的 32 位元與 64 位元的安裝程式。 分別被呼叫 MultiFactorAuthenticationUserPortalSetupXX.msi 和 MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi。</li>
<li>若要安裝網頁伺服器上的 [使用者] 入口網站，開啟命令提示字元以系統管理員身分並執行 MultiFactorAuthenticationUserPortalSetupXX.msi。 請注意，現在預設虛擬目錄名稱 「 MultiFactorAuth 」，而不是 「 PhoneFactor 」。 如果您想要使用先前的名稱，您必須在安裝期間變更虛擬目錄的名稱。 否則，如果您允許安裝]，以使用新的預設名稱，請按一下 [使用者入口網站] 圖示，在多重因素驗證伺服器並更新使用者入口網站 URL，在 [設定] 索引標籤上。 現有的使用者會需要新的 URL 通知。</li>
<li>移至 [使用者] 入口網站安裝位置 (例如 C:\inetpub\wwwroot\MultiFactorAuth) 及編輯 web.config 檔案。 複製和 appSettings 和 applicationSettings 各節從原始 web.config 檔案可在升級前先備份到新的 web.config 檔案中的值。 如果新的預設虛擬目錄名稱已保留安裝 Web 服務 SDK 時，請，變更 [applicationSettings] 區段中的 URL 指向正確的位置。 如果先前的 web.config 檔案中的任何其他的預設值已變更，套用相同變更新 web.config 檔案。</li>
<li>若要在網頁伺服器上安裝行動應用程式的 Web 服務，開啟命令提示字元以系統管理員身分並執行 MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi。 請注意，現在預設虛擬目錄名稱 「 MultiFactorAuthMobileAppWebService 」，而不是 「 PhoneFactorPhoneAppWebService 」。 如果您想要使用先前的名稱，您必須在安裝期間變更虛擬目錄的名稱。 若要選擇較短的名稱，讓他們在行動裝置上輸入的使用者。 否則，如果您允許安裝]，以使用新的預設名稱，請按一下多重因素驗證伺服器的行動應用程式圖示，並更新行動應用程式的 Web 服務 URL。</li>
<li>移至行動應用程式的 Web 服務安裝位置 (例如 C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService) 及編輯 web.config 檔案。 複製和 appSettings 和 applicationSettings 各節從原始 web.config 檔案可在升級前先備份到新的 web.config 檔案中的值。 如果新的預設虛擬目錄名稱已保留安裝 Web 服務 SDK 時，請，變更 [applicationSettings] 區段中的 URL 指向正確的位置。 如果先前的 web.config 檔案中的任何其他的預設值已變更，套用相同變更新 web.config 檔案。</li></ol>
