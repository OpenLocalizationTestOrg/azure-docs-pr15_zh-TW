<properties 
    pageTitle="教學課程︰ Azure Active Directory 整合 PolicyStat |Microsoft Azure" 
    description="瞭解如何使用 PolicyStat 與 Azure Active Directory 啟用單一登入、 自動化佈建和更多 ！" 
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

#<a name="tutorial-azure-active-directory-integration-with-policystat"></a>教學課程︰ 使用 PolicyStat 的 Azure Active Directory 整合
  
本教學課程中的目標是以顯示 Azure 和 PolicyStat 的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   PolicyStat 租用戶
  
完成後本教學課程中，您指派給 PolicyStat Azure AD 使用者將能夠單一登入 PolicyStat 公司網站 （服務提供者發起的租用戶登入），或使用[[存取面板簡介](active-directory-saas-access-panel-introduction.md)應用程式。
  
本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用 PolicyStat 應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-policystat-tutorial/IC808662.png "案例")
##<a name="enabling-the-application-integration-for-policystat"></a>啟用 PolicyStat 應用程式整合
  
本節的目標是大綱如何啟用 PolicyStat 的整合應用程式。

###<a name="to-enable-the-application-integration-for-policystat-perform-the-following-steps"></a>若要啟用的 PolicyStat 整合應用程式，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-policystat-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-policystat-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-policystat-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-policystat-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**PolicyStat**。

    ![應用程式組件庫](./media/active-directory-saas-policystat-tutorial/IC808627.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**PolicyStat**，，然後按一下要新增應用程式**完成**。

    ![PolicyStat](./media/active-directory-saas-policystat-tutorial/IC810430.png "PolicyStat")
##<a name="configuring-single-sign-on"></a>設定單一登入
  
本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD PolicyStat。  
PolicyStat 應用程式的期待 SAML 判斷提示以特定格式會要求您將自訂屬性對應至**saml 權杖屬性**設定。  
以下螢幕擷取畫面會顯示這個範例。

![屬性](./media/active-directory-saas-policystat-tutorial/IC808628.png "屬性")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 Azure 的傳統入口網站， **PolicyStat**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-policystat-tutorial/IC808629.png "設定單一登入")

2.  在**您要如何登入 PolicyStat 的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-policystat-tutorial/IC808630.png "設定單一登入")

3.  **設定應用程式設定**] 頁面上的 [在 [**登入 URL** ] 文字方塊中，輸入您登入您的 URL PolicyStat 應用程式的使用者所使用的 URL (例如: *「 https://demo-azure.policystat.com 」*)，然後按一下 [**下一步**。

    ![設定應用程式設定](./media/active-directory-saas-policystat-tutorial/IC808631.png "設定應用程式設定")

4.  在**設定單一登入 PolicyStat 在**頁面上，按一下**下載的中繼資料**]，然後儲存您的電腦上的中繼資料檔案。

    ![設定單一登入](./media/active-directory-saas-policystat-tutorial/IC808632.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您 PolicyStat 公司的網站。

6.  按一下 [**管理員**] 索引標籤，然後按一下**單一登入設定**在左側的功能窗格中。

    ![系統管理員] 功能表](./media/active-directory-saas-policystat-tutorial/IC808633.png "系統管理員] 功能表")

7.  在 [**設定**] 區段中，選取 [**啟用單一登入整合**]。

    ![單一登入設定](./media/active-directory-saas-policystat-tutorial/IC808634.png "單一登入設定")

8.  按一下 [**設定屬性**]，然後在 [**設定屬性**] 區段中，執行下列步驟︰

    ![單一登入設定](./media/active-directory-saas-policystat-tutorial/IC808635.png "單一登入設定")

    1.  在 [**使用者名稱屬性**] 文字方塊中，輸入**uid**。
    2.  在**第一個名稱屬性**] 文字方塊中輸入**名字**。
    3.  在**最後一個名稱屬性**] 文字方塊中輸入**[姓氏]**。
    4.  在 [**電子郵件屬性**] 文字方塊中，輸入**電子郵件地址**。
    5.  按一下 [**儲存變更**]。

9.  按一下 [**您的 IDP 中繼資料**，，然後在 [**您的 IDP 中繼資料**] 區段中，執行下列步驟︰

    ![單一登入設定](./media/active-directory-saas-policystat-tutorial/IC808635.png "單一登入設定")

    1.  開啟下載的中繼資料的檔案與複製內容，然後貼到**您的身分識別提供者中繼資料**文字方塊
    2.  按一下 [**儲存變更**]。

10. 在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-policystat-tutorial/IC771723.png "設定單一登入")

11. 12. 在頂端的功能表，按一下 [**屬性**開啟 [ **SAML 權杖屬性**] 對話方塊。

    ![屬性](./media/active-directory-saas-policystat-tutorial/IC795920.png "屬性")

13. 若要新增必要的屬性對應，請執行下列步驟︰

    ![屬性](./media/active-directory-saas-policystat-tutorial/IC804823.png "屬性")

    1.  按一下 [**新增使用者屬性**]。
    2.  在 [**屬性名稱**] 文字方塊中，輸入**uid**。
    3.  在 [**屬性值**] 文字方塊中，選取 [ **ExtractMailPrefix()**]。
    4.  從 [**郵件**] 清單中，選取 [ **User.mail**]。
    5.  按一下 [**完成**]。
##<a name="configuring-user-provisioning"></a>設定使用者佈建
  
若要啟用 Azure AD 使用者登入 PolicyStat，他們必須佈建到 PolicyStat。  
PolicyStat 支援僅在時間使用者佈建。 這表示您不需要手動將使用者新增至 PolicyStat。  
使用者會取得自動加入上透過單一登其第一個登入上。

>[AZURE.NOTE]您可以使用任何其他 PolicyStat 使用者帳戶建立工具或 Api 提供 PolicyStat 佈建 AAD 使用者帳戶。

##<a name="assigning-users"></a>將使用者指派
  
若要測試您的設定，您需要授與 Azure AD 使用者想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-policystat-perform-the-following-steps"></a>若要指定 PolicyStat 使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **PolicyStat**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-policystat-tutorial/IC808636.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-policystat-tutorial/IC767830.png "[是]")
  
如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。