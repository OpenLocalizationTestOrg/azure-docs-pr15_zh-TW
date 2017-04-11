<properties
    pageTitle="應用程式註冊入口網站的說明主題 |Microsoft Azure"
    description="Microsoft 應用程式註冊入口網站中的各種功能描述。"
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="app-registration-reference"></a>應用程式註冊參照
這份文件提供內容和描述的 Microsoft 應用程式註冊入口網站[https://apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)中找到的各種功能。

## <a name="my-applications"></a>我的應用程式
此清單包含所有註冊 Azure AD v2.0 端點搭配使用的應用程式。  這些應用程式能夠登入 Microsoft 帳戶和 Azure Active Directory 中的工作/學校帳戶兩個人帳戶的使用者。  若要進一步瞭解 Azure AD v2.0 端點，請參閱我們[v2.0 概觀](active-directory-appmodel-v2-overview.md)。  這些應用程式也可以用來與 Microsoft 帳戶驗證端點，整合`https://login.live.com`。

## <a name="live-sdk-applications"></a>即時 SDK 應用程式
此清單包含所有註冊的單獨使用 Microsoft 帳戶使用應用程式。  未啟用 Azure Active Directory 皆不適用。  這是您可以在此找到任何先前已在 MSA 開發人員入口網站註冊的應用程式`https://account.live.com/developers/applications`。  在先前執行的所有函數`https://account.live.com/developers/applications`現在可在此新的入口網站執行`https://apps.dev.microsoft.com`。  如果您有 Microsoft 帳戶應用程式的相關的任何進一步的問題，請與我們連絡。

## <a name="application-secrets"></a>應用程式密碼
應用程式密碼是允許您執行的 Azure AD 可靠的[用戶端驗證](http://tools.ietf.org/html/rfc6749#section-2.3)的應用程式的認證。  在 OAuth 與 OpenID 連線，應用程式密碼通常稱為`client_secret`。  V2.0 通訊協定，會收到 web 定址位置的安全性權杖任何應用程式中 (使用`https`配置) 必須使用應用程式密碼時的安全性權杖贖回 Azure ad 識別本身。  此外，任何原生的用戶端的將收到權杖上使用應用程式密碼以執行用戶端驗證]，若要防止不安全的環境中的機密資料的儲存空間禁止裝置。

每一個應用程式可以包含時間的任一特定點的兩個有效的應用程式密碼。  保留兩個密碼，您有應用程式的整個環境在執行定期金鑰變換 ablilty。  一旦您有移轉至新的密碼的應用程式的全部內容，您可能會刪除舊的密碼和佈建到新的訂閱。

此時，只有兩種應用程式密碼允許在應用程式註冊入口網站。  選擇 [**產生新的密碼**會產生和儲存在個別的資料存放區，您可以使用您的應用程式中的共用的密碼。  選擇 [**產生金鑰**，將會建立新的公用/私人金鑰組，可以下載和 Azure ad 的用戶端驗證時所使用。

## <a name="profile"></a>設定檔
應用程式註冊入口網站的 [設定] 區段可用來自訂登入您的應用程式的頁面。  現在您可以變更登入] 頁面的應用程式標誌、 規定服務 URL 和隱私權聲明。  標記必須是 15 kb GIF、 PNG 或 JPEG 檔案中的透明 48 x 48 或 50 x 50 像素圖像或縮小。  請嘗試變更的值，並檢視產生的登入頁面 ！

## <a name="live-sdk-support"></a>即時 SDK 支援
當您啟用 「 Live SDK 支援 」 時，您建立的任何應用程式機密會佈建到 Azure AD 與 Microsoft 帳戶資料存放區。  這可讓您的應用程式整合直接與 Microsoft 帳戶服務 (login.live.com)。  如果您想要建立應用程式直接 （而不使用 Azure AD v2.0 端點） 使用 Microsoft 帳戶，請確定已啟用 [Live SDK 支援。

停用 Live SDK 支援可確保應用程式密碼僅寫入 Azure AD 資料儲存。  Azure AD 資料儲存區整合，使其符合特定標準，例如 FISMA 規範企業級法規。  如果您啟用 Live SDK 支援，您的應用程式可能不達到部分這些標準的規範。

如果您只有打算使用 Azure AD v2.0 端點，您可以安全地停用 Live SDK 支援。

