<properties 
    pageTitle="教學課程︰ 使用 Adobe EchoSign 的 Azure Active Directory 整合 |Microsoft Azure" 
    description="瞭解如何使用 Adobe EchoSign 與 Azure Active Directory 啟用單一登入、 自動化佈建和更多 ！" 
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

#<a name="tutorial-azure-active-directory-integration-with-adobe-echosign"></a>教學課程︰ 使用 Adobe EchoSign 的 Azure Active Directory 整合

本教學課程中的目標是以顯示 Azure 和 Adobe EchoSign 的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   Adobe EchoSign 單一登入已啟用的訂閱

完成後本教學課程中，您指派給 Adobe EchoSign Azure AD 使用者將能夠單一登入 Adobe EchoSign 公司網站 （服務提供者發起的租用戶登入），或使用[[存取面板簡介](active-directory-saas-access-panel-introduction.md)應用程式。

本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用 Adobe EchoSign 的整合應用程式
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-adobe-echosign-tutorial/IC789511.png "案例")
##<a name="enabling-the-application-integration-for-adobe-echosign"></a>啟用 Adobe EchoSign 的整合應用程式

本節的目標是大綱如何啟用的 Adobe EchoSign 整合應用程式。

###<a name="to-enable-the-application-integration-for-adobe-echosign-perform-the-following-steps"></a>若要啟用的 Adobe EchoSign 整合應用程式，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-adobe-echosign-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-adobe-echosign-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-adobe-echosign-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-adobe-echosign-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**Adobe EchoSign**。

    ![應用程式組件庫](./media/active-directory-saas-adobe-echosign-tutorial/IC789514.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**Adobe EchoSign**，，然後按一下要新增應用程式**完成**。

    ![Adobe EchoSign](./media/active-directory-saas-adobe-echosign-tutorial/IC789515.png "Adobe EchoSign")
##<a name="configuring-single-sign-on"></a>設定單一登入

本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD Adobe EchoSign。  
此程序的一部分，您所需建立 64 基本編碼的憑證檔案。  
如果您不熟悉這個程序，請參閱[如何將轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站， **Adobe EchoSign**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-adobe-echosign-tutorial/IC789516.png "設定單一登入")

2.  在**您要如何登入 Adobe EchoSign 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-adobe-echosign-tutorial/IC789517.png "設定單一登入")

3.  **設定應用程式 URL**在頁面上，在 [ **Adobe EchoSign 登入 URL** ] 文字方塊中，輸入您使用下列模式 」*https://company.echosign.com/*」 的 URL，然後再按 [**下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-adobe-echosign-tutorial/IC789518.png "設定應用程式 URL")

4.  在**設定單一登入 Adobe EchoSign 在**頁面上，按一下**下載憑證**]，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-adobe-echosign-tutorial/IC789519.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Adobe EchoSign 公司網站。

6.  在頂端的功能表，按一下 [**帳戶**]，然後，在左側骰子功能窗格] 中按一下**SAML 設定**在 [**帳戶設定**。

    ![帳戶](./media/active-directory-saas-adobe-echosign-tutorial/IC789520.png "帳戶")

7.  在 [SAML 設定] 區段中，執行下列步驟︰

    ![SAML 設定](./media/active-directory-saas-adobe-echosign-tutorial/IC789521.png "SAML 設定")

    1.  做為**SAML 模式**中，選取 [**強制 SAML**]。
    2.  選取 [**允許 EchoSign 帳戶管理員使用其 EchoSign 認證登入**]。
    3.  為**使用者建立**]，選取 [**自動新增 [透過 SAML 驗證的使用者**]。

8.  移動上，執行下列步驟︰

    ![SAML 設定](./media/active-directory-saas-adobe-echosign-tutorial/IC789522.png "SAML 設定")

    1.  Azure 傳統入口網站，在 [**設定單一登入在 Adobe EchoSign**對話方塊] 頁面中的**實體識別碼**值，複製，然後再貼到 [ **IdP 實體 ID** ] 文字方塊。
    2.  Azure 傳統入口網站中，**設定單一登入在 Adobe EchoSign** ] 對話方塊在頁面上，將**遠端登入 URL**值，複製，然後再貼到 [ **IdP 登入 URL** ] 文字方塊。
    3.  在 Azure 的傳統入口網站中**設定單一登入在 Adobe EchoSign** ] 對話方塊在頁面上，複製 [**遠端登出 URL**的值，，然後再貼到 [ **IdP 登出 URL** ] 文字方塊。
    4.  建立**64 基本編碼**檔案從您下載的憑證。  

        >[AZURE.TIP] 如需詳細資訊，請參閱[如何轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)
 5。  在記事本中開啟您 64 基本編碼的憑證，將其內容複製到剪貼簿，並貼到**IdP 憑證**文字方塊 6。  按一下 [**儲存變更**]。

9.  在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-adobe-echosign-tutorial/IC789523.png "設定單一登入")
##<a name="configuring-user-provisioning"></a>設定使用者佈建

若要啟用 Azure AD 使用者登入 Adobe EchoSign，必須會佈建到 Adobe EchoSign。  
若是 Adobe EchoSign 佈建是手動的工作。

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>佈建使用者帳戶，請執行下列步驟︰

1.  以系統管理員身分登入您的**Adobe EchoSign**公司網站。

2.  在頂端的功能表，按一下 [**帳戶**]，然後在左側骰子功能窗格] 中按一下 [**使用者和群組**]，然後按一下 [**建立新的使用者**。

    ![帳戶](./media/active-directory-saas-adobe-echosign-tutorial/IC789524.png "帳戶")

3.  在 [**建立新的使用者**] 區段中，執行下列步驟︰

    ![建立使用者](./media/active-directory-saas-adobe-echosign-tutorial/IC789525.png "建立使用者")

    1.  輸入**電子郵件地址**、**名字**和**姓氏**有效 AAD 您想要的帳戶佈建到相關的文字方塊。
    2.  按一下 [**建立使用者**]。

        >[AZURE.NOTE] Azure Active Directory 帳戶擁有者會收到包含確認帳戶，再會變成作用中連結的電子郵件。

>[AZURE.NOTE] 您可以使用任何其他 Adobe EchoSign 使用者帳戶建立工具或 Api 提供 Adobe EchoSign 佈建 AAD 使用者帳戶。

##<a name="assigning-users"></a>將使用者指派

若要測試您的設定，您需要授與 Azure AD 使用者想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-adobe-echosign-perform-the-following-steps"></a>若要指定 Adobe EchoSign 使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **Adobe EchoSign**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-adobe-echosign-tutorial/IC789526.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-adobe-echosign-tutorial/IC767830.png "[是]")

如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。
