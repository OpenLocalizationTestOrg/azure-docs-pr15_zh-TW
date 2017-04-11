<properties
    pageTitle="運作方式︰ Azure AD 密碼管理 |Microsoft Azure"
    description="深入了解 Azure AD 密碼管理，讓使用者註冊、 重設，及變更其密碼，包括不同元件與管理員設定的位置報告，以及啟用管理內部部署 Active Directory 密碼。"
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

# <a name="how-password-management-works"></a>密碼管理的運作方式

> [AZURE.IMPORTANT] **以下是您，因為您無法登入方面的問題嗎？** 如果是這樣，[下列是您可以變更與重設您自己的密碼](active-directory-passwords-update-your-own-password.md)。

Azure Active Directory 中的密碼管理包含數個邏輯元件，如下所述。  按一下每個連結，若要進一步瞭解該元件。

- [**密碼管理設定入口網站**](#password-management-configuration-portal)-系統管理員可以控制不同層面的租用戶中管理密碼的方式，瀏覽至他們的目錄中[Azure 管理入口網站](https://manage.windowsazure.com)設定] 索引標籤。
- [**使用者註冊入口網站**](#user-registration-portal)– 使用者可以自行註冊此入口網站透過重設密碼。
- [**使用者的密碼重設入口網站**](#user-password-reset-portal)– 使用者可以重設自己使用不同的挑戰，根據系統管理員控制密碼重設原則的數字的密碼
- [**使用者密碼變更入口網站**](#user-password-change-portal)– 使用者可以變更隨時自己的密碼輸入 [舊密碼，然後選取 [使用此入口網站的新密碼
- [**密碼管理報告**](#password-management-reports)-系統管理員可以檢視和分析其租用戶中的密碼重設和註冊活動瀏覽至他們的目錄中[Azure 管理入口網站](https://manage.windowsazure.com)的 「 報表 」 這個] 索引標籤的 「 活動報表 」 區段
- [**Azure AD Connect 密碼回寫元件**](#password-writeback-component-of-azure-ad-connect)– 安裝 Azure AD Connect 啟用管理同盟或密碼同步處理雲端使用者密碼時，系統管理員可以選擇性地啟用密碼回寫功能。

## <a name="password-management-configuration-portal"></a>密碼管理設定入口網站
您可以設定密碼管理原則特定目錄使用[Azure 管理入口網站](https://manage.windowsazure.com)瀏覽至 [**使用者密碼重設原則**] 區段中的目錄**設定**] 索引標籤。  此設定頁面上，您可以控制貴組織中管理密碼的方式的各種包括︰

- 啟用及停用在目錄中的所有使用者重設密碼
- 設定的挑戰數目 （一或兩個） 使用者必須通過重設密碼
- 設定特定類型的挑戰您要啟用使用者的組織從以下幾個選擇︰
 - 行動電話 （透過文字或語音通話的驗證碼）
 - 辦公室電話 （語音通話）
 - 備用電子郵件 （透過電子郵件的驗證碼）
 - 安全性問題 （知識庫型驗證）
- 設定的問題使用者必須註冊，才能使用安全性問題驗證方法 （僅能看到如果啟用安全性問題）
- 設定的問題使用者必須提供期間重設為使用安全性問題驗證方法 （僅能看到如果啟用安全性問題）
- 使用預先定義的本地化，安全性問題的使用者可以選擇註冊時使用密碼重設 （僅能看到如果啟用安全性問題）
- 定義自訂的安全性問題的使用者可以選擇註冊時使用密碼重設 （僅能看到如果啟用安全性問題）
- 要求使用者註冊時移至該應用程式存取面板在[http://myapps.microsoft.com](http://myapps.microsoft.com)重設密碼。
- 若要重新設定天數後確認其先前已註冊的資料需要使用者過去之後 （僅能看到如果啟用強制的註冊）
- 提供自訂的服務台電子郵件或萬一遇到問題，重設密碼會顯示使用者的 URL
- 啟用或停用密碼回寫功能，（時使用 AAD 連線部署密碼回寫）
- 檢視密碼回寫代理程式的狀態 （時使用 AAD 連線部署密碼回寫）
- 自己的密碼已重設 （ [Azure 管理入口網站](https://manage.windowsazure.com)的 [**通知**] 區段中找到） 時，啟用使用者的電子郵件通知
- 其他系統管理員重設自己的密碼 （在[Azure 管理入口網站](https://manage.windowsazure.com)的 [**通知**] 區段中找到的系統管理員啟用電子郵件通知
- 商標使用者密碼重設入口網站，並使用商標自訂功能 （在[Azure 管理入口網站](https://manage.windowsazure.com)的 [**目錄內容**] 區段中找到的租用戶密碼重設的電子郵件與您組織的標誌和名稱

若要進一步瞭解您組織中設定密碼管理，請參閱[快速入門︰ Azure AD 密碼管理](active-directory-passwords-getting-started.md)。

##<a name="user-registration-portal"></a>使用者註冊入口網站
使用者都可以使用密碼重設之前，必須使用正確的驗證資料，以確保可以傳送到適當的數字的定義系統管理員的密碼重設挑戰更新雲端使用者的帳戶。  管理員也可以定義此驗證資訊他們的使用者代表使用 Azure] 或 [Office 入口網站，DirSync / Azure AD Connect 或 Windows PowerShell。

不過，如果您希望使用者註冊自己的資料，我們也提供一個網頁，使用者可以移至，才能提供這項資訊。  此頁面可讓使用者指定驗證資訊，根據的密碼重設已啟用讓組織中的原則。  一旦此資料就完成驗證了，它會儲存在雲端使用者帳戶以用於稍後帳戶復原。 以下是註冊入口網站看起來像什麼︰

  ![][001]

如需詳細資訊，請參閱[快速入門︰ Azure AD 密碼管理](active-directory-passwords-getting-started.md)和[最佳作法︰ Azure AD 密碼管理](active-directory-passwords-best-practices.md)。

##<a name="user-password-reset-portal"></a>使用者的密碼重設入口網站
一旦您啟用自助密碼重設、 設定貴組織的自助密碼重設原則，並確保您的使用者的目錄中有適當的連絡資料，您組織中的使用者將能夠重設自己的密碼，會自動從任何網頁使用的公司或學校帳戶登入 （例如[portal.microsoftonline.com](https://portal.microsoftonline.com))。 在頁面上這類，使用者會看到**無法存取您的帳戶？**連結。

  ![][002]

按一下這個連結，便會啟動自助密碼重設入口網站。

  ![][003]

若要進一步瞭解使用者如何可以重設自己的密碼，請參閱[快速入門︰ Azure AD 密碼管理](active-directory-passwords-getting-started.md)。

##<a name="user-password-change-portal"></a>使用者密碼變更入口網站
如果使用者想要變更自己的密碼，他們可以隨時使用密碼變更入口網站執行。  使用者可以存取透過存取面板設定檔頁面中，或按一下 [變更密碼] 中的連結在 Office 365 應用程式中的 [密碼] 變更入口網站。  大小寫在後他們的密碼到期時，使用者會也要求登入時自動變更它們。

  ![][004]

在這兩種情況下，如果已啟用密碼回寫，請同盟使用者或想密碼同步處理，這些變更的密碼會寫入返回您內部部署的 Active Directory。 以下是密碼變更入口網站看起來像什麼︰

  ![][005]

若要進一步瞭解使用者如何變更自己內部部署的 Active Directory 密碼，請參閱[快速入門︰ Azure AD 密碼管理](active-directory-passwords-getting-started.md)。

##<a name="password-management-reports"></a>密碼管理報告
藉由瀏覽至 [**報表**] 索引標籤，並尋找**活動記錄**] 區段下，您會看到兩個密碼管理報表︰**重設密碼的活動**和**密碼重設登錄活動**。  使用下列兩個報表，您可以取得使用者註冊，並使用密碼重設您的組織中的檢視。 以下是這些報表中[Azure 管理入口網站](https://manage.windowsazure.com)的外觀︰

  ![][006]

如需詳細資訊，請參閱[取得深入見解︰ Azure AD 密碼管理報表](active-directory-passwords-get-insights.md)。

##<a name="password-writeback-component-of-azure-ad-connect"></a>密碼回寫元件的 [Azure AD Connect
如果您組織中的使用者的密碼來自內部部署環境 （可能是透過同盟或密碼同步處理），您可以安裝最新版 Azure AD Connect 啟用更新直接從雲端這些密碼。  這表示時要修改 AD 密碼或忘記您的使用者，他們可以，因此直接從網頁。  以下是 Azure AD Connect 安裝精靈中的密碼回寫位置︰

  ![][007]

如需有關 Azure AD Connect 的詳細資訊，請參閱[快速入門︰ Azure AD Connect](active-directory-aadconnect.md)。 如需密碼回寫的詳細資訊，請參閱[快速入門︰ Azure AD 密碼管理](active-directory-passwords-getting-started.md)。


<br/>
<br/>
<br/>

## <a name="links-to-password-reset-documentation"></a>連結至密碼重設文件
以下是所有 Azure AD 密碼重設文件頁面的連結︰

* **以下是您，因為您無法登入方面的問題嗎？** 如果是這樣，[下列是您可以變更與重設您自己的密碼](active-directory-passwords-update-your-own-password.md)。
* [**快速入門**](active-directory-passwords-getting-started.md)-瞭解如何允許您使用者重設並變更其雲端或內部部署的密碼
* [**自訂**](active-directory-passwords-customize.md)-瞭解如何自訂外觀與風格與您組織的需求服務的行為
* [**最佳作法**](active-directory-passwords-best-practices.md)-瞭解如何快速部署及有效地管理您的組織中的密碼
* [**取得深入見解**](active-directory-passwords-get-insights.md)-瞭解我們整合式的報告權限
* [**常見問題集**](active-directory-passwords-faq.md)-取得常見問題的解答
* [**疑難排解**](active-directory-passwords-troubleshoot.md)-瞭解如何快速疑難排解服務的問題
* [**進一步瞭解**](active-directory-passwords-learn-more.md)移深的 service 的運作方式技術詳細資料



[001]: ./media/active-directory-passwords-how-it-works/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-how-it-works/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-how-it-works/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-how-it-works/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-how-it-works/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-how-it-works/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-how-it-works/007.jpg "Image_007.jpg"
