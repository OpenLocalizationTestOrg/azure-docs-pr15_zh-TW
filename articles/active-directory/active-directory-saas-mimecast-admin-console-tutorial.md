<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合 Mimecast 管理主控台 |Microsoft Azure" 
    description="瞭解如何使用 Mimecast 管理主控台與 Azure Active Directory 啟用單一登入、 自動化佈建和更多 ！" 
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

#<a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>教學課程︰ Azure Active Directory 整合 Mimecast 管理主控台
  
本教學課程中的目標是以顯示 Azure 和 Mimecast 管理主控台的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   Mimecast 管理主控台單一登入啟用訂閱
  
完成後本教學課程中，您指派給 Mimecast 管理主控台 Azure AD 使用者將能夠單一登入 Mimecast 管理主控台公司網站 （服務提供者發起的租用戶登入），或使用[[存取面板簡介](active-directory-saas-access-panel-introduction.md)應用程式。
  
本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用應用程式適用於整合 Mimecast 管理主控台
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795008.png "案例")
##<a name="enabling-the-application-integration-for-mimecast-admin-console"></a>啟用應用程式適用於整合 Mimecast 管理主控台
  
本節的目標是大綱如何啟用應用程式適用於整合 Mimecast 管理主控台。

###<a name="to-enable-the-application-integration-for-mimecast-admin-console-perform-the-following-steps"></a>若要啟用 Mimecast 管理主控台應用程式整合，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-mimecast-admin-console-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-mimecast-admin-console-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-mimecast-admin-console-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-mimecast-admin-console-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**Mimecast 管理主控台**。

    ![應用程式組件庫](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795009.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**Mimecast 管理主控台**]，然後按一下**完成**新增應用程式。

    ![Mimecast 管理主控台](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795010.png "Mimecast 管理主控台")
##<a name="configuring-single-sign-on"></a>設定單一登入
  
本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD Mimecast 管理主控台。  
此程序的一部分，您所需建立 64 基本編碼的憑證檔案。  
如果您不熟悉這個程序，請參閱[如何將轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站， **Mimecast 管理主控台**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795011.png "設定單一登入")

2.  在**您要如何登入 Mimecast 管理主控台的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795012.png "設定單一登入")

3.  **設定應用程式 URL**在頁面上， **Mimecast 管理主控台登入 URL** ] 文字方塊中，輸入您的使用者用來登入 Mimecast 管理主控台應用程式的 URL (例如: 「 https://webmail-uk.mimecast.com 」 或 「 https://webmail-us.mimecast.com 」)，然後按一下 [**下一步**。

    >[AZURE.NOTE] 登入 URL 是特定的區域。

    ![設定應用程式 URL](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795013.png "設定應用程式 URL")

4.  在**設定單一登入 Mimecast 管理主控台在**頁面上，若要下載您的憑證，按一下 [**下載憑證**] 並儲存在本機電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795014.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Mimecast 管理主控台。

6.  移至 [**服務\>應用程式**。

    ![服務](./media/active-directory-saas-mimecast-admin-console-tutorial/IC794998.png "服務")

7.  按一下 [**驗證設定檔**]。

    ![驗證設定檔](./media/active-directory-saas-mimecast-admin-console-tutorial/IC794999.png "驗證設定檔")

8.  按一下 [**新的驗證設定檔**]。

    ![新的驗證設定檔](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795000.png "新的驗證設定檔")

9.  在 [**驗證設定檔**] 區段中，執行下列步驟︰

    ![驗證設定檔](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795015.png "驗證設定檔")

    1.  在 [**描述**] 文字方塊中，輸入您的設定的名稱。
    2.  選取 [**強制執行 Mimecast 管理主控台 SAML 驗證**]。
    3.  為**提供者**，請選取 [ **Azure Active Directory**]。
    4.  在 Azure 的傳統入口網站中**設定單一登入在 Mimecast 管理主控台**] 對話方塊在頁面上，複製**簽發者 URL**的值，，然後再貼到 [**簽發者 URL** ] 文字方塊。
    5.  在 Azure 的傳統入口網站中**設定單一登入在 Mimecast 管理主控台**] 對話方塊在頁面上，將**遠端登入 URL**值，複製，然後再貼到 [**登入 URL** ] 文字方塊。
    6.  在 Azure 的傳統入口網站中**設定單一登入在 Mimecast 管理主控台**] 對話方塊在頁面上，複製 [**遠端登入 URL**的值，，然後再貼到 [**登出 URL** ] 文字方塊。  

        >[AZURE.NOTE]登入 URL 值和登出 URL 值是 Mimecast 管理主控台相同。

    7.  建立**64 基本編碼**檔案從您下載的憑證。  

        >[AZURE.TIP]如需詳細資訊，請參閱[如何將轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)。

    8.  開啟您 64 基本編碼的憑證在記事本中，移除的第一行 (「*--*」) 和最後一列 (「*--*」)，將剩餘的其內容複製到剪貼簿，然後將其貼到**身分識別提供者憑證 （中繼資料）**文字方塊。
    9.  選取 [**允許單一登入**]。
    10. 按一下 [**儲存**]。

10. 在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795016.png "設定單一登入")
##<a name="configuring-user-provisioning"></a>設定使用者佈建
  
若要啟用 Azure AD 使用者登入 Mimecast 管理主控台，他們必須佈建到 Mimecast 管理主控台。  
若是 Mimecast 管理主控台，佈建是手動的工作。
  
您需要註冊網域，才能建立使用者。

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，執行下列步驟︰

1.  以系統管理員身分登入您的**Mimecast 管理主控台**。

2.  移至 [**目錄\>內部**。

    ![目錄](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795003.png "目錄")

3.  按一下 [**註冊新網域**]。

    ![註冊新的網域](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795004.png "註冊新的網域")

4.  建立您的新網域之後，請按一下 [**新的地址**]。

    ![新的地址](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795005.png "新的地址")

5.  在新的地址] 對話方塊中，執行下列步驟︰

    ![儲存](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795006.png "儲存")

    1.  輸入您想要在相關的文字方塊佈建有效 AAD 帳戶的**電子郵件地址**、**全域的名稱**、**密碼**並**確認密碼**屬性。
    2.  按一下 [**儲存**]。

>[AZURE.NOTE]您可以使用任何其他 Mimecast 管理主控台使用者帳戶建立工具] 或 [Mimecast 管理主控台佈建 AAD 使用者帳戶所提供的 Api。

##<a name="assigning-users"></a>將使用者指派
  
若要測試您的設定，您需要授與 Azure AD 使用者想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-mimecast-admin-console-perform-the-following-steps"></a>若要指定 Mimecast 管理主控台使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **Mimecast 管理主控台**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-mimecast-admin-console-tutorial/IC795017.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-mimecast-admin-console-tutorial/IC767830.png "[是]")
  
如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。