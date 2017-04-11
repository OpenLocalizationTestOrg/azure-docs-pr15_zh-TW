<properties
    pageTitle="Azure Active Directory B2C︰ 自訂屬性 |Microsoft Azure"
    description="如何使用自訂屬性中 Azure Active Directory B2C 收集您客戶的相關資訊"
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
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

#  <a name="azure-active-directory-b2c-use-custom-attributes-to-collect-information-about-your-consumers"></a>Azure Active Directory B2C︰ 收集您使用者能使用的自訂屬性

Azure Active Directory (Azure AD) B2C 目錄隨附一組內建的資訊 （屬性）︰ 名字、 姓氏、 縣/市、 郵遞區號和其他屬性。 不過，每個消費者具應用程式具有唯一需求上屬性，以從使用者收集。 您可以使用 Azure AD B2C，擴充屬性儲存在每個消費者帳戶的設定。 您可以在[Azure 入口網站](https://portal.azure.com/)上建立的自訂屬性，並將其用於您註冊的原則，如下所示。 您也可以讀取及撰寫這些屬性使用[Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md)。

> [AZURE.NOTE]
自訂屬性使用[Azure AD Graph API 目錄架構延伸](https://msdn.microsoft.com/library/azure/dn720459.aspx)。

## <a name="create-a-custom-attribute"></a>建立自訂屬性

1. [請遵循下列步驟，瀏覽至 Azure 入口網站上 B2C 功能刀](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 按一下 [**使用者屬性**]。
3. 按一下 [ **+ 新增**刀頂端]。
4. 提供的自訂屬性 (例如，「 ShoeSize 」) 和 （選擇性），**描述**的**名稱**。 按一下 [**建立**]。

    > [AZURE.NOTE]
    目前使用 「 字串 」**資料類型**。

現在在清單中的**使用者屬性**，以及在您註冊原則中使用的自訂屬性。

## <a name="use-a-custom-attribute-in-your-sign-up-policy"></a>使用您註冊的原則中的自訂屬性

1. [請遵循下列步驟，瀏覽至 Azure 入口網站上 B2C 功能刀](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 按一下 [**註冊原則**]。
3. 按一下您註冊的原則 (例如，「 B2C_1_SiUp 」)，將其開啟。 按一下 [刀頂端的 [**編輯**]。
4. 按一下 [**註冊屬性**]，然後選取 [自訂屬性 (例如，「 ShoeSize 」)。 按一下**[確定]**。
5. 按一下 [**應用程式宣告**，然後選取 [自訂屬性。 按一下**[確定]**。
6. 按一下 [刀頂端的 [**儲存**]。

您可以使用 [立即執行] 功能的原則驗證的消費者體驗。 您現在應該消費者註冊，期間收集屬性的清單中看到 「 ShoeSize 」，然後權杖傳回給您的應用程式中檢視。

## <a name="notes"></a>備忘稿

- 註冊原則，以及自訂屬性也可註冊或登入的原則和編輯原則的設定檔中。
- 有已知的限制的自訂屬性。 這只被建立任何原則，並將其新增到**使用者屬性**的清單時，不使用的第一次。
