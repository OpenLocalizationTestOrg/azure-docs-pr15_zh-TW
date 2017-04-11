<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合 LogicMonitor |Microsoft Azure" 
    description="瞭解如何使用 LogicMonitor 與 Azure Active Directory 啟用單一登入、 自動化佈建和更多 ！" 
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

#<a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>教學課程︰ 使用 LogicMonitor 的 Azure Active Directory 整合
  
本教學課程中的目標是以顯示 Azure 和 LogicMonitor 的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   LogicMonitor 租用戶
  
本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用 LogicMonitor 應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-logicmonitor-tutorial/IC790045.png "案例")
##<a name="enabling-the-application-integration-for-logicmonitor"></a>啟用 LogicMonitor 應用程式整合
  
本節的目標是大綱如何啟用 LogicMonitor 的整合應用程式。

###<a name="to-enable-the-application-integration-for-logicmonitor-perform-the-following-steps"></a>若要啟用的 LogicMonitor 整合應用程式，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-logicmonitor-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-logicmonitor-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-logicmonitor-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-logicmonitor-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**logicmonitor**。

    ![應用程式組件庫](./media/active-directory-saas-logicmonitor-tutorial/IC790046.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**LogicMonitor**，，然後按一下要新增應用程式**完成**。

    ![LogicMonitor](./media/active-directory-saas-logicmonitor-tutorial/IC790047.png "LogicMonitor")
##<a name="configuring-single-sign-on"></a>設定單一登入
  
本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD LogicMonitor。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站， **LogicMonitor**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-logicmonitor-tutorial/IC790048.png "設定單一登入")

2.  在**您要如何登入 LogicMonitor 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-logicmonitor-tutorial/IC790049.png "設定單一登入")

3.  **設定應用程式 URL**在頁面上，在 [**登入 URL** ] 文字方塊中，輸入您的 URL，讓使用者用來登 LogicMonitor \(e、 g、: 「*http://company.logicmonitor.com*」\)，然後按一下 [**下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-logicmonitor-tutorial/IC790050.png "設定應用程式 URL")

4.  在**設定單一登入 LogicMonitor 在**頁面上，按一下**下載的中繼資料**]，然後將其儲存在您的電腦上。

    ![設定單一登入](./media/active-directory-saas-logicmonitor-tutorial/IC790051.png "設定單一登入")

5.  以系統管理員身分登入您**LogicMonitor**公司的網站。

6.  在頂端的功能表，按一下 [**設定**]。

    ![設定](./media/active-directory-saas-logicmonitor-tutorial/IC790052.png "設定")

7.  在左側導覽列，按一下 [**單一登入**

    ![單一登入](./media/active-directory-saas-logicmonitor-tutorial/IC790053.png "單一登入")

8.  在**單一登入 (SSO) 設定**] 區段中，執行下列步驟︰

    ![單一登入設定](./media/active-directory-saas-logicmonitor-tutorial/IC790054.png "單一登入設定")

    1.  選取 [**啟用單一登入**]。
    2.  為**預設角色指派**中，選取 [**唯讀**]。
    3.  在 [記事本] 開啟下載的中繼資料檔案，然後貼入 [**身分識別提供者中繼資料**] 文字方塊中的檔案的內容。
    4.  按一下 [**儲存變更**]。

9.  在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-logicmonitor-tutorial/IC790055.png "設定單一登入")
##<a name="configuring-user-provisioning"></a>設定使用者佈建
  
針對 AAD 都能登入的使用者，他們必須佈 LogicMonitor 應用程式使用他們的 Azure Active Directory 使用者名稱。

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，執行下列步驟︰

1.  以系統管理員身分登入您 LogicMonitor 公司的網站。

2.  在頂端的功能表，按一下 [**設定**]，然後按一下**角色和使用者**。

    ![角色和使用者](./media/active-directory-saas-logicmonitor-tutorial/IC790056.png "角色和使用者")

3.  按一下 [**新增**]。

4.  在 [**新增帳戶**] 區段中，執行下列步驟︰

    ![新增帳戶](./media/active-directory-saas-logicmonitor-tutorial/IC790057.png "新增帳戶")

    1.  輸入您想要佈建到相關的文字方塊的 Azure Active Directory 使用者的**使用者名稱**、**電子郵件**、**密碼**及**重新輸入密碼**的值。
    2.  選取 [**角色**與**檢視權限**的 [**狀態**]。
    3.  按一下 [**送出**]。

>[AZURE.NOTE]您可以使用任何其他 LogicMonitor 使用者帳戶建立工具或 Api 提供 LogicMonitor 進行佈建 Azure Active Directory 使用者帳戶。

##<a name="assigning-users"></a>將使用者指派
  
若要測試您的設定，您需要授與 Azure AD 使用者您想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-logicmonitor-perform-the-following-steps"></a>若要指定 LogicMonitor 使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **LogicMonitor**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-logicmonitor-tutorial/IC790058.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-logicmonitor-tutorial/IC767830.png "[是]")
  
如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。




