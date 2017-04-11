<properties 
    pageTitle="教學課程︰ 以集結軟體的 Azure Active Directory 整合 |Microsoft Azure" 
    description="瞭解如何使用集結軟體與 Azure Active Directory 啟用單一登入、 自動化佈建和更多 ！" 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-rally-software"></a>教學課程︰ 以集結軟體的 Azure Active Directory 整合
  
本教學課程中的目標是以顯示 Azure 和集結軟體的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   集結軟體租用戶
  
本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用集結軟體應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-rally-software-tutorial/IC769525.png "案例")
##<a name="enabling-the-application-integration-for-rally-software"></a>啟用集結軟體應用程式整合
  
本節的目標是大綱如何啟用集結軟體應用程式整合。

###<a name="to-enable-the-application-integration-for-rally-software-perform-the-following-steps"></a>若要啟用集結軟體應用程式整合，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-rally-software-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-rally-software-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-rally-software-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-rally-software-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**集結**。

    ![應用程式組件庫](./media/active-directory-saas-rally-software-tutorial/IC769526.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**集結軟體**，，然後按一下要新增應用程式**完成**。

    ![集結軟體](./media/active-directory-saas-rally-software-tutorial/IC769527.png "集結軟體")
##<a name="configuring-single-sign-on"></a>設定單一登入
  
本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD 集結軟體。  
此程序的一部分，您需要上傳至集結軟體的憑證。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 [Azure 傳統入口網站，**集結軟體**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-rally-software-tutorial/IC749323.png "設定單一登入")

2.  **如何要登入集結的使用者**] 頁面上選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![Microsoft Azure AD 單一登入](./media/active-directory-saas-rally-software-tutorial/IC769528.png "Microsoft Azure AD 單一登入")

3.  在 [**設定應用程式 URL** ] 頁面的 [在**集結軟體租用戶 URL** ] 文字方塊中，輸入您使用下列模式的 URL 「*https://\<租用戶名稱\>。 rally.com*」，然後按一下 [**下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-rally-software-tutorial/IC769529.png "設定應用程式 URL")

4.  在**設定單一登入集結在**頁面上，按一下下載的中繼資料，然後將其儲存在您的電腦上。

    ![設定單一登入](./media/active-directory-saas-rally-software-tutorial/IC769530.png "設定單一登入")

5.  登入您的**集結軟體**租用戶。

6.  在工具列在頂端，按一下 [**設定**]，然後選取**訂閱**。

    ![訂閱](./media/active-directory-saas-rally-software-tutorial/IC769531.png "訂閱")

7.  按一下 [在右側的 [工具] 列中的 [**動作**] 按鈕，然後選取 [**編輯訂閱**。

8.  在**訂閱**對話方塊頁面上，執行下列步驟，然後按 [**儲存並關閉]**:

    ![驗證](./media/active-directory-saas-rally-software-tutorial/IC769542.png "驗證")

    1.  從驗證下拉式清單中選取**集結或 SSO 驗證**
    2.  Azure 傳統入口網站，在 [**設定單一登入集結軟體**對話方塊] 頁面中的**身分識別提供者識別碼**值，複製，然後再貼到 [**身分識別提供者 URL** ] 文字方塊
    3.  在 Azure 的傳統入口網站中**設定單一登入集結軟體**] 對話方塊在頁面上，複製 [**遠端登出 URL**值。

9.  在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-rally-software-tutorial/IC769547.png "設定單一登入")
##<a name="configuring-user-provisioning"></a>設定使用者佈建
  
AAD 使用者可以登入，他們必須提供集結軟體應用程式使用他們的 Azure Active Directory 使用者名稱。

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要設定使用者佈建，執行下列步驟︰

1.  登入您的集結軟體租用戶。

2.  移至 [**設定\>使用者**，然後按一下 [ **+ 新增新增**。

    ![使用者](./media/active-directory-saas-rally-software-tutorial/IC781039.png "使用者")

3.  在新的使用者] 文字方塊中輸入名稱，然後按一下 [**新增詳細資料**。

4.  在 [**建立使用者**] 區段中，執行下列步驟︰

    ![建立使用者](./media/active-directory-saas-rally-software-tutorial/IC781040.png "建立使用者")

    1.  在 [**使用者名稱**] 文字方塊中，輸入您想要佈建 Azure AD 使用者的名稱。
    2.  在 [**電子郵件地址**] 文字方塊中，輸入您想要佈建 Azure AD 使用者的電子郵件地址。
    3.  按一下 [**儲存並關閉**]。

>[AZURE.NOTE]您可以使用任何其他集結軟體使用者帳戶建立工具或 Api 提供集結軟體佈建 AAD 使用者帳戶。

##<a name="assigning-users"></a>將使用者指派
  
若要測試您的設定，您需要授與 Azure AD 使用者想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-rally-software-perform-the-following-steps"></a>若要指定集結軟體的使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **集結軟體**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-rally-software-tutorial/IC769548.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-rally-software-tutorial/IC767830.png "[是]")
  
如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。




