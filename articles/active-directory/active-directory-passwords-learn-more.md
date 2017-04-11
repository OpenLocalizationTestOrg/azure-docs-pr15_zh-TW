<properties
    pageTitle="深入瞭解︰ Azure AD 密碼管理 |Microsoft Azure"
    description="Azure AD 密碼管理，包括密碼回寫的運作方式，密碼回寫安全性、 如何重設密碼入口網站的運作正常，以及資料使用的密碼重設的進階的主題。"
    services="active-directory"
    documentationCenter=""
    authors="asteen"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="asteen"/>

# <a name="learn-more-about-password-management"></a>深入瞭解密碼管理

> [AZURE.IMPORTANT] **以下是您，因為您無法登入方面的問題嗎？** 如果是這樣，[下列是您可以變更與重設您自己的密碼](active-directory-passwords-update-your-own-password.md)。

如果您已經部署密碼管理，或只想要更深入的技術講述至今的部署之前的運作方式，本節會提供您的技術服務概念的概觀。 我們會包含下列內容︰

* [**密碼回寫概觀**](#password-writeback-overview)
  - [密碼回寫的運作方式](#how-password-writeback-works)
  - [支援的密碼回寫的案例](#scenarios-supported-for-password-writeback)
  - [密碼回寫安全性模型](#password-writeback-security-model)
* [**如何密碼重設入口網站的工作？**](#how-does-the-password-reset-portal-work)
  - [資料會使用重設密碼？](#what-data-is-used-by-password-reset)
  - [如何存取密碼重設使用者的資料](#how-to-access-password-reset-data-for-your-users)

## <a name="password-writeback-overview"></a>密碼回寫概觀
密碼回寫是[Azure [Active Directory 連線](active-directory-aadconnect.md)的元件，可以啟用，並用於目前的 Azure Active Directory 進階版訂閱者。 如需詳細資訊，請參閱[Azure Active Directory 版本](active-directory-editions.md)。

密碼回寫可讓您設定您的雲端租用戶，若要撰寫回到您的密碼內部部署 Active Directory。  將其排除您不必將設定及管理複雜的內部部署自助密碼重設解決方案，並提供雲端方便使用者重設內部部署密碼，無論在何處。  閱讀一些重要密碼回寫功能︰

- **零延遲的意見反應。**  密碼回寫是同步作業。  如果使用者的密碼不符合原則，或無法重設或變更任何原因，就會立即通知您的使用者。
- **重設密碼的使用者使用 AD FS 或其他同盟的技術支援。**  使用密碼回寫，只要同盟的使用者帳戶會同步處理至您的 Azure AD 租用戶，他們能夠管理其內部部署雲端 AD 密碼。
- **重設密碼的使用者使用密碼雜湊同步處理支援。** 當密碼重設服務偵測到密碼雜湊同步處理的啟用同步處理的使用者帳戶時，重設此帳戶的內部部署和雲端密碼同時。
- **支援從 access] 面板以及 Office 365 中變更密碼。**  當同盟或密碼同步處理將使用者具備變更密碼過期，或按一下 [非過期，我們將這些密碼回寫本機 AD 環境。
- **支援密碼管理員重設從撰寫**[**Azure 管理入口網站**](https://manage.windowsazure.com)。  隨時想在[Azure 管理入口網站](https://manage.windowsazure.com)，如果使用者同盟或密碼同步處理的使用者的密碼管理員重設，我們將會選取本機 AD，以及的 [系統管理員的密碼。  這是目前不支援 Office 管理入口網站。
- **強制執行您的內部部署 AD 密碼原則。**  當使用者重設您自己的密碼時，我們確認它是否符合您的內部部署 AD 原則之前先測試其該目錄。  包括歷程記錄、 複雜度、 年齡、 密碼篩選器，以及您已經定義您的本機 AD 中的任何其他密碼限制。
- **不需要任何輸入的防火牆規則。**  密碼回寫使用 Azure 服務匯流排轉送為基礎的通訊通道，這表示您沒有開啟任何輸入的連接埠，在您的防火牆，此功能才能運作。
- **不支援您內部部署的 Active Directory 中受保護的群組中的使用者帳戶。** 如需有關受保護的群組的詳細資訊，請參閱[受保護的帳戶和 Active Directory 中的群組](https://technet.microsoft.com/library/dn535499.aspx)。

### <a name="how-password-writeback-works"></a>密碼回寫的運作方式
密碼回寫具有三個主要元件︰

- 密碼重設雲端服務 （這也整合 Azure AD 密碼變更頁面）
- 租用戶專屬 Azure 服務匯流排轉送
- 內部部署密碼重設端點

搭配中所述下方圖表︰

  ![][001]

同盟或密碼雜湊同步處理將使用者傳入重設或變更其密碼在雲端，會發生以下情況︰

1.  我們檢查何種類型的密碼的使用者有。  如果我們請參閱在部署中管理密碼，然後我們確定回寫服務和使用。  如果是，我們讓使用者繼續，如果沒有，我們告知使用者無法以下重設使用者的密碼。
2.  接下來，使用者將傳遞適當的驗證 gates 和到達 [重設密碼] 畫面。
3.  使用者選取新的密碼，並確認它。
4.  在按一下 [提交]，我們會以對稱金鑰回寫設定程序時所建立的加密純文字密碼。
5.  之後加密密碼，我們將其加入中透過 HTTPS 通道，傳送您的租用戶特定服務匯流排轉送 （的我們也設定為您在安裝過程中回寫） 的內容。  只有您的內部部署安裝知道的隨機產生密碼保護此轉送。
6.  當郵件到達時服務匯流排時，密碼重設端點自動醒，並會看到已擱置重設要求。
7.  服務會尋找使用者問題使用雲端錨點屬性。  順利完成，此查閱使用者物件必須存在於 AD 連接器空間中，則必須連結至對應的 MV 物件，並連接至對應的 AAD 連接器物件。 最後，讓同步處理]，找出這個使用者帳戶，AD 連接器物件 MV 連結必須同步處理規則`Microsoft.InfromADUserAccountEnabled.xxx`的連結。  這不需要時通話雲端，同步處理引擎查閱 AAD 連接器空間物件中，使用 cloudAnchor 屬性，然後遵循連結回 MV 物件，因為，然後依照回 AD 物件的 [連結。 同步處理引擎可能有多個 AD 物件 （多樹系） 相同的使用者，因為已依賴`Microsoft.InfromADUserAccountEnabled.xxx`挑選正確的連結。
8.  後找不到使用者帳戶，我們會嘗試直接在適當的 AD 樹系密碼。
9.  如果密碼設定作業是成功，我們會告訴的使用者他們的密碼已修改，並在其快樂的方式可以移。
10. 如果密碼設定作業失敗，我們會傳回錯誤的使用者，並讓他們再試一次。  作業可能會失敗，因為服務已關閉，因為他們所選取的密碼不符合組織原則，因為我們找不到使用者在本機 AD 中，或任何數目的原因。  我們有許多這種情況下的特定訊息，告訴使用者他們可以執行以解決此問題。

### <a name="scenarios-supported-for-password-writeback"></a>支援的密碼回寫的案例
下表說明我們同步處理功能的版本支援的案例。  一般而言，強烈建議您安裝最新版[Azure AD Connect](active-directory-aadconnect.md#install-azure-ad-connect) ，如果您想要使用密碼回寫。

  ![][002]

### <a name="password-writeback-security-model"></a>密碼回寫安全性模型
密碼回寫是高度安全和強大的服務。  若要確保您的資訊受到保護，我們啟用 4 層安全性模型的下方說明。

- **租用戶特定服務匯流排轉送**– 設定服務時，我們設定 Microsoft 永遠不會有權存取的隨機產生強式密碼保護的租用戶專屬服務匯流排轉送。
- **向下、 密碼編譯是強式密碼加密金鑰鎖定**– 服務匯流排轉送建立後，我們建立強式對稱金鑰我們用來加密的密碼，進入網路連線時。  此按鍵放僅在您公司的密碼儲存在雲端，是重度鎖定和稽核，就像任何在目錄中的密碼。
- **工業標準 TLS** – 密碼重設或變更作業時，就會發生在雲端，我們對純文字密碼，並以您的公開金鑰加密它。  我們然後 plop 的到 HTTPS 訊息傳送透過加密通道，使用您的服務匯流排轉送 Microsoft 的 SSL 憑證。  該郵件到達時將服務匯流排之後，您的內部部署代理程式醒驗證服務匯流排使用先前已產生強式密碼，挑選 [加密的郵件、 解密使用的私密金鑰我們產生，然後嘗試透過 AD DS SetPassword API 密碼設定。  此步驟是什麼讓我們來強制執行 AD 內部部署密碼原則 （複雜度年齡、 歷程記錄、 篩選、 等等） 在雲端。
- **郵件到期原則**– 最後，如果基於某種原因郵件位於服務匯流排因為您內部部署的服務已關閉，它會逾時並移除幾分鐘之後，以提高安全性進一步。

## <a name="how-does-the-password-reset-portal-work"></a>如何密碼重設入口網站的工作？
當使用者瀏覽重設密碼入口網站，來判斷是否正確，該使用者帳戶開始工作流程，管理該使用者的密碼，以及是否使用者獲授權可使用的功能，使用者所屬，哪些組織。  若要深入瞭解密碼重設頁面背後的邏輯以下步驟，請閱讀。

1.  使用者在 [無法存取您的帳戶] 連結或移至[https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com)直接。
2.  使用者輸入的使用者識別碼，並將傳遞的驗證碼。
3.  Azure AD 確認使用者是否可以使用這項功能，執行下列動作︰
    - 檢查使用者已啟用此功能和 Azure AD 授權指派。
        - 如果使用者沒有啟用此功能或將授權指派，使用者會詢問連絡他或她的管理員，重設密碼。
    - 檢查使用者擁有的權限是他自己的帳戶，根據系統管理員原則的定義資料的挑戰。
        - 如果原則需要只有一個的挑戰，它被確保使用者有適當的資料定義至少一個由系統管理員原則啟用的挑戰。
          - 如果使用者未設定，使用者建議連絡他或她的管理員，重設密碼。
        - 如果原則需要兩個的挑戰，它被確保使用者有適當的資料定義至少兩個由系統管理員原則啟用的挑戰。
          - 如果使用者未設定，則使用者是我們建議您連絡他或她的管理員，重設密碼。
    - 檢查管理使用者的密碼的內部部署 （同盟或密碼雜湊同步處理之前）。
       - 如果回寫已部署，並且在部署中管理使用者的密碼，然後允許使用者進行驗證和重設密碼。
       - 如果沒有部署回寫在部署中管理使用者的密碼，使用者會要求連絡他或她的管理員，重設密碼。
4.  如果決定使用者就可以成功重設密碼，使用者將引導重設程序。

進一步瞭解如何部署在密碼回寫[快速入門︰ Azure AD 密碼管理](active-directory-passwords-getting-started.md)。

### <a name="what-data-is-used-by-password-reset"></a>資料會使用重設密碼？
下列表格框線位置和方式此資料期間應使用的密碼重設並設計用來協助您決定哪些驗證選項包括 [適用於您的組織。 此表格也會顯示任何格式設定的需求，您可以在其中提供使用者輸入的路徑，不會驗證這項資料代表資料的情況下。

> [AZURE.NOTE] 辦公室電話不在註冊入口網站，因為使用者不目前無法編輯此屬性，在目錄中。

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>連絡方式名稱</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Azure Active Directory 資料的項目</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>使用 / 可設定位置嗎？</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>格式需求</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>辦公室電話</p>
            </td>
            <td>
              <p>電話號碼</p>
              <p>例如的設定 MsolUser UserPrincipalName JWarner@contoso.com -電話號碼 」 x 1234 + 1 1234567890 」</p>
            </td>
            <td>
              <p>用於︰</p>
              <p>密碼重設入口網站</p>
              <p>從可設定︰</p>
              <p>電話號碼是可設定 PowerShell、 DirSync、 Azure 管理入口網站，與 Office 管理入口網站</p>
            </td>
            <td>
              <p>+ ccc xxxyyyzzzz (例如 + 1 1234567890)</p>
              <ul>
                <li class="unordered">
必須提供國家/地區碼<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
必須提供區碼 （如果適用）<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
必須有提供 + 的前方的國家/地區的程式碼<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
必須國家/地區碼及其餘的數字之間的間距<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
不支援的副檔名]，如果您有指定任何擴充功能，我們會刪除其從數字之前分派電話。<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>行動電話</p>
            </td>
            <td>
              <p>AuthenticationPhone</p>
              <p>OR</p>
              <p>MobilePhone</p>
              <p>（如果存在的資料，則使用電話驗證]，否則這退回到行動電話] 欄位）。</p>
              <p>例如的設定 MsolUser UserPrincipalName JWarner@contoso.com -MobilePhone"x 1234 + 1 1234567890 」</p>
            </td>
            <td>
              <p>用於︰</p>
              <p>密碼重設入口網站</p>
              <p>註冊入口網站</p>
              <p>從可設定︰ </p>
              <p>AuthenticationPhone 是從密碼重設註冊入口網站或 MFA 註冊入口網站可設定。</p>
              <p>MobilePhone 是從 PowerShell、 DirSync、 Azure 管理入口網站，以及 Office 管理入口網站可設定</p>
            </td>
            <td>
              <p>+ ccc xxxyyyzzzz (例如 + 1 1234567890)</p>
              <ul>
                <li class="unordered">
必須提供國家/地區碼。<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
（如果適用），則必須提供區碼。<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
必須有提供 + 的前方的國家/地區的程式碼。<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
必須具備國家/地區碼及其餘的數字之間的間距。<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
不支援副檔名，如果您有指定任何擴充功能，予以忽略分派來電時。<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>備用電子郵件</p>
            </td>
            <td>
              <p>AuthenticationEmail</p>
              <p>OR</p>
              <p>AlternateEmailAddresses [0] </p>
              <p>（驗證資料簡報時使用電子郵件]，否則這回到 [備用電子郵件] 欄位）。</p>
              <p>附註: [備用電子郵件] 欄位會指定為陣列的目錄中的字串。  我們使用此陣列中的第一個項目。</p>
              <p>例如的設定 MsolUser UserPrincipalName JWarner@contoso.com -AlternateEmailAddresses"email@live.com"</p>
            </td>
            <td>
              <p>用於︰</p>
              <p>密碼重設入口網站</p>
              <p>註冊入口網站</p>
              <p>從可設定︰ </p>
              <p>AuthenticationEmail 是從密碼重設註冊入口網站或 MFA 註冊入口網站可設定。</p>
              <p>AlternateEmail 是 PowerShell、 Azure 管理入口網站，與 Office 管理入口網站可設定</p>
            </td>
            <td>
              <p>
                <a href="mailto:user@domain.com">user@domain.com</a>或甲斐@黒川.日本</p>
              <ul>
                <li class="unordered">
電子郵件應該遵循標準格式化為每個。<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Unicode 電子郵件系統支援。<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>安全性問題及答案</p>
            </td>
            <td>
              <p>若要修改直接在目錄中無法使用。</p>
            </td>
            <td>
              <p>用於︰</p>
              <p>密碼重設入口網站</p>
              <p>註冊入口網站 </p>
              <p>從可設定︰ </p>
              <p>若要設定安全性問題的唯一方法是透過 Azure 管理入口網站。</p>
              <p>若要設定為特定使用者的安全性問題的答案的唯一方法是透過 [註冊] 入口網站。</p>
            </td>
            <td>
              <p>安全性問題有 200 個字元的最大] 和 [最小值的 3 個字元</p>
              <p>答案有 40 個字元的最大] 和 [最小值的 3 個字元</p>
            </td>
          </tr>
        </tbody></table>

###<a name="how-to-access-password-reset-data-for-your-users"></a>如何存取密碼重設使用者的資料
####<a name="data-settable-via-synchronization"></a>資料可透過同步處理設定
從內部部署能夠同步處理下列欄位︰

* 行動電話
* 辦公室電話

####<a name="data-settable-with-azure-ad-powershell"></a>使用 PowerShell 的 Azure AD 可設定的資料
下列欄位是可使用 PowerShell 的 Azure AD 與圖形 API 存取︰

* 備用電子郵件
* 行動電話
* 辦公室電話
* 驗證電話
* 驗證電子郵件

####<a name="data-settable-with-registration-ui-only"></a>可設定登錄只使用者介面與資料
透過 SSPR 註冊使用者介面 (https://aka.ms/ssprsetup) 只在下列欄位︰

* 安全性問題及答案

####<a name="what-happens-when-a-user-registers"></a>當使用者註冊時，會發生什麼情況？
當使用者在進行註冊 [註冊] 頁面會**永遠**設定下列欄位︰

* 驗證電話
* 驗證電子郵件
* 安全性問題及答案

如果您有**行動電話**或**備用電子郵件**提供值，使用者可以立即使用重設密碼，即使他們您尚未註冊此服務。  此外，使用者將第一次，註冊時，請參閱這些值，並加以修改，當中。  不過，成功註冊之後，這些值將保存在 [**驗證電話**和**驗證電子郵件**] 欄位中，分別。

這可能是解除封鎖大量使用者同時仍可讓使用者驗證這項資訊進行註冊程序使用 SSPR 的有用方式。

####<a name="setting-password-reset-data-with-powershell"></a>設定密碼重設使用 PowerShell 的資料
您可以設定使用 PowerShell 的 Azure AD 下列欄位的值。

* 備用電子郵件
* 行動電話
* 辦公室電話

若要開始，您必須[下載](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule)並安裝 PowerShell 的 Azure AD 模組。  一旦您已經安裝，您可以遵循下列步驟來設定每一個欄位。

#####<a name="alternate-email"></a>備用電子郵件
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
```

#####<a name="mobile-phone"></a>行動電話
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
```

#####<a name="office-phone"></a>辦公室電話
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"
```

####<a name="reading-password-reset-data-with-powershell"></a>讀取密碼重設使用 PowerShell 的資料
您可以瞭解下列使用 PowerShell 的 Azure AD 欄位的值。

* 備用電子郵件
* 行動電話
* 辦公室電話
* 驗證電話
* 驗證電子郵件

若要開始，您必須[下載](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule)並安裝 PowerShell 的 Azure AD 模組。  一旦您已經安裝，您可以遵循下列步驟來設定每一個欄位。

#####<a name="alternate-email"></a>備用電子郵件
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
```

#####<a name="mobile-phone"></a>行動電話
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
```

#####<a name="office-phone"></a>辦公室電話
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber
```

#####<a name="authentication-phone"></a>驗證電話
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
```

#####<a name="authentication-email"></a>驗證電子郵件
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

## <a name="links-to-password-reset-documentation"></a>連結至密碼重設文件
以下是所有 Azure AD 密碼重設文件頁面的連結︰

* **以下是您，因為您無法登入方面的問題嗎？** 如果是這樣，[下列是您可以變更與重設您自己的密碼](active-directory-passwords-update-your-own-password.md)。
* [**運作方式**](active-directory-passwords-how-it-works.md)-瞭解六個不同的元件服務，以及每個會
* [**快速入門**](active-directory-passwords-getting-started.md)-瞭解如何允許您使用者重設並變更其雲端或內部部署的密碼
* [**自訂**](active-directory-passwords-customize.md)-瞭解如何自訂外觀與風格與您組織的需求服務的行為
* [**最佳作法**](active-directory-passwords-best-practices.md)-瞭解如何快速部署及有效地管理您的組織中的密碼
* [**取得深入見解**](active-directory-passwords-get-insights.md)-瞭解我們整合式的報告權限
* [**常見問題集**](active-directory-passwords-faq.md)-取得常見問題的解答
* [**疑難排解**](active-directory-passwords-troubleshoot.md)-瞭解如何快速疑難排解服務的問題



[001]: ./media/active-directory-passwords-learn-more/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-learn-more/002.jpg "Image_002.jpg"
