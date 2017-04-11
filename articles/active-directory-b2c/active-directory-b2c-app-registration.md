<properties
    pageTitle="Azure Active Directory B2C︰ 應用程式註冊 |Microsoft Azure"
    description="如何與 Azure Active Directory B2C 註冊您的應用程式"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/30/2016"
    ms.author="swkrish"/>


# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C︰ 註冊您的應用程式

## <a name="prerequisite"></a>必要條件

若要建立可接受消費者註冊和登入應用程式，您必須註冊 Azure Active Directory B2C 租用戶的應用程式。 使用在[建立 Azure AD B2C 租用戶](active-directory-b2c-get-started.md)中所述的步驟，以取得您自己的租用戶。 您追蹤的文件中的所有步驟之後，您會有釘選到您 Startboard B2C 功能刀。

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="navigate-to-the-b2c-features-blade"></a>瀏覽至 B2C 功能刀

如果您有釘選到您 Startboard B2C 功能刀，您會看到刀，當您登入[Azure 入口網站](https://portal.azure.com/)以全域管理員 B2C 租用戶。

您也可以按一下 [在[Azure 入口網站](https://portal.azure.com/)上的左側的功能窗格中的 [**瀏覽**]，然後**Azure AD B2C**存取刀。

> [AZURE.IMPORTANT] 您必須是全域管理員，才能存取 B2C 功能刀 B2C 租用戶。 從任何其他租用戶的全域管理員或從任何租用戶的使用者無法存取。  您可以利用 Azure 入口網站的右上角中的租用戶切換器切換至您的 B2C 租用戶。

## <a name="register-an-application"></a>登錄應用程式

1. 在 B2C 功能會刀 Azure 入口網站上，按一下 [**應用程式**]。
2. 按一下 [ **+ 新增**刀頂端]。
3. 輸入可描述您的應用程式使用者皆應用程式的**名稱**。 例如，您可以輸入 「 Contoso B2C 應用程式]。
4. 如果您正在撰寫的 web 應用程式，請切換**包括 web 應用程式 / 網頁 API**切換為 [**是]**。 **回覆 Url**是端點 Azure AD B2C 會傳回應用程式要求任何權杖的位置。 例如，輸入`https://localhost:44321/`。 如果您的 web 應用程式會也呼叫某些網路 Azure AD B2C 受保護的 API，您要以及建立 [**應用程式密碼**，按一下 [**產生金鑰**] 按鈕。

    > [AZURE.NOTE] **應用程式密碼**的重要的安全性憑證，而適當安全。

5. 如果您正在撰寫的行動應用程式，請切換**包含原生用戶端**切換為 [**是]**。 複製下**重新導向 URI** ，會自動為您建立的預設值。
6. 按一下 [**建立**]，以註冊您的應用程式]。
7. 按一下您剛才建立的應用程式，然後複製您稍後在您的程式碼會使用全域唯一**的應用程式的用戶端識別碼**。

> [AZURE.IMPORTANT] 在 B2C 功能刀中建立的應用程式必須在同一個位置管理。 如果您編輯 B2C 使用 PowerShell 或另一個入口網站應用程式使用者成為不支援且可能無法搭配 Azure AD B2C。

## <a name="build-a-quick-start-application"></a>建置應用程式快速入門

有註冊 Azure AD B2C 的應用程式之後，您可以完成其中一個我們的快速入門教學課程來快速上手。 以下是一些建議︰

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]
