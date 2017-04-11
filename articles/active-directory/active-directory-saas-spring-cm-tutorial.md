<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合春天公分 |Microsoft Azure" 
    description="瞭解如何使用 Azure Active Directory 中的春天公分，若要啟用單一登入、 自動化佈建和更多 ！" 
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

#<a name="tutorial-azure-active-directory-integration-with-spring-cm"></a>教學課程︰ 使用春天公分的 Azure Active Directory 整合
  
本教學課程中的目標是要如何設定 [單一登入 Azure Active Directory 和 SpringCM 之間。
  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   SpringCM 單一登入啟用訂閱
  
完成後本教學課程中，您指派給 SpringCM 的 Azure Active Directory 使用者將能夠單一登入使用 AAD 存取面板。

1.  啟用 SpringCM 應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-spring-cm-tutorial/IC797044.png "案例")

##<a name="enabling-the-application-integration-for-springcm"></a>啟用 SpringCM 應用程式整合
  
本節的目標是大綱如何啟用 SpringCM 的整合應用程式。

###<a name="to-enable-the-application-integration-for-springcm-perform-the-following-steps"></a>若要啟用的 SpringCM 整合應用程式，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-spring-cm-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-spring-cm-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-spring-cm-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-spring-cm-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**SpringCM**。

    ![應用程式組件庫](./media/active-directory-saas-spring-cm-tutorial/IC797045.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**SpringCM**，，然後按一下 [新增應用程式**完成**。

    ![SpringCM](./media/active-directory-saas-spring-cm-tutorial/IC797046.png "SpringCM")

##<a name="configuring-single-sign-on"></a>設定單一登入
  
本節說明如何讓使用者以 SpringCM 的 Azure Active Directory，使用 SAML 通訊協定所根據的同盟其帳戶驗證方法。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站， **SpringCM**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-spring-cm-tutorial/IC797047.png "設定單一登入")

2.  在**您要如何登入 SpringCM 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-spring-cm-tutorial/IC797048.png "設定單一登入")

3.  **設定應用程式 URL**在頁面上，在 [ **SpringCM 登入 URL** ] 文字方塊中，輸入您的使用者用來登入您的 SpringCM 應用程式的 URL，然後再按 [**下一步**。 

    應用程式 URL 是您 SpringCM 租用戶的 URL (例如︰ *https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=16826*):

    ![設定應用程式 URL](./media/active-directory-saas-spring-cm-tutorial/IC797049.png "設定應用程式 URL")

4.  在**設定單一登入 SpringCM 在**頁面上，若要下載您的憑證，按一下**下載憑證**]，然後將憑證檔案儲存到本機電腦。

    ![設定單一試用](./media/active-directory-saas-spring-cm-tutorial/IC797050.png "設定單一試用")

5.  在不同的網頁瀏覽器視窗中，登入**SpringCM**公司網站管理員。

6.  在頂端的功能表，按一下 [**移至**，按一下 [**喜好設定**]，然後在 [**帳戶喜好設定**] 區段中，按一下 [ **SAML SSO**。

    ![SAML SSO](./media/active-directory-saas-spring-cm-tutorial/IC797051.png "SAML SSO")

7.  在 [身分識別提供者組態] 區段中，執行下列步驟︰

    ![身分識別提供者設定](./media/active-directory-saas-spring-cm-tutorial/IC797052.png "身分識別提供者設定")

    1.  若要上傳您下載的 Azure Active Directory 憑證，請按一下 [**選取發行者憑證**或**變更發行者憑證**。
    2.  Azure 傳統入口網站中，在**設定單一登入 SpringCM 在**頁面上，複製**簽發者 URL**的值，，然後再貼到 [**簽發者**] 文字方塊。
    3.  Azure 傳統入口網站中，在**設定單一登入 SpringCM 在**頁面上，複製 [ **Singel 登入服務 URL**的值，，然後再貼到 [**服務提供者 (SP) 發起的租用戶的結束點**] 文字方塊。
    4.  為**SAML 啟用**，選取 [**啟用**]。
    5.  按一下 [**儲存**]。

8.  在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一試用](./media/active-directory-saas-spring-cm-tutorial/IC797053.png "設定單一試用")

##<a name="configuring-user-provisioning"></a>設定使用者佈建
  
若要啟用 Azure Active Directory 使用者登入 SpringCM，必須會佈建到 SpringCM。  
若是 SpringCM，佈建是手動的工作。

>[AZURE.NOTE] 如需詳細資訊，請參閱[建立及編輯 SpringCM 使用者](http://knowledge.springcm.com/create-and-edit-a-springcm-user)

###<a name="to-provision-a-user-account-to-springcm-perform-the-following-steps"></a>佈建 SpringCM 使用者帳戶，請執行下列步驟︰

1.  以系統管理員身分登入您**SpringCM**公司的網站。

2.  按一下 [**移至**，，然後按一下 [**通訊錄**。

    ![建立使用者](./media/active-directory-saas-spring-cm-tutorial/IC797054.png "建立使用者")

3.  按一下 [**建立使用者**]。

4.  選取 [**使用者角色**]。

5.  選取 [**啟動電子郵件傳送**]。

6.  輸入名字、 姓氏及電子郵件地址的有效的 Azure Active Directory 使用者帳戶，您想要佈建到相關的文字方塊。

7.  將使用者新增至**安全性群組**。

8.  按一下 [**儲存**]。

>[AZURE.NOTE] 您可以使用任何其他 SpringCM 使用者帳戶建立工具或 Api 提供 SpringCM 佈建 AAD 使用者帳戶。

##<a name="assigning-users"></a>將使用者指派
  
若要測試您的設定，您需要授與 Azure AD 使用者您想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-springcm-perform-the-following-steps"></a>若要指定 SpringCM 使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **SpringCM**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-spring-cm-tutorial/IC797055.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-spring-cm-tutorial/IC767830.png "[是]")
  
如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。




