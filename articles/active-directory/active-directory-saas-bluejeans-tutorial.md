<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合 BlueJeans |Microsoft Azure" 
    description="瞭解如何使用 BlueJeans 與 Azure Active Directory 啟用單一登入、 自動化佈建和更多 ！" 
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

#<a name="tutorial-azure-ad-integration-with-bluejeans"></a>教學課程︰ Azure AD BlueJeans 整合

本教學課程中的目標是以顯示 Azure 和 BlueJeans 的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   BlueJeans 單一登入啟用訂閱

完成後本教學課程中，您指派給 BlueJeans Azure AD 使用者將能夠單一登入 BlueJeans 公司網站 （服務提供者發起的租用戶登入），或使用[[存取面板簡介](active-directory-saas-access-panel-introduction.md)應用程式。

本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用 BlueJeans 應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-bluejeans-tutorial/IC785860.png "案例")
##<a name="enabling-the-application-integration-for-bluejeans"></a>啟用 BlueJeans 應用程式整合

本節的目標是大綱如何啟用 BlueJeans 的整合應用程式。

###<a name="to-enable-the-application-integration-for-bluejeans-perform-the-following-steps"></a>若要啟用的 BlueJeans 整合應用程式，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-bluejeans-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-bluejeans-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-bluejeans-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-bluejeans-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**BlueJeans**。

    ![應用程式組件庫](./media/active-directory-saas-bluejeans-tutorial/IC785861.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**BlueJeans**，，然後按一下要新增應用程式**完成**。

    ![blueJeans](./media/active-directory-saas-bluejeans-tutorial/IC785862.png "blueJeans")
##<a name="configuring-single-sign-on"></a>設定單一登入

本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD BlueJeans。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站， **BlueJeans**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-bluejeans-tutorial/IC785863.png "設定單一登入")

2.  在**您要如何登入 BlueJeans 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-bluejeans-tutorial/IC785864.png "設定單一登入")

3.  **設定應用程式 URL**在頁面上，在 [ **BlueJeans 登入 URL** ] 文字方塊中，輸入您使用下列模式 」*https://company.BlueJeans.com*」 的 URL，然後再按 [**下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-bluejeans-tutorial/IC785865.png "設定應用程式 URL")

4.  在**設定單一登入 BlueJeans 在**頁面上，若要下載您的憑證，按一下 [**下載憑證**] 並儲存在您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-bluejeans-tutorial/IC785866.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您**BlueJeans**公司的網站。

6.  移至 [**管理員\>群組設定\>安全性**。

    ![管理員](./media/active-directory-saas-bluejeans-tutorial/IC785868.png "管理員")

7.  在 [**安全性**] 區段中，執行下列步驟︰

    ![SAML 單一登入](./media/active-directory-saas-bluejeans-tutorial/IC785869.png "SAML 單一登入")

    1.  選取 [ **SAML 單一登入**]。
    2.  選取 [**啟用自動佈建**。

8.  移動執行下列步驟︰

    ![憑證路徑](./media/active-directory-saas-bluejeans-tutorial/IC785870.png "憑證路徑")

    1.  按一下 [**選擇 [檔案**]，然後上傳已下載的憑證。
    2.  在 Azure 的傳統入口網站中**設定單一登入，BlueJeans** ] 對話方塊在頁面上，複製 [**遠端登入 URL**的值，，然後再貼到 [**登入 URL** ] 文字方塊。
    3.  在 Azure 的傳統入口網站中**設定單一登入，BlueJeans** ] 對話方塊在頁面上，複製 [**變更密碼 URL**的值，，然後再貼到 [**變更密碼的 URL** ] 文字方塊。
    4.  在 Azure 的傳統入口網站中**設定單一登入，BlueJeans** ] 對話方塊在頁面上，複製 [**遠端登出 URL**的值，，然後再貼到 [**登出 URL** ] 文字方塊。

9.  移動執行下列步驟︰

    ![儲存變更](./media/active-directory-saas-bluejeans-tutorial/IC785874.png "儲存變更")

    1.  在 [**使用者識別碼**] 文字方塊中，輸入**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**。
    2.  在 [**電子郵件**] 文字方塊中，輸入**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**。
    3.  按一下 [**儲存變更**]。

10. 在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-bluejeans-tutorial/IC785876.png "設定單一登入")
##<a name="configuring-user-provisioning"></a>設定使用者佈建

若要啟用 Azure AD 使用者登入 BlueJeans，他們必須佈建到 BlueJeans。  
若是 BlueJeans，佈建是手動的工作。

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>佈建使用者帳戶，請執行下列步驟︰

1.  以系統管理員身分登入您**BlueJeans**公司的網站。

2.  移至 [**管理員\>管理使用者\>將使用者新增**。

    ![管理員](./media/active-directory-saas-bluejeans-tutorial/IC785877.png "管理員")

    >[AZURE.IMPORTANT] 只使用如果在 [**安全性] 索引標籤**中，若要**啟用自動佈建**未核取 [**新增使用者**] 索引標籤。

3.  在 [**新增使用者**] 區段中，執行下列步驟︰

    ![新增使用者](./media/active-directory-saas-bluejeans-tutorial/IC785886.png "新增使用者")

    1.  輸入**BlueJeans 使用者名稱**、**電子郵件地址**、 **BlueJeans 會議 ID**、**仲裁者密碼**、**完整名稱**、**公司**有效 AAD 您想要的帳戶佈建到相關的文字方塊。
    2.  按一下 [**新增使用者**]。

>[AZURE.NOTE] 您可以使用任何其他 BlueJeans 使用者帳戶建立工具或 Api 提供 BlueJeans 佈建 AAD 使用者帳戶。

##<a name="assigning-users"></a>將使用者指派

若要測試您的設定，您需要授與 Azure AD 使用者想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-bluejeans-perform-the-following-steps"></a>若要指定 BlueJeans 使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **BlueJeans**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-bluejeans-tutorial/IC785887.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-bluejeans-tutorial/IC767830.png "[是]")

如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。
