<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合明白 ！ |Microsoft Azure" 
    description="瞭解如何使用明白 ！ 與 Azure Active Directory 啟用單一登入、 自動化佈建和更多 ！" 
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

#<a name="tutorial-azure-active-directory-integration-with-aha"></a>教學課程︰ Azure Active Directory 整合明白 ！

本教學課程中的目標是以顯示整合 Azure 和明白 ！  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   好 ！ 單一登入啟用訂閱

完成後本教學課程，Azure AD 使用者已指派給明白 ！ 將能夠單一的登入該應用程式在您明白 ！ 公司網站 （服務提供者發起的租用戶登入），或使用[[存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用應用程式適用於整合明白 ！
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-aha-tutorial/IC798944.png "案例")
##<a name="enabling-the-application-integration-for-aha"></a>啟用應用程式適用於整合明白 ！

本節的目標是大綱如何啟用應用程式適用於整合明白 ！。

###<a name="to-enable-the-application-integration-for-aha-perform-the-following-steps"></a>若要啟用應用程式適用於整合明白 ！，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-aha-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-aha-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-aha-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-aha-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**明白 ！**。

    ![應用程式組件庫](./media/active-directory-saas-aha-tutorial/IC798945.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取 [**明白 ！**，然後按一下 [**完成**]，以便新增應用程式。

    ![好 ！](./media/active-directory-saas-aha-tutorial/IC802746.png "好 ！")
##<a name="configuring-single-sign-on"></a>設定單一登入

本節的目標是大綱如何讓使用者明白通過驗證 ！ 以中使用根據 SAML 通訊協定的同盟 Azure AD 帳戶。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 傳統入口網站上**明白 ！** 整合應用程式頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-aha-tutorial/IC798946.png "設定單一登入")

2.  在**您要如何登入明白使用者 ！** 頁面，選取 [ **Microsoft Azure AD 單一登入**，，再按一下 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-aha-tutorial/IC798947.png "設定單一登入")

3.  在 [**設定應用程式 URL** ] 頁面中**明白 ！登入 URL** ] 文字方塊中，輸入 URL，讓使用者中用來登入您明白 ！ 應用程式 (例如: 「*https://company.aha.io/session/new*」)，然後按一下 [**下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-aha-tutorial/IC798948.png "設定應用程式 URL")

4.  在**在明白設定單一登入 ！** 要下載的中繼資料檔案，請按一下 [**下載中繼資料**，然後儲存在本機電腦上的中繼資料檔案的頁面。

    ![設定單一登入](./media/active-directory-saas-aha-tutorial/IC798949.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，請登入您明白 ！ 以系統管理員的公司網站。

6.  在頂端的功能表，按一下 [**設定**]。

    ![設定](./media/active-directory-saas-aha-tutorial/IC798950.png "設定")

7.  按一下 [**帳戶**]。

    ![設定檔](./media/active-directory-saas-aha-tutorial/IC798951.png "設定檔")

8.  按一下 [**安全性和單一登入**]。

    ![安全性和單一登入](./media/active-directory-saas-aha-tutorial/IC798952.png "安全性和單一登入")

9.  在**單一登入**] 區段中，為**身分識別提供者**，選取**SAML2.0**。

    ![安全性和單一登入](./media/active-directory-saas-aha-tutorial/IC798953.png "安全性和單一登入")

10. **單一登入**設定] 頁面上，執行下列步驟︰

    ![單一登入](./media/active-directory-saas-aha-tutorial/IC798954.png "單一登入")

    1.  在 [**名稱**] 文字方塊中，輸入您的設定的名稱。
    2.  **設定使用**，請選取 [**中繼資料檔案**。
    3.  若要將檔案上傳已下載的中繼資料，請按一下 [**瀏覽**]。
    4.  按一下 [**更新**]。

11. 在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-aha-tutorial/IC798955.png "設定單一登入")
##<a name="configuring-user-provisioning"></a>設定使用者佈建

若要讓 Azure AD 使用者登入明白 ！，他們必須佈建到明白 ！。  
若是明白 ！，佈建是自動化的工作。  
沒有您的動作項目。
  
使用者會自動建立必要在第一個單一登入嘗試。

>[AZURE.NOTE] 您可以使用任何其他明白 ！ 使用者帳戶建立工具] 或 [明白所提供的 Api ！ 佈建 AAD 使用者帳戶。

##<a name="assigning-users"></a>將使用者指派

若要測試您的設定，您需要授與 Azure AD 使用者想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-aha-perform-the-following-steps"></a>若要將使用者指派至明白 ！，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  在**好 ！**整合應用程式頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-aha-tutorial/IC798956.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-aha-tutorial/IC767830.png "[是]")

如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。
