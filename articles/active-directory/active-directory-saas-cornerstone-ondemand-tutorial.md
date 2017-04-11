<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合基石視需要 |Microsoft Azure" 
    description="瞭解如何使用基石視需要與 Azure Active Directory 啟用單一登入，自動化佈建和更多 ！" 
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

#<a name="tutorial-azure-active-directory-integration-with-cornerstone-ondemand"></a>教學課程︰ 使用基石視需要的 Azure Active Directory 整合

本教學課程中的目標是以顯示 Azure 和基石視需要的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   基石視需要租用戶

完成本教學課程之後，您指派給基石視需要 Azure AD 使用者將無法單一登入基石視需要公司網站 （服務提供者發起的租用戶登入），或使用[[存取面板簡介](active-directory-saas-access-panel-introduction.md)應用程式。

本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用基石視需要的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781593.png "案例")
##<a name="enabling-the-application-integration-for-cornerstone-ondemand"></a>啟用基石視需要的應用程式整合

本節的目標是大綱如何啟用基石視需要的應用程式整合。

###<a name="to-enable-the-application-integration-for-cornerstone-ondemand-perform-the-following-steps"></a>若要讓應用程式適用於整合基石視需要，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**基石視需要**。

    ![應用程式組件庫](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781594.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**基石視需要**，，然後按一下要新增應用程式**完成**。

    ![基石視需要](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781595.png "基石視需要")
##<a name="configuring-single-sign-on"></a>設定單一登入

本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD 基石視需要。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站，**基石視需要**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![啟用單一登入](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781596.png "啟用單一登入")

2.  在**您要如何登入基石視需要的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![Microsoft Azure AD 單一登入](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781597.png "Microsoft Azure AD 單一登入")

3.  **設定應用程式 URL**在頁面上，在 [**基石視需要登入 URL** ] 文字方塊中，輸入您使用下列模式 」*http://company.csod.com*」 的 URL，然後再按 [**下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781598.png "設定應用程式 URL")

4.  在**設定單一登入基石視需要在**頁面上，若要下載您的憑證，按一下 [**下載憑證**] 並儲存在本機電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781599.png "設定單一登入")

5.  傳送基石視需要下列項目支援小組︰

    1.  下載的憑證
    2.  **遠端登入 URL**值
    3.  [**遠端登出 URL**的值。

    >[AZURE.NOTE] 單一登入您必須設定基石視需要支援小組。
設定完成後，會從支援小組收到通知。

6.  選取單一登入設定確認，然後按一下 [**完成**，關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781600.png "設定單一登入")
##<a name="configuring-user-provisioning"></a>設定使用者佈建

若要啟用 Azure AD 使用者登入基石視需要，請他們必須提供基石視需要將。  
若是基石視需要，佈建是手動的工作。

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，執行下列步驟︰

1.  傳送的資訊 (例如︰ 名稱 」、 「 Emial) 有關您想要佈建基石視需要以 Azure AD 使用者支援小組。

>[AZURE.NOTE] 您可以使用任何其他基石視需要使用者帳戶建立工具或 Api 提供基石視需要佈建 AAD 使用者帳戶。

##<a name="assigning-users"></a>將使用者指派

若要測試您的設定，您需要授與 Azure AD 使用者您想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-cornerstone-ondemand-perform-the-following-steps"></a>若要指定基石視需要使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **基石視需要**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC775564.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![為使用者指派](./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781601.png "為使用者指派")

如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。
