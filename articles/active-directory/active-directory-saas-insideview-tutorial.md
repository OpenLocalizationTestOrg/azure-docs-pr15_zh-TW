<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合 InsideView |Microsoft Azure" 
    description="瞭解如何使用 InsideView 與 Azure Active Directory 啟用單一登入、 自動化佈建和更多 ！" 
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

#<a name="tutorial-azure-active-directory-integration-with-insideview"></a>教學課程︰ 使用 InsideView 的 Azure Active Directory 整合
  
本教學課程中的目標是以顯示 Azure 和 InsideView 的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   InsideView 租用戶
  
完成後本教學課程中，您指派給 InsideView Azure AD 使用者將能夠單一登入 InsideView 公司網站 （服務提供者發起的租用戶登入），或使用[[存取面板簡介](active-directory-saas-access-panel-introduction.md)應用程式。
  
本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用 InsideView 應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-insideview-tutorial/IC794128.png "案例")
##<a name="enabling-the-application-integration-for-insideview"></a>啟用 InsideView 應用程式整合
  
本節的目標是大綱如何啟用 InsideView 的整合應用程式。

###<a name="to-enable-the-application-integration-for-insideview-perform-the-following-steps"></a>若要啟用的 InsideView 整合應用程式，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-insideview-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-insideview-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-insideview-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-insideview-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**InsideView**。

    ![應用程式組件庫](./media/active-directory-saas-insideview-tutorial/IC794129.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**InsideView**，，然後按一下要新增應用程式**完成**。

    ![InsideView](./media/active-directory-saas-insideview-tutorial/IC794130.png "InsideView")
##<a name="configuring-single-sign-on"></a>設定單一登入
  
本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD InsideView。  
此程序的一部分，您所需建立 64 基本編碼的憑證檔案。  
如果您不熟悉這個程序，請參閱[如何將轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站， **InsideView**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一試用](./media/active-directory-saas-insideview-tutorial/IC794131.png "設定單一試用")

2.  在**您要如何登入 InsideView 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一試用](./media/active-directory-saas-insideview-tutorial/IC794132.png "設定單一試用")

3.  **設定應用程式 URL**在頁面上，在**InsideView 回覆 URL** ] 文字方塊中輸入您 InsideView SSO URL (例如︰ `https://my.insideview.com/iv/<STS Name>/login.iv`)，然後按一下 [**下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-insideview-tutorial/IC794133.png "設定應用程式 URL")

4.  在**設定單一登入 InsideView 在**頁面上，若要下載您的憑證，按一下 [**下載憑證**] 並儲存在您的電腦上的憑證檔案。

    ![設定單一試用](./media/active-directory-saas-insideview-tutorial/IC794134.png "設定單一試用")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您 InsideView 公司的網站。

6.  在 [工具列] 按一下**管理員**， **SingleSignOn 設定**]，然後按一下**新增 SAML**。

    ![SAML 單一登入設定值](./media/active-directory-saas-insideview-tutorial/IC794135.png "SAML 單一登入設定值")

7.  在 [**新增新的 SAML** ] 區段中，執行下列步驟︰

    ![新增新的 SAML](./media/active-directory-saas-insideview-tutorial/IC794136.png "新增新的 SAML")

    1.  在 [ **STS 名稱**] 文字方塊中，輸入您的設定的名稱。
    2.  Azure 傳統入口網站中，**設定單一登入，InsideView** ] 對話方塊在頁面上，複製 [**服務提供者 (SP) 發起的租用戶的結束點**的值，，然後再貼到 [ **SamlP/WS-Fed Unsolicated 結束點**] 文字方塊。
    3.  建立**64 基本編碼**檔案從您下載的憑證。
        
        >[AZURE.TIP]如需詳細資訊，請參閱[如何轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)

    4.  在記事本中開啟您 64 基本編碼的憑證，將其內容複製到剪貼簿，然後將其貼到**STS 憑證**文字方塊
    5.  在 [**對應的 Crm 使用者識別碼**] 文字方塊中輸入**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**。
    6.  在 [ **Crm 電子郵件對應**] 文字方塊中輸入**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**。
    7.  在 [ **Crm 第一個名稱對應**] 文字方塊中輸入**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**。
    8.  在 [**對應 Crm 姓氏在前**] 文字方塊中輸入**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**。
    9.  按一下 [**儲存**]。

8.  在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一試用](./media/active-directory-saas-insideview-tutorial/IC794137.png "設定單一試用")
##<a name="configuring-user-provisioning"></a>設定使用者佈建
  
若要啟用 Azure AD 使用者登入 InsideView，他們必須佈建到 InsideView。  
若是 InsideView，佈建是手動的工作。
  
若要取得使用者或 InsideView 中建立連絡人，請連絡您的客戶成功經理，或傳送電子郵件**support@insideview.com**

>[AZURE.NOTE] 您可以使用任何其他 InsideView 使用者帳戶建立工具或 Api 提供 InsideView 佈建 Azure AD 使用者帳戶。

##<a name="assigning-users"></a>將使用者指派
  
若要測試您的設定，您需要授與 Azure AD 使用者您想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-insideview-perform-the-following-steps"></a>若要指定 InsideView 使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **InsideView**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-insideview-tutorial/IC794138.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-insideview-tutorial/IC767830.png "[是]")
  
如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。