<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合 RightAnswers |Microsoft Azure" 
    description="瞭解如何使用 RightAnswers 與 Azure Active Directory 啟用單一登入，自動化佈建和更多 ！" 
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

#<a name="tutorial-azure-active-directory-integration-with-rightanswers"></a>教學課程︰ 使用 RightAnswers 的 Azure Active Directory 整合
  
本教學課程中的目標是以顯示 Azure 和 RightAnswers 的整合。 本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   RightAnswers 單一登入啟用訂閱
  
完成後本教學課程中，您指派給 RightAnswers Azure AD 使用者將能夠[存取面板簡介](active-directory-saas-access-panel-introduction.md)在應用程式中的單一登。
  
本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用 RightAnswers 應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-rightanswers-tutorial/IC802925.png "案例")
##<a name="enabling-the-application-integration-for-rightanswers"></a>啟用 RightAnswers 應用程式整合
  
本節的目標是大綱如何啟用 RightAnswers 的整合應用程式。

###<a name="to-enable-the-application-integration-for-rightanswers-perform-the-following-steps"></a>若要啟用的 RightAnswers 整合應用程式，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-rightanswers-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-rightanswers-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-rightanswers-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-rightanswers-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**RightAnswers**。

    ![應用程式組件庫](./media/active-directory-saas-rightanswers-tutorial/IC802926.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**RightAnswers**，，然後按一下 [新增應用程式**完成**。
##<a name="configuring-single-sign-on"></a>設定單一登入
  
本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD RightAnswers。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站， **RightAnswers**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-rightanswers-tutorial/IC802927.png "設定單一登入")

2.  在**您要如何登入 RightAnswers 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-rightanswers-tutorial/IC802928.png "設定單一登入")

3.  **設定應用程式設定**] 頁面上的 [在 [**登入 URL** ] 文字方塊中，輸入您登入您的 RightAnswers 應用程式的使用者所使用的 URL (例如︰ *https://fortify.rightanswers.com/portal/ss/*)，然後按一下 [**下一步**。

    ![設定應用程式設定](./media/active-directory-saas-rightanswers-tutorial/IC802929.png "設定應用程式設定")

4.  在**設定單一登入 RightAnswers 在**頁面上，若要下載您的中繼資料，按一下**下載的中繼資料**]，然後儲存在本機電腦上的中繼資料檔案。

    ![設定單一登入](./media/active-directory-saas-rightanswers-tutorial/IC802930.png "設定單一登入")

5.  下載的中繼資料檔案傳送至 RightAnswers 支援小組。

    >[AZURE.NOTE] 若要執行的實際 SSO 設定有 RightAnswers 支援小組。
當 SSO 已啟用您的訂閱，您會收到通知。

6.  在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-rightanswers-tutorial/IC802931.png "設定單一登入")
##<a name="configuring-user-provisioning"></a>設定使用者佈建
  
若要啟用 Azure AD 使用者登入 RightAnswers，他們必須佈建到 RightAnswers。  
若是 RightAnswers，佈建是 「 自動化的工作。  
沒有您的動作項目。
  
使用者會自動建立必要在第一個單一登入嘗試。

>[AZURE.NOTE]您可以使用任何其他 RightAnswers 使用者帳戶建立工具或 Api 提供 RightAnswers 佈建 AAD 使用者帳戶。

##<a name="assigning-users"></a>將使用者指派
  
若要測試您的設定，您需要授與 Azure AD 使用者您想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-rightanswers-perform-the-following-steps"></a>若要指定 RightAnswers 使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **RightAnswers**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-rightanswers-tutorial/IC802932.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-rightanswers-tutorial/IC767830.png "[是]")
  
如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。