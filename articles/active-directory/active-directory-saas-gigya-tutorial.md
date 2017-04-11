<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合 Gigya |Microsoft Azure" 
    description="瞭解如何使用 Gigya 與 Azure Active Directory 啟用單一登入、 自動化佈建和更多 ！" 
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
    ms.date="09/01/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-gigya"></a>教學課程︰ 使用 Gigya 的 Azure Active Directory 整合
  
本教學課程中的目標是以顯示 Azure 和 Gigya 的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   Gigya 單一登入啟用訂閱
  
完成後本教學課程中，您指派給 Gigya Azure AD 使用者將能夠單一登入 Gigya 公司網站 （服務提供者發起的租用戶登入），或使用[[存取面板簡介](active-directory-saas-access-panel-introduction.md)應用程式。
  
本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用 Gigya 應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![設定單一登入](./media/active-directory-saas-gigya-tutorial/IC789512.png "設定單一登入")
##<a name="enabling-the-application-integration-for-gigya"></a>啟用 Gigya 應用程式整合
  
本節的目標是大綱如何啟用 Gigya 的整合應用程式。

###<a name="to-enable-the-application-integration-for-gigya-perform-the-following-steps"></a>若要啟用的 Gigya 整合應用程式，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-gigya-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-gigya-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-gigya-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-gigya-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**Gigya**。

    ![應用程式組件庫](./media/active-directory-saas-gigya-tutorial/IC789513.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**Gigya**，，然後按一下要新增應用程式**完成**。

    ![Gigya](./media/active-directory-saas-gigya-tutorial/IC789527.png "Gigya")
##<a name="configuring-single-sign-on"></a>設定單一登入
  
本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD Gigya。  
此程序的一部分，您所需建立 64 基本編碼的憑證檔案。  
如果您不熟悉這個程序，請參閱[如何將轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站， **Gigya**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-gigya-tutorial/IC789528.png "設定單一登入")

2.  在**您要如何登入 Gigya 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-gigya-tutorial/IC789529.png "設定單一登入")

3.  **設定應用程式 URL**在頁面上，在 [ **Gigya 登入 URL** ] 文字方塊中，輸入您使用下列模式 」*http://company.gigya.com*」 的 URL，然後再按 [**下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-gigya-tutorial/IC789530.png "設定應用程式 URL")

4.  在**設定單一登入 Gigya 在**頁面上，按一下**下載憑證**]，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-gigya-tutorial/IC789531.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您 Gigya 公司的網站。

6.  移至 [**設定\>SAML 登入**，然後按一下 [**新增**] 按鈕。

    ![SAML 登入](./media/active-directory-saas-gigya-tutorial/IC789532.png "SAML 登入")

7.  在 [ **SAML 登入**] 區段中，執行下列步驟︰

    ![SAML 設定](./media/active-directory-saas-gigya-tutorial/IC789533.png "SAML 設定")

    1.  在 [**名稱**] 文字方塊中，輸入您的設定的名稱。
    2.  Azure 傳統入口網站中，**設定單一登入，Gigya** ] 對話方塊在頁面上，複製**簽發者 URL**的值，，然後再貼到 [**簽發者**] 文字方塊。
    3.  Azure 傳統入口網站，在 [**設定單一登入在 Gigya**對話方塊] 頁面中的**單一登入服務 URL**值，複製，然後再貼到**單一登入服務 URL** ] 文字方塊。
    4.  在 Azure 的傳統入口網站中**設定單一登入，Gigya** ] 對話方塊在頁面上，複製 [**姓名識別碼格式**的值，然後貼到**名稱識別碼格式**文字方塊。
    5.  建立**64 基本編碼**檔案從您下載的憑證。
        
        >[AZURE.TIP]如需詳細資訊，請參閱[如何轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)

    6.  在記事本中開啟您 64 基本編碼的憑證、 將其內容複製到剪貼簿，並貼到**X.509 憑證**文字方塊。
    7.  按一下 [**儲存設定**]。

8.  在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-gigya-tutorial/IC789534.png "設定單一登入")
##<a name="configuring-user-provisioning"></a>設定使用者佈建
  
若要啟用 Azure AD 使用者登入 Gigya，他們必須佈建到 Gigya。  
若是 Gigya，佈建是手動的工作。

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>佈建使用者帳戶，請執行下列步驟︰

1.  以系統管理員身分登入您**Gigya**公司的網站。

2.  移至 [**管理員\>管理使用者**，然後按一下 [**邀請使用者**。

    ![管理使用者](./media/active-directory-saas-gigya-tutorial/IC789535.png "管理使用者")

3.  在 [邀請的使用者] 對話方塊中，執行下列步驟︰

    ![邀請使用者](./media/active-directory-saas-gigya-tutorial/IC789536.png "邀請使用者")

    1.  在 [**電子郵件**] 文字方塊中，輸入您想要佈建有效 Azure Active Directory 帳戶的電子郵件別名。
    2.  按一下 [**邀請使用者**。
    
        >[AZURE.NOTE] Azure Active Directory 帳戶擁有者會收到包含確認帳戶，再會變成作用中連結的電子郵件。

>[AZURE.NOTE] 您可以使用任何其他 Gigya 使用者帳戶建立工具或 Api 提供 Gigya 佈建 AAD 使用者帳戶。

##<a name="assigning-users"></a>將使用者指派
  
若要測試您的設定，您需要授與 Azure AD 使用者想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-gigya-perform-the-following-steps"></a>若要指定 Gigya 使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **Gigya**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-gigya-tutorial/IC789537.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-gigya-tutorial/IC767830.png "[是]")
  
如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。