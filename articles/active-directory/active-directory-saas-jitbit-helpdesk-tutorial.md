<properties 
    pageTitle="教學課程︰ 使用 Jitbit 技術服務的 Azure Active Directory 整合 |Microsoft Azure" 
    description="瞭解如何使用 Azure Active Directory 中的 Jitbit 技術服務，來啟用單一登入、 自動化佈建和更多 ！" 
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

#<a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>教學課程︰ Azure Active Directory 整合 Jitbit 技術服務
  
本教學課程中的目標是以顯示 Azure 和 Jitbit 技術服務的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   Jitbit 服務台租用戶
  
完成後本教學課程中，您指派給 Jitbit 服務台 Azure AD 使用者將能夠單一登入 Jitbit 服務台公司網站 （服務提供者發起的租用戶登入），或使用[[存取面板簡介](active-directory-saas-access-panel-introduction.md)應用程式。
  
本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用 Jitbit 技術服務應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777676.png "案例")
##<a name="enabling-the-application-integration-for-jitbit-helpdesk"></a>啟用 Jitbit 技術服務應用程式整合
  
本節的目標是大綱如何啟用 Jitbit 技術服務應用程式整合。

###<a name="to-enable-the-application-integration-for-jitbit-helpdesk-perform-the-following-steps"></a>若要啟用 Jitbit 技術服務應用程式整合，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**Jitbit 技術服務**。

    ![應用程式組件庫](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777677.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**Jitbit 技術服務**，，然後按一下要新增應用程式**完成**。

    ![JitBit](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC781008.png "JitBit")
##<a name="configuring-single-sign-on"></a>設定單一登入
  
本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD Jitbit 技術服務。 .  
此程序的一部分，您所需建立 64 基本編碼的憑證檔案。  
如果您不熟悉這個程序，請參閱[如何將轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)。

>[AZURE.IMPORTANT] 才能設定單一登入您的 Jitbit 服務台租用戶，您需要先連絡 Jitbit 服務台技術支援，以取得啟用此功能。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站， **Jitbit 技術服務**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777678.png "設定單一登入")

2.  在**您要如何登入 Jitbit 技術服務的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777679.png "設定單一登入")

3.  **設定應用程式 URL**在頁面上，在 [ **Jitbit 技術服務登入 URL** ] 文字方塊中，輸入您使用下列模式的 URL 「*https://\<租用戶名稱\>。Jitbit.com*」，然後按一下 [**下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777528.png "設定應用程式 URL")

4.  在**設定單一登入 Jitbit 技術服務在**頁面上，若要下載您的憑證，按一下**下載憑證**]，然後憑證檔案儲存至本機為**c:\\Jitbit Helpdesk.cer**。

    ![設定單一登入](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777680.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您 Jitbit 技術服務公司的網站。

6.  在頂端上工具列中，按一下 [**管理**]。

    ![管理](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "管理")

7.  按一下 [**一般設定**]。

    ![使用者、 公司與權限](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "使用者、 公司與權限")

8.  在 [**驗證設定**組態] 區段中，執行下列步驟︰

    ![驗證設定](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777683.png "驗證設定")

    1.  選取**單一的 [啟用 SAML 2.0 身分登入**登入**OneLogin**搭配使用單一登入 (SSO)。
    2.  Azure 傳統入口網站，在 [**設定單一登入在 Jitbit 服務台**對話] 頁面中的**服務提供者 (SP) 發起的租用戶結束點**的值，複製，然後再貼到 [**端點 URL** ] 文字方塊。
    3.  建立**64 基本編碼**檔案從您下載的憑證。
        
        >[AZURE.TIP]如需詳細資訊，請參閱[如何轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)

    4.  開啟您 64 基本編碼的憑證，將其內容複製到剪貼簿，然後將其貼到**X.509 憑證**文字方塊
    5.  按一下 [**儲存變更**]。

9.  在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777684.png "設定單一登入")
##<a name="configuring-user-provisioning"></a>設定使用者佈建
  
若要啟用 Azure AD 使用者登入 Jitbit 技術服務，他們必須佈建到 Jitbit 技術服務。  
若是 Jitbit 服務台佈建是手動的工作。

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>佈建使用者帳戶，請執行下列步驟︰

1.  登入您的**Jitbit 技術服務**租用戶。

2.  在頂端的功能表，按一下 [**管理**]。

    ![管理](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "管理")

3.  按一下 [**使用者與公司的權限**]。

    ![使用者、 公司與權限](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "使用者、 公司與權限")

4.  按一下 [**新增使用者**]。

    ![新增使用者](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777685.png "新增使用者")

5.  在 [建立] 區段中，輸入您想要在下列文字方塊佈建的 Azure AD 帳戶的資料︰**使用者名稱**、**電子郵件**、**名字**、**姓氏**

    ![建立](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777686.png "建立")

6.  按一下 [**建立**]。

>[AZURE.NOTE] 您可以使用任何其他 Jitbit 技術服務使用者帳戶建立工具或 Api 提供 Jitbit 技術服務佈建 AAD 使用者帳戶。

##<a name="assigning-users"></a>將使用者指派
  
若要測試您的設定，您需要授與 Azure AD 使用者想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-jitbit-helpdesk-perform-the-following-steps"></a>若要指定 Jitbit 技術服務使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **Jitbit 技術服務**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777687.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC767830.png "[是]")
  
如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。