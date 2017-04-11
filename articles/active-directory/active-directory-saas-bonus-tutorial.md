<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合 Bonus.ly |Microsoft Azure" 
    description="瞭解如何使用 Bonus.ly 與 Azure Active Directory 啟用單一登入，自動化佈建和更多 ！" 
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

#<a name="tutorial-azure-active-directory-integration-with-bonusly"></a>教學課程︰ 使用 Bonus.ly 的 Azure Active Directory 整合

本教學課程中的目標是以顯示 Azure 和 Bonus.ly 的整合。 本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   測試租用戶中 Bonus.ly

本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用 Bonus.ly 應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-bonus-tutorial/IC773679.png "案例")
##<a name="enabling-the-application-integration-for-bonusly"></a>啟用 Bonus.ly 應用程式整合

本節的目標是大綱如何啟用 Bonus.ly 的整合應用程式。

###<a name="to-enable-the-application-integration-for-bonusly-perform-the-following-steps"></a>若要啟用的 Bonus.ly 整合應用程式，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![啟用單一登入](./media/active-directory-saas-bonus-tutorial/IC773680.png "啟用單一登入")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-bonus-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-bonus-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-bonus-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**Bonus.ly**。

    ![應用程式組件庫](./media/active-directory-saas-bonus-tutorial/IC773681.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**Bonus.ly**，，然後按一下要新增應用程式**完成**。

    ![bonusly](./media/active-directory-saas-bonus-tutorial/IC773682.png "bonusly")
##<a name="configuring-single-sign-on"></a>設定單一登入

本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD Bonus.ly。  
設定單一登入的 Bonus.ly 需要您從憑證擷取指紋值。  
如果您不熟悉這個程序，請參閱[如何擷取的憑證指紋值](http://youtu.be/YKQF266SAxI)。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站， **Bonus.ly**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-bonus-tutorial/IC749323.png "設定單一登入")

2.  在**您要如何登入 Bonus.ly 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-bonus-tutorial/IC773683.png "設定單一登入")

3.  **設定應用程式 URL**在頁面上，在 [ **Bonus.ly 租用戶 URL** ] 文字方塊中，輸入您使用下列模式的 URL 「*https://\<租用戶名稱\>。Bonus.ly*」，然後按一下 [**下一步**︰ 

    ![設定應用程式 URL](./media/active-directory-saas-bonus-tutorial/IC773684.png "設定應用程式 URL")

4.  在**設定單一登入 Bonus.ly 在**頁面上，按一下**下載憑證**]，然後憑證檔案儲存至本機為**c:\\Bonusly.cer**。

    ![設定單一登入](./media/active-directory-saas-bonus-tutorial/IC773685.png "設定單一登入")

5.  在不同的瀏覽器視窗中，登入您的**Bonus.ly**租用戶。

6.  在 [工具列] 按一下 [**設定**]，然後選取**整合及應用程式**。

    ![bonusly](./media/active-directory-saas-bonus-tutorial/IC773686.png "bonusly")

7.  在 [**單一登入**，選取**SAML**。

8.  在**SAML**對話方塊頁面上，執行下列步驟︰

    ![bonusly](./media/active-directory-saas-bonus-tutorial/IC773687.png "bonusly")

    1.  在 Azure 的傳統入口網站中**設定單一登入，Bonus.ly** ] 對話方塊在頁面上，複製 [**遠端登入 URL**的值，，然後再貼到 [ **IdP SSO 目標 URL** ] 文字方塊。
    2.  在 Azure 的傳統入口網站中**設定單一登入，Bonus.ly** ] 對話方塊在頁面上，複製 [**發行者識別碼**值]，並再貼到**IdP 發行者**文字方塊。
    3.  在 Azure 的傳統入口網站中**設定單一登入，Bonus.ly** ] 對話方塊在頁面上，複製 [**遠端登入 URL**的值，，然後再貼到 [ **IdP 登入 URL** ] 文字方塊。
    4.  **指紋**值複製匯出的憑證，然後再貼到**憑證指紋**文字方塊。

        >[AZURE.TIP] 如需詳細資訊，請參閱[如何擷取的憑證指紋值](http://youtu.be/YKQF266SAxI)

9.  按一下 [**儲存**]。

10. 在 Microsoft Azure 傳統入口網站中，選取設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-bonus-tutorial/IC773689.png "設定單一登入")
##<a name="configuring-user-provisioning"></a>設定使用者佈建

若要啟用 Azure AD 使用者登入 Bonus.ly，他們必須佈建到 Bonus.ly。  
若是 Bonus.ly，佈建是手動的工作。

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，執行下列步驟︰

1.  在網頁瀏覽器視窗中，請登入您的 Bonus.ly 租用戶。

2.  按一下 [**設定]**

    ![設定](./media/active-directory-saas-bonus-tutorial/IC781041.png "設定")

3.  按一下 [**使用者和加分**] 索引標籤。

    ![使用者和加分](./media/active-directory-saas-bonus-tutorial/IC781042.png "使用者與優點")

4.  按一下 [**管理使用者**]。

    ![管理使用者](./media/active-directory-saas-bonus-tutorial/IC781043.png "管理使用者")

5.  按一下 [**新增使用者**]。

    ![新增使用者](./media/active-directory-saas-bonus-tutorial/IC781044.png "新增使用者")

6.  在 [**新增使用者**] 對話方塊中，執行下列步驟︰

    ![新增使用者](./media/active-directory-saas-bonus-tutorial/IC781045.png "新增使用者")

    1.  輸入 」**電子郵件**，**名字**、**姓氏**」 有效 AAD 您想要的帳戶佈建到相關的文字方塊。
    2.  按一下 [**儲存**]。

    >[AZURE.NOTE] AAD 帳戶擁有者會收到包含確認帳戶，再會變成作用中連結的電子郵件。

>[AZURE.NOTE] 您可以使用任何其他 Bonus.ly 使用者帳戶建立工具或 Api 提供 Bonus.ly 佈建 AAD 使用者帳戶。

##<a name="assigning-users"></a>將使用者指派

若要測試您的設定，您需要授與 Azure AD 使用者您想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-bonusly-perform-the-following-steps"></a>若要指定 Bonus.ly 使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  Bonus.ly 應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-bonus-tutorial/IC773690.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-bonus-tutorial/IC767830.png "[是]")

如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。
