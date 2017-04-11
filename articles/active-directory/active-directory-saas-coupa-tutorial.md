<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合 Coupa |Microsoft Azure" 
    description="瞭解如何使用 Coupa 與 Azure Active Directory 啟用單一登入，自動化佈建和更多 ！" 
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

#<a name="tutorial-azure-active-directory-integration-with-coupa"></a>教學課程︰ 使用 Coupa 的 Azure Active Directory 整合

本教學課程中的目標是以顯示 Azure 和 Coupa 的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   Coupa 單一登入啟用訂閱

完成後本教學課程中，您指派給 Coupa Azure AD 使用者將能夠[存取面板簡介](active-directory-saas-access-panel-introduction.md)在應用程式中的單一登。

本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用 Coupa 應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-coupa-tutorial/IC791897.png "案例")
##<a name="enabling-the-application-integration-for-coupa"></a>啟用 Coupa 應用程式整合

本節的目標是大綱如何啟用 Coupa 的整合應用程式。

###<a name="to-enable-the-application-integration-for-coupa-perform-the-following-steps"></a>若要啟用的 Coupa 整合應用程式，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-coupa-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-coupa-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-coupa-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-coupa-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**Coupa**。

    ![應用程式組件庫](./media/active-directory-saas-coupa-tutorial/IC791898.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**Coupa**，，然後按一下 [新增應用程式**完成**。

    ![Coupa](./media/active-directory-saas-coupa-tutorial/IC791899.png "Coupa")
##<a name="configuring-single-sign-on"></a>設定單一登入

本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD Coupa。  
設定單一登入的 Coupa 需要您從憑證擷取指紋值。  
如果您不熟悉這個程序，請參閱[如何擷取的憑證指紋值](http://youtu.be/YKQF266SAxI)。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  以系統管理員身分登入您 Coupa 公司的網站。

2.  移至 [**設定\>安全性控制項**。

    ![安全性控制項](./media/active-directory-saas-coupa-tutorial/IC791900.png "安全性控制項")

3.  若要下載 Coupa 中繼資料檔案至您的電腦，按一下 [**下載並匯入預存程序中繼資料**]。

    ![Coupa SP 中繼資料](./media/active-directory-saas-coupa-tutorial/IC791901.png "Coupa SP 中繼資料")

4.  在不同的瀏覽器視窗中，登入 Azure 傳統入口網站。

5.  在 [ **Coupa**應用程式整合] 頁面中，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-coupa-tutorial/IC791902.png "設定單一登入")

6.  在**您要如何登入 Coupa 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-coupa-tutorial/IC791903.png "設定單一登入")

7.  **設定應用程式 URL**在頁面上，執行下列步驟︰

    ![設定應用程式 URL](./media/active-directory-saas-coupa-tutorial/IC791904.png "設定應用程式 URL")

    1.  在 [**登入 URL** ] 文字方塊中，輸入您的使用者用來登入 Coupa 應用程式的 URL (例如: 「*http://company.Coupa.com*」)。
    2.  開啟所下載的 Coupa 中繼資料檔案，然後再複製**AssertionConsumerService 索引/URL**。
    3.  在 [ **Coupa 回覆 URL** ] 文字方塊中貼上**AssertionConsumerService 索引/URL**的值。
    4.  按一下 [**下一步**]。

8.  在**設定單一登入 Coupa 在**頁面上，若要下載的中繼資料檔案，按一下**下載的中繼資料**]，然後儲存在本機電腦上的檔案。

    ![設定單一登入](./media/active-directory-saas-coupa-tutorial/IC791905.png "設定單一登入")

9.  Coupa 公司網站上，移至**設定\>安全性控制項**。

    ![安全性控制項](./media/active-directory-saas-coupa-tutorial/IC791900.png "安全性控制項")

10. 在 [**使用 Coupa 認證登入**] 區段中，執行下列步驟︰

    ![使用 Coupa 認證登入](./media/active-directory-saas-coupa-tutorial/IC791906.png "使用 Coupa 認證登入")

    1.  選取 [**使用 SAML 登入**]。
    2.  按一下 [**瀏覽**至您已下載的 Azure Active 中繼資料檔案上傳]。
    3.  按一下 [**儲存**]。

11. 在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-coupa-tutorial/IC791907.png "設定單一登入")
##<a name="configuring-user-provisioning"></a>設定使用者佈建

若要啟用 Azure AD 使用者登入 Coupa，他們必須佈建到 Coupa。  
若是 Coupa，佈建是手動的工作。

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，執行下列步驟︰

1.  以系統管理員身分登入您**Coupa**公司的網站。

2.  在頂端的功能表，按一下 [**設定**]，然後再按一下 [**使用者**。

    ![使用者](./media/active-directory-saas-coupa-tutorial/IC791908.png "使用者")

3.  按一下 [**建立**]。

    ![建立使用者](./media/active-directory-saas-coupa-tutorial/IC791909.png "建立使用者")

4.  在 [**建立使用者**] 區段中，執行下列步驟︰

    ![使用者詳細資料](./media/active-directory-saas-coupa-tutorial/IC791910.png "使用者詳細資料")

    1.  輸入**登入**、**名字**、**姓氏**、**單一登入 ID**，您想要在相關的文字方塊佈建有效 Azure Active Directory 帳戶的**電子郵件**屬性。
    2.  按一下 [**建立**]。

    >[AZURE.NOTE] Azure Active Directory 帳戶擁有者會收到確認帳戶，再會變成作用中連結的電子郵件。

>[AZURE.NOTE] 您可以使用任何其他 Coupa 使用者帳戶建立工具或 Api 提供 Coupa 佈建 AAD 使用者帳戶。

##<a name="assigning-users"></a>將使用者指派

若要測試您的設定，您需要授與 Azure AD 使用者您想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-coupa-perform-the-following-steps"></a>若要指定 Coupa 使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **Coupa**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-coupa-tutorial/IC791911.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-coupa-tutorial/IC767830.png "[是]")

如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。
