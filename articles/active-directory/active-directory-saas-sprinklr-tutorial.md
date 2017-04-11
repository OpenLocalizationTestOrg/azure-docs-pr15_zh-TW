<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合 Sprinklr |Microsoft Azure" 
    description="瞭解如何使用 Sprinklr 與 Azure Active Directory 啟用單一登入、 自動化佈建和更多 ！" 
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
    ms.date="09/19/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>教學課程︰ 使用 Sprinklr 的 Azure Active Directory 整合
  
本教學課程中的目標是以顯示 Azure 和 Sprinklr 的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   Sprinklr 租用戶
  
完成後本教學課程中，您指派給 Sprinklr Azure AD 使用者將能夠單一登入 Sprinklr 公司網站 （服務提供者發起的租用戶登入），或使用[[存取面板簡介](active-directory-saas-access-panel-introduction.md)應用程式。
  
本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用 Sprinklr 應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-sprinklr-tutorial/IC782900.png "案例")

##<a name="enabling-the-application-integration-for-sprinklr"></a>啟用 Sprinklr 應用程式整合
  
本節的目標是大綱如何啟用 Sprinklr 的整合應用程式。

###<a name="to-enable-the-application-integration-for-sprinklr-perform-the-following-steps"></a>若要啟用的 Sprinklr 整合應用程式，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-sprinklr-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-sprinklr-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-sprinklr-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-sprinklr-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**Sprinklr**。

    ![應用程式組件庫](./media/active-directory-saas-sprinklr-tutorial/IC782901.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**Sprinklr**，，然後按一下要新增應用程式**完成**。

    ![Sprinklr](./media/active-directory-saas-sprinklr-tutorial/IC782902.png "Sprinklr")

##<a name="configuring-single-sign-on"></a>設定單一登入
  
本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD Sprinklr。  
此程序的一部分，您所需建立 64 基本編碼的憑證檔案。  
如果您不熟悉這個程序，請參閱[如何將轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站， **Sprinklr**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-sprinklr-tutorial/IC782903.png "設定單一登入")

2.  在**您要如何登入 Sprinklr 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-sprinklr-tutorial/IC782904.png "設定單一登入")

3.  **設定應用程式 URL**在頁面上，在 [ **Sprinklr 登入 URL** ] 文字方塊中，輸入您使用下列模式的 URL 「*https://\<租用戶名稱\>。 sprinklr.com*」，然後按一下 [**下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-sprinklr-tutorial/IC782905.png "設定應用程式 URL")

4.  在**設定單一登入 Sprinklr 在**頁面上，若要下載您的憑證，按一下 [**下載憑證**] 並儲存在您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-sprinklr-tutorial/IC782906.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您 Sprinklr 公司的網站。

6.  移至 [**管理\>設定**。

    ![管理](./media/active-directory-saas-sprinklr-tutorial/IC782907.png "管理")

7.  移至 [**管理合作夥伴\>單一登**上從左側的窗格。

    ![管理合作夥伴](./media/active-directory-saas-sprinklr-tutorial/IC782908.png "管理合作夥伴")

8.  按一下 [ **+ 新增單一登元件**]。

    ![單一登元件](./media/active-directory-saas-sprinklr-tutorial/IC782909.png "單一登元件")

9.  在**單一登入**頁面上，執行下列步驟︰

    ![單一登元件](./media/active-directory-saas-sprinklr-tutorial/IC782910.png "單一登元件")

    1.  在 [**名稱**] 文字方塊中，輸入您設定的名稱 (例如︰ *WAADSSOTest*)。
    2.  選取 [**啟用**]。
    3.  選取 [**使用新的 SSO 憑證**]。
    4.  建立**64 基本編碼**檔案從您下載的憑證。  

        >[AZURE.TIP] 如需詳細資訊，請參閱[如何轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)

    5.  在記事本中開啟您 64 基本編碼的憑證，將其內容複製到剪貼簿，然後將其貼到 [**身分識別提供者的憑證**] 文字方塊中
    6.  在 Azure 的傳統入口網站中**設定單一登入，Sprinklr** ] 對話方塊在頁面上，複製**身分識別提供者識別碼**值]，並再貼到 [**實體 Id** ] 文字方塊。
    7.  在 Azure 的傳統入口網站中**設定單一登入，Sprinklr** ] 對話方塊在頁面上，複製 [**遠端登入 URL**的值，，然後再貼到 [**身分識別提供者登入 URL** ] 文字方塊。
    8.  在 Azure 的傳統入口網站中**設定單一登入，Sprinklr** ] 對話方塊在頁面上，複製 [**遠端登出 URL**的值，，然後再貼到 [**身分識別提供者登出 URL** ] 文字方塊。
    9.  為**SAML 使用者識別碼類型**，選取 [**判斷提示包含使用者 「 s sprinklr.com 使用者名稱**。
    10. 為**SAML 使用者識別碼位置**，選取 [**使用者識別碼主旨陳述式的名稱識別碼項目中**。
    11. 關閉**儲存**。

        ![SAML](./media/active-directory-saas-sprinklr-tutorial/IC782911.png "SAML")

10. 在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-sprinklr-tutorial/IC782912.png "設定單一登入")

##<a name="configuring-user-provisioning"></a>設定使用者佈建
  
AAD 使用者可以登入，他們必須提供存取內 Sprinklr 應用程式。  
本節說明如何建立內部 Sprinklr AAD 使用者帳戶。

###<a name="to-provision-a-user-account-in-sprinklr-perform-the-following-steps"></a>佈建 Sprinklr 中的使用者帳戶，請執行下列步驟︰

1.  請以系統管理員身分登入您 Sprinklr 公司的網站。

2.  移至 [**管理\>設定**。

    ![管理](./media/active-directory-saas-sprinklr-tutorial/IC782907.png "管理")

3.  移至 [**管理用戶端\>使用者**從左側的窗格。

    ![設定](./media/active-directory-saas-sprinklr-tutorial/IC782914.png "設定")

4.  按一下 [**新增使用者**]。

    ![設定](./media/active-directory-saas-sprinklr-tutorial/IC782915.png "設定")

5.  在 [**編輯使用者**] 對話方塊中，執行下列步驟︰

    ![編輯使用者](./media/active-directory-saas-sprinklr-tutorial/IC782916.png "編輯使用者")

    1.  在 [**電子郵件**、**名字**和**姓氏**文字方塊、 輸入 Azure AD 使用者帳戶的資訊您想要佈建。
    2.  選取 [**停用密碼**]。
    3.  選取 [**語言**]。
    4.  選取**使用者類型**。
    5.  按一下 [**更新**]。

    >[AZURE.IMPORTANT] **停用密碼**必須選取要啟用使用者的身分識別提供者透過登入。

6.  移至**角色**，然後執行下列步驟︰

    ![合作夥伴角色](./media/active-directory-saas-sprinklr-tutorial/IC782917.png "合作夥伴角色")

    1.  從**[通用**] 清單中，選取 [**所有\_權限**。
    2.  按一下 [**更新**]。

>[AZURE.NOTE] 您可以使用任何其他 Sprinklr 使用者帳戶建立工具或 Api 提供 Sprinklr 佈建 Azure AD 使用者帳戶。

##<a name="assigning-users"></a>將使用者指派
  
若要測試您的設定，您需要授與 Azure AD 使用者您想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-sprinklr-perform-the-following-steps"></a>若要指定 Sprinklr 使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **Sprinklr**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-sprinklr-tutorial/IC782918.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-sprinklr-tutorial/IC767830.png "[是]")
  
如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。