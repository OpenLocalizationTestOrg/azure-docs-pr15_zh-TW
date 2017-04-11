<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合 FM︰ 系統 |Microsoft Azure" 
    description="瞭解如何使用 FM︰ 若要啟用單一登入、 自動化佈建和更多的 Azure Active Directory 系統 ！" 
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

#<a name="tutorial-azure-active-directory-integration-with-fm-systems"></a>教學課程︰ Azure Active Directory 整合 FM︰ 系統
  
本教學課程中的目標是以顯示 Azure 和 FM:Systems 的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   FM:Systems 單一登入啟用訂閱
  
完成後本教學課程中，您指派給 FM:Systems Azure AD 使用者將能夠單一登入 FM:Systems 公司網站 （服務提供者發起的租用戶登入），或使用[[存取面板簡介](active-directory-saas-access-panel-introduction.md)應用程式。
  
本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用 FM:Systems 應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-fm-systems-tutorial/IC795899.png "案例")
##<a name="enabling-the-application-integration-for-fmsystems"></a>啟用 FM:Systems 應用程式整合
  
本節的目標是大綱如何啟用 FM:Systems 的整合應用程式。

###<a name="to-enable-the-application-integration-for-fmsystems-perform-the-following-steps"></a>若要啟用的 FM:Systems 整合應用程式，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-fm-systems-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-fm-systems-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-fm-systems-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-fm-systems-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**FM:Systems**。

    ![應用程式組件庫](./media/active-directory-saas-fm-systems-tutorial/IC795900.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**FM:Systems**，，然後按一下要新增應用程式**完成**。

    ![FM︰ 系統](./media/active-directory-saas-fm-systems-tutorial/IC800213.png "FM︰ 系統")
##<a name="configuring-single-sign-on"></a>設定單一登入
  
本節的目標是大綱如何啟用中使用根據 SAML 通訊協定的同盟 Azure AD FM:Systems 以帳戶驗證的使用者。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站， **FM:Systems**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-fm-systems-tutorial/IC790810.png "設定單一登入")

2.  在**您要如何登入 FM:Systems 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-fm-systems-tutorial/IC795901.png "設定單一登入")

3.  **設定應用程式 URL**在頁面上，執行下列步驟︰

    ![設定應用程式 URL](./media/active-directory-saas-fm-systems-tutorial/IC795902.png "設定應用程式 URL")

    1.  在 [ **FM:Systems 登入 URL** ] 文字方塊中，輸入您 FM:Systems**回覆 URL** (例如︰ *https://dpr.fmshosted.com/fminteract/ConsumerService2.aspx*)。  

        >[AZURE.WARNING] 您可以從您 FM 取得此值︰ 系統支援小組。

    2.  按一下 [**下一步**

4.  在**設定單一登入 FM:Systems 在**頁面上，以下載您的中繼資料，請按一下**下載的中繼資料**]，然後儲存您的電腦上的 [中繼資料。

    ![設定單一登入](./media/active-directory-saas-fm-systems-tutorial/IC795903.png "設定單一登入")

5.  提交您 FM 下載的中繼資料檔案︰ 系統支援小組。

    >[AZURE.NOTE] 您 FM︰ 若要執行的實際 SSO 設定具有系統支援小組。
當 SSO 已啟用您的訂閱，您會收到通知。

6.  在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-fm-systems-tutorial/IC795904.png "設定單一登入")
##<a name="configuring-user-provisioning"></a>設定使用者佈建
  
若要啟用 Azure AD 使用者登入 FM:Systems，他們必須佈建到 FM:Systems。  
若是 FM:Systems，佈建是手動的工作。

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，執行下列步驟︰

1.  在網頁瀏覽器視窗中，以系統管理員身分登入您 FM:Systems 公司的網站。

2.  移至 [**系統管理\>管理安全性\>使用者\>使用者清單**。

    ![系統管理](./media/active-directory-saas-fm-systems-tutorial/IC795905.png "系統管理")

3.  按一下 [**建立新的使用者**]。

    ![建立新的使用者](./media/active-directory-saas-fm-systems-tutorial/IC795906.png "建立新的使用者")

4.  在 [**建立使用者**] 區段中，執行下列步驟︰

    ![建立使用者](./media/active-directory-saas-fm-systems-tutorial/IC795907.png "建立使用者")

    1.  輸入使用者名稱、 密碼並確認其、 電子郵件地址和有效的 Azure Active Directory 帳戶，您想要佈建到相關的文字方塊的員工識別碼。
    2.  按一下 [**下一步**]。

>[AZURE.NOTE] 您可以使用任何其他 FM:Systems 使用者帳戶建立工具或 Api 提供 FM:Systems 佈建 AAD 使用者帳戶。

##<a name="assigning-users"></a>將使用者指派
  
若要測試您的設定，您需要授與 Azure AD 使用者想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-fmsystems-perform-the-following-steps"></a>若要指定 FM:Systems 使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **FM:Systems**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-fm-systems-tutorial/IC795908.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-fm-systems-tutorial/IC767830.png "[是]")
  
如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。