<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合 Lucidchart |Microsoft Azure" 
    description="瞭解如何使用 Lucidchart 與 Azure Active Directory 啟用單一登入，自動化佈建和更多 ！" 
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

#<a name="tutorial-azure-active-directory-integration-with-lucidchart"></a>教學課程︰ 使用 Lucidchart 的 Azure Active Directory 整合
  
本教學課程中的目標是以顯示 Azure 和 Lucidchart 的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   Lucidchart 單一登入啟用訂閱
  
完成後本教學課程中，您指派給 Lucidchart Azure AD 使用者將能夠單一登入 Lucidchart 公司網站 （服務提供者發起的租用戶登入），或使用[[存取面板簡介](active-directory-saas-access-panel-introduction.md)應用程式。
  
本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用 Lucidchart 應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-lucidchart-tutorial/IC791183.png "案例")
##<a name="enabling-the-application-integration-for-lucidchart"></a>啟用 Lucidchart 應用程式整合
  
本節的目標是大綱如何啟用 Lucidchart 的整合應用程式。

###<a name="to-enable-the-application-integration-for-lucidchart-perform-the-following-steps"></a>若要啟用的 Lucidchart 整合應用程式，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-lucidchart-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-lucidchart-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-lucidchart-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-lucidchart-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**Lucidchart**。

    ![應用程式組件庫](./media/active-directory-saas-lucidchart-tutorial/IC791184.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**Lucidchart**，，然後按一下要新增應用程式**完成**。

    ![Lucidchart](./media/active-directory-saas-lucidchart-tutorial/IC791185.png "Lucidchart")
##<a name="configuring-single-sign-on"></a>設定單一登入
  
本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD Lucidchart。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站， **Lucidchart**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-lucidchart-tutorial/IC791186.png "設定單一登入")

2.  在**您要如何登入 Lucidchart 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-lucidchart-tutorial/IC791187.png "設定單一登入")

3.  **設定應用程式 URL**在頁面上， **Lucidchart 登入 URL** ] 文字方塊中，輸入您的使用者用來登入 Lucidchart 應用程式的 URL (例如: 「*https://chart2.office.lucidchart.com/saml/sso/azure*」)，然後按一下 [**下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-lucidchart-tutorial/IC791188.png "設定應用程式 URL")

4.  在**設定單一登入 Lucidchart 在**頁面上，若要下載您的中繼資料，按一下**下載的中繼資料**]，然後儲存在本機電腦上的資料檔案。

    ![設定單一登入](./media/active-directory-saas-lucidchart-tutorial/IC791189.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您 Lucidchart 公司的網站。

6.  在頂端的功能表，按一下 [**小組**。

    ![小組](./media/active-directory-saas-lucidchart-tutorial/IC791190.png "小組")

7.  按一下 [**應用程式\>管理 SAML**。

    ![管理 SAML](./media/active-directory-saas-lucidchart-tutorial/IC791191.png "管理 SAML")

8.  **SAML 驗證設定**] 對話方塊在頁面上，執行下列步驟︰

    1.  選取 [**啟用 SAML 驗證**]，然後按一下 [**選擇性**。
        ![SAML 驗證設定](./media/active-directory-saas-lucidchart-tutorial/IC791192.png "SAML 驗證設定")
    2.  在 [**網域**] 文字方塊中，輸入您的網域，然後按一下**變更憑證**。
        ![變更憑證](./media/active-directory-saas-lucidchart-tutorial/IC791193.png "變更憑證")
    3.  開啟下載的中繼資料的檔案、 複製內容，並再貼到 [**上傳的中繼資料**] 文字方塊。
        ![上傳的中繼資料](./media/active-directory-saas-lucidchart-tutorial/IC791194.png "上傳的中繼資料")
    4.  選取 [**自動新增至小組的新使用者**，，然後按一下 [**儲存變更**。
        ![儲存變更](./media/active-directory-saas-lucidchart-tutorial/IC791195.png "儲存變更")

9.  選取單一登入設定確認，然後按一下 [**完成**，關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-lucidchart-tutorial/IC791196.png "設定單一登入")
##<a name="configuring-user-provisioning"></a>設定使用者佈建
  
沒有進行設定以 Lucidchart 佈建的使用者動作項目。  
當指派的使用者嘗試登入 Lucidchart 使用 access 面板時，檢查有 Lucidchart 使用者是否存在。  
如果不有任何使用者帳戶還，它就會自動建立 Lucidchart。
##<a name="assigning-users"></a>將使用者指派
  
若要測試您的設定，您需要授與 Azure AD 使用者想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-lucidchart-perform-the-following-steps"></a>若要指定 Lucidchart 使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **Lucidchart**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-lucidchart-tutorial/IC791197.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-lucidchart-tutorial/IC767830.png "[是]")
  
如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。