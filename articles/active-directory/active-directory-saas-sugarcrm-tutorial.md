<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合整合 SugarCRM |Microsoft Azure" 
    description="瞭解如何使用 SugarCRM 與 Azure Active Directory 啟用單一登入，自動化佈建和更多 ！" 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-integration-with-sugarcrm"></a>教學課程︰ Azure Active Directory 整合 SugarCRM 整合
  
本教學課程中的目標是以顯示 Azure 和上的裝飾 CRM 的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   上的裝飾 CRM 單一登入啟用訂閱
  
完成後本教學課程中，您指派給上的裝飾 CRM Azure AD 使用者將能夠單一登入應用程式上的裝飾 CRM 的公司網站 （服務提供者發起的租用戶登入），或使用[[存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用上的裝飾 CRM 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-sugarcrm-tutorial/IC795881.png "案例")

##<a name="enabling-the-application-integration-for-sugar-crm"></a>啟用上的裝飾 CRM 的應用程式整合
  
本節的目標是大綱如何啟用上的裝飾 CRM 的整合應用程式。

###<a name="to-enable-the-application-integration-for-sugar-crm-perform-the-following-steps"></a>若要啟用上的裝飾 CRM 的應用程式整合，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-sugarcrm-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-sugarcrm-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-sugarcrm-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-sugarcrm-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**上的裝飾 CRM**。

    ![應用程式組件庫](./media/active-directory-saas-sugarcrm-tutorial/IC795882.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**上的裝飾 CRM**、，然後按一下要新增應用程式**完成**。

    ![上的裝飾 CRM](./media/active-directory-saas-sugarcrm-tutorial/IC795883.png "上的裝飾 CRM")

##<a name="configuring-single-sign-on"></a>設定單一登入
  
本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD 上的裝飾 CRM。  
此程序的一部分，您會需要上傳到您上的裝飾 CRM 租用戶的底數 64 編碼的憑證。  
如果您不熟悉這個程序，請參閱[如何轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 [Azure 傳統入口網站**上的裝飾 CRM**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-sugarcrm-tutorial/IC795884.png "設定單一登入")

2.  在**您要如何登入上的裝飾 CRM 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-sugarcrm-tutorial/IC795885.png "設定單一登入")

3.  在 [**設定應用程式 URL** ] 頁面**上的裝飾 CRM 登入 URL** ] 文字方塊中，輸入您登入應用程式上的裝飾 CRM 的使用者所使用的 URL (例如: 「*http://company.sugarondemand.com*」，然後按一下 [**下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-sugarcrm-tutorial/IC795886.png "設定應用程式 URL")

4.  在**設定單一登入上的裝飾 CRM 在**頁面上，若要下載您的憑證，按一下 [**下載憑證**] 並儲存在您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-sugarcrm-tutorial/IC796918.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您上的裝飾 CRM 公司的網站。

6.  移至 [**管理員**。

    ![管理員](./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "管理員")

7.  在 [**管理**] 區段中，按一下 [**密碼管理**]。

    ![管理](./media/active-directory-saas-sugarcrm-tutorial/IC795889.png "管理")

8.  選取 [**啟用 SAML 驗證**。

    ![管理](./media/active-directory-saas-sugarcrm-tutorial/IC795890.png "管理")

9.  在 [ **SAML 驗證**] 區段中，執行下列步驟︰

    ![SAML 驗證](./media/active-directory-saas-sugarcrm-tutorial/IC795891.png "SAML 驗證")

    1.  在 Azure 的傳統入口網站中**設定單一登入在上的裝飾 CRM** ] 對話方塊在頁面上，複製 [**遠端登入 URL**的值，，然後再貼到 [**登入 URL** ] 文字方塊。
    2.  在 Azure 的傳統入口網站中**設定單一登入在上的裝飾 CRM** ] 對話方塊在頁面上，複製 [**遠端登入 URL**的值，，然後再貼到 [ **SLO URL** ] 文字方塊。
    3.  建立**64 基本編碼**檔案從您下載的憑證。

        >[AZURE.TIP] 如需詳細資訊，請參閱[如何轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)

    4.  在記事本中開啟您 64 基本編碼的憑證，將其內容複製到剪貼簿，並貼整個憑證**X.509 憑證**文字方塊。
    5.  按一下 [**儲存**]。

10. 在 Azure 的傳統入口網站中**設定單一登入在上的裝飾 CRM** ] 對話方塊在頁面上，選取單一登入設定確認，然後按 [**完成**。

    ![設定單一登入](./media/active-directory-saas-sugarcrm-tutorial/IC796919.png "設定單一登入")

##<a name="configuring-user-provisioning"></a>設定使用者佈建
  
若要啟用 Azure AD 使用者登入上的裝飾 CRM，他們必須提供給上的裝飾 CRM。  
若是上的裝飾 CRM 佈建是手動的工作。

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>佈建使用者帳戶，請執行下列步驟︰

1.  以系統管理員身分登入您**上的裝飾 CRM**公司的網站。

2.  移至 [**管理員**。

    ![管理員](./media/active-directory-saas-sugarcrm-tutorial/IC795888.png "管理員")

3.  在 [**管理**] 區段中，按一下 [**使用者管理**]。

    ![管理](./media/active-directory-saas-sugarcrm-tutorial/IC795893.png "管理")

4.  移至 [**使用者\>建立新的使用者**。

    ![建立新的使用者](./media/active-directory-saas-sugarcrm-tutorial/IC795894.png "建立新的使用者")

5.  在 [**使用者設定檔**] 索引標籤中，執行下列步驟︰

    ![新的使用者](./media/active-directory-saas-sugarcrm-tutorial/IC795895.png "新的使用者")

    1.  使用者名稱，最後一個名稱和電子郵件地址是有效的 Azure Active Directory 使用者輸入相關的文字方塊。

6.  [**狀態**] 中，選取 [**作用中**。

7.  在 [密碼] 索引標籤中，執行下列步驟︰

    ![新的使用者](./media/active-directory-saas-sugarcrm-tutorial/IC795896.png "新的使用者")

    1.  在相關的文字方塊中輸入密碼。
    2.  按一下 [**儲存**]。

>[AZURE.NOTE] 您可以使用任何其他上的裝飾 CRM 使用者帳戶建立工具或 Api 提供上的裝飾 CRM 佈建 AAD 使用者帳戶。

##<a name="assigning-users"></a>將使用者指派
  
若要測試您的設定，您需要授與 Azure AD 使用者想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-sugar-crm-perform-the-following-steps"></a>若要指定上的裝飾 CRM 使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  在**上的裝飾 CRM**應用程式整合頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-sugarcrm-tutorial/IC795897.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-sugarcrm-tutorial/IC767830.png "[是]")
  
如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。