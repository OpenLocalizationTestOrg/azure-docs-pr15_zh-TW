<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合 ScreenSteps |Microsoft Azure" 
    description="瞭解如何使用 ScreenSteps 與 Azure Active Directory 啟用單一登入、 自動化佈建和更多 ！" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-screensteps"></a>教學課程︰ 使用 ScreenSteps 的 Azure Active Directory 整合
  
本教學課程中的目標是以顯示 Azure 和 ScreenSteps 的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   ScreenSteps 租用戶
  
完成後本教學課程中，您指派給 ScreenSteps Azure AD 使用者將能夠單一登入 ScreenSteps 公司網站 （服務提供者發起的租用戶登入），或使用[[存取面板簡介](active-directory-saas-access-panel-introduction.md)應用程式。
  
本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用 ScreenSteps 應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-screensteps-tutorial/IC778516.png "案例")
##<a name="enabling-the-application-integration-for-screensteps"></a>啟用 ScreenSteps 應用程式整合
  
本節的目標是大綱如何啟用 ScreenSteps 的整合應用程式。

###<a name="to-enable-the-application-integration-for-screensteps-perform-the-following-steps"></a>若要啟用的 ScreenSteps 整合應用程式，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-screensteps-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-screensteps-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-screensteps-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-screensteps-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**ScreenSteps**。

    ![應用程式組件庫](./media/active-directory-saas-screensteps-tutorial/IC778517.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**ScreenSteps**，，然後按一下要新增應用程式**完成**。

    ![ScreenSteps](./media/active-directory-saas-screensteps-tutorial/IC778518.png "ScreenSteps")
##<a name="configuring-single-sign-on"></a>設定單一登入
  
本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD ScreenSteps。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站， **ScreenSteps**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-screensteps-tutorial/IC778519.png "設定單一登入")

2.  在**您要如何登入 ScreenSteps 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-screensteps-tutorial/IC778520.png "設定單一登入")

3.  **設定應用程式 URL**在頁面上，在 [ **ScreenSteps 登入 URL** ] 文字方塊中，輸入您使用下列模式的 URL 「*https://\<租用戶名稱\>。ScreenSteps.com*」，然後按一下 [**下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-screensteps-tutorial/IC778521.png "設定應用程式 URL")

4.  在**設定單一登入 ScreenSteps 在**頁面上，若要下載您的憑證，按一下 [**下載憑證**] 並儲存在您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-screensteps-tutorial/IC778522.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您 ScreenSteps 公司的網站。

6.  按一下 [**管理帳戶**]。

    ![帳戶管理](./media/active-directory-saas-screensteps-tutorial/IC778523.png "帳戶管理")

7.  按一下 [**遠端驗證**]。

    ![遠端驗證](./media/active-directory-saas-screensteps-tutorial/IC778524.png "遠端驗證")

8.  按一下 [**建立驗證結束點**]。

    ![遠端驗證](./media/active-directory-saas-screensteps-tutorial/IC778525.png "遠端驗證")

9.  在 [**建立端點驗證**] 區段中，執行下列步驟︰

    ![建立驗證端點](./media/active-directory-saas-screensteps-tutorial/IC778526.png "建立驗證端點")

    1.  在 [**標題**] 文字方塊中，輸入標題。
    2.  從 [**模式**] 清單中，選取 [ **SAML**]。
    3.  按一下 [**建立**]。

10. 在 [**遠端驗證結束點**] 區段中，執行下列步驟︰

    ![遠端驗證端點](./media/active-directory-saas-screensteps-tutorial/IC778527.png "遠端驗證端點")

    1.  Azure 的傳統入口網站，在**設定單一登入 ScreenSteps 在**頁面上，在**遠端登入 URL**值，複製，然後再貼到 [**遠端登入 URL** ] 文字方塊。
    2.  Azure 傳統入口網站中，在**設定單一登入 ScreenSteps 在**頁面上，複製 [**遠端登出 URL**的值，，然後再貼到 [**登出 URL** ] 文字方塊。
    3.  按一下 [**選擇檔案**，然後上傳已下載的憑證。
    4.  按一下 [**更新**]。

11. 在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-screensteps-tutorial/IC778542.png "設定單一登入")
##<a name="configuring-user-provisioning"></a>設定使用者佈建
  
若要啟用 Azure AD 使用者登入**ScreenSteps**，他們必須佈建到**ScreenSteps**。  
若是**ScreenSteps**，佈建是手動的工作。

###<a name="to-provision-a-user-account-to-screensteps-perform-the-following-steps"></a>佈建 ScreenSteps 使用者帳戶，請執行下列步驟︰

1.  登入您的**ScreenSteps**租用戶。

2.  按一下 [**管理帳戶**]。

    ![帳戶管理](./media/active-directory-saas-screensteps-tutorial/IC778523.png "帳戶管理")

3.  按一下 [**使用者**]。

    ![使用者](./media/active-directory-saas-screensteps-tutorial/IC778544.png "使用者")

4.  按一下 [**建立使用者**。

    ![所有使用者](./media/active-directory-saas-screensteps-tutorial/IC778545.png "所有使用者")

5.  從**使用者角色**] 清單中，選取 [使用者角色]。

6.  在 [使用者角色] 區段中，輸入 「**名字**、**姓氏**、**電子郵件**、**登入**、**密碼**和**密碼的確認**」 有效 AAD 您想要的帳戶佈建到相關的文字方塊。

    ![新的使用者](./media/active-directory-saas-screensteps-tutorial/IC778546.png "新的使用者")

7.  在 [群組] 區段中，選取 [**驗證] 群組 (SAML)**，，然後按一下 [**建立使用者**。

    ![群組](./media/active-directory-saas-screensteps-tutorial/IC778547.png "群組")

>[AZURE.NOTE]您可以使用任何其他 ScreenSteps 使用者帳戶建立工具或 Api 提供 ScreenSteps 佈建 AAD 使用者帳戶。

##<a name="assigning-users"></a>將使用者指派
  
若要測試您的設定，您需要授與 Azure AD 使用者想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-screensteps-perform-the-following-steps"></a>若要指定 ScreenSteps 使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **ScreenSteps**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-screensteps-tutorial/IC773094.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![為使用者指派](./media/active-directory-saas-screensteps-tutorial/IC778548.png "為使用者指派")
  
如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。