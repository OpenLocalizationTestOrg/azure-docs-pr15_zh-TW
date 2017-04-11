<properties
    pageTitle="教學課程︰ Azure Active Directory 整合 Lifesize 雲端 |Microsoft Azure"
    description="瞭解如何設定單一登入 Azure Active Directory 和 Lifesize 雲端之間。"
    services="active-directory"
    documentationCenter=""
    authors="jeevansd"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>教學課程︰ 使用 Lifesize 雲端的 Azure Active Directory 整合

在本教學課程中，您可以瞭解如何與 Azure Active Directory (Azure AD) 整合 Lifesize 雲端。

Azure AD 與整合 Lifesize 雲端為您提供下列優點︰

- 您可以控制可存取至 Lifesize 雲端 Azure AD
- 您可以使用其 Azure AD 帳戶啟用使用者會自動取得登入至 Lifesize 雲端 （單一登入）
- 您可以管理您的帳戶，在一個中央位置-Azure 傳統入口網站

如果您想要知道 Azure AD SaaS 應用程式整合相關的更多詳細資料，請參閱[什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必要條件

若要設定 Azure AD 整合與 Lifesize 雲端，您需要下列項目︰

- Azure AD 訂閱
- 啟用訂閱 Lifesize 雲端單一正負號


> [AZURE.NOTE] 若要測試此教學課程中的步驟，我們不建議使用生產環境。


若要測試此教學課程中的步驟，您應該先按照這些建議︰

- 您應該使用生產環境，除非這是必要的。
- 如果您沒有安裝 Azure AD 試用環境，您可以在一個月試用[這裡](https://azure.microsoft.com/pricing/free-trial/)取得。


## <a name="scenario-description"></a>案例說明
在本教學課程中，您測試 Azure AD 單一登入的測試環境中。

本教學課程中所述的案例是由兩個主要的建置組塊所組成︰

1. 從圖庫新增 Lifesize 雲端
2. 設定及測試 Azure AD 單一登入


## <a name="adding-lifesize-cloud-from-the-gallery"></a>從圖庫新增 Lifesize 雲端
若要設定 Lifesize 雲端整合 Azure AD，必須將 Lifesize 雲端從圖庫新增至您的受管理的 SaaS 應用程式清單。

**若要從圖庫新增 Lifesize 雲端，請執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![Active Directory][1]
2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [在上方的功能表中的 [**應用程式**]。

    ![應用程式][2]

4. 按一下 [**新增**頁面的底部。

    ![應用程式][3]

5. 在 [**您想要做什麼**] 對話方塊中，按一下 [**新增應用程式，從圖庫**。

    ![應用程式][4]

6. 在 [搜尋] 方塊中，輸入**Lifesize 雲端**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_01.png)

7. 在 [結果] 窗格中，選取**Lifesize 雲端**，，然後按一下要新增應用程式**完成**。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>設定及測試 Azure AD 單一登入
在此區段中，您設定和測試 Azure AD 單一登入有 Lifesize 雲端基礎測試使用者稱為 「 許承恩 」。

單一登入的工作，必須知道對應使用者 Lifesize 雲端功能使用者 Azure AD Azure AD。 也就是說，必須建立連結和之間的關聯 Azure AD 使用者 Lifesize 雲端相關的使用者。

所指派**的使用者名稱**的值為 Lifesize 雲端**使用者名稱**的值 Azure AD 中建立此連結關聯。

若要設定並測試 Azure AD 單一登入與 Lifesize 雲端，您需要完成下列建置組塊︰

1. **[Azure AD 設定單一登入](#configuring-azure-ad-single-sign-on)**-若要讓使用者能使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)**-Azure AD 單一登入與許承恩測試。
3. **[建立 Lifesize 雲端測試使用者](#creating-a-lifesize-cloud-test-user)**-將對應的許承恩 Lifesize 雲端 Azure AD 表示她的連結。
4. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)**-啟用許承恩使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)**-驗證是否設定運作。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD 單一登入設定

在此區段中，您可以 Azure AD 單一登入 [傳統] 入口網站中啟用並 Lifesize 雲端應用程式中設定單一登入。


**若要使用 Lifesize 雲端設定 Azure AD 單一登入，請執行下列步驟︰**

1. 在 [傳統] 入口網站， **Lifesize 雲端**應用程式整合在頁面上，按一下 [**設定單一登入**以開啟 [**設定單一登入**] 對話方塊。
     
    ![設定單一登入][6] 

2. 在**您要如何登入 Lifesize 雲端的使用者**] 頁面上，選取**Azure AD 單一登入**，然後按 [**下一步**。

    ![設定單一登入](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_03.png) 

3. **設定應用程式設定**] 對話方塊在頁面上，執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_04.png) 

    。 在 [**登入 URL** ] 文字方塊中，輸入您登入您的 Lifesize 雲端應用程式，使用下列模式的使用者所使用的 URL: **https://login.lifesizecloud.com/ls/?acs**。
    
    b。 按一下 [**下一步**
 
4. 在**設定單一登入 Lifesize 雲端在**頁面上，執行下列步驟︰

    ![設定單一登入](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_05.png)

    。 按一下 [**下載憑證**]，然後儲存您的電腦上的檔案。

    b。 按一下 [**下一步**]。


5. 若要取得 SSO 設定您的應用程式登入至 Lifesize 雲端應用程式中使用管理員權限。

6. 在右上角按一下您的名稱，然後按一下**進階設定**

    ![設定單一登入](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

7. 在 [進階設定立即按一下 [ **SSO 設定**連結。 這會開啟您的執行個體的 SSO 設定頁面。

    ![設定單一登入](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

8. 現在 SSO 設定使用者介面中設定下列的值。    

    ![設定單一登入](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)

    • 複製 Azure AD 發行者 URL 的值，並貼上的**身分識別提供者發行者**文字方塊。

    • 遠端登入 URL 的值複製 Azure AD，並在**登入 URL** ] 文字方塊中貼上。

    • [記事本] 開啟下載的憑證，並且複製內容的憑證，不包括開始的憑證和憑證結尾的線條， **X.509 憑證**] 文字方塊中貼上。

    • SAML 屬性對應的**第一個名稱**] 文字方塊中輸入值為**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**

    • SAML 屬性對應的**最後一個名稱**] 文字方塊中輸入值為**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**

    • SAML 屬性對應的 [**電子郵件**的文字] 方塊中輸入值為**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**

9. 若要檢查的設定，您可以按一下 [**測試**] 按鈕上。

    > [AZURE.NOTE] 如果測試成功必須完成設定精靈中 Azure AD 並也提供的存取權的使用者或群組管理員可以執行測試。
    
10. 核取 [**啟用 SSO** ] 按鈕，以啟用 SSO。

11. 現在按一下 [**更新**] 按鈕，好讓所有設定會都儲存。 這會產生 RelayState 值。 複製的 [文字] 方塊中會產生 RelayState 值。 我們需要此值，接下來的步驟。

12. 在 [傳統] 入口網站，選取單一登入設定確認，然後按 [**下一步**。
    
    ![Azure AD 單一登入][10]

13. 在**單一登入確認**] 頁面上，按一下 [**完成**]。  
 
    ![Azure AD 單一登入][11]

14. 現在將管理入口網站 Azure **https://portal.azure.com**使用管理員認證登入

15. 按一下左側的功能窗格中的**其他服務**連結
    
    ![設定單一登入](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_09.png)

16. Azure Active Directory 和**Azure Active Directory**連結按一下搜尋
    
    ![設定單一登入](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_10.png)

17. 您會發現 [**企業應用程式**] 按鈕下的所有 SaaS 應用程式。

    ![設定單一登入](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_11.png)

18. 接著按一下 [下一步刀中的**所有應用程式**連結
    
    ![設定單一登入](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_12.png)

19. 搜尋您要設定 RelayState Lifesize 應用程式。 
    
    ![設定單一登入](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_13.png)

20. 現在，請按一下刀中的**單一登入**連結

    ![設定單一登入](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_14.png)

21. 您會看到 [**顯示進階 URL 設定**] 核取方塊。 按一下核取方塊。
    
    ![設定單一登入](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_15.png)
    
22. 現在設定應用程式，就會看到 [Lifesize 應用程式 SSO 設定] 頁面中的 RelayState。 

    ![設定單一登入](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_16.png)

23. 儲存設定。

### <a name="creating-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者
在此區段中，您可以建立在傳統入口網站中名為許承恩測試使用者。


![建立 Azure AD 使用者][20]

**若要建立的測試使用者 Azure AD 中，執行下列步驟︰**

1. 在**Azure 傳統入口網站**，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_09.png) 

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 若要顯示的使用者清單頂端的功能表中，按一下 [**使用者**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_03.png) 

4. 若要開啟 [**新增使用者**] 對話方塊底部的工具列中，按一下 [**新增使用者**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_04.png) 

5. 在 [**告訴我們瞭解此使用者**的對話方塊頁面，請執行下列步驟︰ ![建立 Azure AD 測試使用者](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_05.png) 

    。 為使用者類型，選取您組織中的 [新的使用者]。

    b。 在 [使用者名稱]**文字方塊**中，輸入**BrittaSimon**。

    c。 按一下 [**下一步**]。

6.  在 [**使用者設定檔**] 對話方塊頁面，請執行下列步驟︰![建立 Azure AD 測試使用者](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_06.png) 

    。 在 [**名字**] 文字方塊中輸入**許**。  

    b。 在 [**姓氏**] 文字方塊中輸入，**承恩**。

    c。 在 [**顯示名稱**] 文字方塊中，輸入**許承恩**。

    d。 在 [**角色**] 清單中選取 [**使用者**]。

    e。 按一下 [**下一步**]。

7. 在 [**取得暫時密碼**] 對話方塊頁面上，按一下 [**建立**]。

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_07.png) 

8. **取得暫時密碼**] 對話方塊在頁面上，執行下列步驟︰

    ![建立 Azure AD 測試使用者](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_08.png) 

    。 寫下的值的**新密碼**。

    b。 按一下 [**完成**]。   



### <a name="creating-an-lifesize-cloud-test-user"></a>建立 Lifesize 雲端測試使用者

在此區段中，您可以建立稱為許承恩 Lifesize 雲端中的使用者。 自動使用者佈建支援 Lifesize 雲端。 順利驗證後，Azure AD，使用者會自動提供應用程式中。 


### <a name="assigning-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在此區段中，您可以啟用許承恩 Azure 單一登入使用其存取權授與 Lifesize 雲端。

![為使用者指派][200] 

**若要指定 Lifesize 雲端許承恩，請執行下列步驟︰**

1. 在 [傳統] 入口網站，若要開啟 [應用程式] 檢視中，在 [目錄] 檢視中，按一下 [**應用程式**頂端的功能表中。

    ![為使用者指派][201] 

2. 在應用程式清單中，選取 [ **Lifesize 雲端**]。

    ![設定單一登入](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_50.png) 

3. 在頂端的功能表，按一下 [**使用者**]。

    ![為使用者指派][203]

4. 在 [使用者] 清單中，選取**許承恩**。

5. 在底部工具列中，按一下 [**指派**]。

    ![為使用者指派][205]


### <a name="testing-single-sign-on"></a>測試單一登入

在此區段中，您測試 Azure AD 單一登入設定使用 Access 面板。

當您按一下 Lifesize 雲端存取] 面板中的標題時，您應該取得自動登入 Lifesize 雲端應用程式。


## <a name="additional-resources"></a>其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程的清單](active-directory-saas-tutorial-list.md)
* [什麼是應用程式存取和單一登入與 Azure Active Directory？](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_205.png
