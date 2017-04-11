<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合 AirWatch |Microsoft Azure" 
    description="瞭解如何使用 AirWatch 與 Azure Active Directory 啟用單一登入、 自動化佈建和更多 ！" 
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

#<a name="tutorial-azure-active-directory-integration-with-airwatch"></a>教學課程︰ 使用 AirWatch 的 Azure Active Directory 整合

本教學課程中的目標是以顯示 Azure 和 AirWatch 的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   AirWatch 單一登入啟用訂閱

完成後本教學課程中，您指派給 AirWatch Azure AD 使用者將能夠單一登入 AirWatch 公司網站 （服務提供者發起的租用戶登入），或使用[[存取面板簡介](active-directory-saas-access-panel-introduction.md)應用程式。

本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用 AirWatch 應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![AirWatch](./media/active-directory-saas-airwatch-tutorial/IC791913.png "AirWatch")
##<a name="enabling-the-application-integration-for-airwatch"></a>啟用 AirWatch 應用程式整合

本節的目標是大綱如何啟用 AirWatch 的整合應用程式。

###<a name="to-enable-the-application-integration-for-airwatch-perform-the-following-steps"></a>若要啟用的 AirWatch 整合應用程式，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-airwatch-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-airwatch-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-airwatch-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-airwatch-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**AirWatch**。

    ![應用程式組件庫](./media/active-directory-saas-airwatch-tutorial/IC791914.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**AirWatch**，，然後按一下要新增應用程式**完成**。

    ![AirWatch](./media/active-directory-saas-airwatch-tutorial/IC791915.png "AirWatch")
##<a name="configuring-single-sign-on"></a>設定單一登入

本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD AirWatch。  
此程序的一部分，您所需建立 64 基本編碼的憑證檔案。  
如果您不熟悉這個程序，請參閱[如何將轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站， **AirWatch**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-airwatch-tutorial/IC791916.png "設定單一登入")

2.  在**您要如何登入 AirWatch 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-airwatch-tutorial/IC791917.png "設定單一登入")

3.  **設定應用程式 URL**在頁面上，在**AirWatch 登入 URL** ] 文字方塊中輸入您用來登入您的 AirWatch 應用程式使用者的 URL (例如: 「*https:// companycode.awmdm.com/AirWatch/Login?gid=companycode*」)，然後按一下 [**下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-airwatch-tutorial/IC791918.png "設定應用程式 URL")

4.  在**設定單一登入 AirWatch 在**頁面上，按一下**下載憑證**]，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-airwatch-tutorial/IC791919.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您 AirWatch 公司的網站。

6.  在左側的功能窗格中，按一下 [**帳戶**]，然後按一下**系統管理員**。

    ![系統管理員](./media/active-directory-saas-airwatch-tutorial/IC791920.png "系統管理員")

7.  展開 [**設定**] 功能表，然後按一下 [**目錄服務**。

    ![設定](./media/active-directory-saas-airwatch-tutorial/IC791921.png "設定")

8.  按一下 [**使用者**] 索引標籤的 [**基本 DN** textfield 中，輸入您的網域名稱，再按一下 [**儲存**。

    ![使用者](./media/active-directory-saas-airwatch-tutorial/IC791922.png "使用者")

9.  按一下 [**伺服器**] 索引標籤。

    ![伺服器](./media/active-directory-saas-airwatch-tutorial/IC791923.png "伺服器")

10. 執行下列步驟︰

    ![上傳](./media/active-directory-saas-airwatch-tutorial/IC791924.png "上傳")

    1.  為**目錄類型**，請選取 [**無**]。
    2.  選取 [**使用 SAML 進行驗證**。
    3.  若要上傳下載的憑證，請按一下 [**上傳**]。

11. 在 [**邀請**] 區段中，執行下列步驟︰

    ![要求](./media/active-directory-saas-airwatch-tutorial/IC791925.png "要求")

    1.  為**要求繫結的類型**，選取 [**文章**]。
    2.  Azure 傳統入口網站，在 [**設定單一登入 Airwatch 在**對話方塊] 頁面中的**單一登入服務 URL**值，複製，然後再貼到 [**身分識別提供者單一登入 URL** ] 文字方塊。
    3.  為**NameID 格式**，請選取 [**電子郵件地址**]。
    4.  按一下 [**儲存**]。

12. 再按一下 [**使用者**] 索引標籤。

    ![使用者](./media/active-directory-saas-airwatch-tutorial/IC791926.png "使用者")

13. 在 [**屬性**] 區段中，執行下列步驟︰

    ![屬性](./media/active-directory-saas-airwatch-tutorial/IC791927.png "屬性")

    1.  在 [**物件識別碼**] 文字方塊中，輸入**http://schemas.microsoft.com/identity/claims/objectidentifier**。
    2.  在 [**使用者名稱**] 文字方塊中，輸入**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**。
    3.  在 [**顯示名稱**] 文字方塊中，輸入**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**。
    4.  在 [**名字**] 文字方塊中輸入**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**。
    5.  在 [**姓氏**] 文字方塊中輸入**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**。
    6.  在 [**電子郵件**] 文字方塊中，輸入**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**。
    7.  按一下 [**儲存**]。

14. 在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-airwatch-tutorial/IC791928.png "設定單一登入")
##<a name="configuring-user-provisioning"></a>設定使用者佈建

若要啟用 Azure AD 使用者登入 AirWatch，他們必須佈建到 AirWatch。  
若是 AirWatch，佈建是手動的工作。

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>佈建使用者帳戶，請執行下列步驟︰

1.  以系統管理員身分登入您**AirWatch**公司的網站。

2.  在左側功能窗格] 中按一下 [**帳戶**]，然後再按一下 [**使用者**。

    ![使用者](./media/active-directory-saas-airwatch-tutorial/IC791929.png "使用者")

3.  [**使用者**] 功能表中，按一下 [**清單檢視**]，然後按一下**新增\>新增使用者**。

    ![新增使用者](./media/active-directory-saas-airwatch-tutorial/IC791930.png "新增使用者")

4.  在 [**新增 / 編輯使用者**] 對話方塊中，執行下列步驟︰

    ![新增使用者](./media/active-directory-saas-airwatch-tutorial/IC791931.png "新增使用者")

    1.  輸入**使用者名稱**、**密碼**、**確認密碼**、**名字**、**姓氏**、**電子郵件地址**的有效的 Azure Active Directory 帳戶，您想要佈建到相關的文字方塊。
    2.  按一下 [**儲存**]。

>[AZURE.NOTE] 您可以使用任何其他 AirWatch 使用者帳戶建立工具或 Api 提供 AirWatch 佈建 AAD 使用者帳戶。

##<a name="assigning-users"></a>將使用者指派

若要測試您的設定，您需要授與 Azure AD 使用者您想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-airwatch-perform-the-following-steps"></a>若要指定 AirWatch 使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **AirWatch**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-airwatch-tutorial/IC791932.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-airwatch-tutorial/IC767830.png "[是]")

如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。
