<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合 Huddle |Microsoft Azure" 
    description="瞭解如何使用 Huddle 與 Azure Active Directory 啟用單一登入，自動化佈建和更多 ！" 
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

#<a name="tutorial-azure-active-directory-integration-with-huddle"></a>教學課程︰ 使用 Huddle 的 Azure Active Directory 整合
  
本教學課程中的目標是以顯示 Azure 和 Huddle 的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   Huddle 單一登入啟用訂閱
  
完成後本教學課程中，您指派給 Huddle Azure AD 使用者將能夠單一登入 Huddle 公司網站 （服務提供者發起的租用戶登入），或使用[[存取面板簡介](active-directory-saas-access-panel-introduction.md)應用程式。
  
本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用 Huddle 應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![設定單一登入](./media/active-directory-saas-huddle-tutorial/IC787830.png "設定單一登入")
##<a name="enabling-the-application-integration-for-huddle"></a>啟用 Huddle 應用程式整合
  
本節的目標是大綱如何啟用 Huddle 的整合應用程式。

###<a name="to-enable-the-application-integration-for-huddle-perform-the-following-steps"></a>若要啟用的 Huddle 整合應用程式，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-huddle-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-huddle-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-huddle-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-huddle-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**Huddle**。

    ![應用程式組件庫](./media/active-directory-saas-huddle-tutorial/IC787831.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**Huddle**，，然後按一下要新增應用程式**完成**。

    ![huddle](./media/active-directory-saas-huddle-tutorial/IC787832.png "huddle")
##<a name="configuring-single-sign-on"></a>設定單一登入
  
本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD Huddle。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 [Azure 傳統入口網站， **Huddle**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-huddle-tutorial/IC787833.png "設定單一登入")

2.  **如何將您想要 Huddle 登入的使用者**] 頁面上選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-huddle-tutorial/IC787834.png "設定單一登入")

3.  **設定應用程式 URL**在頁面上，在 [ **Huddle 登入 URL** ] 文字方塊中，輸入您使用下列模式 」*http://company.huddle.com*」 的 Huddle 租用戶的 URL，然後再按 [**下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-huddle-tutorial/IC787835.png "設定應用程式 URL")

4.  在**設定單一登入 Huddle 在**頁面上，執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-huddle-tutorial/IC787836.png "設定單一登入")

    1.  按一下 [**下載憑證**]，並儲存在您的電腦上的憑證檔案。
    2.  複製**發行者 URL**值、 **SAML SSO URL**值和下載的憑證，然後再傳送給 Huddle 支援小組。

    >[AZURE.NOTE] 單一登入必須啟用 Huddle 支援小組。
設定完成後，您會收到通知。

5.  在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-huddle-tutorial/IC787837.png "設定單一登入")
##<a name="configuring-user-provisioning"></a>設定使用者佈建
  
若要啟用 Azure AD 使用者登入 Huddle，他們必須佈建到 Huddle。  
若是 Huddle，佈建是手動的工作。

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，執行下列步驟︰

1.  以系統管理員身分登入**Huddle**公司網站。

2.  按一下**工作區**。

3.  按一下 [**人員\>邀請人員**。

    ![人員](./media/active-directory-saas-huddle-tutorial/IC787838.png "人員")

4.  在 [**建立新的邀請**] 區段中，執行下列步驟︰

    ![新的邀請](./media/active-directory-saas-huddle-tutorial/IC787839.png "新的邀請")

    1.  在 [**選擇要邀請的人員加入小組**] 清單中選取 [**小組**]。
    2.  輸入您想要在相關的文字方塊佈建有效 AAD 帳戶的**電子郵件地址**。
    3.  按一下 [**邀請**]。

    >[AZURE.NOTE] Azure AD 帳戶擁有者會收到電子郵件包含確認帳戶，再將其成為使用中的連結。

>[AZURE.NOTE] 您可以使用任何其他 Huddle 使用者帳戶建立工具或使用者帳戶進行佈建 AAD Huddle Api 提供。

##<a name="assigning-users"></a>將使用者指派
  
若要測試您的設定，您需要授與 Azure AD 使用者您想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-huddle-perform-the-following-steps"></a>若要指定 Huddle 使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **Huddle**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-huddle-tutorial/IC787840.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-huddle-tutorial/IC767830.png "[是]")
  
如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。