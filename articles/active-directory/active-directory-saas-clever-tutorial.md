<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合 Clever |Microsoft Azure" 
    description="瞭解如何使用 Clever 與 Azure Active Directory 啟用單一登入，自動化佈建和更多 ！" 
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

#<a name="tutorial-azure-active-directory-integration-with-clever"></a>教學課程︰ 使用 Clever 的 Azure Active Directory 整合

本教學課程中的目標是以顯示 Azure 和 Clever 的整合。 本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   聰明租用戶

完成後本教學課程中，您指派給 Clever Azure AD 使用者將能夠單一登入聰明公司網站 （服務提供者發起的租用戶登入），或使用[[存取面板簡介](active-directory-saas-access-panel-introduction.md)應用程式。

本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用 Clever 應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-clever-tutorial/IC798977.png "案例")
##<a name="enabling-the-application-integration-for-clever"></a>啟用 Clever 應用程式整合

本節的目標是大綱如何啟用 Clever 的整合應用程式。

###<a name="to-enable-the-application-integration-for-clever-perform-the-following-steps"></a>若要啟用的 Clever 整合應用程式，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-clever-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-clever-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-clever-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-clever-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**Clever**。

    ![應用程式組件庫](./media/active-directory-saas-clever-tutorial/IC798978.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**Clever**，，然後按一下要新增應用程式**完成**。

    ![聰明](./media/active-directory-saas-clever-tutorial/IC798979.png "聰明")
##<a name="configuring-single-sign-on"></a>設定單一登入

本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD Clever。  
聰明應用程式的期待 SAML 判斷提示以特定格式會要求您將自訂屬性對應至**saml 權杖屬性**設定。  
以下螢幕擷取畫面會顯示這個範例。

![屬性](./media/active-directory-saas-clever-tutorial/IC798980.png "屬性")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站， **Clever**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-clever-tutorial/IC784682.png "設定單一登入")

2.  在**您要如何登入 Clever 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-clever-tutorial/IC798981.png "設定單一登入")

3.  **設定應用程式 URL**在頁面上，在**聰明登入 URL** ] 文字方塊中輸入您登入聰明應用程式的使用者所使用的 URL (例如︰ *https://clever.com/in/azsandbox*)，然後按一下 [**下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-clever-tutorial/IC798982.png "設定應用程式 URL")

4.  在**設定單一登入 Clever 在**頁面上，若要下載您的中繼資料，按一下**下載的中繼資料**]，然後儲存在本機電腦上的中繼資料檔案。

    ![設定單一登入](./media/active-directory-saas-clever-tutorial/IC798983.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您聰明公司的網站。

6.  在工具列上，按一下 [**立即登入**。

    ![立即登入](./media/active-directory-saas-clever-tutorial/IC798984.png "立即登入")

7.  在 [**立即登入**] 頁面中，執行下列步驟︰

    ![立即登入](./media/active-directory-saas-clever-tutorial/IC798985.png "立即登入")

    1.  輸入的**登入 URL**。  

        >[AZURE.NOTE] **登入 URL**是自訂的值。
您可以取得的實際值從聰明的支援小組。

    2.  **識別系統**，請選取 [ **ADFS**]。
    3.  按一下 [**儲存**]。

8.  在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-clever-tutorial/IC798986.png "設定單一登入")

9.  在頂端的功能表，按一下 [**屬性**開啟 [ **SAML 權杖屬性**] 對話方塊。

    ![屬性](./media/active-directory-saas-clever-tutorial/IC795920.png "屬性")

10. 若要新增必要的屬性對應，請執行下列步驟︰

    ![saml 權杖屬性](./media/active-directory-saas-clever-tutorial/IC795921.png "saml 權杖屬性")

  	|屬性名稱|屬性值|
  	|---|---|
  	|clever.student.credentials.district\_使用者名稱|User.userprincipalname|

    1.  每個資料列以上表格中，按一下 [**新增使用者屬性**]。
    2.  在 [**屬性名稱**] 文字方塊中，輸入顯示該資料列的屬性名稱。
    3.  在 [**屬性值**] 文字方塊中，選取 [顯示該資料列的屬性值。
    4.  按一下 [**完成**]。

11. 按一下 [**套用變更**]。

##<a name="configuring-user-provisioning"></a>設定使用者佈建

若要啟用 Azure AD 使用者登入 Clever，他們必須佈建到 Clever。  
若是 Clever，佈建是手動工作需要執行聰明的支援小組。

>[AZURE.NOTE] 您可以使用任何其他聰明的使用者帳戶建立工具或 Api 提供 Clever 佈建 AAD 使用者帳戶。

##<a name="assigning-users"></a>將使用者指派

若要測試您的設定，您需要授與 Azure AD 使用者想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-clever-perform-the-following-steps"></a>若要指定 Clever 使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **Clever**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-clever-tutorial/IC798987.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-clever-tutorial/IC767830.png "[是]")

如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。
