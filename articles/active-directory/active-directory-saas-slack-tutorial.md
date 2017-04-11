<properties 
    pageTitle="教學課程︰ 使用可寬延時間的 Azure Active Directory 整合 |Microsoft Azure" 
    description="瞭解如何使用 Azure Active Directory 中的可寬延時間，若要啟用單一登入、 自動化佈建和更多 ！" 
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
    ms.date="09/19/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-slack"></a>教學課程︰ 使用可寬延時間的 Azure Active Directory 整合
  
本教學課程中的目標是以顯示 Azure 和可寬延時間的整合。  
本教學課程中所述的案例假設您已經有下列項目︰

-   有效的 Azure 訂閱
-   可寬延時間單一登入啟用訂閱
  
完成本教學課程之後，您已指派到可寬延時間 Azure AD 使用者將無法單一的登入應用程式，您可寬延時間的公司的網站 （服務提供者發起的租用戶登入），或使用[[存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中所述的案例是由下列建置組塊所組成︰

1.  啟用整合應用程式的可寬延時間
2.  設定單一登入
3.  設定使用者佈建
4.  將使用者指派

![案例](./media/active-directory-saas-slack-tutorial/IC794980.png "案例")

##<a name="enabling-the-application-integration-for-slack"></a>啟用整合應用程式的可寬延時間
  
本節的目標是大綱如何啟用整合應用程式的可寬延時間。

###<a name="to-enable-the-application-integration-for-slack-perform-the-following-steps"></a>若要啟用整合應用程式的可寬延時間，請執行下列步驟︰

1.  Azure 傳統入口網站中，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory](./media/active-directory-saas-slack-tutorial/IC700993.png "Active Directory")

2.  從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式](./media/active-directory-saas-slack-tutorial/IC700994.png "應用程式")

4.  按一下 [**新增**頁面的底部。

    ![新增應用程式](./media/active-directory-saas-slack-tutorial/IC749321.png "新增應用程式")

5.  在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![新增 gallerry 應用程式](./media/active-directory-saas-slack-tutorial/IC749322.png "新增 gallerry 應用程式")

6.  在**搜尋] 方塊**中，輸入**可寬延時間**。

    ![應用程式組件庫](./media/active-directory-saas-slack-tutorial/IC794981.png "應用程式組件庫")

7.  在 [結果] 窗格中，選取**可寬延時間**，然後按一下**完成**新增應用程式。

    ![案例](./media/active-directory-saas-slack-tutorial/IC796925.png "案例")

##<a name="configuring-single-sign-on"></a>設定單一登入
  
本節的目標是大綱如何啟用使用者進行驗證其帳戶中使用根據 SAML 通訊協定的同盟 Azure AD 可寬延時間。  
此程序的一部分，您所需建立 64 基本編碼的憑證檔案。  
如果您不熟悉這個程序，請參閱[如何轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要設定單一登入，請執行下列步驟︰

1.  在 [Azure 傳統入口網站，**可寬延時間**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-slack-tutorial/IC794982.png "設定單一登入")

2.  在**如何可寬延時間登入的使用者**] 頁面上，選取**Microsoft Azure AD 單一登入**，然後再按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-slack-tutorial/IC794983.png "設定單一登入")

3.  **設定應用程式 URL**在頁面上，在 [**可寬延時間登入 URL** ] 文字方塊中，輸入您可寬延時間的租用戶的 URL (例如: 「*https://azuread.slack.com*」)，然後按一下 [**下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-slack-tutorial/IC794984.png "設定應用程式 URL")

4.  在**設定單一登入可寬延時間在**頁面上，若要下載您的憑證，按一下**下載憑證**]，然後儲存在本機電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-slack-tutorial/IC794985.png "設定單一登入")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的可寬延時間的公司網站。

6.  移至 [**至 Microsoft Azure AD\>小組設定**。

    ![小組設定](./media/active-directory-saas-slack-tutorial/IC794986.png "小組設定")

7.  在**小組設定**] 區段中，按一下 [**驗證**] 索引標籤，然後按一下 [**變更設定**。

    ![小組設定](./media/active-directory-saas-slack-tutorial/IC794987.png "小組設定")

8.  在 [ **SAML 驗證設定**] 對話方塊中，執行下列步驟︰

    ![SAML 設定](./media/active-directory-saas-slack-tutorial/IC794988.png "SAML 設定")

    1.  Azure 傳統入口網站中，**設定單一登入，可寬延時間**] 對話方塊在頁面上，複製**SAML SSO URL**的值，並將其貼入**SAML 2.0 結束點 (HTTP)**文字方塊。
    2.  在 Azure 的傳統入口網站中**設定單一登入，可寬延時間**] 對話方塊在頁面上，複製**簽發者 URL**的值，，然後再貼到 [**身分識別提供者簽發者**] 文字方塊。
    3.  建立**64 基本編碼**檔案從您下載的憑證。
    
        >[AZURE.TIP] 如需詳細資訊，請參閱[如何轉換成文字檔的二進位憑證](http://youtu.be/PlgrzUZ-Y1o)

    4.  在記事本中開啟您 64 基本編碼的憑證、 將其內容複製到剪貼簿，並貼到**公用憑證**文字方塊。
    5.  取消選取 [**允許使用者變更自己的電子郵件地址**。
    6.  選取 [**允許使用者選擇自己的使用者名稱**]。
    7.  **驗證您的團隊必須使用**，請選取**它為選用步驟**。
    8.  按一下 [**儲存設定**。

9.  在 Azure 傳統的入口網站中，選取單一登入設定確認，然後再按一下**完成**關閉 [**設定單一登入**] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-slack-tutorial/IC794989.png "設定單一登入")

##<a name="configuring-user-provisioning"></a>設定使用者佈建
  
若要啟用 Azure AD 使用者登入可寬延時間，他們必須佈建到可寬延時間。
  
沒有為您設定使用者佈建到可寬延時間的動作項目。  
當指派的使用者嘗試登入可寬延時間時，可寬延時間的帳戶會自動建立必要。

##<a name="assigning-users"></a>將使用者指派
  
若要測試您的設定，您需要授與 Azure AD 使用者想要允許使用您的應用程式存取分派給他們。

###<a name="to-assign-users-to-slack-perform-the-following-steps"></a>若要指定可寬延時間的使用者，請執行下列步驟︰

1.  在 Azure 傳統入口網站中建立測試帳戶。

2.  **可寬延時間**應用程式整合在頁面上，按一下 [**指派給使用者**。

    ![為使用者指派](./media/active-directory-saas-slack-tutorial/IC794990.png "為使用者指派")

3.  選取您測試的使用者、**指派**，請按一下 [，然後按一下**[是]**以確認您的工作分派。

    ![[是]](./media/active-directory-saas-slack-tutorial/IC767830.png "[是]")
  
如果您想要測試您的單一登入設定，開啟 [存取面板。 如需存取畫面的詳細資訊，請參閱[簡介存取面板](active-directory-saas-access-panel-introduction.md)。