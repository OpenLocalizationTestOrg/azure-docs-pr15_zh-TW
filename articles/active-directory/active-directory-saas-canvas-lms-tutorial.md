<properties
    pageTitle="教學課程︰ Azure Active Directory 整合畫布 LMS |Microsoft Azure" 
    description="瞭解如何使用畫布 LMS 與 Azure Active Directory 啟用單一登入、 自動化佈建和更多 ！" 
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

#<a name="tutorial-azure-active-directory-integration-with-canvas-lms"></a>教學課程︰ 使用畫布 LMS 的 Azure Active Directory 整合

本教學課程中的目標是以顯示 Azure 和畫布的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   畫布租用戶

完成後本教學課程中，您已指派到畫布 Azure AD 使用者將能夠單一登入畫布公司網站 （服務提供者發起的租用戶登入），或使用[[存取面板簡介](active-directory-saas-access-panel-introduction.md)應用程式。

本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用應用程式適用於整合畫布
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-canvas-lms-tutorial/IC775984.png "案例")
##<a name="enabling-the-application-integration-for-canvas"></a>啟用應用程式適用於整合畫布

本節的目標是大綱如何啟用應用程式適用於整合畫布。

###<a name="to-enable-the-application-integration-for-canvas-perform-the-following-steps"></a>若要啟用的畫布整合應用程式，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-canvas-lms-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-canvas-lms-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-canvas-lms-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-canvas-lms-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**畫布**。

    ![應用程式組件庫](./media/active-directory-saas-canvas-lms-tutorial/IC775985.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**畫布**]，然後按一下**完成**新增應用程式。

    ![畫布](./media/active-directory-saas-canvas-lms-tutorial/IC775986.png "畫布")
##<a name="configuring-single-sign-on"></a>設定單一登入

本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD 畫布。  
設定單一登入的畫布需要您從憑證擷取指紋值。  
如果您不熟悉這個程序，請參閱[如何擷取的憑證指紋值](http://youtu.be/YKQF266SAxI)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站，在**畫布**應用程式整合頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-canvas-lms-tutorial/IC771709.png "設定單一登入")

2.  在**您要如何登入畫布上的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-canvas-lms-tutorial/IC775987.png "設定單一登入")

3.  在 [**設定應用程式 URL** ] 頁面**畫布登入 URL** ] 文字方塊中，輸入您使用下列模式的 URL `https://<tenant-name>.instructure.com`，然後按一下 [**下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-canvas-lms-tutorial/IC775988.png "設定應用程式 URL")

4.  在**設定單一登入畫布在**頁面上，若要下載您的憑證，按一下**下載憑證**]，然後儲存在本機電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-canvas-lms-tutorial/IC775989.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的畫布公司網站。

6.  移至 [**課程\>受管理的帳戶\>Microsoft**。

    ![畫布](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "畫布")

7.  在左側功能窗格] 中選取**驗證**]，然後按一下**新增 SAML 設定**。

    ![驗證](./media/active-directory-saas-canvas-lms-tutorial/IC775991.png "驗證")

8.  在目前的整合頁面上，執行下列步驟︰

    ![目前的整合](./media/active-directory-saas-canvas-lms-tutorial/IC775992.png "目前的整合")

    1.  在 Azure 的傳統入口網站中**設定單一登入在畫布**] 對話方塊在頁面上，複製 [**實體識別碼**值]，並再貼到 [ **IdP 實體 ID** ] 文字方塊。
    2.  在 Azure 的傳統入口網站中**設定單一登入在畫布**] 對話方塊在頁面上，複製 [**遠端登入 URL**的值，，然後再貼到**記錄在 [URL** ] 文字方塊。
    3.  在 Azure 的傳統入口網站中**設定單一登入在畫布**] 對話方塊在頁面上，複製 [**遠端登入 URL**的值，，然後再貼到**記錄出 URL** ] 文字方塊。
    4.  在 Azure 的傳統入口網站中**設定單一登入在畫布**] 對話方塊在頁面上，複製 [**變更密碼 URL**的值，，然後再貼到 [**變更密碼] 連結**的文字方塊。
    5.  **指紋**值複製匯出的憑證，然後再貼到**憑證指紋**文字方塊。  

        >[AZURE.TIP] 如需詳細資訊，請參閱[如何擷取的憑證指紋值](http://youtu.be/YKQF266SAxI)

    6.  從**登入屬性**] 清單中，選取 [ **NameID**]。
    7.  從 [**識別碼格式**] 清單中，選取 [**電子郵件地址**]。
    8.  按一下 [**儲存驗證設定**。

9.  在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-canvas-lms-tutorial/IC775993.png "設定單一登入")
##<a name="configuring-user-provisioning"></a>設定使用者佈建

若要啟用 Azure AD 使用者登入畫布，必須會佈建到畫布。  
若是畫布佈建是手動的工作。

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>佈建使用者帳戶，請執行下列步驟︰

1.  登入您的**畫布**租用戶。

2.  移至 [**課程\>受管理的帳戶\>Microsoft**。

    ![畫布](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "畫布")

3.  按一下 [**使用者**]。

    ![使用者](./media/active-directory-saas-canvas-lms-tutorial/IC775995.png "使用者")

4.  按一下 [**新增使用者**]。

    ![使用者](./media/active-directory-saas-canvas-lms-tutorial/IC775996.png "使用者")

5.  在 [新增新的使用者] 對話方塊頁面，請執行下列步驟︰

    ![新增使用者](./media/active-directory-saas-canvas-lms-tutorial/IC775997.png "新增使用者")

    1.  在 [**完整名稱**] 文字方塊中，輸入使用者的名稱。
    2.  在 [**電子郵件**] 文字方塊中，輸入使用者的電子郵件地址。
    3.  在 [**登入**] 文字方塊中，輸入使用者的 Azure AD 電子郵件地址。
    4.  選取 [**關於此帳戶建立使用者的電子郵件**]。
    5.  按一下 [**新增使用者**]。

>[AZURE.NOTE] 您可以使用任何其他畫布使用者帳戶建立工具或 Api 提供畫布佈建 AAD 使用者帳戶。

##<a name="assigning-users"></a>將使用者指派

若要測試您的設定，您需要授與 Azure AD 使用者想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-canvas-perform-the-following-steps"></a>若要指定使用者畫布上，執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  在**畫布**應用程式整合頁面上，按一下 [**指派給使用者**。

    ![將使用者指派](./media/active-directory-saas-canvas-lms-tutorial/IC775998.png "將使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-canvas-lms-tutorial/IC767830.png "[是]")

如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。
