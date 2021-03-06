<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合 Citrix ShareFile |Microsoft Azure" 
    description="瞭解如何使用 Citrix ShareFile 與 Azure Active Directory 啟用單一登入、 自動化佈建和更多 ！" 
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

#<a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>教學課程︰ 使用 Citrix ShareFile 的 Azure Active Directory 整合

本教學課程中的目標是以顯示 Azure 和 Citrix ShareFile 的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   Citrix ShareFile 租用戶

完成後本教學課程中，您指派給 Citrix ShareFile Azure AD 使用者將能夠單一登入 Citrix ShareFile 公司網站 （服務提供者發起的租用戶登入），或使用[[存取面板簡介](active-directory-saas-access-panel-introduction.md)應用程式。

本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用 Citrix ShareFile 應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-citrix-sharefile-tutorial/IC773620.png "案例")
##<a name="enabling-the-application-integration-for-citrix-sharefile"></a>啟用 Citrix ShareFile 應用程式整合

本節的目標是大綱如何啟用 Citrix ShareFile 的整合應用程式。

###<a name="to-enable-the-application-integration-for-citrix-sharefile-perform-the-following-steps"></a>若要啟用的 Citrix ShareFile 整合應用程式，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-citrix-sharefile-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-citrix-sharefile-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-citrix-sharefile-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-citrix-sharefile-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**Citrix ShareFile**。

    ![應用程式組件庫](./media/active-directory-saas-citrix-sharefile-tutorial/IC773621.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**Citrix ShareFile**，，然後按一下要新增應用程式**完成**。

    ![Citrix ShareFile](./media/active-directory-saas-citrix-sharefile-tutorial/IC773622.png "Citrix ShareFile")
##<a name="configuring-single-sign-on"></a>設定單一登入

本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD Citrix ShareFile。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站， **Citrix ShareFile**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![啟用單一登入](./media/active-directory-saas-citrix-sharefile-tutorial/IC773623.png "啟用單一登入")

2.  在**您要如何登入 Citrix ShareFile 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-citrix-sharefile-tutorial/IC773624.png "設定單一登入")

3.  在 [**設定應用程式 URL** ] 頁面上的 [在**Citrix ShareFile 登入 URL** ] 文字方塊中，輸入您使用下列模式的 URL `https://<tenant-name>.shareFile.com`，然後按一下 [**下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-citrix-sharefile-tutorial/IC773625.png "設定應用程式 URL")

4.  在**設定單一登入 Citrix ShareFile 在**頁面上，若要下載您的憑證，按一下 [**下載憑證**] 並儲存在您的電腦上的憑證檔案。

    ![ConfigureSingle 登入](./media/active-directory-saas-citrix-sharefile-tutorial/IC773626.png "ConfigureSingle 登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您**Citrix ShareFile**公司的網站。

6.  在 [工具列] 按一下 [**管理員**]。

7.  在左側的功能窗格中，選取 [**設定單一登入**。

    ![帳戶管理](./media/active-directory-saas-citrix-sharefile-tutorial/IC773627.png "帳戶管理")

8.  在**單一登入 / SAML 2.0 設定**對話方塊頁面，在 [**基本設定**] 底下，執行下列步驟︰

    ![單一登入](./media/active-directory-saas-citrix-sharefile-tutorial/IC773628.png "單一登入")

    1.  按一下 [**啟用 SAML**。
    2.  在 Azure 的傳統入口網站，在 [**設定單一登入在 Citrix ShareFile**對話方塊] 頁面複製的**實體識別碼**值，，然後將它貼到**您 IDP 發行者 / 實體識別碼**文字方塊。
    3.  在 Azure 的傳統入口網站中**設定單一登入在 Citrix ShareFile** ] 對話方塊在頁面上，複製 [**遠端登入 URL**的值，，然後再貼到 [**登入 URL** ] 文字方塊。
    4.  在 Azure 的傳統入口網站中**設定單一登入在 Citrix ShareFile** ] 對話方塊在頁面上，複製 [**遠端登出 URL**的值，，然後再貼到 [**登出 URL** ] 文字方塊。
    5.  按一下 [**變更**] 旁的**X.509 憑證**欄位，然後上傳您從 Azure AD 傳統 portal 下載的憑證。
        ![基本設定](./media/active-directory-saas-citrix-sharefile-tutorial/IC773629.png "基本設定")

9.  按一下 [Citrix ShareFile 管理入口網站上的 [**儲存**]。

10. 在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-citrix-sharefile-tutorial/IC773630.png "設定單一登入")
##<a name="configuring-user-provisioning"></a>設定使用者佈建

若要啟用 Azure AD 使用者登入 Citrix ShareFile，他們必須佈建到 Citrix ShareFile。  
若是 Citrix ShareFile 佈建是手動的工作。

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>佈建使用者帳戶，請執行下列步驟︰

1.  登入您的**Citrix ShareFile**租用戶。

2.  按一下 [**管理使用者\>管理使用者常用\>+ 建立員工**。

    ![建立員工](./media/active-directory-saas-citrix-sharefile-tutorial/IC781050.png "建立員工")

3.  輸入**電子郵件**、**名字**和**姓氏**的有效 Azure AD 帳戶您想要佈建。

    ![基本資訊](./media/active-directory-saas-citrix-sharefile-tutorial/IC799951.png "基本資訊")

4.  按一下 [**新增使用者**]。

    >[AZURE.NOTE] AAD 帳戶擁有者會收到電子郵件，並確認其帳戶，再會變成作用中連結。

>[AZURE.NOTE] 您可以使用任何其他 Citrix ShareFile 使用者帳戶建立工具或 Api 提供 Citrix ShareFile 佈建 AAD 使用者帳戶。

##<a name="assigning-users"></a>將使用者指派

若要測試您的設定，您需要授與 Azure AD 使用者想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-citrix-sharefile-perform-the-following-steps"></a>若要指定 Citrix ShareFile 使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  在**Citrix ShareFile**應用程式整合頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-citrix-sharefile-tutorial/IC773631.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-citrix-sharefile-tutorial/IC767830.png "[是]")

如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。
