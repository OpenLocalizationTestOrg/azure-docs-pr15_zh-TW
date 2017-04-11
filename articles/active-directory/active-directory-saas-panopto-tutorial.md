<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合 Panopto |Microsoft Azure" 
    description="瞭解如何使用 Panopto 與 Azure Active Directory 啟用單一登入、 自動化佈建和更多 ！" 
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

#<a name="tutorial-azure-active-directory-integration-with-panopto"></a>教學課程︰ 使用 Panopto 的 Azure Active Directory 整合
  
本教學課程中的目標是以顯示 Azure 和 Panopto 的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   Panopto 租用戶
  
完成後本教學課程中，您指派給 Panopto Azure AD 使用者將能夠單一登入 Panopto 公司網站 （服務提供者發起的租用戶登入），或使用[[存取面板簡介](active-directory-saas-access-panel-introduction.md)應用程式。
  
本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用 Panopto 應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-panopto-tutorial/IC777665.png "案例")
##<a name="enabling-the-application-integration-for-panopto"></a>啟用 Panopto 應用程式整合
  
本節的目標是大綱如何啟用 Panopto 的整合應用程式。

###<a name="to-enable-the-application-integration-for-panopto-perform-the-following-steps"></a>若要啟用的 Panopto 整合應用程式，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-panopto-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-panopto-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-panopto-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-panopto-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**Panopto**。

    ![Appkication 圖庫](./media/active-directory-saas-panopto-tutorial/IC777666.png "Appkication 圖庫")

7.  在 [結果] 窗格中，選取**Panopto**，，然後按一下要新增應用程式**完成**。

    ![Panopto](./media/active-directory-saas-panopto-tutorial/IC782936.png "Panopto")
##<a name="configuring-single-sign-on"></a>設定單一登入
  
本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD Panopto。  
此程序的一部分，您所需建立 64 基本編碼的憑證檔案。  
如果您不熟悉這個程序，請參閱[如何將轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站， **Panopto**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-panopto-tutorial/IC777667.png "設定單一登入")

2.  在**您要如何登入 Panopto 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-panopto-tutorial/IC777668.png "設定單一登入")

3.  **設定應用程式 URL**在頁面上，在 [ **Panopto 登入 URL** ] 文字方塊中，輸入您使用下列模式的 URL 「*https://\<租用戶名稱\>。Panopto.com*」，然後按一下 [**下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-panopto-tutorial/IC777528.png "設定應用程式 URL")

4.  在**設定單一登入 Panopto 在**頁面上，若要下載您的憑證，按一下 [**下載憑證**] 並儲存在您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-panopto-tutorial/IC777669.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您 Panopto 公司的網站。

6.  在 [左側] 工具列，按一下 [**系統**]，然後按一下**身分識別提供者**。

    ![系統](./media/active-directory-saas-panopto-tutorial/IC777670.png "系統")

7.  按一下 [**新增提供者**。

    ![身分識別提供者](./media/active-directory-saas-panopto-tutorial/IC777671.png "身分識別提供者")

8.  在 [SAML 提供者] 區段中，執行下列步驟︰

    ![SaaS 設定](./media/active-directory-saas-panopto-tutorial/IC777672.png "SaaS 設定")

    1.  從 [**提供者類型**] 清單中，選取 [ **SAML20**
    2.  在 [**執行個體名稱**] 文字方塊中，輸入執行個體的名稱。
    3.  在 [**易記的描述**] 文字方塊中，輸入好記的描述。
    4.  在 Azure 的傳統入口網站中**設定單一登入，Panopto** ] 對話方塊在頁面上，複製**簽發者 URL**的值，，然後再貼到 [**簽發者**] 文字方塊。
    5.  在 Azure 的傳統入口網站中**設定單一登入，Panopto** ] 對話方塊在頁面上，複製 [ **SAML SSO URL**的值，，然後再貼到 [**彈入頁面的 Url** ] 文字方塊。
    6.  建立**64 基本編碼**檔案從您下載的憑證。  

        >[AZURE.TIP] 如需詳細資訊，請參閱[如何轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)

    7.  在記事本中開啟您 64 基本編碼的憑證，將其內容複製到剪貼簿，然後將其貼到**公開金鑰**文字方塊
    8.  按一下 [**儲存**]。
        ![儲存](./media/active-directory-saas-panopto-tutorial/IC777673.png "儲存")

9.  在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-panopto-tutorial/IC777674.png "設定單一登入")
##<a name="configuring-user-provisioning"></a>設定使用者佈建
  
沒有進行設定以 Panopto 佈建的使用者動作項目。  
當指派的使用者嘗試登入 Panopto 使用 access 面板時，檢查有 Panopto 使用者是否存在。  
如果不有任何使用者帳戶還，它就會自動建立 Panopto。

>[AZURE.NOTE]您可以使用任何其他 Panopto 使用者帳戶建立工具或 Api 提供 Panopto 佈建 Azure AD 使用者帳戶。

##<a name="assigning-users"></a>將使用者指派
  
若要測試您的設定，您需要授與 Azure AD 使用者您想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-panopto-perform-the-following-steps"></a>若要指定 Panopto 使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **Panopto**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-panopto-tutorial/IC777675.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-panopto-tutorial/IC767830.png "[是]")
  
如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。