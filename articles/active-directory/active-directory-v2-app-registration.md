<properties
    pageTitle="v2.0 應用程式註冊 |Microsoft Azure"
    description="如何啟用登入及存取 Microsoft 服務使用 v2.0 端點向 Microsoft 註冊應用程式"
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

# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>如何註冊應用程式與 v2.0 端點

若要建立應用程式可接受 MSA 與 Azure AD 同時登入，首先必須向 Microsoft 註冊應用程式。  此時，您將無法使用任何現有的應用程式，您可能會有 Azure AD MSA-您需要建立全新的項目。

> [AZURE.NOTE]
    並非所有的 Azure Active Directory 案例與功能支援 v2.0 結束點。  若要判斷是否您應該使用 v2.0 端點，請閱讀有關[v2.0 限制](active-directory-v2-limitations.md)。

## <a name="visit-the-microsoft-app-registration-portal"></a>請造訪 Microsoft 應用程式註冊入口網站
第一個項目第一次-瀏覽至 [ [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)。  這是您可以在哪裡管理您的 Microsoft 應用程式的新應用程式註冊入口網站。

登入 [個人或公司或學校的 Microsoft 帳戶。  如果您沒有 [註冊新的個人帳戶。 請繼續進行，就不會花費長-我們會在這裡等。

完成？ 您應該現在會看到您的 Microsoft 應用程式，清單這可能是空值。  讓我們來變更]。

按一下 [**新增應用程式**]，並為它命名。  入口網站將會指派全域唯一的應用程式識別碼會稍後在您的程式碼中使用您的應用程式。  如果您的應用程式中包含伺服器端元件的所需的存取權杖呼叫 Api (認為︰ Office、 Azure 或您自己的網路 API)，您會想要同時建立**應用程式密碼**以下。
<!-- TODO: Link for app secrets -->

接下來，新增您的應用程式會使用的平台。

- Web 應用程式，提供**重新導向 URI**登入訊息傳送位置。
- 行動應用程式，請複製下自動為您建立的預設重新導向 uri。

或者，您可以自訂的設定檔] 區段中登入頁面的外觀與風格。  請務必按一下 [**儲存**]，再繼續。

> [AZURE.NOTE] 當您建立一個使用[https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)應用程式時，會之帳戶的您用來登入入口網站首頁的租用戶中登錄應用程式。  這表示您可以在您使用個人 Microsoft 帳戶的 Azure AD 租用戶中註冊您的應用程式。  如果您明確特定的租用戶中登錄應用程式，使用非最初的建立的租用戶中的帳戶登入。

## <a name="build-a-quick-start-app"></a>快速入門應用程式
您已經有 Microsoft 應用程式，您可以將其中一個我們 v2.0 快速入門教學課程完成。  以下是一些建議︰

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]
