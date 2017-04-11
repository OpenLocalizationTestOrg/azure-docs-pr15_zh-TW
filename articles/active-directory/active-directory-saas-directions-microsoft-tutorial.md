<properties 
    pageTitle="教學課程︰ Microsoft 的指示的 Azure Active Directory 整合 |Microsoft Azure" 
    description="瞭解如何使用 Microsoft Azure Active Directory 與上的指示來啟用單一登入、 自動化佈建和更多 ！" 
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

#<a name="tutorial-azure-active-directory-integration-with-directions-on-microsoft"></a>教學課程︰ Azure Active Directory 整合 Microsoft 的指示

本教學課程中的目標是顯示 Microsoft Azure Active Directory 和方向的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   Microsoft 訂閱的說明

如果您沒有 Microsoft 訂閱尚未安裝同盟的指示，電子郵件的要求 「*service@DirectionsOnMicrosoft.com*」。

完成後本教學課程中，您指派的指示，在 Microsoft Azure Active Directory 使用者將能夠單一登入應用程式使用單一登入。

本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用應用程式整合，在 Microsoft 的方向
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-directions-microsoft-tutorial/IC786877.png "案例")
##<a name="enabling-the-application-integration-for-directions-on-microsoft"></a>啟用應用程式整合，在 Microsoft 的方向

本節的目標是大綱如何啟用應用程式整合，在 Microsoft 的方向。

###<a name="to-enable-the-application-integration-for-directions-on-microsoft-perform-the-following-steps"></a>若要讓應用程式整合，在 Microsoft 的方向，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-directions-microsoft-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-directions-microsoft-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-directions-microsoft-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-directions-microsoft-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**Microsoft 上的指示**。

    ![應用程式組件庫](./media/active-directory-saas-directions-microsoft-tutorial/IC786878.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取 [**在 Microsoft 的指示**，，然後按一下**完成**新增應用程式。

    ![案例](./media/active-directory-saas-directions-microsoft-tutorial/IC793922.png "案例")
##<a name="configuring-single-sign-on"></a>設定單一登入

本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD Microsoft 上的指示。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站中**有關 Microsoft**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**對話方塊]。

    ![啟用單一登入](./media/active-directory-saas-directions-microsoft-tutorial/IC786879.png "啟用單一登入")

2.  在**您要如何登入 Microsoft 方向的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![Microsoft Azure AD Singel 登入](./media/active-directory-saas-directions-microsoft-tutorial/IC786880.png "Microsoft Azure AD Singel 登入")

3.  **設定應用程式 URL**在頁面上，在 [登入 URL] 文字方塊中，輸入**https://www.directionsonmicrosoft.com/user/login**，然後再按 [**下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-directions-microsoft-tutorial/IC786881.png "設定應用程式 URL")

4.  **設定單一登入 Microsoft 上的指示，**在頁面上，按一下**下載的中繼資料**]，然後儲存在本機電腦上的中繼資料檔案。

    ![設定單一登入](./media/active-directory-saas-directions-microsoft-tutorial/IC786882.png "設定單一登入")

5.  中繼資料檔案傳送至 Microsoft 支援小組的指示 (*service@DirectionsOnMicrosoft.com*)。 若要啟用 Microsoft 支援小組的指示，找出您的同盟的網站成員資格，請在 [您的電子郵件中包含您公司的資訊。

    >[AZURE.NOTE] 單一登入 Microsoft 有關必須啟用 Microsoft 支援小組的指示。
單一登入已啟用，您會收到通知。

6.  在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-directions-microsoft-tutorial/IC786883.png "設定單一登入")
##<a name="configuring-user-provisioning"></a>設定使用者佈建

沒有您的設定指示，在 Microsoft 佈建的使用者動作項目。  
當指派的使用者嘗試登入 Microsoft 使用存取面板上的指示時，檢查有 Microsoft 指示使用者是否存在。 如果不有任何使用者帳戶還，它就會自動建立 Microsoft 上的指示。
##<a name="assigning-users"></a>將使用者指派

若要測試您的設定，您需要授與 Azure AD 使用者想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-directions-on-microsoft-perform-the-following-steps"></a>若要指定使用者在 Microsoft 的指示，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **指示 Microsoft**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-directions-microsoft-tutorial/IC786884.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-directions-microsoft-tutorial/IC767830.png "[是]")
