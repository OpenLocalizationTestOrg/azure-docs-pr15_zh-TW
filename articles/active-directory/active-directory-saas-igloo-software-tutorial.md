<properties 
    pageTitle="教學課程︰ 使用 Igloo 軟體的 Azure Active Directory 整合 |Microsoft Azure" 
    description="瞭解如何使用 Igloo 軟體與 Azure Active Directory 啟用單一登入、 自動化佈建和更多 ！" 
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
    ms.date="10/20/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>教學課程︰ 使用 Igloo 軟體的 Azure Active Directory 整合
  
本教學課程中的目標是以顯示 Azure 和 Igloo 軟體的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   啟用訂閱[Igloo 軟體](http://www.igloosoftware.com/)單一登
  
完成後本教學課程中，您指派給 Igloo 軟體 Azure AD 使用者將能夠單一登入 Igloo 軟體公司網站 （服務提供者發起的租用戶登入），或使用[[存取面板簡介](active-directory-saas-access-panel-introduction.md)應用程式。
  
本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用 Igloo 軟體應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-igloo-software-tutorial/IC783961.png "案例")
##<a name="enabling-the-application-integration-for-igloo-software"></a>啟用 Igloo 軟體應用程式整合
  
本節的目標是大綱如何啟用 Igloo 軟體應用程式整合。

###<a name="to-enable-the-application-integration-for-igloo-software-perform-the-following-steps"></a>若要啟用 Igloo 軟體應用程式整合，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-igloo-software-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-igloo-software-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-igloo-software-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-igloo-software-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**Igloo 軟體**。

    ![應用程式組件庫](./media/active-directory-saas-igloo-software-tutorial/IC783962.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**Igloo 軟體**，然後按一下**完成**新增應用程式。

    ![igloo](./media/active-directory-saas-igloo-software-tutorial/IC783963.png "igloo")
##<a name="configuring-single-sign-on"></a>設定單一登入
  
本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD Igloo 軟體。  
此程序的一部分，您會需要上傳至您的中央桌面租用戶的底數 64 編碼的憑證。  
如果您不熟悉這個程序，請參閱[如何將轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)。

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站， **Igloo 軟體**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-igloo-software-tutorial/IC783964.png "設定單一登入")

2.  在**您要如何登入 Igloo 軟體的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![Microsoft Azure AD 單一登入](./media/active-directory-saas-igloo-software-tutorial/IC783965.png "Microsoft Azure AD 單一登入")

3.  **設定應用程式 URL**在頁面上，在 [ **Igloo 軟體登入 URL** ] 文字方塊中，輸入您使用下列模式 」*https://company.igloocommunities.com/?signin*」 的 URL，然後再按 [**下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-igloo-software-tutorial/IC773625.png "設定應用程式 URL")

4.  在**設定單一登入 Igloo 軟體在**頁面上，若要下載您的憑證，按一下**下載憑證**]，然後儲存在本機電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-igloo-software-tutorial/IC783966.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您 Igloo 軟體公司的網站。

6.  移至 [**控制台**]。

    ![[控制台]](./media/active-directory-saas-igloo-software-tutorial/IC799949.png "[控制台]")

7.  在 [**成員資格**] 索引標籤中按一下 [**登入設定**。

    ![登入設定](./media/active-directory-saas-igloo-software-tutorial/IC783968.png "登入設定")

8.  在 SAML 組態] 區段中，按一下 [**設定 SAML 驗證**]。

    ![SAML 設定](./media/active-directory-saas-igloo-software-tutorial/IC783969.png "SAML 設定")

9.  在 [**一般設定**] 區段中，執行下列步驟︰

    ![一般設定](./media/active-directory-saas-igloo-software-tutorial/IC783970.png "一般設定")

    1.  在 [**連線名稱**] 文字方塊中，輸入您的設定的自訂名稱。
    2.  Azure 傳統入口網站，在 [**設定單一登入 Igloo 軟體**對話] 頁面中複製 [**遠端登入 URL**的值，，然後再貼到 [ **IdP 登入 URL** ] 文字方塊。
    3.  Azure 傳統入口網站，在 [**設定單一登入 Igloo 軟體**對話] 頁面中複製 [**遠端登出 URL**的值，，然後再貼到 [ **IdP 登出 URL** ] 文字方塊。
    4.  為**登出回應和要求 HTTP 類型**，請選取 [**文章**]。
    5.  下載憑證建立文字檔案。
        
        >[AZURE.TIP]如需詳細資訊，請參閱[如何轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)

    6.  移除您的憑證的文字檔案版本中的第一行及最後一行複製剩餘的憑證文字，，然後貼到**公用憑證**文字方塊。

10. 在**回應和驗證設定**，請執行下列步驟︰

    ![回應和驗證設定](./media/active-directory-saas-igloo-software-tutorial/IC783971.png "回應和驗證設定")

    1.  為**身分識別提供者**，請選取 [ **Microsoft ADFS**]。
    2.  為**識別碼類型**，選取 [**電子郵件地址**]。
    3.  在 [**電子郵件屬性**] 文字方塊中，輸入**電子郵件地址**。
    4.  在**第一個名稱屬性**] 文字方塊中輸入**givenname**。
    5.  在**最後一個名稱屬性**] 文字方塊中輸入**姓氏**。

11. Preform 下列步驟，以完成設定︰

    ![在登入的使用者建立](./media/active-directory-saas-igloo-software-tutorial/IC783972.png "在登入的使用者建立")

    1.  為**使用者建立在登入**]，選取 [**建立新的使用者在您的網站，當使用者登入**。
    2.  **登入設定**，請選取**[登入] 畫面上的使用 SAML] 按鈕**。
    3.  按一下 [**儲存**]。

12. 在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-igloo-software-tutorial/IC783973.png "設定單一登入")
##<a name="configuring-user-provisioning"></a>設定使用者佈建
  
沒有為您設定使用者佈建 Igloo 軟體的動作項目。  
當指派的使用者嘗試登入 Igloo 軟體使用 access 面板時，檢查有 Igloo 軟體使用者是否存在。  
如果不有任何使用者帳戶還，就會自動建立 Igloo 軟體。
##<a name="assigning-users"></a>將使用者指派
  
若要測試您的設定，您需要授與 Azure AD 使用者想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-igloo-software-perform-the-following-steps"></a>若要指定 Igloo 軟體的使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **Igloo 軟體**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-igloo-software-tutorial/IC783974.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-igloo-software-tutorial/IC767830.png "[是]")
  
如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。