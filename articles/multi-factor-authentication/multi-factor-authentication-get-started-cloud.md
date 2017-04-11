<properties
    pageTitle="取得在雲端啟動 Azure MFA |Microsoft Azure"
    description="這是說明如何開始使用 Azure MFA 雲端中的 Microsoft Azure 多重因素驗證頁面。"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/17/2016"
    ms.author="kgremban"/>

# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>開始使用雲端中的 Azure 多重因素驗證
本文將引導如何開始使用雲端中的 Azure 多重因素驗證。

> [AZURE.NOTE]  下列文件如何讓使用者使用**Azure 傳統入口網站**提供資訊。 如果您要尋找如何設定 Azure 多重因素驗證 O365 使用者的詳細資訊，請參閱[設定 Office 365 的多重因素驗證。](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US)

![在雲端的 MFA](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## <a name="prerequisites"></a>必要條件
您可以為您的使用者啟用 Azure 多重因素驗證下列先決條件所需的。


1. [Azure 訂閱註冊](https://azure.microsoft.com/pricing/free-trial/)-如果您還沒有 Azure 的訂閱，您需要註冊一個。 如果您只是開始，並使用 Azure MFA 您可以使用試用訂閱
2. [建立多重因素驗證提供者](multi-factor-authentication-get-started-auth-provider.md)並將其指派給您的目錄或[指派給使用者的授權](multi-factor-authentication-get-started-assign-licenses.md)

> [AZURE.NOTE]  授權可供使用者 Azure MFA、 Azure AD 進階版或企業版行動性套件 (EMS)。  MFA 會包含在 Azure AD 進階版及 EMS。 如果您有足夠的授權，您不需要建立驗證提供者。


## <a name="turn-on-two-step-verification-for-users"></a>開啟雙步驟驗證的使用者
若要開始需要兩個開始驗證的使用者，請從 [停用，以啟用變更使用者的狀態。  如需有關使用者狀態的詳細資訊，請參閱[Azure 多重因素驗證的使用者狀態](multi-factor-authentication-get-started-user-states.md)

使用下列程序來啟用 MFA 使用者。

### <a name="to-turn-on-multi-factor-authentication"></a>若要開啟多重因素驗證

1.  以系統管理員身分登入[Azure 傳統入口網站](https://manage.windowsazure.com)。
2.  在左側，按一下 [ **Active Directory**。
3.  在 [目錄] 中，選取您想要啟用的使用者的目錄。
![按一下 [目錄]](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  在頂端，按一下 [**使用者**]。
5.  在頁面底部，按一下 [**管理多重因素驗證**]。 隨後便會開啟新的瀏覽器索引標籤。
![按一下 [目錄]](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  尋找您想要啟用的雙步驟驗證的使用者。 您可能需要變更 [頂端] 檢視。 確保您的狀態是 [**停用。**
![啟用使用者](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7.  置於**核取**其名稱旁的方塊。
7.  在右側，按一下 [**啟用**]。
![啟用使用者](./media/multi-factor-authentication-get-started-cloud/user1.png)
8.  按一下 [**啟用多重因素驗證**。
![啟用使用者](./media/multi-factor-authentication-get-started-cloud/enable2.png)
9.  請注意，若要**啟用**使用者的狀態已從**停用**中變更。
![讓使用者](./media/multi-factor-authentication-get-started-cloud/user.png)

您已啟用您的使用者後，您應該透過電子郵件通知。 下一次嘗試登入，他們會要求註冊雙步驟驗證其帳戶。 一旦您開始使用雙步驟驗證，他們也必須設定應用程式密碼以避免被鎖定非瀏覽器應用程式。


## <a name="use-powershell-to-automate-turning-on-two-step-verification"></a>使用 PowerShell 來自動化開啟雙步驟驗證

若要變更使用[PowerShell 的 Azure AD](../powershell-install-configure.md)[狀態](multi-factor-authentication-whats-next.md)，您可以使用下列項目。  您可以變更`$st.State`相當於下列狀態其中一項︰

- 啟用
- 強制執行
- 停用  

> [AZURE.IMPORTANT]  我們建議不要針對強制狀態，將使用者移直接從停用狀態。 非瀏覽器型的應用程式會停止運作，因為使用者不經過 MFA 註冊，並取得[應用程式密碼](multi-factor-authentication-whats-next.md#app-passwords)。 如果您有非瀏覽器型的應用程式，需要應用程式密碼時，建議您為 [已啟用移從停用狀態。 這個選項可讓使用者註冊並取得其應用程式的密碼。 之後，您可以將其移動至強制。

使用 PowerShell 會啟用使用者的大量的選項。 目前 Azure 入口網站中沒有任何的大量啟用功能，必須先選取個別的每個使用者。 如果您有多個使用者，這可能是很工作。 藉由建立 PowerShell 指令碼使用下列步驟，您可以透過使用者清單循環播放，並且啟用這些。

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "\*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

以下是範例︰

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "\*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }


如需詳細資訊，請參閱[Azure 多重因素驗證的使用者狀態](multi-factor-authentication-get-started-user-states.md)

## <a name="next-steps"></a>後續步驟
現在您已設定 Azure 多重因素驗證，在雲端，您可以設定並設定您的部署。 如需詳細資訊，請參閱[設定 Azure 多重因素驗證](multi-factor-authentication-whats-next.md)。
