<properties
    pageTitle="設定在安裝期間 Azure AD 的新裝置 |Microsoft Azure"
    description="說明如何使用者設定 Azure AD 加入第一個執行經驗期間的主題。"
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

# <a name="set-up-a-new-device-with-azure-ad-during-setup"></a>Azure AD 在安裝期間的新裝置設定

在 Windows 10 中，使用者可以加入其裝置 Azure Active Directory (Azure AD) 中的第一個執行體驗 (FRX)。 這個選項可讓組織發佈至其員工或學生，這些的裝置，或讓他們選擇自己的裝置 (CYOD)。
如果裝置上已安裝在 Windows 10 專業版或 Windows 10 企業版本，獲得的體驗預設為公司所擁有的裝置的設定程序。

## <a name="to-join-a-device-to-azure-ad"></a>Azure AD 聯結裝置


1. 當您開啟新裝置，並開始安裝程序時，您應該會看到**好收到**的郵件。 依提示設定您的裝置。
2. 開始自訂您的地區及語言。 再按一下 [接受 Microsoft 軟體授權條款。
![自訂您的地區](./media/active-directory-azureadjoin/active-directory-azureadjoin-customize-region.png)
3. 選取您想要用於連線至網際網路的網路。
4. 選取您是否正在使用的個人裝置或公司所擁有的裝置。 如果是公司擁有者，請按一下 [**此裝置所屬組織**]。 此舉會啟動 Azure AD 加入體驗。 以下是您會看到正在使用 Windows 10 專業的畫面。
<center>
![此電腦螢幕擁有者是誰](./media/active-directory-azureadjoin/active-directory-azureadjoin-who-owns-pc.png)

5.  輸入您的組織所提供給您的認證。
<center>
![登入畫面](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png)
6.  輸入您的使用者名稱之後，相符的租用戶位於 Azure AD。 如果您是同盟網域中，您會重新導向至內部部署安全性 Token 服務 (STS) 伺服器，例如 Active Directory Federation Services (AD FS)。
7. 如果您是在非聯盟網域中的使用者，請直接在 Azure AD 裝載於頁面上輸入您的認證。 如果已設定為公司商標，也會看到貴組織的標誌和支援文字。
8.  系統提示您的多重因素驗證挑戰。 這個問題是由 IT 系統管理員設定的。
9.  Azure AD 檢查此使用者/裝置是否需要註冊在行動裝置管理]。
10. Windows Azure AD 中，在組織的目錄中註冊裝置和註冊行動裝置管理] 中，視。
11. 如果您是受管理的使用者，則 Windows 會帶您到桌面透過自動登入程序。
12. 如果您是同盟的使用者時，您獲指引須 Windows 登入畫面輸入您的認證。

> [AZURE.NOTE] 不支援加入內部部署的 Windows Server Active Directory 網域中的 Windows 現成的體驗。 因此，如果您打算將電腦加入網域，您應該改用選取**設定 Windows 與本機帳戶**的連結。 然後您可以在您的電腦上加入從設定網域，為您已完成之前。

## <a name="additional-information"></a>其他資訊
* [企業版的 Windows 10︰ 裝置用於工作的方法](active-directory-azureadjoin-windows10-devices-overview.md)
* [延伸至 Windows 10 裝置，透過 Azure [Active Directory 加入雲端功能](active-directory-azureadjoin-user-upgrade.md)
* [不需要密碼透過 Microsoft 密碼驗證身分識別](active-directory-azureadjoin-passport.md)
* [瞭解有關使用案例的 Azure AD 加入](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [加入網域的裝置連接到 Windows 10 體驗 Azure AD](active-directory-azureadjoin-devices-group-policy.md)
* [設定 Azure AD 加入](active-directory-azureadjoin-setup.md)
