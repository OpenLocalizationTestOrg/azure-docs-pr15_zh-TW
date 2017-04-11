<properties
    pageTitle="設定設定 Azure AD 的 Windows 10 裝置 |Microsoft Azure"
    description="說明如何使用者可以加入 Azure AD 透過 [設定] 功能表。"
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="set-up-a-windows-10-device-with-azure-ad-from-settings"></a>設定 Azure AD 的 Windows 10 裝置設定
如果您已經在使用 Windows 7 或 Windows 8 電腦或裝置已升級至 Windows 10，您可以透過 [設定] 功能表來加入 Azure Active Directory (Azure AD)。

## <a name="to-join-to-azure-ad-from-the-settings-menu"></a>若要加入至 Azure AD 從 [設定] 功能表


1. 從**[開始**] 功能表中，按一下 [**設定**] 快速鍵。
2. 從 [**設定**] 中，選取 [**系統**->**相關**->**加入 Azure AD**。
<center>
![加入 [設定] 功能表的 Azure AD](./media/active-directory-azureadjoin/active-directory-azureadjoin-settings.png)</center>

3. 按一下 [**繼續**Azure AD 加入郵件視窗中。
<center>
![加入 Azure AD 訊息視窗](./media/active-directory-azureadjoin/active-directory-azureadjoin-message.png)</center>
4. 提供您的登入認證。 此登入體驗會包含，才能完成驗證的所有步驟。 如果您是同盟租用戶的一部分，您的系統管理員會提供您裝載在您的組織同盟體驗。
<center>
![提供登入認證](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png)</center>
5. 如果您的組織已設定 Azure 多重因素驗證加入至 Azure AD，提供第二個因子變異數，再繼續執行。
6. 按一下 [**允許此裝置管理**] 畫面上的 [**接受**]。
7. 您應該會看到訊息 「 您的裝置現在已加入至您的組織中 Azure AD 」。


## <a name="additional-information"></a>其他資訊
* [瞭解有關使用案例的 Azure AD 加入](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [加入網域的裝置連接到 Windows 10 體驗 Azure AD](active-directory-azureadjoin-devices-group-policy.md)
* [設定 Azure AD 加入](active-directory-azureadjoin-setup.md)
* [不需要密碼透過 Microsoft 密碼驗證身分識別](active-directory-azureadjoin-passport.md)
