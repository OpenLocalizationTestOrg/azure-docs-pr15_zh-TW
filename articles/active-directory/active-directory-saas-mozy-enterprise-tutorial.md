<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合 Mozy 企業版 |Microsoft Azure" 
    description="瞭解如何使用 Mozy 企業版與 Azure Active Directory 啟用單一登入，自動化佈建和更多 ！" 
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

#<a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>教學課程︰ Azure Active Directory Mozy 企業整合
  
本教學課程中的目標是要顯示的 Azure 和 Mozy 企業整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   Mozy 企業租用戶
  
完成後本教學課程中，您指派給 Mozy 企業 Azure AD 使用者將能夠單一登入 Mozy 企業公司網站 （服務提供者發起的租用戶登入），或使用[[存取面板簡介](active-directory-saas-access-panel-introduction.md)應用程式。
  
本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用 Mozy 企業版的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-mozy-enterprise-tutorial/IC777308.png "案例")
##<a name="enabling-the-application-integration-for-mozy-enterprise"></a>啟用 Mozy 企業版的應用程式整合
  
本節的目標是大綱如何啟用 Mozy 企業版的應用程式整合。

###<a name="to-enable-the-application-integration-for-mozy-enterprise-perform-the-following-steps"></a>若要啟用 Mozy 企業版的應用程式整合，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-mozy-enterprise-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-mozy-enterprise-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-mozy-enterprise-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-mozy-enterprise-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**mozy 企業版**。

    ![應用程式組件庫](./media/active-directory-saas-mozy-enterprise-tutorial/IC777309.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**Mozy 企業**、，然後按一下要新增應用程式**完成**。

    ![Mozy 企業版](./media/active-directory-saas-mozy-enterprise-tutorial/IC777310.png "Mozy 企業版")
##<a name="configuring-single-sign-on"></a>設定單一登入
  
本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD Mozy 企業版。  
此程序的一部分，您會需要上傳至您的企業 Mozy 租用戶的底數 64 編碼的憑證。  
如果您不熟悉這個程序，請參閱[如何轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站， **Mozy 企業**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-mozy-enterprise-tutorial/IC771709.png "設定單一登入")

2.  在**您要如何登入 Mozy 企業版的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-mozy-enterprise-tutorial/IC777311.png "設定單一登入")

3.  **設定應用程式 URL**在頁面上，在 [ **Mozy 企業登入 URL** ] 文字方塊中，輸入您使用下列模式的 URL 「*https://\<租用戶名稱\>。Mozyenterprise.com*」，然後按一下 [**下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-mozy-enterprise-tutorial/IC777312.png "設定應用程式 URL")

4.  在**設定單一登入 Mozy 企業在**頁面上，若要下載您的憑證，按一下 [**下載憑證**] 並儲存在您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-mozy-enterprise-tutorial/IC777313.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您 Mozy 企業的公司網站。

6.  在 [**設定**] 區段中，按一下 [**驗證原則**]。

    ![驗證原則](./media/active-directory-saas-mozy-enterprise-tutorial/IC777314.png "驗證原則")

7.  在 [**驗證原則**] 區段中，執行下列步驟︰

    ![驗證原則](./media/active-directory-saas-mozy-enterprise-tutorial/IC777315.png "驗證原則")

    1.  選取 [為**提供者**的 [**目錄服務**]。
    2.  選取 [**使用 LDAP 推入**]。
    3.  按一下 [ **SAML 驗證**] 索引標籤。
    4.  在 Azure 的傳統入口網站中**設定單一登入在 Mozy 企業**] 對話方塊在頁面上，**驗證要求 URL**值，複製，然後再貼到 [**驗證 URL** ] 文字方塊。
    5.  在 Azure 的傳統入口網站中**設定單一登入在 Mozy 企業**] 對話方塊在頁面上，複製**身分識別提供者識別碼**值]，並再貼到**SAML 端點**文字方塊。
    6.  建立**64 基本編碼**檔案從您下載的憑證。  

        >[AZURE.TIP]如需詳細資訊，請參閱[如何轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)

    7.  在記事本中開啟您 64 基本編碼的憑證，將其內容複製到剪貼簿，並貼整個憑證**SAML 憑證**文字方塊。
    8.  選取 [**啟用 SSO 管理員使用網路認證登入**]。
    9.  按一下 [**儲存變更**]。

8.  在 Azure 的傳統入口網站中**設定單一登入在 Mozy 企業**] 對話方塊在頁面上，選取單一登入設定確認，然後按 [**完成**。

    ![設定單一登入](./media/active-directory-saas-mozy-enterprise-tutorial/IC777316.png "設定單一登入")
##<a name="configuring-user-provisioning"></a>設定使用者佈建
  
若要啟用 Azure AD 使用者登入 Mozy 企業版，必須會佈建到 Mozy 企業。  
若是 Mozy 企業佈建是手動的工作。

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>佈建使用者帳戶，請執行下列步驟︰

1.  登入您的**企業 Mozy**租用戶。

2.  按一下 [**使用者**]，然後按一下 [**新增使用者**。

    ![使用者](./media/active-directory-saas-mozy-enterprise-tutorial/IC777317.png "使用者")

    >[AZURE.NOTE]為提供者**驗證**原則選取**Mozy**時才，只會顯示 [**新增使用者**] 選項。 如果設定了 SAML 驗證然後使用者會自動加入上透過單一登其第一個登入上。

3.  在 [新增使用者] 對話方塊中，執行下列步驟︰

    ![新增使用者](./media/active-directory-saas-mozy-enterprise-tutorial/IC777318.png "新增使用者")

    1.  從**選擇群組**清單中，選取 [群組]。
    2.  從**使用者的類型**] 清單中，選取類型。
    3.  在 [**使用者名稱**] 文字方塊中，輸入 Azure AD 使用者的名稱。
    4.  在 [**電子郵件**] 文字方塊中，輸入 Azure AD 使用者的電子郵件地址。
    5.  選取 [**使用者指示的電子郵件傳送**]。
    6.  按一下 [**新增使用者**]。

    >[AZURE.NOTE]建立使用者之後, 的電子郵件會傳送 Azure AD 使用者包含確認帳戶，再將其成為使用中的連結。

>[AZURE.NOTE]您可以使用任何其他 Mozy 企業使用者帳戶建立工具或 Api 提供 Mozy 企業佈建 AAD 使用者帳戶。

##<a name="assigning-users"></a>將使用者指派
 
若要測試您的設定，您需要授與 Azure AD 使用者您想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-mozy-enterprise-perform-the-following-steps"></a>若要指定 Mozy 企業使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **Mozy 企業**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-mozy-enterprise-tutorial/IC777319.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-mozy-enterprise-tutorial/IC767830.png "[是]")
  
如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。