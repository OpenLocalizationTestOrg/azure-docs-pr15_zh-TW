<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合 AnswerHub |Microsoft Azure" 
    description="瞭解如何使用 AnswerHub 與 Azure Active Directory 啟用單一登入、 自動化佈建和更多 ！" 
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
    ms.date="10/10/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-answerhub"></a>教學課程︰ 使用 AnswerHub 的 Azure Active Directory 整合

本教學課程中的目標是以顯示 Azure 和[AnswerHub](http://www.dzonesoftware.com/products/answerhub-question-answer-software)的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   [AnswerHub](http://www.dzonesoftware.com/products/answerhub-question-answer-software)單一登入啟用訂閱

完成後本教學課程中，您指派給 AnswerHub Azure AD 使用者將能夠單一登入 AnswerHub 公司網站 （服務提供者發起的租用戶登入），或使用[[存取面板簡介](active-directory-saas-access-panel-introduction.md)應用程式。

本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用 AnswerHub 應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-answerhub-tutorial/IC785165.png "案例")

##<a name="enabling-the-application-integration-for-answerhub"></a>啟用 AnswerHub 應用程式整合

本節的目標是大綱如何啟用 AnswerHub 的整合應用程式。

###<a name="to-enable-the-application-integration-for-answerhub-perform-the-following-steps"></a>若要啟用的 AnswerHub 整合應用程式，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-answerhub-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-answerhub-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-answerhub-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-answerhub-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**AnswerHub**。

    ![應用程式組件庫](./media/active-directory-saas-answerhub-tutorial/IC785166.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**AnswerHub**，，然後按一下要新增應用程式**完成**。

    ![AnswerHub](./media/active-directory-saas-answerhub-tutorial/IC785167.png "AnswerHub")

##<a name="configuring-single-sign-on"></a>設定單一登入

本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD AnswerHub。  
此程序的一部分，您所需建立 64 基本編碼的憑證檔案。  
如果您不熟悉這個程序，請參閱[如何轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站， **AnswerHub**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-answerhub-tutorial/IC785168.png "設定單一登入")

2.  在**您要如何登入 AnswerHub 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-answerhub-tutorial/IC785169.png "設定單一登入")

3.  **設定應用程式 URL**在頁面上，在 [ **AnswerHub 登入 URL** ] 文字方塊中，輸入您使用下列模式 」*https://company.answerhub.com*」 的 URL，然後再按 [**下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-answerhub-tutorial/IC785170.png "設定應用程式 URL")

4.  在**設定單一登入 AnswerHub 在**頁面上，若要下載您的憑證，按一下 [**下載憑證**] 並儲存在本機電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-answerhub-tutorial/IC785171.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您 AnswerHub 公司的網站。
    >[AZURE.NOTE] 如果您需要設定 AnswerHub 的說明，請連絡[AnswerHub 的支援小組](mailto:success@answerhub.com. )。








6.  移至 [**系統管理**。

7.  按一下 [**使用者和群組**] 索引標籤。

8.  在功能窗格中的左邊，在**社交設定**] 區段中，按一下 [ **SAML 設定**]。

9.  按一下**IDP 組態**] 索引標籤。

10. [ **IDP 組態**] 索引標籤中，執行下列步驟︰

    ![SAML 設定](./media/active-directory-saas-answerhub-tutorial/IC785172.png "SAML 設定")

    1.  Azure 傳統入口網站中，**設定單一登入，AnswerHub** ] 對話方塊在頁面上，將**遠端登入 URL**值，複製，然後再貼到 [ **IDP 登入 URL** ] 文字方塊。
    2.  在 Azure 的傳統入口網站中**設定單一登入，AnswerHub** ] 對話方塊在頁面上，複製 [**遠端登出 URL**的值，，然後再貼到 [ **IDP 登出 URL** ] 文字方塊。
    3.  Azure 傳統入口網站中，**設定單一登入，AnswerHub** ] 對話方塊在頁面上，將**名稱識別碼格式**值，複製，然後再貼到**IDP 名稱識別碼格式**文字方塊。
    4.  按一下 [**索引鍵和憑證**]。

11. 在索引鍵和憑證的索引標籤，請執行下列步驟︰

    ![索引鍵和憑證](./media/active-directory-saas-answerhub-tutorial/IC785173.png "索引鍵和憑證")

    1.  建立**64 基本編碼**檔案從您下載的憑證。  

        >[AZURE.TIP] 如需詳細資訊，請參閱[如何轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)

    2.  在記事本中開啟您 64 基本編碼的憑證、 將其內容複製到剪貼簿，並貼到**IDP 公開金鑰 (x 509 格式）**文字方塊。
    3.  按一下 [**儲存**]。

12. 在 [ **IDP 組態**] 索引標籤上按一下 [**儲存**]。

13. 在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-answerhub-tutorial/IC785174.png "設定單一登入")

##<a name="configuring-user-provisioning"></a>設定使用者佈建

若要啟用 Azure AD 使用者登入 AnswerHub，他們必須佈建到 AnswerHub。  
若是 AnswerHub，佈建是手動的工作。

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，執行下列步驟︰

1.  以系統管理員身分登入您**AnswerHub**公司的網站。

2.  移至 [**系統管理**。

3.  按一下 [**使用者和群組**] 索引標籤。

4.  在功能窗格中的左邊，在 [**管理使用者**] 區段中，按一下 [**建立或匯入的使用者**]。

    ![使用者和群組](./media/active-directory-saas-answerhub-tutorial/IC785175.png "使用者和群組")

5.  輸入****電子郵件地址**、 使用者名稱**和**密碼**的有效的 Azure Active Directory 帳戶，您想要佈建到相關的文字方塊，，然後再按一下 [**儲存**]。

>[AZURE.NOTE] 您可以使用任何其他 AnswerHub 使用者帳戶建立工具或 Api 提供 AnswerHub 佈建 AAD 使用者帳戶。

##<a name="assigning-users"></a>將使用者指派

若要測試您的設定，您需要授與 Azure AD 使用者您想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-answerhub-perform-the-following-steps"></a>若要指定 AnswerHub 使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **AnswerHub**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-answerhub-tutorial/IC785176.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-answerhub-tutorial/IC767830.png "[是]")

如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。
