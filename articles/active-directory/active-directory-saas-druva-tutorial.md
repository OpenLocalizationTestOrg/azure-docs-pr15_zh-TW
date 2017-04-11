<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合整合 Druva |Microsoft Azure" 
    description="瞭解如何使用 Druva 與 Azure Active Directory 啟用單一登入，自動化佈建和更多 ！" 
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

#<a name="tutorial-azure-active-directory-integration-integration-with-druva"></a>教學課程︰ Azure Active Directory 整合 Druva 整合

本教學課程中的目標是以顯示 Azure 和 Druva 的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   Druva 單一登入啟用訂閱

完成後本教學課程中，您指派給 Druva Azure AD 使用者將能夠單一登入 Druva 公司網站 （服務提供者發起的租用戶登入），或使用[[存取面板簡介](active-directory-saas-access-panel-introduction.md)應用程式。

本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用 Druva 應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-druva-tutorial/IC795084.png "案例")
##<a name="enabling-the-application-integration-for-druva"></a>啟用 Druva 應用程式整合

本節的目標是大綱如何啟用 Druva 的整合應用程式。

###<a name="to-enable-the-application-integration-for-druva-perform-the-following-steps"></a>若要啟用的 Druva 整合應用程式，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-druva-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-druva-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-druva-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-druva-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**Druva**。

    ![應用程式組件庫](./media/active-directory-saas-druva-tutorial/IC795085.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**Druva**，，然後按一下要新增應用程式**完成**。

    ![Druva](./media/active-directory-saas-druva-tutorial/IC795086.png "Druva")
##<a name="configuring-single-sign-on"></a>設定單一登入

本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD Druva。  
此程序的一部分，您所需建立 64 基本編碼的憑證檔案。  
如果您不熟悉這個程序，請參閱[如何將轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)。

Druva 應用程式的期待 SAML 判斷提示以特定格式會要求您將自訂屬性對應至**saml 權杖屬性**設定。  
以下螢幕擷取畫面會顯示這個範例。

![SAML 權杖屬性](./media/active-directory-saas-druva-tutorial/IC795087.png "SAML 權杖屬性")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站， **Druva**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-druva-tutorial/IC795027.png "設定單一登入")

2.  在**您要如何登入 Druva 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-druva-tutorial/IC795088.png "設定單一登入")

3.  在 [**設定應用程式 URL** ] 頁面**Druva 登入 URL** ] 文字方塊中，輸入您的使用者用來登入 Druva 應用程式的 URL (例如: 「*https://cloud.druva.com/home/*」)，然後按一下 [**下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-druva-tutorial/IC795089.png "設定應用程式 URL")

4.  在**設定單一登入 Druva 在**頁面上，若要下載您的憑證，按一下 [**下載憑證**] 並儲存在本機電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-druva-tutorial/IC795090.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您 Druva 公司的網站。

6.  移至 [**管理\>設定**。

    ![設定](./media/active-directory-saas-druva-tutorial/IC795091.png "設定")

7.  在單一登入設定] 對話方塊中，執行下列步驟︰

    ![Singl 登入設定](./media/active-directory-saas-druva-tutorial/IC795092.png "Singl 登入設定")

    1.  Azure 傳統入口網站中，**設定單一登入，Druva** ] 對話方塊在頁面上，將**遠端登入 URL**值，複製，然後再貼到 [**識別碼提供者登入 URL** ] 文字方塊。
    2.  在 Azure 的傳統入口網站中**設定單一登入，Druva** ] 對話方塊在頁面上，複製 [**遠端登出 URL**的值，，然後再貼到 [**識別碼提供者登出 URL** ] 文字方塊。
    3.  建立**64 基本編碼**檔案從您下載的憑證。  

        >[AZURE.TIP] 如需詳細資訊，請參閱[如何轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)

    4.  在記事本中開啟您 64 基本編碼的憑證，將其內容複製到剪貼簿，然後將其貼到**識別碼提供者的憑證**文字方塊
    5.  若要開啟 [**設定**] 頁面，按一下 [**儲存**]。

8.  在 [**設定**] 頁面上，按一下 [**產生 SSO Token**。

    ![設定](./media/active-directory-saas-druva-tutorial/IC795093.png "設定")

9.  在**單一登入驗證 Token**對話方塊中，執行下列步驟︰

    ![SSO 權杖](./media/active-directory-saas-druva-tutorial/IC795094.png "SSO 權杖")

    1.  按一下 [**複製**]。
    2.  按一下 [**關閉**]。

10. 在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-druva-tutorial/IC795095.png "設定單一登入")

11. 在頂端的功能表，按一下 [**屬性**開啟 [ **SAML 權杖屬性**] 對話方塊。

    ![屬性](./media/active-directory-saas-druva-tutorial/IC795096.png "屬性")

12. 若要新增必要的屬性對應，請執行下列步驟︰

  	|屬性名稱|屬性值|
  	|---|---|
  	|同步\_驗證\_權杖|<*剪貼簿] 的值*>|

    1.  每個資料列以上表格中，按一下 [**新增使用者屬性**]。
    2.  在 [**屬性名稱**] 文字方塊中，輸入顯示該資料列的屬性名稱。
    3.  在 [**屬性值**] 文字方塊中輸入顯示該資料列的屬性值。
    4.  按一下 [**完成**]。

13. 按一下 [**套用變更**]。
##<a name="configuring-user-provisioning"></a>設定使用者佈建

若要啟用 Azure AD 使用者登入 Druva，他們必須佈建到 Druva。  
若是 Druva，佈建是手動的工作。

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，執行下列步驟︰

1.  以系統管理員身分登入您**Druva**公司的網站。

2.  移至 [**管理\>使用者**。

    ![管理使用者](./media/active-directory-saas-druva-tutorial/IC795097.png "管理使用者")

3.  按一下 [**建立新的**]。

    ![管理使用者](./media/active-directory-saas-druva-tutorial/IC795098.png "管理使用者")

4.  在 [建立新的使用者] 對話方塊中，執行下列步驟︰

    ![建立 NewUser](./media/active-directory-saas-druva-tutorial/IC795099.png "建立 NewUser")

    1.  輸入電子郵件地址] 和 [有效的 Azure Active Directory 使用者帳戶，您想要佈建到相關的文字方塊的名稱。
    2.  按一下 [**建立使用者**]。

>[AZURE.NOTE] 您可以使用任何其他 Druva 使用者帳戶建立工具或 Api 提供 Druva 佈建 AAD 使用者帳戶。

##<a name="assigning-users"></a>將使用者指派

若要測試您的設定，您需要授與 Azure AD 使用者您想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-druva-perform-the-following-steps"></a>若要指定 Druva 使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **Druva**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-druva-tutorial/IC795100.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-druva-tutorial/IC767830.png "[是]")

如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。
