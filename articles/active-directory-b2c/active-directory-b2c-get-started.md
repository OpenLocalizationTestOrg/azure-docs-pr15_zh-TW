<properties
    pageTitle="Azure Active Directory B2C︰ 建立 Azure Active Directory B2C 租用戶 |Microsoft Azure"
    description="如何建立 Azure Active Directory B2C 租用戶主題"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.topic="article"
    ms.devlang="na"
    ms.date="08/30/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-create-an-azure-ad-b2c-tenant"></a>Azure Active Directory B2C︰ 建立 Azure AD B2C 租用戶

若要開始使用 Microsoft Azure Active Directory (Azure AD) B2C，請遵循本文中所述的三個步驟。

## <a name="step-1-sign-up-for-an-azure-subscription"></a>步驟 1︰ 註冊 Azure 的訂閱

如果您已經 Azure 的訂閱，請略過此步驟。 如果不是，註冊[Azure 訂閱](../active-directory/sign-up-organization.md)和 Azure AD B2C 存取。

## <a name="step-2-create-an-azure-ad-b2c-tenant"></a>步驟 2︰ 建立 Azure AD B2C 租用戶

若要建立新的 Azure AD B2C 租用戶中使用下列步驟。 目前 B2C 功能無法開啟您現有的租用戶中。

1. 登入[Azure 傳統入口網站](https://manage.windowsazure.com/)以訂閱系統管理員。 這是相同的工作或學校帳戶或您用來註冊 Azure 的相同 Microsoft 帳戶。
2. 按一下 [**新** > **應用程式服務** > **Active Directory** > **目錄** > **建立自訂**。

    ![開始建立租用戶的螢幕擷取畫面](./media/active-directory-b2c-get-started/new-directory.png)

3. 租用戶中選擇 [**名稱**]、**網域名稱**與**國家或地區**]。
4. 核取 [指出**這是 B2C 目錄**] 選項。
5. 按一下 [核取記號，完成的動作。

    ![若要建立 B2C 目錄核取記號的螢幕擷取畫面](./media/active-directory-b2c-get-started/create-b2c-directory.png)

6. 您的租用戶現在建立，而會出現在 Active Directory 副檔名。 您也會有租用戶全域管理員。 您可以視需要新增其他全域系統管理員。

    > [AZURE.IMPORTANT]
    如果您打算使用 B2C 租用戶生產應用程式，請閱讀[生產縮放比例與預覽 B2C 租用戶](active-directory-b2c-reference-tenant-type.md)上的文件。 請注意，那里已知問題，當您刪除現有的 B2C 租用戶，並重新建立相同的網域名稱。 您必須使用不同的網域名稱建立 B2C 租用戶。

## <a name="step-3-navigate-to-the-b2c-features-blade-on-the-azure-portal"></a>步驟 3︰ 瀏覽至 Azure 入口網站上 B2C 功能刀

1. 瀏覽至左側導覽列上的 Active Directory 副檔名。
2. 尋找您的租用戶，在 [**目錄**] 索引標籤，然後按一下它。
3. 按一下 [**設定**] 索引標籤。
4. 按一下 [ **B2C 管理**] 區段中的 [**管理 B2C 設定**] 連結。

    ![B2C 目錄設定的螢幕擷取畫面](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)

5. 顯示 B2C 功能刀 Azure 入口網站會在新瀏覽器索引標籤或視窗中開啟。

    > [AZURE.IMPORTANT]
    它需要 2-3 分鐘 Azure 入口網站上存取您租用戶。 一些時間會修正此問題之後，請重試這些步驟。 如果沒有的話，請連絡支援人員。

6. 釘選到您 Startboard 此刀，以方便存取。 （釘選工具功能刀右上角的紅色標記）。

    ![B2C 功能刀的螢幕擷取畫面](./media/active-directory-b2c-get-started/b2c-features-blade.png)

    > [AZURE.NOTE]
    您可以管理使用者和群組、 自助密碼重設設定，以及您的租用戶[Azure 傳統入口網站](https://manage.windowsazure.com/)上的公司商標功能。

## <a name="easy-access-to-the-b2c-features-blade-on-the-azure-portal"></a>Azure 入口網站上 B2C 功能刀輕鬆存取

若要改善可搜尋性，我們已 B2C 功能刀 Azure 入口網站上新增捷徑。

1. 以全域管理員的您 B2C 租用戶登入 Azure 入口網站。 如果您已經登入不同的租用戶，切換租用戶 （在右上角）。
2. 在左側導覽中按一下 [**瀏覽**]。
3. 按一下 [存取 B2C 功能刀**Azure AD B2C** ]。

    ![瀏覽] 的螢幕擷取畫面至 B2C 功能刀](./media/active-directory-b2c-get-started/b2c-browse.png)

## <a name="next-steps"></a>後續步驟

瞭解如何註冊 Azure AD B2C 應用程式，以及建置快速入門的應用程式，請閱讀[Azure Active Directory B2C︰ 註冊您的應用程式](active-directory-b2c-app-registration.md)。
