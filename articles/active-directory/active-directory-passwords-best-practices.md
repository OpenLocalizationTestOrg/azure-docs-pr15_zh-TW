<properties
    pageTitle="最佳作法︰ Azure AD 密碼管理 |Microsoft Azure"
    description="部署及使用方式的最佳作法、 文件範例使用者和 Azure Active Directory 中的密碼管理訓練輔助線。"
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

# <a name="deploying-password-management-and-training-users-to-use-it"></a>部署密碼管理和訓練使用者使用

> [AZURE.IMPORTANT] **以下是您，因為您無法登入方面的問題嗎？** 如果是這樣，[下列是您可以變更與重設您自己的密碼](active-directory-passwords-update-your-own-password.md)。

啟用密碼重設之後，您需要採取的下一個步驟是讓使用者在您的組織使用的服務。 若要這麼做，必須先確定您的使用者已設定正確，同時使用服務使用者擁有一定要管理自己的密碼成功的訓練。 本文將說明您以下的概念︰

* [**如何設定密碼管理使用者**](#how-to-get-users-configured-for-password-reset)
  * [如何建立密碼重設所設定的帳戶](#what-makes-an-account-configured)
  * [您可以用來填入您自己的驗證資料的方式](#ways-to-populate-authentication-data)
* [**部署至您的組織重設密碼的最佳方法**](#what-is-the-best-way-to-roll-out-password-reset-for-users)
  * [以電子郵件為基礎的首展 + 範例電子郵件通訊](#email-based-rollout)
  * [建立您自己的自訂密碼管理入口網站使用者](#creating-your-own-password-portal)
  * [如何使用強制的註冊強制使用者註冊在登入](#using-enforced-registration)
  * [如何上傳的使用者帳戶的驗證資料](#uploading-data-yourself)
* [**範例使用者和支援訓練資料 （即將推出 ！）**](#sample-training-materials)

## <a name="how-to-get-users-configured-for-password-reset"></a>如何取得使用者設定密碼重設
本節會告訴您，您可以確保您組織中的每位使用者都可以使用自助密碼重有效，以避免使用者忘了使用者的密碼的各種方法。

### <a name="what-makes-an-account-configured"></a>有何所設定的帳戶
使用者可以使用密碼重設之前，**所有**的下列條件，必須符合︰

1.  在目錄中必須啟用重設密碼。  瞭解如何啟用閱讀[啟用重設 Azure AD 密碼的使用者](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords)，或[讓使用者重設或變更其 AD 密碼](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)重設密碼
2.  使用者必須擁有使用權。
 - 雲端使用者，使用者必須**任何付費的 Office 365 授權**，或**AAD 基本**或**AAD 進階版授權**指派。
 - 針對內部部署使用者 （同盟或雜湊同步處理），**必須要有 AAD 進階版授權指派給**使用者。
3.  使用者必須根據目前的密碼重設原則的**最小定義驗證資料集**。
 - 驗證資料會被視為定義如果目錄中的相對應欄位包含正確的資料。
 - 如果兩個閘道原則設定為在**至少一個**啟用的驗證選項，如果設定一門原則，或在**至少兩**個啟用的驗證選項定義一組最小值驗證資料。
4.  如果使用者使用內部部署帳戶，然後[密碼回寫](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)必須啟用並開啟

### <a name="ways-to-populate-authentication-data"></a>若要填入驗證資料的方式
您有幾個選項如何指定您組織中的使用者資料来用於重設密碼。

- 編輯[Azure 管理入口網站](https://manage.windowsazure.com)或[Office 365 管理入口網站](https://portal.microsoftonline.com)中的使用者
- 使用 Azure AD Sync，從內部部署的 Active Directory 網域同步到 Azure AD 的使用者屬性
- 使用 Windows PowerShell 來編輯使用者屬性[的步驟進行](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users)。
- 允許使用者註冊入口網站在[http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)引導其登錄自己的資料
- 要求使用者註冊使用密碼重設當使用者登入他們 Azure AD 帳戶設定[**要求使用者在註冊時登入？**](active-directory-passwords-customize.md#require-users-to-register-when-signing-in)設定為 [**是**] 選項。

使用者需要註冊系統運作的重設密碼。  例如，如果您有現有的行動電話] 或 [辦公室電話號碼，在您的本機目錄中，您可以同步處理中 Azure AD 和我們將會用於自動重設密碼。

您也可以瞭解更多關於[如何資料使用密碼重設](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset)和[您可以在如何填入使用 PowerShell 個別驗證欄位](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users)。

## <a name="what-is-the-best-way-to-roll-out-password-reset-for-users"></a>什麼是推行重設使用者的密碼的最好方法？
重設密碼的一般範圍步驟如下︰

1.  啟用移至[Azure 管理入口網站](https://manage.windowsazure.com)中的 [**設定**] 索引標籤，並**啟用使用者進行密碼重設**] 選項中選取 [ **** ，在您的目錄重設密碼。
2.  將適當的授權指派給每個您想要提供中重設密碼對象的使用者移至[Azure 管理入口網站](https://manage.windowsazure.com)中的 [**授權**] 索引標籤。
3.  您也可以選擇限制密碼重設為要推行功能速度緩慢並且一段時間設定**限制存取重設密碼**切換為**[是]** ，然後選取 [啟用密碼重設 （請注意下列使用者必須都是授權指派給他們） 安全性群組的使用者群組。
4.  指示您使用 [傳送電子郵件指示他們註冊，重設密碼的使用者啟用強制執行存取] 面板中，或上傳這些使用者的適當的驗證資料自己透過目錄同步、 PowerShell 或[Azure 管理入口網站](https://manage.windowsazure.com)註冊。  更多詳細資料，在此提供。
5.  一段時間，請檢閱瀏覽至 [報表] 索引標籤，並檢視[**密碼重設登錄活動**](active-directory-passwords-get-insights.md#view-password-reset-registration-activity)報表的註冊的使用者。
6.  一旦許多使用者已註冊，請觀看這些瀏覽至 [報表] 索引標籤，並檢視[**密碼重設活動**](active-directory-passwords-get-insights.md#view-password-reset-activity)報表來重設密碼] 使用。

有數種方式可通知您的使用者，他們可以註冊，並使用您的組織中重設密碼。  他們的詳細資訊] 下方。

### <a name="email-based-rollout"></a>以電子郵件為基礎的首展
或許以通知您的使用者註冊或使用密碼重設最簡單的方法是傳送電子郵件指示他們執行此作業。  以下是您可以使用執行此動作的範本。  隨意取代 [色彩 / 與您自己的商標選擇自訂以符合您的需求。

  ![][001]

您可以[下載的電子郵件範本](http://1drv.ms/1xWFtQM)。

### <a name="creating-your-own-password-portal"></a>建立您自己的密碼入口網站
適用於較大的客戶部署密碼管理功能的運作方式是，建立一個 「 密碼入口網站 「 您的使用者可以使用來管理所有項目的策略與他們的密碼，在單一位置。  

許多客戶的最大選擇建立根目錄 DNS 項目，例如 https://passwords.contoso.com 為連結至 Azure AD 密碼重設入口網站、 重設密碼註冊入口網站，與變更頁面的密碼等。  如此一來，任何電子郵件通訊或 fliers 您送出，您可以在包含單一、 令人印象深刻，使用者可以移至有秒若要開始使用服務時的 URL。

若要上手以下，我們建立簡單的頁面，使用最新回應使用者介面設計架構後,，並可在所有的瀏覽器和行動裝置上運作。

  ![][007]

您可以[下載的網站範本](https://github.com/kenhoff/password-reset-page)。  我們建議您自訂的標誌和色彩貴組織的需求。

### <a name="using-enforced-registration"></a>使用強制的註冊
若要將您的使用者註冊重設自己的密碼，您也可以強制即可註冊當使用者登入[http://myapps.microsoft.com](http://myapps.microsoft.com)在存取面板。  啟用**要求使用者登入存取畫面時的 Register** ] 選項，您可以啟用此選項，從您的目錄**設定**] 索引標籤。  

您也可以選擇可以定義會要求他們重新註冊可設定一段時間後修改為非零值的 [**之前使用者必須確認其連絡人資料天數**] 選項。 如需詳細資訊，請參閱[自訂使用者的密碼管理行為](active-directory-passwords-customize.md#password-management-behavior)。

  ![][002]

當使用者登入存取畫面時，啟用此選項之後，就會看到快顯視窗，告訴他們的管理員有必要，驗證他們的連絡人資訊。 他們可以使用它來重設使用者的密碼，如果其曾即將無法存取其帳戶。

  ![][003]

按一下 [**立即確認**為**密碼重設註冊入口網站**在[http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) ，並要求他們註冊。  註冊，透過這種方式可以關閉藉由按一下 [**取消**] 按鈕，或關閉視窗，但是使用者都會提醒每次執行註冊如果登入。

  ![][004]

### <a name="uploading-data-yourself"></a>上傳資料自己
如果您想要上傳驗證資料自己，然後使用者需要註冊才能重設密碼重設密碼。  只要有使用者驗證資料定義的帳號符合密碼重設已定義的原則，然後這些使用者能夠重設密碼。

若要瞭解哪些內容，您可以設定透過 AAD 連線或 Windows PowerShell，請參閱[什麼資料由重設密碼](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset)。

您上傳驗證資料透過[Azure 管理入口網站](https://manage.windowsazure.com)，請遵循下列步驟︰

1.  瀏覽至您的目錄中在[管理入口網站 Azure](https://manage.windowsazure.com) **Active Directory 副檔名**。
2.  按一下 [**使用者**] 索引標籤。
3.  選取想要從清單中的使用者。
4.  在第一個標籤中，您會發現**備用電子郵件**，可以為屬性用來啟用重設密碼。

    ![][005]

5.  按一下 [**工作資訊**] 索引標籤。
6.  在此頁面上，您會發現**辦公室電話**、**行動電話**、**驗證電話**和**驗證電子郵件**。  這些屬性也可以設定允許使用者重設密碼。

    ![][006]

瞭解[什麼資料由重設密碼](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset)，瞭解如何使用每個屬性。

請參閱[如何存取密碼重設使用者從 PowerShell 的資料](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users)以查看您可以讀取與設定此資料與 PowerShell。

## <a name="sample-training-materials"></a>範例訓練資料
我們正在處理範例訓練資料，您可以使用存取您的 IT 組織和您的使用者充分快速部署及使用重設密碼的方式。  見 ！


<br/>
<br/>
<br/>

## <a name="links-to-password-reset-documentation"></a>連結至密碼重設文件
以下是所有 Azure AD 密碼重設文件頁面的連結︰

* **以下是您，因為您無法登入方面的問題嗎？** 如果是這樣，[下列是您可以變更與重設您自己的密碼](active-directory-passwords-update-your-own-password.md)。
* [**運作方式**](active-directory-passwords-how-it-works.md)-瞭解六個不同的元件服務，以及每個會
* [**快速入門**](active-directory-passwords-getting-started.md)-瞭解如何允許您使用者重設並變更其雲端或內部部署的密碼
* [**自訂**](active-directory-passwords-customize.md)-瞭解如何自訂外觀與風格與您組織的需求服務的行為
* [**取得深入見解**](active-directory-passwords-get-insights.md)-瞭解我們整合式的報告權限
* [**常見問題集**](active-directory-passwords-faq.md)-取得常見問題的解答
* [**疑難排解**](active-directory-passwords-troubleshoot.md)-瞭解如何快速疑難排解服務的問題
* [**進一步瞭解**](active-directory-passwords-learn-more.md)移深的 service 的運作方式技術詳細資料



[001]: ./media/active-directory-passwords-best-practices/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-best-practices/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-best-practices/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-best-practices/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-best-practices/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-best-practices/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-best-practices/007.jpg "Image_007.jpg"
