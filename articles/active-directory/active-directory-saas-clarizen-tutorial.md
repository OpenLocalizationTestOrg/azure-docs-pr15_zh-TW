<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合 Clarizen |Microsoft Azure" 
    description="瞭解如何使用 Clarizen 與 Azure Active Directory 啟用單一登入、 自動化佈建和更多 ！" 
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
    ms.date="09/29/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-clarizen"></a>教學課程︰ 使用 Clarizen 的 Azure Active Directory 整合

本教學課程中的目標是以顯示 Azure 和 Clarizen 的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   Clarizen 單一登入啟用訂閱

完成後本教學課程中，您指派給 Clarizen Azure AD 使用者將能夠單一登入 Clarizen 公司網站 （服務提供者發起的租用戶登入），或使用[[存取面板簡介](active-directory-saas-access-panel-introduction.md)應用程式。

本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用 Clarizen 應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-clarizen-tutorial/IC784679.png "案例")
##<a name="enabling-the-application-integration-for-clarizen"></a>啟用 Clarizen 應用程式整合

本節的目標是大綱如何啟用 Clarizen 的整合應用程式。

###<a name="to-enable-the-application-integration-for-clarizen-perform-the-following-steps"></a>若要啟用的 Clarizen 整合應用程式，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-clarizen-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-clarizen-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-clarizen-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-clarizen-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**Clarizen**。

    ![應用程式組件庫](./media/active-directory-saas-clarizen-tutorial/IC784680.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**Clarizen**，，然後按一下要新增應用程式**完成**。

    ![Clarizen](./media/active-directory-saas-clarizen-tutorial/IC784681.png "Clarizen")
##<a name="configuring-single-sign-on"></a>設定單一登入

本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD Clarizen。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站， **Clarizen**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-clarizen-tutorial/IC784682.png "設定單一登入")

2.  在**您要如何登入 Clarizen 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-clarizen-tutorial/IC784683.png "設定單一登入")

3.  在**設定單一登入 Clarizen 在**頁面上，若要下載您的憑證，按一下 [**下載憑證**] 並儲存在您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-clarizen-tutorial/IC784684.png "設定單一登入")

4.  在不同的網頁瀏覽器視窗中，請登入以系統管理員身分**Clarizen**公司網站 (例如︰ *https://app2.clarizen.com/Clarizen/Pages/Service/Login.aspx*)。

5.  按一下 [您的使用者名稱，然後按一下 [**設定**]。

    ![設定](./media/active-directory-saas-clarizen-tutorial/IC784685.png "設定")

6.  按一下 [**通用設定**] 索引標籤，然後按一下 [**同盟驗證**] 旁的 [**編輯**。

    ![通用設定](./media/active-directory-saas-clarizen-tutorial/IC786906.png "通用設定")

7.  在 [**同盟驗證**] 對話方塊中，執行下列步驟︰

    ![同盟驗證](./media/active-directory-saas-clarizen-tutorial/IC785892.png "同盟驗證")

    1.  按一下 [**上傳**上傳您下載的憑證。
    2.  Azure 傳統入口網站，在 [**設定單一登入在 Clarizen**對話方塊] 頁面中的**單一登入服務 URL**值，複製，然後再貼到 [**登入 URL** ] 文字方塊。
    3.  Azure 傳統入口網站，在 [**設定單一教具借出在 Clarizen**對話方塊] 頁面中的**單一 Sign-Out 服務 URL**值，複製，然後再貼到 [ **Sign-out URL** ] 文字方塊。
    4.  選取 [**使用文章**。
    5.  按一下 [**儲存**]。

8.  在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-clarizen-tutorial/IC784688.png "設定單一登入")
##<a name="configuring-user-provisioning"></a>設定使用者佈建

若要啟用 Azure AD 使用者登入 Clarizen，他們必須佈建到 Clarizen。  
若是 Clarizen，佈建是手動的工作。

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>佈建使用者帳戶，請執行下列步驟︰

1.  以系統管理員身分登入您**Clarizen**公司的網站。

2.  按一下 [**人員**]。

    ![人員](./media/active-directory-saas-clarizen-tutorial/IC784689.png "人員")

3.  按一下 [**邀請使用者**。

    ![邀請使用者](./media/active-directory-saas-clarizen-tutorial/IC784690.png "邀請使用者")

4.  在 [邀請人員] 對話方塊頁面，請執行下列步驟︰

    ![邀請人員](./media/active-directory-saas-clarizen-tutorial/IC784691.png "邀請人員")

    1.  在 [**電子郵件**] 文字方塊中，輸入您想要佈建有效 Azure Active Directory 帳戶的電子郵件地址。
    2.  按一下 [**邀請**]。

    >[AZURE.NOTE] Azure Active Directory 帳戶擁有者會收到電子郵件，並確認其帳戶，再會變成作用中連結。

##<a name="assigning-users"></a>將使用者指派

若要測試您的設定，您需要授與 Azure AD 使用者您想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-clarizen-perform-the-following-steps"></a>若要指定 Clarizen 使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **Clarizen**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-clarizen-tutorial/IC784692.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-clarizen-tutorial/IC767830.png "[是]")

如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。
