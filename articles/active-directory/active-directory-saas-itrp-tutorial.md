<properties
    pageTitle="教學課程︰ Azure Active Directory 整合 ITRP |Microsoft Azure" 
    description="瞭解如何使用 ITRP 與 Azure Active Directory 啟用單一登入、 自動化佈建和更多 ！" 
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
    ms.date="09/07/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-itrp"></a>教學課程︰ 使用 ITRP 的 Azure Active Directory 整合
  
本教學課程中的目標是以顯示 Azure 和 ITRP 的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   ITRP 租用戶
  
完成後本教學課程中，您指派給 ITRP Azure AD 使用者將能夠單一登入 ITRP 公司網站 （服務提供者發起的租用戶登入），或使用[[存取面板簡介](active-directory-saas-access-panel-introduction.md)應用程式。
  
本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用 ITRP 應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-itrp-tutorial/IC775551.png "案例")
##<a name="enabling-the-application-integration-for-itrp"></a>啟用 ITRP 應用程式整合
  
本節的目標是大綱如何啟用 ITRP 的整合應用程式。

###<a name="to-enable-the-application-integration-for-itrp-perform-the-following-steps"></a>若要啟用的 ITRP 整合應用程式，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-itrp-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-itrp-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-itrp-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-itrp-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**ITRP**。

    ![應用程式組件庫](./media/active-directory-saas-itrp-tutorial/IC775565.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**ITRP**，，然後按一下要新增應用程式**完成**。

    ![ITRP](./media/active-directory-saas-itrp-tutorial/IC775566.png "ITRP")
##<a name="configuring-single-sign-on"></a>設定單一登入
  
本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD ITRP。  
設定單一登入的 ITRP 需要您從憑證擷取指紋值。  
如果您不熟悉這個程序，請參閱[如何擷取的憑證指紋值](http://youtu.be/YKQF266SAxI)。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站， **ITRP**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-itrp-tutorial/IC771709.png "設定單一登入")

2.  在**您要如何登入 ITRP 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-itrp-tutorial/IC775567.png "設定單一登入")

3.  **設定應用程式 URL**在頁面上，在 [ **ITRP 登入 URL** ] 文字方塊中，輸入您使用下列模式的 URL 「*https://\<租用戶名稱\>。ITRP.com*」，然後按一下 [**下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-itrp-tutorial/IC775568.png "設定應用程式 URL")

4.  在**設定單一登入 ITRP 在**頁面上，若要下載您的憑證，按一下**下載憑證**]，然後憑證檔案儲存至本機為**c:\\ITRP.cer**。

    ![設定單一登入](./media/active-directory-saas-itrp-tutorial/IC775569.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您 ITRP 公司的網站。

6.  在頂端上工具列中，按一下 [**設定**]。

    ![ITRP](./media/active-directory-saas-itrp-tutorial/IC775570.png "ITRP")

7.  在左側的功能窗格中，選取**單一登入**。

    ![單一登入](./media/active-directory-saas-itrp-tutorial/IC775571.png "單一登入")

8.  在單一登入組態] 區段中，執行下列步驟︰

    ![單一登入](./media/active-directory-saas-itrp-tutorial/IC775572.png "單一登入")

    ![單一登入](./media/active-directory-saas-itrp-tutorial/IC775573.png "單一登入")

    1.  按一下 [**啟用**]。
    2.  在 Azure 的傳統入口網站中**設定單一登入，ITRP** ] 對話方塊在頁面上，複製 [**遠端登出 URL**的值，，然後再貼到 [**遠端登出 URL** ] 文字方塊。
    3.  在 Azure 的傳統入口網站中**設定單一登入，ITRP** ] 對話方塊在頁面上，複製 [ **SAML SSO URL**的值，，然後再貼到 [ **SAML SSO URL** ] 文字方塊。
    4.  **指紋**值複製匯出的憑證，然後再貼到**憑證指紋**文字方塊。
        
        >[AZURE.TIP]如需詳細資訊，請參閱[如何擷取的憑證指紋值](http://youtu.be/YKQF266SAxI)

    5.  按一下 [**儲存**]。

9.  在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-itrp-tutorial/IC775574.png "設定單一登入")
##<a name="configuring-user-provisioning"></a>設定使用者佈建
  
若要啟用 Azure AD 使用者登入 ITRP，他們必須佈建到 ITRP。  
若是 ITRP，佈建是手動的工作。

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>佈建使用者帳戶，請執行下列步驟︰

1.  登入您的**ITRP**租用戶。

2.  在頂端上工具列中，按一下 [**記錄**]。

    ![管理員](./media/active-directory-saas-itrp-tutorial/IC775575.png "管理員")

3.  從快顯功能表中，選取 [**人員**]。

    ![人員](./media/active-directory-saas-itrp-tutorial/IC775587.png "人員")

4.  按一下 [**新增新的人員**（[+]）。

    ![管理員](./media/active-directory-saas-itrp-tutorial/IC775576.png "管理員")

5.  在 [新增新的人員] 對話方塊中，執行下列步驟︰

    ![使用者](./media/active-directory-saas-itrp-tutorial/IC775577.png "使用者")

    1.  輸入**名稱**，您想要佈建有效 AAD 帳戶的**電子郵件**。
    2.  按一下 [**儲存**]。

>[AZURE.NOTE] 您可以使用任何其他 ITRP 使用者帳戶建立工具或 Api 提供 ITRP 佈建 AAD 使用者帳戶。

##<a name="assigning-users"></a>將使用者指派
  
若要測試您的設定，您需要授與 Azure AD 使用者您想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-itrp-perform-the-following-steps"></a>若要指定 ITRP 使用者，請執行下列步驟︰

1.  Azure AD 入口網站中建立測試帳戶。

2.  **ITRP**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-itrp-tutorial/IC775588.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-itrp-tutorial/IC767830.png "[是]")
  
如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。