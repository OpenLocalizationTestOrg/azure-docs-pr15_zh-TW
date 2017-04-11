<properties
    pageTitle="設定使用者的 Azure AD 加入 |Microsoft Azure"
    description="說明如何系統管理員設定 Azure AD 加入的內部部署目錄及裝置註冊。"
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
    ms.topic="get-started-article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="setting-up-azure-ad-join-in-your-organization"></a>設定您的組織中的 Azure AD 加入

您設定 Azure Active Directory 加入 （Azure AD 加入） 之前，必須先設定您的使用者至雲端的內部部署目錄同步處理或是手動建立 Azure AD 中的 [受管理的帳戶。

討論[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)同步處理您的內部部署使用者 Azure AD 的詳細的指示。


若要手動建立 Azure AD 中管理使用者，請參閱[Azure AD 中的使用者管理](https://msdn.microsoft.com/library/azure/hh967609.aspx)。

## <a name="set-up-device-registration"></a>設定裝置註冊
1. 以系統管理員身分登入 Azure 入口網站。
2. 在左窗格中，選取 [ **Active Directory**]。
3. 在 [**目錄**] 索引標籤中，選取 [目錄]。
4. 選取 [**設定**] 索引標籤。
5. 移至 [**裝置**] 區段。
6. 在 [**裝置**] 索引標籤中，設定下列項目︰  
   * **最大值數字的裝置每位使用者**︰ 選取的使用者可以有 Azure AD 裝置的最大數目。  如果使用者達到此配額時，他們無法新增其他裝置，直到移除一或多個現有的裝置。
   * **需要多重因素驗證來加入裝置**︰ 設定使用者必須提供第二個驗證 factor Azure ad 加入他們的裝置是否。 如需有關 Azure 多重因素驗證的詳細資訊，請參閱[快速入門雲端中的 Azure 多重因素驗證](..\multi-factor-authentication\multi-factor-authentication-get-started-cloud.md)。
   * **使用者可能 AZURE AD 加入裝置**︰ 選取使用者和群組的允許 Azure AD 連接裝置。
   * **其他管理員開啟 AZURE AD 加入裝置**︰ 使用 Azure AD 進階版或企業版行動性套件 (EMS)，您可以選擇裝置的本機管理員權限授與使用者。 全域管理員和裝置擁有人預設會授與本機系統管理員權限。

<center>![設定裝置註冊](./media/active-directory-azureadjoin/active-directory-aadjoin-configure-devices.png)</center>

您設定 Azure AD 加入您的使用者之後，他們可以連線至 Azure AD 透過其公司或個人的裝置。

以下是您可以使用，讓您的使用者，可以設定 Azure AD 加入的三個案例︰

- 使用者會直接將 Azure AD 加入公司所擁有的裝置。
- 使用者網域連接到內部部署的 Active Directory 公司所擁有的裝置，然後將 Azure AD 延伸裝置。
- 使用者個人的裝置上的公司或學校帳戶新增至 Windows

## <a name="additional-information"></a>其他資訊
* [企業版的 Windows 10︰ 裝置用於工作的方法](active-directory-azureadjoin-windows10-devices-overview.md)
* [延伸至 Windows 10 裝置，透過 Azure [Active Directory 加入雲端功能](active-directory-azureadjoin-user-upgrade.md)
* [瞭解有關使用案例的 Azure AD 加入](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [加入網域的裝置連接到 Windows 10 體驗 Azure AD](active-directory-azureadjoin-devices-group-policy.md)
* [設定 Azure AD 加入](active-directory-azureadjoin-setup.md)
