<properties
    pageTitle="設定裝置條件存取原則的 Azure Active Directory 連線應用程式 |Microsoft Azure"
    description="Azure AD 連線的應用程式的裝置型條件存取原則設定。"
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="markvi"/>


# <a name="set-device-based-conditional-access-policy-for-azure-active-directory-connected-applications"></a>設定裝置條件存取原則的 Azure Active Directory 連線應用程式


Azure Active Directory (Azure AD) 裝置型條件存取可以協助您保護組織資源︰

- Access 會嘗試從未知或未受管理的裝置。
- 裝置不符合您的組織的安全性原則。

如需條件的存取權的概觀，請參閱[Azure Active Directory 條件存取](active-directory-conditional-access.md)。

您可以設定裝置型條件存取原則以保護這些應用程式︰

- Office 365 SharePoint Online 來保護您組織的網站和文件
- Office 365 Exchange Online 保護貴組織的電子郵件
- 以連線至 Azure AD 驗證服務 (SaaS) 應用程式的軟體
- 內部部署的發佈使用 Azure AD 應用程式 Proxy 服務應用程式

若要設定裝置型條件存取原則，Azure 入口網站中，移至特定的應用程式目錄中。


  ![Azure 入口網站目錄中的應用程式清單](./media/active-directory-conditional-access-policy-connected-applications/01.png "應用程式")


選取應用程式，然後按一下 [若要設定的條件存取原則的 [**設定**] 索引標籤。  


  ![設定應用程式](./media/active-directory-conditional-access-policy-connected-applications/02.png "裝置以存取規則")




若要設定裝置型條件存取原則，在 [**裝置以存取規則**] 區段中**啟用存取規則**，選取 [**上**]。

裝置條件存取原則有三個元件︰

- **若要套用**。 使用者原則套用到範圍。

- **裝置規則**。 條件才能存取應用程式，必須符合的裝置。

- **應用程式強制執行**。 用戶端應用程式 （原生瀏覽器與） 原則適用於。

  ![裝置存取原則的三個元件](./media/active-directory-conditional-access-policy-connected-applications/03.png "裝置以存取規則")


## <a name="select-the-users-the-policy-applies-to"></a>選取套用原則的使用者

在 [**套用至**] 區段中，您可以選擇此原則適用於的使用者的範圍。

當您建立的使用者存取原則範圍時，您會有兩個選項︰

- **所有使用者**。 所有的使用者可存取應用程式套用原則。

- **群組**。 限制特定群組的成員使用者原則。

![套用原則的所有使用者或群組](./media/active-directory-conditional-access-policy-connected-applications/11.png "套用至")


 若要排除原則的使用者，選取 [**除了**] 核取方塊。 當您需要授與權限給特定的使用者，以暫時存取應用程式時，這是很有幫助。 選取此選項，例如，如果您的使用者準備好進行條件的存取權的裝置。 裝置可能無法註冊，或要不相容性。


## <a name="select-the-conditions-that-devices-must-meet"></a>選取的裝置必須符合條件

使用**裝置規則**設定條件的裝置必須符合存取應用程式。

您可以設定裝置型條件存取這些裝置類型︰

- 在 Windows 10 週年紀念日更新與 Windows 8.1、 Windows 7
- Windows Server 2016 與 Windows Server 2012 R2、 Windows Server 2012、 Windows Server 2008 R2
- iOS 裝置 （iPad、 iPhone）
- Android 裝置

在 Mac 版支援即將推出。

  ![套用至裝置的原則](./media/active-directory-conditional-access-policy-connected-applications/04.png "應用程式")

 >[AZURE.NOTE] 網域和 Azure AD 加入裝置之間的差異的資訊，請參閱[使用 Windows 10 裝置，在您工作地點](active-directory-azureadjoin-windows10-devices.md)。

您有兩個裝置規則的選項︰

- **所有的裝置必須遵守**。 您必須遵守存取應用程式的所有裝置平台。 不支援的裝置型條件存取的平台執行的裝置無法存取。

- **選取的裝置必須遵守**。 僅限特定裝置平台必須遵守。 其他平台或其他平台可存取應用程式，有權存取。

  ![設定裝置規則的範圍](./media/active-directory-conditional-access-policy-connected-applications/05.png "應用程式")

Azure AD 加入裝置是如果 Intune 或協力廠商的行動裝置管理系統的整合 Azure AD 有標示為**相容**的目錄中。

在網域的裝置是相容如果︰

- Azure AD 向其註冊。 許多組織會視為受信任的裝置中的網域的裝置。
- 標示為**相容**Azure AD 中的 [系統管理中心設定管理員 2016年。

 ![為相容的網域的裝置](./media/active-directory-conditional-access-policy-connected-applications/06.png "裝置規則")

Windows 的個人裝置是如果 Intune 或協力廠商的行動裝置管理系統的整合 Azure AD 有標示為**相容**的目錄中。

非 Windows 裝置是如果它們標示為**相容**的目錄中 Intune。

 >[AZURE.NOTE] 如需有關如何設定在不同的管理系統的 Azure AD 裝置法規遵循的詳細資訊，請參閱[Azure Active Directory 條件存取](active-directory-conditional-access.md)。


您可以選取裝置存取原則的一或多個裝置平台。 這包含 Android、 iOS、 Windows Mobile （Windows 8.1 電話及平板電腦） 和 Windows （所有其他視窗的裝置，包括所有的 Windows 10 裝置）。
原則評估發生只能在所選的平台上。 如果嘗試存取的裝置未執行其中一個所選的平台，裝置可以存取應用程式，如果使用者擁有存取權。 沒有裝置的原則，會評估。

![選取裝置規則的平台](./media/active-directory-conditional-access-policy-connected-applications/07.png "裝置規則")


## <a name="set-policy-evaluation-for-a-type-of-application"></a>設定原則評估的一種應用程式

在 [**應用程式強制**] 區段中，設定原則會評估使用者或裝置存取的應用程式類型。

您有兩種選項包含的應用程式的類型︰

- 在瀏覽器和原生應用程式
- 原生應用程式

![選擇瀏覽器或原生應用程式](./media/active-directory-conditional-access-policy-connected-applications/08.png "應用程式")

若要強制執行存取原則應用程式，請選取**瀏覽器和原生應用程式**。 然後，您可以包含︰

- （例如，在 Windows 10 中的 Microsoft 邊緣） 或 ios Safari 瀏覽器。
- 應用程式的使用 Active Directory 驗證文件庫 (ADAL) 在任何平台，或使用 WebAccountManager (WAM) API Windows 10 中。

>[AZURE.NOTE] 如需支援瀏覽器及其他考量的使用者存取裝置為基礎，憑證授權單位保護應用程式，請參閱[Azure Active Directory 條件存取](active-directory-conditional-access.md)。

## <a name="help-protect-email-access-from-exchange-activesync-based-applications"></a>保護電子郵件存取 Exchange ActiveSync 應用程式

在 Office 365 Exchange Online 的應用程式，您可以使用 Exchange ActiveSync 封鎖的電子郵件存取 Exchange ActiveSync 為基礎的郵件應用程式。

![Exchange ActiveSync 相容性選項](./media/active-directory-conditional-access-policy-connected-applications/09.png "應用程式")

![需要相容的裝置存取電子郵件](./media/active-directory-conditional-access-policy-connected-applications/10.png "應用程式")


## <a name="next-steps"></a>後續步驟

- [Azure Active Directory 條件的存取](active-directory-conditional-access.md)
